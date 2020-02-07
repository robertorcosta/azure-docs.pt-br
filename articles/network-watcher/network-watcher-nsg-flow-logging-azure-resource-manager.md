---
title: Observador de rede – criar logs de fluxo de NSG usando um modelo de Azure Resource Manager
description: Use um modelo de Azure Resource Manager e o PowerShell para configurar facilmente os logs de fluxo do NSG.
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
ms.openlocfilehash: 6980518da00e6849c327ca712bbeadaa816ae479
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77056658"
---
# <a name="configure-nsg-flow-logs-from-an-azure-resource-manager-template"></a>Configurar logs de fluxo de NSG de um modelo de Azure Resource Manager

> [!div class="op_single_selector"]
> - [Azure portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)
> - [Azure Resource Manager](network-watcher-nsg-flow-logging-azure-resource-manager.md)


[Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/) é o modo nativo e avançado do Azure para gerenciar sua [Infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code).

Este artigo mostra como habilitar logs de [fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) programaticamente usando um modelo de Azure Resource Manager e Azure PowerShell. Começamos fornecendo uma visão geral das propriedades do objeto de log de fluxo NSG, seguido por alguns modelos de exemplo. Em seguida, usamos o modelo de implantação usando uma instância local do PowerShell.


## <a name="nsg-flow-logs-object"></a>Objeto de logs de fluxo NSG

O objeto de logs de fluxo NSG com todos os parâmetros é mostrado abaixo.
Para obter uma visão geral completa das propriedades, você pode ler a [referência de modelo de logs de fluxo NSG](https://docs.microsoft.com/azure/templates/microsoft.network/2019-11-01/networkwatchers/flowlogs#RetentionPolicyParameters).

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
Para criar um recurso Microsoft. Network/networkWatchers/flowLogs, adicione o JSON acima à seção de recursos do seu modelo.


## <a name="creating-your-template"></a>Criando seu modelo

Se você estiver usando modelos de Azure Resource Manager pela primeira vez, poderá aprender mais sobre eles usando os links abaixo.

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Tutorial: criar e implantar seu primeiro modelo de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)


Abaixo estão dois exemplos de modelos completos para configurar os logs de fluxo do NSG.

**Exemplo 1**: a versão mais simples do acima com os parâmetros mínimos passados. O modelo abaixo habilita os logs de fluxo NSG em um NSG de destino e os armazena em uma determinada conta de armazenamento.

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
> * O nome do recurso tem o formato "Parent Resource >/Child Resource". Aqui, o recurso pai é a instância do observador de rede regional (Format: NetworkWatcher_<RegionName>. Exemplo: NetworkWatcher_centraluseuap)
> * targetResourceId é a ID de recurso do NSG de destino
> * storageid é a ID de recurso da conta de armazenamento de destino

**Exemplo 2**: os modelos a seguir habilitam os logs de fluxo do NSG (versão 2) com uma retenção por 5 dias. Habilitar Análise de Tráfego com um intervalo de processamento de 10 minutos.

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
            "enabled": true,
        "workspaceResourceId": "91a3d1e9-698e-4a49-96dc-f6fc585ae888",
        "trafficAnalyticsInterval": 10
      },
      "retentionPolicy": {
        "days": 5,
        "enabled": true
      },
      "format": {
        "type": "JSON",
        "version": 1
      }
    }

  }
  ]
}
```

## <a name="deploying-your-azure-resource-manager-template"></a>Implantando seu modelo de Azure Resource Manager

Este tutorial pressupõe que você tenha um grupo de recursos existente e um NSG você possa habilitar o log de fluxo.
Você pode salvar qualquer um dos modelos de exemplo acima localmente como `azuredeploy.json`. Atualize os valores de propriedade para que eles apontem para recursos válidos em sua assinatura.

Para implantar o modelo, execute o seguinte comando no PowerShell.
```azurepowershell
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```


## <a name="verifying-your-deployment"></a>Verificando sua implantação

Há duas maneiras de verificar se sua implantação foi bem-sucedida. O console do PowerShell deve mostrar "ProvisioningState" como "êxito". Além disso, você pode visitar a [página do portal de logs de fluxo do NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar suas alterações. Se houver problemas com a implantação, dê uma olhada em [solucionar erros comuns de implantação do Azure com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).


## <a name="next-steps"></a>Próximas etapas

Saiba como visualizar seus dados de fluxo do NSG usando:
* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Análise de Tráfego do Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
