---
title: 'Início Rápido: Configurar os logs do fluxo NSG usando um modelo do Azure Resource Manager'
description: Saiba como habilitar os Logs do Fluxo do NSG programaticamente usando um modelo do Azure Resource Manager e o Azure PowerShell.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "87986310"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>Início Rápido: Configurar os logs de fluxo NSG usando o modelo ARM

Neste guia de início rápido, você habilita [Logs de Fluxo NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) usando um modelo do ARM ([modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview)) e o Azure PowerShell. 

Começamos fornecendo uma visão geral das propriedades do objeto de log de fluxo NSG, seguido por alguns modelos de amostra. Em seguida, implantamos o modelo usando uma instância do PowerShell local.

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="nsg-flow-logs-object"></a>Objeto de logs do fluxo NSG

O objeto de logs do fluxo NSG com todos os parâmetros é mostrado abaixo.
Para obter uma visão geral completa das propriedades, você pode ler a [referência do modelo dos logs do fluxo NSG](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs).

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

Se você estiver usando modelos do Azure Resource Manager pela primeira vez, use os links abaixo para obter mais informações.

* [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [Tutorial: Criar e implantar seu primeiro modelo do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create).

O exemplo abaixo de modelo completo é a versão mais simples com parâmetros mínimos passados para configurar Logs de Fluxo NSG. Para mais exemplos, acesse este [link](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager).

**Exemplo**: O modelo abaixo habilita os logs do fluxo NSG em um NSG de destino e os armazena em uma determinada conta de armazenamento.

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
> * O nome do recurso tem o formato “Recurso pai_Recurso filho”. Aqui, o recurso pai é a instância do Observador de Rede regional (formato: ObservadordeRede_NomedaRegião. Exemplo: ObservadordeRede_euacentraleuap)
> * targetResourceId é a ID do recurso do NSG de destino
> * storageId é a ID do recurso da conta de armazenamento de destino


## <a name="deploying-your-azure-resource-manager-template"></a>Implantando o modelo do Azure Resource Manager

Este tutorial pressupõe que você tem um grupo de Recursos existente e um NSG que pode habilitar o log do fluxo.
Você pode salvar qualquer um dos modelos do exemplo acima localmente como `azuredeploy.json`. Atualize os valores da propriedade para que eles apontem para recursos válidos em sua assinatura.

Para implantar o modelo, execute o seguinte comando no PowerShell.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> Os comandos acima estão implantando um recurso no grupo de recursos NetworkWatcherRG e não no grupo de recursos que contém o NSG


## <a name="validate-the-deployment"></a>Validar a implantação

Há algumas maneiras de verificar se sua implantação foi bem-sucedida. O console do PowerShell deve mostrar “ProvisioningState” como “Bem-sucedido”. Além disso, você pode visitar a [página do portal de logs do fluxo NSG](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs) para confirmar suas alterações. Se houver problemas com a implantação, consulte [Solução de problemas comuns de implantação do Azure com o Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors).

## <a name="deleting-your-resource"></a>Excluindo o recurso
O Azure permite a exclusão de recursos por meio do modo de implantação “Completo”. Para excluir um recurso de logs do fluxo, especifique uma implantação no modo Completo sem incluir o recurso que você deseja excluir. Leia mais sobre o [Modo de implantação completa](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)

Como alternativa, você pode desabilitar um Log de Fluxo do NSG do portal do Azure de acordo com as etapas abaixo:
1. Logon no portal do Azure
2. No canto superior esquerdo do portal, selecione **Todos os serviços**. Na caixa **Filtro**, digite *Observador de Rede*. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
3. Em **LOGS**, selecione **Logs de fluxo do NSG**
4. Na lista de NSGs, selecione o NSG para o qual você deseja desabilitar os logs de fluxo
5. Em **Configurações de logs de fluxo**, defina o status do log de fluxos como **Desativado**
6. Role para baixo e selecione **Salvar**

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você habilitou os logs de Fluxo NSG. Agora, você precisa aprender a visualizar os dados do fluxo NSG usando: 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [Ferramentas de software livre](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Análise de Tráfego do Azure](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
