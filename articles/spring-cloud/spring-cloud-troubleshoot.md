---
title: Guia de solução de problemas do Azure Spring Cloud | Microsoft Docs
description: Guia de solução de problemas do Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: ebb960085691206b096090813636ef56366e6536
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038355"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Guia de solução de problemas para problemas comuns

Este artigo detalha alguns problemas comuns e etapas de solução de problemas para desenvolvedores que trabalham com o Azure Spring Cloud. Também recomendamos ler nosso [artigo de perguntas frequentes](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Disponibilidade, desempenho e problemas de aplicativos

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Meu aplicativo não pode ser iniciado (por exemplo, o ponto de extremidade não pode ser conectado ou retorna 502 após algumas repetições)

Exporte os logs para o _Azure Log Analytics_. A tabela para os logs do aplicativo Spring é denominada `AppPlatformLogsforSpring`. Para saber mais, acesse [Analisar logs e métricas com as configurações de diagnóstico](diagnostic-services.md)

Encontrar os seguintes erros em seus logs indica um de dois prováveis problemas:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Um dos grãos ou uma de suas dependências está ausente.
* Uma das propriedades do Bean está ausente ou é inválida. Você provavelmente verá `java.lang.IllegalArgumentException` nesse caso.

As associações de serviço também podem causar falhas de início do aplicativo. Use palavras-chave relacionadas aos serviços associados para consultar os logs.  Por exemplo, suponha que seu aplicativo tenha uma associação a uma instância do MySQL definida como hora do sistema local. Se o aplicativo falhar ao iniciar, você poderá encontrar o seguinte erro no log:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Para corrigir esse erro, acesse o `server parameters` de sua instância do MySql e altere `time_zone` de `SYSTEM` para `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Meu aplicativo falha ou gera um erro inesperado

Ao depurar falhas de aplicativo, comece verificando o status da execução e o status de descoberta do aplicativo. Acesse _Gerenciamento de aplicativos_ no portal do Azure para verificar se todos os aplicativos estão _Em execução_ e _ATIVOS_.

* Se o status estiver _Em execução_, mas o status de descoberta não estiver _ATIVO_, acesse [Meu aplicativo não pode ser registrado](#my-application-cannot-be-registered).

* Se o status da descoberta estiver _ATIVO_, acesse _Métricas_ para verificar a integridade do aplicativo. Inspecione as seguintes métricas:


  - `TomcatErrorCount` (_tomcat.global.error_): Todas as exceções de aplicativo Spring serão contadas aqui. Se este número for muito alto, acesse o _Azure Log Analytics_ para inspecionar seus logs de aplicativo.

  - `AppMemoryMax` (_jvm.memory.max_): a quantidade máxima de memória disponível para o aplicativo. Ela pode ser indefinida ou pode ser alterada ao longo do tempo, se definido. A quantidade de memória usada e confirmada será sempre menor ou igual ao máximo se ela for definida. No entanto, uma alocação de memória poderá falhar com `OutOfMemoryError` se ela tentar aumentar a memória usada de tal forma que a usada > compromisso mesmo se usada <= o máximo ainda seria verdadeiro. Nesse caso, tente aumentar o tamanho de heap máximo via parâmetro `-Xmx`.

  - `AppMemoryUsed` (_jvm.memory.used_): a quantidade de memória, em bytes, usada no momento pelo aplicativo. Para um aplicativo Java de carregamento normal, essa série de métricas será formada em um padrão de "sawtooth", em que o uso de memória aumenta e diminui constantemente em pequenos incrementos e deixa muito subitamente e esse padrão se repete. Isso ocorre devido à coleta de lixo dentro da máquina virtual Java, em que as ações de coleta representam quedas no ‘sawteeth’.
    Essa métrica é importante para identificar problemas de memória, como * a explosão de memória no início * alocação de memória de sobretensão para um caminho lógico específico * vazamentos de memória graduais

  Para obter mais detalhes, acesse [Métricas](spring-cloud-concept-metrics.md).

Acesse [este artigo de introdução](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) para começar a usar o _Azure Log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Meu aplicativo experimenta alto uso da CPU ou alta utilização de memória

Se o aplicativo apresentar alto uso de CPU/memória, uma das duas coisas será verdadeira:
* Todas as instâncias de aplicativo apresentam alto uso de CPU/memória ou
* Algumas das instâncias de aplicativo apresentam alto uso de CPU/memória.

Para confirmar qual é a situação,

1. Vá para _Métricas_, selecione `Service CPU Usage Percentage` ou `Service Memory Used`,
2. Adicione um filtro `App=` para especificar qual aplicativo você deseja monitorar e
3. Divida as métricas por `Instance`.

Se todas as instâncias estiverem apresentando alta CPU/memória, você precisará expandir o aplicativo ou aumentar a CPU/memória. Para obter mais detalhes, acesse [Dimensionar aplicativos](spring-cloud-tutorial-scale-manual.md)

Se algumas instâncias estiverem apresentando alta CPU/memória, verifique o status da instância e seu status de descoberta.

Para obter mais detalhes, acesse [Métricas](spring-cloud-concept-metrics.md).

Se todas as instâncias estiverem em execução, vá para o _Azure Log Analytics_ para consultar os logs de aplicativo e examine sua lógica de código para ver se alguma delas pode afetar o particionamento de escala. Para obter mais detalhes, acesse [Analisar logs e métricas com as configurações de diagnóstico](diagnostic-services.md).

Acesse [este artigo de introdução](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) para começar a usar o _Azure Log Analytics_. Consulte os logs usando [Linguagem de Consulta do Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Lista de verificação antes de integrar seu aplicativo Spring ao Azure Spring Cloud

* O aplicativo pode ser executado localmente com a versão do Java Runtime.
* A configuração de ambiente (CPU/RAM/Instâncias) atende ao requisito mínimo definido pelo provedor de aplicativos.
* Os itens de configuração têm valores esperados. Para obter mais informações, confira [Servidor de Configuração](spring-cloud-tutorial-config-server.md).
* As variáveis de ambiente têm valores esperados.
* Os parâmetros de JVM têm valores esperados.
* É recomendável desabilitar/remover o _Servidor de Configuração_ e o serviço _Registro de Serviço do Spring_ do pacote de aplicativos.
* Se algum recurso do Azure for ser associado via _Associação de serviço_, verifique se os recursos de destino estão ativos e em execução.

## <a name="configuration-and-management"></a>Configuração e Gerenciamento

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Encontrei um problema ao criar uma instância do serviço do Azure Spring Cloud

Quando você tentar provisionar uma instância de serviço do _Azure Spring Cloud_ por meio do portal, o Azure Spring Cloud executará a validação para você.

No entanto, se você tentar provisionar a instância de serviço do _Azure Spring Cloud_ por meio da [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ou do [modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), verifique:

* A assinatura está ativa.
* A localização é [compatível](spring-cloud-faq.md) com o _Azure Spring Cloud_.
* O grupo de recursos da instância já foi criado.
* O nome do recurso está em conformidade com a regra de nomenclatura. (Ele pode conter apenas letras minúsculas, números e hifens. O primeiro caractere precisa ser uma letra. O último caractere deve ser uma letra ou um número. O valor precisa ter entre 2 e 32 caracteres.)

Se você tentar provisionar a instância de serviço do _Azure Spring Cloud_ por meio do modelo do Resource Manager, acesse https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates para verificar a sintaxe do modelo.

O nome da instância do serviço do _Azure Spring Cloud_ será usado para solicitar o nome do subdomínio em `azureapps.io`, portanto, o provisionamento falhará se o nome entrar em conflito com um existente. Você pode encontrar mais detalhes nos logs de atividades.

### <a name="i-cannot-deploy-a-jar-package"></a>Não consigo implantar um pacote JAR

Não é possível fazer upload do JAR/pacote de origem por meio do portal ou do modelo do Resource Manager.

Quando você implantar seu pacote de aplicativos usando a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), ele será sondado periodicamente no andamento da implantação e, no final, mostrará o resultado da implantação.

Se a sondagem for interrompida, você ainda poderá usar o seguinte comando para buscar os logs de implantação:

`az spring-cloud app show-deploy-log -n <app-name>`

Verifique se o aplicativo está empacotado no [formato jar executável](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html) correto. Caso contrário, você verá um erro semelhante ao mostrado a seguir:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Não consigo implantar um pacote de origem

Não é possível fazer upload do JAR/pacote de origem por meio do portal ou do modelo do Resource Manager.

Quando você implantar seu pacote de aplicativos através do [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), ele será sondado periodicamente no andamento da implantação e, no final, mostrará o resultado da implantação.

Se a sondagem for interrompida, você ainda poderá usar o seguinte comando para buscar os logs de implantação e build:

`az spring-cloud app show-deploy-log -n <app-name>`

No entanto, observe que uma instância de serviço do _Azure Spring Cloud_ só pode disparar um trabalho de build para um pacote de origem por vez. Para obter mais detalhes, confira [Implantar um aplicativo](spring-cloud-quickstart-launch-app-portal.md) e [Guia do ambiente de preparo](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Meu aplicativo não pode ser registrado

Na maioria dos casos, isso acontece quando as Dependências Necessárias/Descoberta de Serviço não estão configuradas corretamente em seu arquivo POM. Após a configuração, o ponto de extremidade do servidor do Registro de Serviço interno será injetado como uma variável de ambiente com seu aplicativo. Os aplicativos se registrarão no servidor do Registro de Serviço e descobrirão outros microsserviços dependentes.

Aguarde pelo menos 2 minutos antes que uma instância recém-registrada comece a receber tráfego.

Se estiver migrando uma solução baseada no Spring Cloud existente para o Azure, verifique se as instâncias do _Registro de Serviço_ e do _Servidor de Configuração_ são removidas (ou desabilitadas) para evitar conflitos com instâncias gerenciadas fornecidas pelo _Azure Spring Cloud_.

Você também pode verificar os logs do cliente do _Registro de Serviço_ no _Azure Log Analytics_. Para obter mais detalhes, acesse [Analisar logs e métricas com as Configurações de diagnóstico](diagnostic-services.md)

Acesse [este artigo de introdução](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) para começar a usar o _Azure Log Analytics_. Consulte os logs usando [Linguagem de Consulta do Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Não consigo encontrar métricas ou logs para meu aplicativo

Acesse _Gerenciamento de aplicativos_ para garantir o aplicativo esteja _Em execução_ e _ATIVO_.

Se você puder ver as métricas da _JVM_, mas nenhuma métrica do _Tomcat_, verifique se a dependência `spring-boot-actuator` está habilitada no pacote do aplicativos e se foi inicializada com êxito.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se os logs do aplicativo puderem ser arquivados em uma conta de armazenamento, mas não enviados para o _Azure Log Analytics_, verifique se você[ configurou seu espaço de trabalho corretamente](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Se você estiver usando uma camada gratuita do _Azure Log Analytics_, observe que [a camada gratuita não fornece SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).