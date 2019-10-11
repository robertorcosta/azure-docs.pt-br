---
title: Como gerenciar seu ambiente do Azure Time Series Insights usando modelos do Azure Resource Manager | Microsoft Docs
description: Este artigo descreve como gerenciar seu ambiente do Azure Time Series Insights programaticamente usando o Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: b3aa6d06add1d80512eda0e62888b4a36760e98c
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274791"
---
# <a name="create-time-series-insights-resources-using-azure-resource-manager-templates"></a>Criar recursos do Time Series Insights usando modelos do Azure Resource Manager

Este artigo descreve como criar e implantar Time Series Insights recursos usando [Azure Resource Manager modelos](https://docs.microsoft.com/azure/azure-resource-manager/), o PowerShell e o provedor de recursos do time Series insights.

O Time Series Insights oferece suporte aos seguintes recursos:

   | Resource | DESCRIÇÃO |
   | --- | --- |
   | Ambiente | Um ambiente de Time Series Insights é um agrupamento lógico de eventos que são lidos de agentes de evento, armazenados e disponibilizados para consulta. Para obter mais informações, consulte [planejar seu ambiente de Azure Time Series insights](time-series-insights-environment-planning.md) |
   | Origem do Evento | Uma fonte de evento é uma conexão com um agente de eventos de onde o Time Series Insights lê e ingere eventos no ambiente. As fontes de evento com suporte no momento são o Hub IoT e o Hub de Eventos. |
   | Conjuntos de dados de referência | Os conjuntos de dados de referência fornecem metadados sobre os eventos no ambiente. Os metadados nos conjuntos de dados de referência serão adicionados aos eventos durante a entrada. Os conjuntos de dados de referência são definidos como recursos por suas propriedades de chave de evento. Os metadados que compõem o conjunto de dados de referência são carregados ou modificados por meio de APIs de plano de dados. |
   | Política de acesso | As políticas de acesso concedem permissões para emitir consultas de dados, manipular dados de referência no ambiente e compartilhar consultas salvas e perspectivas associadas ao ambiente. Para obter mais informações, leia [conceder acesso a dados para um ambiente de time Series insights usando portal do Azure](time-series-insights-data-access.md) |

Um modelo do Resource Manager é um arquivo JSON que define a infraestrutura e a configuração de recursos em um grupo de recursos. Os documentos a seguir descrevem os arquivos de modelo com mais detalhes:

- [Implantação de modelo de Azure Resource Manager](../azure-resource-manager/template-deployment-overview.md)
- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
- [Tipos de recurso Microsoft.TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

O modelo de início rápido [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) é publicado no GitHub. Este modelo cria um ambiente do Time Series Insights, uma fonte de evento filho configurada para consumir eventos de um Hub de Eventos, além de políticas de acesso que concedem acesso aos dados do ambiente. Se um Hub de Eventos existente não for especificado, ele será criado com a implantação.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Especificar modelo de implantação e parâmetros

O procedimento a seguir descreve como usar o PowerShell para implantar um modelo do Azure Resource Manager que cria um ambiente do Time Series Insights, uma fonte de evento filho configurada para consumir eventos de um Hub de Eventos e políticas de acesso que concedem acesso aos dados do ambiente. Se um Hub de Eventos existente não for especificado, ele será criado com a implantação.

1. Instale o Azure PowerShell seguindo as instruções em [Introdução ao Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

1. Clone ou copie o modelo [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) do GitHub.

   * Criar um arquivo de parâmetros

     Para criar um arquivo de parâmetros, copie o arquivo [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json).

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   * Parâmetros obrigatórios

     | Parâmetro | DESCRIÇÃO |
     | --- | --- |
     | eventHubNamespaceName | O namespace do hub de eventos de origem. |
     | eventHubName | O nome do hub de eventos de origem. |
     | consumerGroupName | O nome do grupo consumidor que o serviço Time Series Insights usará para ler os dados do hub de eventos. **OBSERVAÇÃO:** para evitar a contenção de recursos, esse grupo consumidor deve ser dedicado ao serviço Time Series Insights e não deve ser compartilhado com outros leitores. |
     | environmentName | O nome do ambiente. O nome não pode incluir: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/` e quaisquer caracteres de controle. Todos os outros caracteres são permitidos.|
     | eventSourceName | O nome do recurso filho da fonte do evento. O nome não pode incluir: `<`, `>`, `%`, `&`, `:`, `\\`, `?`, `/` e quaisquer caracteres de controle. Todos os outros caracteres são permitidos. |

    <div id="optional-parameters"></div>

   * Parâmetros opcionais

     | Parâmetro | DESCRIÇÃO |
     | --- | --- |
     | existingEventHubResourceId | Uma ID de recurso opcional de um Hub de Eventos existente que será conectada ao ambiente do Time Series Insights através da fonte do evento. **OBSERVAÇÃO:** o usuário que vai implantar o modelo deve ter privilégios para executar a operação listkeys no Hub de Eventos. Se nenhum valor for transmitido, um novo hub de eventos será criado pelo modelo. |
     | environmentDisplayName | Um nome amigável opcional para exibição nas ferramentas ou interfaces de usuário em vez do nome do ambiente. |
     | environmentSkuName | O nome da SKU. Para saber mais, confira a [Página de Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).  |
     | environmentSkuCapacity | A capacidade da unidade da SKU. Para saber mais, confira a [Página de Preços do Time Series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/).|
     | environmentDataRetentionTime | O intervalo de tempo mínimo em que os eventos do ambiente estarão disponíveis para consulta. O valor deve ser especificado no formato ISO 8601, por exemplo `P30D` para uma política de retenção de 30 dias. |
     | eventSourceDisplayName | Um nome amigável opcional para exibição nas ferramentas ou interfaces de usuário em vez do nome da fonte do evento. |
     | eventSourceTimestampPropertyName | A propriedade de evento que será usada como carimbo de data/hora da fonte do evento. Se não for especificado um valor para timestampPropertyName, ou se for especificado null ou empty-string, a hora de criação do evento será usada. |
     | eventSourceKeyName | O nome da chave de acesso compartilhado que o serviço Time Series Insights usará para se conectar ao hub de eventos. |
     | accessPolicyReaderObjectIds | Uma lista de IDs de objeto dos usuários ou aplicativos no Azure AD que devem ter acesso de Leitor para o ambiente. O objectId da entidade de serviço pode ser obtido chamando o cmdlet **Get-AzADUser** ou **Get-AzADServicePrincipal** . Ainda não há suporte para a criação de uma política de acesso para grupos do Azure AD. |
     | accessPolicyContributorObjectIds | Uma lista de IDs de objeto dos usuários ou aplicativos no Azure AD que devem ter acesso de Colaborador para o ambiente. O objectId da entidade de serviço pode ser obtido chamando o cmdlet **Get-AzADUser** ou **Get-AzADServicePrincipal** . Ainda não há suporte para a criação de uma política de acesso para grupos do Azure AD. |

   * Por exemplo, o arquivo de parâmetros a seguir deve ser usado para criar um ambiente e uma fonte do evento que lê eventos de um hub de eventos existente. Ele também cria duas políticas de acesso que concedem acesso de Colaborador para o ambiente.

     ```json
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }    
         }
     }
     ```
  
    * Para saber mais, consulte o tópico [Parâmetros](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>Implantar o modelo de início rápido localmente usando o PowerShell

> [!IMPORTANT]
> As operações de linha de comando exibidas abaixo descrevem o [módulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/overview).

1. No PowerShell, faça logon em sua conta do Azure.

    * Em um prompt do PowerShell, execute o seguinte comando:

      ```powershell
      Connect-AzAccount
      ```

    * Você precisará entrar em sua conta do Azure. Após o logon, execute o comando a seguir para exibir as assinaturas disponíveis:

      ```powershell
      Get-AzSubscription
      ```

    * Esse comando retorna uma lista de assinaturas do Azure disponíveis. Escolha uma assinatura para a sessão atual executando o comando a seguir. Substitua `<YourSubscriptionId>` pelo GUID da assinatura do Azure que deseja usar:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Crie um novo grupo de recursos se já não tiver um.

   * Se você não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **New-AzResourceGroup** . Forneça o nome do grupo de recursos e local que você deseja usar. Por exemplo:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   * Se for bem-sucedido, um resumo do novo grupo de recursos será exibido.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. Teste a implantação.

   * Valide a implantação executando o cmdlet `Test-AzResourceGroupDeployment`. Ao testar a implantação, forneça parâmetros exatamente como faria durante a sua execução.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. Criar a implantação

    * Para criar a nova implantação, execute o cmdlet `New-AzResourceGroupDeployment` e forneça os parâmetros necessários quando solicitado. Os parâmetros incluem um nome para sua implantação, o nome do seu grupo de recursos e o caminho ou a URL para o arquivo do modelo. Caso o parâmetro **Mode** não esteja especificado, o valor padrão de **Incremental** será usado. Para saber mais, consulte [Implantações incrementais e completas](../azure-resource-manager/deployment-modes.md).

    * O comando abaixo solicita os cinco parâmetros obrigatórios na janela do PowerShell:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
      ```

    * Para especificar um arquivo de parâmetros em vez disso, use o comando a seguir:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    * Você também pode usar parâmetros embutidos quando executa o cmdlet de implantação. O comando é o seguinte:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    * Para executar uma implantação [completa](../azure-resource-manager/deployment-modes.md), defina o parâmetro **Mode** como **Complete**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. Verificar a implantação

    * Se os recursos forem implantados com êxito, um resumo da implantação será exibido na janela do PowerShell:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Implantar o modelo de início rápido por meio do portal do Azure

   * A página inicial do modelo de início rápido no GitHub também inclui um botão **Implantar no Azure**. Clique nele para abrir uma página Implantação Personalizada no portal do Azure. Nessa página, você pode inserir ou selecionar valores para cada parâmetro nas tabelas de [parâmetros obrigatórios](#required-parameters) ou [parâmetros opcionais](#optional-parameters). Depois de preencher as configurações, clique no botão **Comprar** para iniciar a implantação de modelo.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png"/>
    </a>

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como gerenciar programaticamente os recursos do Time Series Insights usando APIs REST, confira [Gerenciamento do Time Series Insights](https://docs.microsoft.com/rest/api/time-series-insights-management/).
