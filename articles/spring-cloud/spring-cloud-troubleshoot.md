---
title: Guia de solução de problemas do Azure Spring Cloud | Microsoft Docs
description: Guia de solução de problemas do Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277578"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Solucionar problemas comuns da Nuvem de Primavera do Azure

Este artigo fornece instruções para solucionar problemas de desenvolvimento da Nuvem de Primavera do Azure. Para obter informações adicionais, consulte [Azure Spring Cloud FAQ](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Disponibilidade, desempenho e problemas de aplicativos

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Meu aplicativo não pode começar (por exemplo, o ponto final não pode ser conectado, ou ele retorna um 502 após algumas tentativas)

Exporte os logs para o Azure Log Analytics. A tabela de logs de aplicativos spring é chamada *AppPlatformLogsforSpring*. Para saber mais, consulte [Analisar registros e métricas com configurações de diagnóstico](diagnostic-services.md).

A seguinte mensagem de erro pode aparecer em seus registros:

> "org.springframework.context.ApplicationContextException: Incapaz de iniciar o servidor web"

A mensagem indica um dos dois problemas prováveis: 
* Um dos grãos ou uma de suas dependências está ausente.
* Uma das propriedades do Bean está ausente ou é inválida. Neste caso, "java.lang.IllegalArgumentException" provavelmente será exibido.

As vinculações de serviço também podem causar falhas no início do aplicativo. Para consultar os logs, use palavras-chave relacionadas aos serviços vinculados. Por exemplo, vamos supor que seu aplicativo tenha uma vinculação a uma instância MySQL definida como tempo do sistema local. Se o aplicativo não for inicial, a seguinte mensagem de erro pode aparecer no registro:

> "java.sql.SQLException: O valor do fuso horário do servidor 'Tempo Universal Coordenado' não é reconhecido ou representa mais de um fuso horário."

Para corrigir esse erro, `server parameters` vá para a instância MySQL e altere o `time_zone` valor do *SYSTEM* para *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>Meu aplicativo falha ou gera um erro inesperado

Quando estiver depurando falhas de aplicativos, comece verificando o status de execução e o status de detecção do aplicativo. Para isso, acesse o _gerenciamento de aplicativos_ no portal Azure para garantir que os status de todos os aplicativos estejam em _execução_ e _UP_.

* Se o status estiver _em execução,_ mas o status de detecção não estiver _UP,_ vá para a seção ["Meu aplicativo não pode ser registrado".](#my-application-cant-be-registered)

* Se o status da descoberta estiver _ATIVO_, acesse Métricas para verificar a integridade do aplicativo. Inspecione as seguintes métricas:


  - `TomcatErrorCount`(_tomcat.global.error_): Todas as exceções de aplicativos da Primavera são contadas aqui. Se este número for muito alto, acesse o Azure Log Analytics para inspecionar seus logs de aplicativo.

  - `AppMemoryMax`(_jvm.memory.max_): A quantidade máxima de memória disponível para o aplicativo. A quantidade pode ser indefinida, ou pode mudar com o tempo se for definida. Se for definido, a quantidade de memória usada e comprometida é sempre menor ou igual ao máximo. No entanto, uma alocação de memória pode falhar com uma `OutOfMemoryError` mensagem se a alocação tentar aumentar a memória usada de tal forma que > *usada,* mesmo se usada <= *max* ainda é verdadeira. Em tal situação, tente aumentar o tamanho `-Xmx` máximo do monte usando o parâmetro.

  - `AppMemoryUsed`(_jvm.memory.used):_ A quantidade de memória em bytes que é atualmente usado pelo aplicativo. Para uma aplicação Java de carga normal, esta série métrica forma um padrão *de dente de serra,* onde o uso da memória aumenta constantemente e diminui em pequenos incrementos e de repente cai muito, e então o padrão se repete. Esta série métrica ocorre por causa da coleta de lixo dentro da máquina virtual Java, onde as ações de coleta representam gotas no padrão dente de serra.
    
    Essa métrica é importante para ajudar a identificar problemas de memória, tais como:
    * Uma explosão de memória no início.
    * A alocação de memória de surto para um caminho lógico específico.
    * Vazamentos de memória graduais.

  Para obter mais informações, consulte [Métricas](spring-cloud-concept-metrics.md).

Para saber mais sobre o Azure Log Analytics, consulte [Comece com o Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>Meu aplicativo experimenta alto uso da CPU ou alta utilização de memória

Se o aplicativo tiver um alto uso de CPU ou memória, uma das duas coisas é verdadeira:
* Todas as instâncias do aplicativo experimentam alto uso de CPU ou memória.
* Algumas das instâncias do aplicativo experimentam alto uso de CPU ou memória.

Para verificar qual situação se aplica, faça o seguinte:

1. Vá para **Métricas**e selecione a porcentagem de **uso da CPU do serviço** ou a memória de serviço **usada**.
2. Adicione um filtro **App=** para especificar qual aplicativo você deseja monitorar.
3. Divida as métricas por **Instância**.

Se *todas as instâncias* estiverem experimentando alto uso de CPU ou memória, você precisa dimensionar o aplicativo ou aumentar o uso da CPU ou da memória. Para obter mais informações, consulte [Tutorial: Dimensione um aplicativo no Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Se *algumas instâncias* estiverem experimentando alto uso de CPU ou memória, verifique o status da ocorrência e seu status de detecção.

Para obter mais informações, consulte [Métricas para Nuvem de Primavera do Azure](spring-cloud-concept-metrics.md).

Se todas as instâncias estiverem em funcionamento, vá ao Azure Log Analytics para consultar os registros de aplicativos e rever sua lógica de código. Isso vai ajudá-lo a ver se algum deles pode afetar o particionamento de escala. Para obter mais informações, consulte [Analisar registros e métricas com configurações de diagnóstico](diagnostic-services.md).

Para saber mais sobre o Azure Log Analytics, consulte [Comece com o Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Consulta os logs usando a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Checklist para implantar seu aplicativo de Primavera no Azure Spring Cloud

Antes de embarcar na sua aplicação, certifique-se de que ela atenda aos seguintes critérios:

* O aplicativo pode ser executado localmente com a versão do Java Runtime.
* A configuração de ambiente (CPU/RAM/Instâncias) atende ao requisito mínimo definido pelo provedor de aplicativos.
* Os itens de configuração têm seus valores esperados. Para obter mais informações, confira [Servidor de Configuração](spring-cloud-tutorial-config-server.md).
* As variáveis ambientais têm seus valores esperados.
* Os parâmetros JVM têm seus valores esperados.
* Recomendamos que você desabilite ou remova os serviços incorporados _do Config Server_ e do Registro de _Serviços_ de Primavera do pacote de aplicativos.
* Se algum recurso do Azure for ser associado via _Associação de serviço_, verifique se os recursos de destino estão ativos e em execução.

## <a name="configuration-and-management"></a>Configuração e gerenciamento

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Eu encontrei um problema com a criação de uma instância de serviço Azure Spring Cloud

Quando você configura uma instância de serviço do Azure Spring Cloud usando o portal Azure, o Azure Spring Cloud realiza a validação para você.

Mas se você tentar configurar a instância de serviço do Azure Spring Cloud usando o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ou o [modelo do Azure Resource Manager,](https://docs.microsoft.com/azure/azure-resource-manager/)verifique que:

* A assinatura está ativa.
* O local é [suportado](spring-cloud-faq.md) pelo Azure Spring Cloud.
* O grupo de recursos da instância já foi criado.
* O nome do recurso está em conformidade com a regra de nomenclatura. Deve conter apenas letras minúsculas, números e hífens. O primeiro caractere precisa ser uma letra. O último caractere deve ser uma letra ou um número. O valor deve conter de 2 a 32 caracteres.

Se você quiser configurar a instância de serviço do Azure Spring Cloud usando o modelo Gerenciador de recursos, consulte primeiro [entender a estrutura e a sintaxe dos modelos do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

O nome da instância de serviço Azure Spring Cloud será `azureapps.io`usado para solicitar um nome de subdomínio em , de modo que a configuração falhará se o nome entrar em conflito com um existente. Você pode encontrar mais detalhes nos registros de atividades.

### <a name="i-cant-deploy-a-jar-package"></a>Eu não posso implantar um pacote JAR

Você não pode carregar o pacote JAR(Java Archive file)/source usando o portal Azure ou o modelo do Gerenciador de recursos.

Quando você implanta seu pacote de aplicativos usando o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), o Azure CLI pesquisa periodicamente o progresso da implantação e, no final, exibe o resultado da implantação.

Se a sondagem for interrompida, você ainda poderá usar o seguinte comando para buscar os logs de implantação:

`az spring-cloud app show-deploy-log -n <app-name>`

Certifique-se de que seu aplicativo está embalado no [formato JAR executável](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)correto . Se não estiver empacotado corretamente, você receberá uma mensagem de erro semelhante à seguinte:

> "Erro: Jarfile inválido ou corrompido /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Eu não posso implantar um pacote de origem

Você não pode carregar o pacote JAR/source usando o portal Azure ou o modelo do Gerenciador de recursos.

Quando você implanta seu pacote de aplicativos usando o [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), o Azure CLI pesquisa periodicamente o progresso da implantação e, no final, exibe o resultado da implantação.

Se a sondagem for interrompida, você ainda poderá usar o seguinte comando para buscar os logs de implantação e build:

`az spring-cloud app show-deploy-log -n <app-name>`

No entanto, observe que uma instância de serviço do Azure Spring Cloud pode desencadear apenas um trabalho de compilação para um pacote de origem ao mesmo tempo. Para obter mais informações, consulte [Implantar um aplicativo](spring-cloud-quickstart-launch-app-portal.md) e configurar um ambiente de preparação no [Azure Spring Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Minha inscrição não pode ser registrada.

Na maioria dos casos, essa situação ocorre quando *as dependências necessárias* e *a descoberta de serviços* não estão configuradas corretamente no arquivo POM (Project Object Model, modelo de objeto do projeto). Uma vez configurado, o ponto final do servidor do Registro de Serviço incorporado é injetado como uma variável de ambiente com o aplicativo. Os aplicativos então se registram no servidor do Registro de Serviços e descobrem outros microserviços dependentes.

Aguarde pelo menos dois minutos antes que uma instância recém-registrada comece a receber tráfego.

Se você estiver migrando uma solução baseada em Nuvem de Primavera existente para o Azure, certifique-se de que as instâncias do Registro de _Serviço_ ad-hoc e _do Servidor config_ sejam removidas (ou desativadas) para evitar conflitos com as instâncias gerenciadas fornecidas pelo Azure Spring Cloud.

Você também pode verificar os registros de clientes do _Registro de Serviços_ no Azure Log Analytics. Para obter mais informações, consulte [Analisar registros e métricas com configurações de diagnóstico](diagnostic-services.md)

Para saber mais sobre o Azure Log Analytics, consulte [Comece com o Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Consulta os logs usando a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Desejo inspecionar as variáveis de ambiente de meu aplicativo

As variáveis de ambiente informam a estrutura do Azure Spring Cloud, garantindo que o Azure entenda onde e como configurar os serviços que compõem seu aplicativo. Verificar se as variáveis de ambiente estão corretas é uma primeira etapa necessária para solucionar problemas potenciais.  Use o ponto de extremidade do Atuador do Spring Boot para examinar as variáveis de ambiente.  

> [!WARNING]
> Este procedimento expõe as variáveis do ambiente usando o ponto final do teste.  Não prossiga se o ponto de extremidade de teste estiver publicamente acessível ou se você tiver atribuído um nome de domínio ao aplicativo.

1. Ir para `https://<your application test endpoint>/actuator/health`.  
    - Uma resposta semelhante a `{"status":"UP"}` indica que o ponto de extremidade foi habilitado.
    - Se a resposta for negativa, inclua a seguinte dependência no seu arquivo *POM.xml:*

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Com o ponto final do Acionador de Inicialização de Mola ativado, vá até o portal Dozure e procure a página de configuração do seu aplicativo.  Adicione uma variável de `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` ambiente `*` com o nome e o valor . 

1. Reinicie o aplicativo.

1. Vá `https://<your application test endpoint>/actuator/env` e inspecione a resposta.  O resultado deve ser assim:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Procure o nó infantil `systemEnvironment`chamado .  Esse nó contém as variáveis de ambiente do aplicativo.

> [!IMPORTANT]
> Lembre-se de reverter a exposição das variáveis de ambiente antes de tornar o aplicativo acessível ao público.  Vá ao portal Azure, procure a página de configuração do `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`seu aplicativo e exclua essa variável de ambiente: .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Eu não consigo encontrar métricas ou logs para o meu aplicativo

Vá para o **gerenciamento do aplicativo** para garantir que os status do aplicativo estejam em _execução_ e _UP_.

Se você pode ver métricas do _JVM,_ mas sem métricas do _Tomcat,_ verifique se a `spring-boot-actuator` dependência está ativada no pacote do aplicativo e se ela é inicializada com sucesso.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se os logs de aplicativos podem ser arquivados em uma conta de armazenamento, mas não enviados para o Azure Log Analytics, verifique se você [configura seu espaço de trabalho corretamente](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Se você estiver usando um nível gratuito do Azure Log Analytics, observe que [o nível gratuito não fornece um contrato de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).
