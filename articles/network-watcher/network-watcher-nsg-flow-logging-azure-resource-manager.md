---
title: Network Watcher - Crie logs de fluxo NSG usando um modelo do Azure Resource Manager
description: Use um modelo do Azure Resource Manager e o PowerShell para configurar facilmente os registros de fluxo do NSG.
services: network-watcher
documentationcenter: na
author: damendo
manager: twooley
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/26/2020
ms.author: damendo
ms.openlocfilehash: 35d185a625a81a259c366a45999769ecf76c6a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538151"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configure os logs de fluxo do NSG a partir de um modelo do Azure Resource Manager

> [!div class="op_single_selector"]
> - [Portal Azure](network-watcher-nsg-flow-logging-portal.md)
> - [Powershell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Gerente de Recursos do Azure](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[O Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) é a maneira nativa e poderosa do Azure de gerenciar sua [infra-estrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code).

Este artigo mostra como você habilita o [NSG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) de forma programática usando um modelo do Azure Resource Manager e o Azure PowerShell. Começamos fornecendo uma visão geral das propriedades do objeto NSG Flow Log, seguida por alguns modelos de amostra. Em seguida, implantamos o modelo usando uma instância local do PowerShell.


## <a name="nsg-flow-logs-object"></a>Objeto DE logs de fluxo NSG

O objeto NSG Flow Logs com todos os parâmetros é mostrado abaixo.
Para obter uma visão geral completa das propriedades, você pode ler a [referência do modelo NSG Flow Logs](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Para criar um recurso Microsoft.Network/networkWatchers/flowLogs, adicione o JSON acima à seção de recursos do seu modelo.


## <a name="creating-your-template"></a>Criando seu modelo

Se você estiver usando modelos do Azure Resource Manager pela primeira vez, você pode aprender mais sobre eles usando os links abaixo.

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Tutorial: Crie e implante seu primeiro modelo de Gerenciador de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Abaixo estão dois exemplos de modelos completos para configurar registros de fluxo DO NSG.

**Exemplo 1**: A versão mais simples do acima com parâmetros mínimos passados. O modelo abaixo permite o NSG Flow Logs em um NSG de destino e os armazena em uma determinada conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * O nome do recurso tem o formato "Recurso pai>/recurso filho". Aqui, o recurso pai é a instância<RegionName>regional do Observador de Rede (Formato: NetworkWatcher_ . Exemplo: NetworkWatcher_centraluseuap)
> * targetResourceId é o ID de recurso do NSG de destino
> * storageId é o ID de recurso da conta de armazenamento de destino

**Exemplo 2**: Os seguintes modelos que permitem o NSG Flow Logs (versão 2) com uma retenção por 5 dias. Habilitando o Traffic Analytics com um intervalo de processamento de 10 minutos.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {
        "networkWatcherFlowAnalyticsConfiguration": {
            "enabled": true,
            "workspaceResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/defaultresourcegroup-wcus/providers/Microsoft.OperationalInsights/workspaces/1c4f42e5-3a02-4146-ac9b-3051d8501db0",
            "trafficAnalyticsInterval": 10
                }
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 2            
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Implantando seu modelo de gerenciador de recursos do Azure

Este tutorial pressupõe que você tem um grupo de recursos existente e um NSG que você pode ativar o logon do Flow.
Você pode salvar qualquer um dos modelos de exemplo acima localmente como `azuredeploy.json`. Atualize os valores da propriedade para que eles apontem para recursos válidos em sua assinatura.

Para implantar o modelo, execute o seguinte comando no PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Verificando sua implantação

Existem algumas maneiras de verificar se sua implantação foi bem sucedida. O console PowerShell deve mostrar "ProvisioningState" como "Sucesso". Além disso, você pode visitar a página do [portal NSG Flow Logs](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar suas alterações. Se houver problemas com a implantação, dê uma olhada nos [erros comuns de implantação do Azure com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).


## <a name="next-steps"></a>Próximas etapas

Saiba como visualizar seus dados do NSG Flow usando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Análise de tráfego do Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
