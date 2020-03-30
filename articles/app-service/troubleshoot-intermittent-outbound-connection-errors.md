---
title: Solução de problemas de conexão de saída intermitente no Serviço de Aplicativos Do Azure
description: Solucionar problemas de conexão intermitente e problemas de desempenho relacionados no Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 03/24/2020
ms.author: ramakoni
ms.custom: security-recommendations
ms.openlocfilehash: 028ddccdb989d35710e387081b08a3b973d75bdc
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80367545"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Solução de problemas de conexão de saída intermitente no Serviço de Aplicativos Do Azure

Este artigo ajuda você a solucionar problemas de conexão intermitente e problemas de desempenho relacionados no [Azure App Service](https://docs.microsoft.com/azure/app-service/overview). Este tópico fornecerá mais informações sobre e metodologias de solução de problemas para o esgotamento das portas de tradução de rede de endereços de origem (SNAT). Se você precisar de mais ajuda em qualquer ponto deste artigo, entre em contato com os especialistas do Azure no [MSDN Azure e nos fóruns Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativamente, registre um incidente de suporte ao Azure. Vá ao site de suporte do [Azure](https://azure.microsoft.com/support/options/) e **selecione Obter suporte**.

## <a name="symptoms"></a>Sintomas

Aplicativos e funções hospedados no serviço do Azure App podem apresentar um ou mais dos seguintes sintomas:

* Tempos de resposta lentos em todas ou algumas das instâncias em um plano de serviço.
* Erros intermitentes de 5xx ou **Bad Gateway**
* Mensagens de erro de tempo
* Não foi possível conectar-se a pontos finais externos (como SQLDB, Service Fabric, outros serviços de aplicativos etc.)

## <a name="cause"></a>Causa

Uma das principais causas desses sintomas é que a instância do aplicativo não é capaz de abrir uma nova conexão com o ponto final externo porque atingiu um dos seguintes limites:

* Conexões TCP: Há um limite no número de conexões de saída que podem ser feitas. Isso está associado ao tamanho do trabalhador utilizado.
* Portas SNAT: Como discutido nas [conexões outbound no Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections), o Azure usa a tradução de endereço de rede de origem (SNAT) e um Balanceador de carga (não exposto aos clientes) para se comunicar com pontos finais fora do Azure no espaço público de endereçoIP. Cada instância no serviço do Azure App recebe inicialmente um número pré-alocado de **128** portas SNAT. Esse limite afeta a abertura de conexões para a mesma combinação de host e porta. Se o aplicativo criar conexões para uma mistura de combinações de endereço e porta, você não usará suas portas SNAT. As portas SNAT são usadas quando você tem chamadas repetidas para o mesmo endereço e combinação de porta. Uma vez que uma porta tenha sido liberada, a porta está disponível para reutilização conforme necessário. O balanceador de carga da Rede Azure recupera a porta SNAT de conexões fechadas somente após esperar 4 minutos.

Quando aplicativos ou funções abrem rapidamente uma nova conexão, eles podem esgotar rapidamente sua cota pré-alocada das 128 portas. Eles são então bloqueados até que uma nova porta SNAT se torne disponível, seja através da alocação dinamicamente de portas SNAT adicionais, ou através da reutilização de uma porta SNAT recuperada. Aplicativos ou funções que estão bloqueados por causa dessa incapacidade de criar novas conexões começarão a experimentar um ou mais dos problemas descritos na seção **Sintomas** deste artigo.

## <a name="avoiding-the-problem"></a>Evitando o problema

Evitar o problema da porta SNAT significa evitar a criação de novas conexões repetidamente para o mesmo host e porta.

Estratégias gerais para mitigar a exaustão da porta SNAT são discutidas na [seção de resolução de problemas](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#problemsolving) das conexões de saída da documentação **do Azure.** Dessas estratégias, as seguintes são aplicáveis a aplicativos e funções hospedados no serviço do Azure App.

### <a name="modify-the-application-to-use-connection-pooling"></a>Modificar o aplicativo para usar o pooling de conexão

* Para agrupar conexões HTTP, revise [as conexões http http com httpclientfactory](https://docs.microsoft.com/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Para obter informações sobre o pool de conexões do SQL Server, revise o pool de [conexão do servidor SQL (ADO.NET)](https://docs.microsoft.com/dotnet/framework/data/adonet/sql-server-connection-pooling).
* Para implementar o pool com aplicativos de framework de entidades, [revise o pool dbcontext](https://docs.microsoft.com/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling).

Aqui está uma coleção de links para implementar o pool de conexão por diferentes pilhas de soluções.

#### <a name="node"></a>Nó

Por padrão, as conexões para NodeJS não são mantidas vivas. Abaixo estão os populares bancos de dados e pacotes para pool de conexões que contêm exemplos de como implementá-los.

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP Mantenha-se vivo

* [agentekeepalive](https://www.npmjs.com/package/agentkeepalive)
* [Documentação do Node.js v13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Abaixo estão as bibliotecas populares usadas para pool de conexões JDBC que contêm exemplos de como implementá-las: Pooling de conexão JDBC.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Pooling de conexão HTTP

* [Gerenciamento de conexão Apache](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Poolde classehttpClientClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Embora o PHP não suporte o pool de conexões, você pode tentar usar conexões de banco de dados persistentes ao seu servidor back-end.
 
* Servidor MySQL

   * [Conexões MySQLi para versões](https://www.php.net/manual/mysqli.quickstart.connections.php) mais recentes
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) para versões mais antigas do PHP

* Outras fontes de dados

   * [Gerenciamento de conexões PHP](https://www.php.net/manual/en/pdo.connections.php)

#### <a name="python"></a>Python

* [Mysql](https://github.com/mysqljs/mysql#pooling-connections)
* [Mongodb](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools) (NOTA: SQLAlchemy pode ser usado com outros bancos de dados além do MicrosoftSQL Server)
* [HTTP Keep-alive](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)(Keep-Alive é automático ao usar [sessões de objetos de sessão](https://requests.readthedocs.io/en/master/user/advanced/#keep-alive)).

Para outros ambientes, revise documentos específicos do provedor ou do driver para implementar o pool de conexões em seus aplicativos.

### <a name="modify-the-application-to-reuse-connections"></a>Modificar o aplicativo para reutilizar conexões

*  Para obter ponteiros adicionais e exemplos sobre o gerenciamento de conexões em funções do Azure, [revise Gerenciar conexões em Funções Azure](https://docs.microsoft.com/azure/azure-functions/manage-connections).

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>Modificar o aplicativo para usar uma lógica de repetição menos agressiva

* Para orientação adicional e exemplos, [revise o padrão Retry](https://docs.microsoft.com/azure/architecture/patterns/retry).

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Usar keepalives para redefinir o tempo limite ocioso de saída

* Para implementar keepalives para aplicativos Node.js, o review [My node application is making bound calls excessivoout](https://docs.microsoft.com/azure/app-service/app-service-web-nodejs-best-practices-and-troubleshoot-guide#my-node-application-is-making-excessive-outbound-calls).

### <a name="additional-guidance-specific-to-app-service"></a>Orientação adicional específica para o Serviço de Aplicativos:

* Um [teste de carga](https://docs.microsoft.com/azure/devops/test/load-test/app-service-web-app-performance-test) deve simular dados do mundo real em uma velocidade de alimentação constante. Testar aplicativos e funções estresse do mundo real pode identificar e resolver problemas de exaustão da porta SNAT antes do tempo.
* Certifique-se de que os serviços back-end podem retornar as respostas rapidamente. Para resolver problemas de desempenho com o banco de dados Azure SQL, revise [problemas de desempenho do Banco de Dados Azure SQL com o Intelligent Insights](https://docs.microsoft.com/azure/sql-database/sql-database-intelligent-insights-troubleshoot-performance#recommended-troubleshooting-flow).
* Dimensione o plano de serviço de aplicativos para mais instâncias. Para saber mais sobre como dimensionar, consulte [Escalar um aplicativo no Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/manage-scale-up). Cada instância do trabalhador em um plano de serviço de aplicativo é alocada em várias portas SNAT. Se você espalhar seu uso em mais instâncias, poderá obter o uso da porta SNAT por instância abaixo do limite recomendado de 100 conexões de saída, por ponto final remoto exclusivo.
* Considere mudar-se para o [App Service Environment (ASE),](https://docs.microsoft.com/azure/app-service/environment/using-an-ase)onde você está alocado um único endereço IP de saída, e os limites para conexões e portas SNAT são muito maiores.

Evitar os limites de TCP de saída é mais fácil de resolver, pois os limites são definidos pelo tamanho do seu trabalhador. Você pode ver os limites em [Limites Numéricos Sandbox Cross VM - Conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Descrição|Pequeno (A1)|Médio (A2)|Grande (A3)|Nível isolado (ASE)|
|---|---|---|---|---|---|
|conexões|Número de conexões em toda a VM|1920|3968|8064|16.000|

Para evitar limites de TCP de saída, você pode aumentar o tamanho de seus trabalhadores ou escalar horizontalmente.

## <a name="troubleshooting"></a>Solução de problemas

Conhecer os dois tipos de limites de conexão de saída, e o que seu aplicativo faz, deve tornar mais fácil solucionar problemas. Se você sabe que seu aplicativo faz muitas chamadas para a mesma conta de armazenamento, você pode suspeitar de um limite de SNAT. Se o seu aplicativo criar muitas chamadas para endpoints em toda a internet, você suspeitaria que está chegando ao limite da VM.

Se você não conhece o comportamento do aplicativo o suficiente para determinar a causa rapidamente, existem algumas ferramentas e técnicas disponíveis no App Service para ajudar nessa determinação.

### <a name="find-snat-port-allocation-information"></a>Encontre informações de alocação da porta SNAT

Você pode usar o [App Service Diagnostics](https://docs.microsoft.com/azure/app-service/overview-diagnostics) para encontrar informações de alocação de portas SNAT e observar a métrica de alocação de portas SNAT de um site do App Service. Para encontrar informações de alocação da porta SNAT, siga as seguintes etapas:

1. Para acessar os diagnósticos do App Service, navegue até o aplicativo web app service ou o app service environment no [portal Azure](https://portal.azure.com/). Na navegação à esquerda, selecione **Diagnosticar e resolver problemas**.
2. Selecione A Categoria Disponibilidade e Desempenho
3. Selecione o ladrilho de exaustão da porta SNAT na lista de telhas disponíveis na categoria. A prática é mantê-lo abaixo de 128.
Se você precisar, você ainda pode abrir um ticket de suporte e o engenheiro de suporte receberá a métrica de back-end para você.

Observe que, como o uso da porta SNAT não está disponível como métrica, não é possível fazer escala automática com base no uso da porta SNAT ou configurar a escala automática com base na métrica de alocação de portas SNAT.

### <a name="tcp-connections-and-snat-ports"></a>Conexões TCP e Portas SNAT

As conexões TCP e portas SNAT não estão diretamente relacionadas. Um detector de uso de conexões TCP está incluído na lâmina Diagnosticar e Resolver Problemas de qualquer site do App Service. Procure a frase "conexões TCP" para encontrá-la.

* As portas SNAT são usadas apenas para fluxos de rede externos, enquanto as conexões TCP totais incluem conexões de loopback locais.
* Uma porta SNAT pode ser compartilhada por diferentes fluxos, se os fluxos forem diferentes em protocolo, endereço IP ou porta. A métrica TCP Connections conta todas as conexões TCP.
* O limite de conexões TCP acontece no nível de instância do trabalhador. O balanceamento de carga de saída da Rede Azure não usa a métrica Conexões TCP para limitação da porta SNAT.
* Os limites de conexões TCP são descritos em [Limites Numéricos Sandbox Cross VM - Conexões TCP](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Nome limite|Descrição|Pequeno (A1)|Médio (A2)|Grande (A3)|Nível isolado (ASE)|
|---|---|---|---|---|---|
|conexões|Número de conexões em toda a VM|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>Conexões WebJobs e Banco de Dados
 
Se as portas SNAT estiverem esgotadas, onde o WebJobs não puder se conectar ao banco de dados SQL do Azure, não há métrica para mostrar quantas conexões são abertas por cada processo de aplicativo web individual. Para encontrar o problemático WebJob, mova vários WebJobs para outro plano de Serviço de Aplicativo para ver se a situação melhora ou se um problema permanece em um dos planos. Repita o processo até encontrar o WebJob problemático.

### <a name="using-snat-ports-sooner"></a>Usando portas SNAT mais cedo

Não é possível alterar nenhuma configuração do Azure para liberar as portas SNAT usadas mais cedo, pois todas as portas SNAT serão liberadas de acordo com as condições abaixo e o comportamento é por design.
 
* Se o servidor ou o cliente enviarem FINACK, a [porta SNAT será liberada](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#tcp-snat-port-release) após 240 segundos.
* Se um RST for visto, a porta SNAT será liberada após 15 segundos.
* Se o tempo de espera foi atingido, a porta será liberada.
 
## <a name="additional-information"></a>Informações adicionais

* [SNAT com Serviço de Aplicativo](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Solucionar problemas de desempenho de aplicativo lento no Serviço de Aplicativo do Azure](https://docs.microsoft.com/azure/app-service/troubleshoot-performance-degradation)
