---
title: 'Início Rápido: Configurar os logs de fluxo NSG usando um modelo do Azure Resource Manager'
description: Saiba como habilitar os logs de fluxo NSG programaticamente usando um modelo do ARM (Azure Resource Manager) e o Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG flow logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 96f30c05527754cbce3b7593c8d62fb56844d41e
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042756"
---
# <a name="quickstart-configure-nsg-flow-logs-using-an-arm-template"></a>Início Rápido: Configurar os logs de fluxo NSG usando um modelo do ARM

Neste guia de início rápido, você habilitará [Logs de fluxo NSG](network-watcher-nsg-flow-logging-overview.md) usando um modelo do ARM ([Azure Resource Manager](../azure-resource-manager/management/overview.md)) e o Azure PowerShell.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Começamos fornecendo uma visão geral das propriedades do objeto de log de fluxo NSG, seguido por alguns modelos de amostra. Em seguida, implantamos o modelo usando uma instância do PowerShell local.

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-networkwatcher-flowLogs-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

:::code language="json" source="~/quickstart-templates/101-networkwatcher-flowlogs-create/azuredeploy.json":::

Vários recursos estão definidos no modelo:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments)

## <a name="nsg-flow-logs-object"></a>Objeto de logs de fluxo NSG

O objeto de logs de fluxo NSG com todos os parâmetros é mostrado abaixo. Para obter uma visão geral das propriedades, confira [Microsoft.Network networkWatchers/flowLogs](/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Para criar um recurso `Microsoft.Network/networkWatchers/flowLogs`, adicione o JSON acima à seção de recursos do seu modelo.

## <a name="creating-your-template"></a>Criando seu modelo

Se você estiver usando modelos do ARM pela primeira vez, use os links abaixo para obter mais informações.

- [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md#deploy-local-template)
- [Tutorial: Criar e implantar seu primeiro modelo do Resource Manager](../azure-resource-manager/templates/template-tutorial-create-first-template.md)

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

O exemplo abaixo de modelo completo é a versão mais simples com parâmetros mínimos passados para configurar os logs de fluxo NSG. Para mais exemplos, acesse este [Guia de instruções](network-watcher-nsg-flow-logging-azure-resource-manager.md).

**Exemplo** : o modelo abaixo habilita os logs de fluxo NSG em um NSG de destino e os armazena em uma determinada conta de armazenamento.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
    {
      "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
      "type": "Microsoft.Network/networkWatchers/FlowLogs/",
      "location": "centraluseuap",
      "apiVersion": "2019-09-01",
      "properties": {
        "targetResourceId": "/subscriptions/<subscription Id>/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
        "storageId": "/subscriptions/<subscription Id>/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
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
> - O nome do recurso tem o formato _Recurso pai_Recurso filho_. Aqui, o recurso pai é a instância do Observador de Rede regional (formato: ObservadordeRede_NomedaRegião. Exemplo: ObservadordeRede_euacentraleuap)
> - `targetResourceId` é a ID do recurso do NSG de destino.
> - `storageId` é a ID do recurso da conta de armazenamento de destino.

## <a name="deploy-the-template"></a>Implantar o modelo

Este tutorial pressupõe que você tem um grupo de Recursos existente e um NSG que pode habilitar o log do fluxo.
Você pode salvar qualquer um dos modelos do exemplo acima localmente como `azuredeploy.json`. Atualize os valores da propriedade para que eles apontem para recursos válidos em sua assinatura.

Para implantar o modelo, execute o seguinte comando no PowerShell.

```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId <subscription Id>
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Os comandos acima estão implantando um recurso no grupo de recursos NetworkWatcherRG e não no grupo de recursos que contém o NSG

## <a name="validate-the-deployment"></a>Validar a implantação

Há algumas maneiras de verificar se sua implantação foi bem-sucedida. O console do PowerShell deve mostrar `ProvisioningState` como `Succeeded`. Além disso, você pode acessar a [página do portal de logs de fluxo NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar suas alterações. Se houver problemas com a implantação, confira [Solucionar problemas comuns de implantação do Azure com o Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md).

## <a name="clean-up-resources"></a>Limpar os recursos

O Azure permite a exclusão de recursos por meio do modo de implantação `Complete`. Para excluir um recurso de logs de fluxo, especifique uma implantação no modo `Complete` sem incluir o recurso que você deseja excluir. Leia mais sobre o [Modo de implantação completa](../azure-resource-manager/templates/deployment-modes.md#complete-mode).

Como alternativa, você pode desabilitar um Log de Fluxo do NSG do portal do Azure de acordo com as etapas abaixo:

1. Logon no portal do Azure
1. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na caixa **Filtro** , digite _Observador de Rede_. Quando os resultados da pesquisa exibirem **Observador de Rede** , selecione essa opção.
1. Em **LOGS** , selecione **Logs de fluxo NSG**.
1. Na lista de NSGs, selecione o NSG para o qual você deseja desabilitar os logs de fluxo.
1. Em **Configurações de logs de fluxo** , defina o status do log de fluxos como **Desativado**.
1. Role para baixo e selecione **Salvar**.

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você habilitou os logs de fluxo NSG. Agora, você precisa aprender a visualizar os dados do fluxo NSG usando:

- [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
- [Ferramentas de software livre](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
- [Análise de Tráfego do Azure](traffic-analytics.md)
