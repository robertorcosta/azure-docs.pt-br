---
title: Evento de grupo de segurança de rede e contador de regras logs de diagnóstico do Azure
titlesuffix: Azure Virtual Network
description: Saiba como ativar logs de diagnóstico de contador do evento e a regra para um grupo de segurança de rede do Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/04/2018
ms.author: kumud
ms.openlocfilehash: 047c92f1c50409e6a1716f0ef2f774464bd12a0a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972772"
---
# <a name="diagnostic-logging-for-a-network-security-group"></a>Log de diagnóstico para um grupo de segurança de rede

Um grupo de segurança de rede (NSG) contém regras que permitem ou negam o tráfego para uma sub-rede de rede virtual, a interface de rede ou ambos. Quando você habilita o log de diagnóstico para um NSG, você pode registrar as seguintes categorias de informações:

* **Evento:** As entradas são registradas para aquelas regras de NSG que são aplicadas às VMs, com base no endereço MAC.
* **Contador de regras:** Contém entradas de quantas vezes cada regra de NSG é aplicada para negar ou permitir tráfego. O status para essas regras é coletado a cada 60 segundos.

Os logs de diagnóstico estão disponíveis apenas para NSGs implantados por meio do modelo de implantação do Azure Resource Manager. Você não pode habilitar o log de diagnóstico para NSGs implantados por meio do modelo de implantação clássico. Para reconhecimento melhor dos dois modelos, consulte [ Compreendendo os modelos de implantação do Azure ](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

O log de diagnóstico é ativado separadamente para *cada* NSG para o qual você deseja coletar dados de diagnóstico. Se você estiver interessado em operações ou logs de atividades, em vez disso, consulte Azure [o log de atividades](../azure-monitor/platform/activity-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="enable-logging"></a>Habilitar registro em log

Você pode usar o [Portal do Azure](#azure-portal), ou [PowerShell](#powershell), ou a [CLI do Azure](#azure-cli) para habilitar o log de diagnóstico.

### <a name="azure-portal"></a>Portal do Azure

1. Entre no [portal](https://portal.azure.com).
2. Selecione **Todos os serviços** e digite *grupos de segurança de rede*. Quando **grupos de segurança de rede** aparecer nos resultados da pesquisa, selecione esta opção.
3. Escolha o NSG para o qual quer habilitar o log.
4. Em **monitoramento**, selecione **logs de diagnóstico**e, em seguida, selecione **Ativar diagnóstico**, conforme mostrado na figura a seguir:

   ![Ligar diagnósticos](./media/virtual-network-nsg-manage-log/turn-on-diagnostics.png)

5. Em **Configurações de diagnóstico**, insira ou selecione as seguintes informações e, em seguida, selecione **Salvar**:

    | Configuração                                                                                     | Valor                                                          |
    | ---------                                                                                   |---------                                                       |
    | Nome                                                                                        | Um nome de sua escolha.  Por exemplo: *myNsgDiagnostics*      |
    | **Arquivo para uma conta de armazenamento**, **fluxo para um hub de eventos**, e **enviar para análise de Log** | Você pode selecionar quantas destinos que escolher. Para saber mais sobre cada um, consulte [destinos de Log](#log-destinations).                                                                                                                                           |
    | LOG                                                                                         | Selecione uma ou ambas as categorias de log. Para saber mais sobre os dados registrados para cada categoria, consulte [categorias de Log](#log-categories).                                                                                                                                             |
6. Exibir e analisar os logs. Para obter mais informações, consulte [Visualizar e analisar logs](#view-and-analyze-logs).

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/powershell) ou executando o PowerShell no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar o PowerShell do seu computador, precisará do módulo Azure PowerShell, versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` no computador para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar `Connect-AzAccount` para entrar no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Para habilitar o log de diagnóstico, você precisa da identificação de um NSG existente. Se você não tiver um NSG existente, poderá criar um com [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup).

Recupere o grupo de segurança de rede para o qual você deseja habilitar o log de diagnóstico com [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup). Por exemplo recuperar um NSG denominado *myNsg* que existe em um grupo de recursos denominado *myResourceGroup*, digite o seguinte comando:

```azurepowershell-interactive
$Nsg=Get-AzNetworkSecurityGroup `
  -Name myNsg `
  -ResourceGroupName myResourceGroup
```

Você pode gravar logs de diagnóstico para três tipos de destino. Para mais informações, consulte [ Destinos de registro ](#log-destinations). Neste artigo, os logs são enviados para o destino *Log Analytics*, como um exemplo. Recupere um espaço de trabalho Log Analytics existente com [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace). Por exemplo, para recuperar um workspace existente denominado *myWorkspace* em um grupo de recursos denominado *myWorkspaces*, digite o seguinte comando:

```azurepowershell-interactive
$Oms=Get-AzOperationalInsightsWorkspace `
  -ResourceGroupName myWorkspaces `
  -Name myWorkspace
```

Se você não tiver um espaço de trabalho existente, poderá criar um com [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace).

Há duas categorias de log, para que você pode habilitar os logs. Para mais informações, consulte [ Categorias de log ](#log-categories). Habilite o log de diagnóstico para o NSG com [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting). O exemplo a seguir registra dados de categoria de evento e o contador para o workspace para um NSG, usando as IDs para o NSG e o workspace que você recuperou anteriormente:

```azurepowershell-interactive
Set-AzDiagnosticSetting `
  -ResourceId $Nsg.Id `
  -WorkspaceId $Oms.ResourceId `
  -Enabled $true
```

Se você quiser registrar dados para uma categoria ou a outra, em vez de ambos, adicione o `-Categories` opção ao comando anterior, seguido por *NetworkSecurityGroupEvent* ou *NetworkSecurityGroupRuleCounter*. Se você quiser fazer logon em outro [destino](#log-destinations) que um espaço de trabalho do Log Analytics, use os parâmetros apropriados para um Azure [conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Exibir e analisar os logs. Para obter mais informações, consulte [Visualizar e analisar logs](#view-and-analyze-logs).

### <a name="azure-cli"></a>CLI do Azure

É possível executar os comandos a seguir no [Azure Cloud Shell](https://shell.azure.com/bash) ou executando a CLI do Azure no computador. O Azure Cloud Shell é um shell interativo gratuito. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Se você executar a CLI do seu computador, você precisa da versão 2.0.38 ou posterior. Execute `az --version` no computador para localizar a versão instalada. Se você precisar atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Se estiver executando o CLI localmente, também precisará executar `az login` para fazer logon no Azure com uma conta que tenha as [permissões necessárias](virtual-network-network-interface.md#permissions).

Para habilitar o log de diagnóstico, você precisa da identificação de um NSG existente. Se você não tiver um NSG existente, você pode criar uma com [az network nsg criar](/cli/azure/network/nsg#az-network-nsg-create).

Recuperar o grupo de segurança de rede que você deseja habilitar o log de diagnóstico para com [z network nsg show mostrar](/cli/azure/network/nsg#az-network-nsg-show). Por exemplo recuperar um NSG denominado *myNsg* que existe em um grupo de recursos denominado *myResourceGroup*, digite o seguinte comando:

```azurecli-interactive
nsgId=$(az network nsg show \
  --name myNsg \
  --resource-group myResourceGroup \
  --query id \
  --output tsv)
```

Você pode gravar logs de diagnóstico para três tipos de destino. Para mais informações, consulte [ Destinos de registro ](#log-destinations). Neste artigo, os logs são enviados para o destino *Log Analytics*, como um exemplo. Para mais informações, consulte [ Categorias de log ](#log-categories).

Habilitar o log de diagnóstico para o NSG com [az monitor diagnostic-settings criar](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create). O exemplo a seguir registra dados de categoria de contador e de eventos para um workspace chamado *myWorkspace*, que existe em um grupo de recursos denominado *myWorkspaces*e a ID do NSG que você recuperou anteriormente:

```azurecli-interactive
az monitor diagnostic-settings create \
  --name myNsgDiagnostics \
  --resource $nsgId \
  --logs '[ { "category": "NetworkSecurityGroupEvent", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "NetworkSecurityGroupRuleCounter", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --workspace myWorkspace \
  --resource-group myWorkspaces
```

Se você não tiver um workspace existente, poderá criar uma usando o [portal do Azure](../azure-monitor/learn/quick-create-workspace.md?toc=%2fazure%2fvirtual-network%2ftoc.json), o [PowerShell](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace). Há duas categorias de log, para que você pode habilitar os logs.

Se você quiser registrar dados para uma categoria ou outra, remova a categoria que você não quiser registrar dados para o comando anterior. Se você quiser fazer logon em outro [destino](#log-destinations) que um espaço de trabalho do Log Analytics, use os parâmetros apropriados para um Azure [conta de armazenamento](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Hub de eventos](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Exibir e analisar os logs. Para obter mais informações, consulte [Visualizar e analisar logs](#view-and-analyze-logs).

## <a name="log-destinations"></a>Destinos do log

Os dados de diagnóstico podem ser:
- [ Escrito em uma conta do Armazenamento do Azure ](../azure-monitor/platform/archive-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json), para auditoria ou inspeção manual. Você pode especificar o tempo de retenção (em dias) usando as Configurações de Diagnóstico do Recurso.
- [ Transmitido para um hub de eventos ](../azure-monitor/platform/resource-logs-stream-event-hubs.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ingestão por um serviço de terceiros ou solução de análise personalizada, como o PowerBI.
- [Gravados em logs de Azure monitor](../azure-monitor/platform/resource-logs-collect-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="log-categories"></a>Categorias de log

Dados formatados em JSON são gravados para as seguintes categorias de log:

### <a name="event"></a>Evento

O log de eventos contém informações sobre quais regras NSG são aplicadas às VMs, com base no endereço MAC. Os dados a seguir são registrados para cada evento. No exemplo a seguir, os dados são registrados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "priority":"[PRIORITY-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "conditions":{
            "protocols":"[PROTOCOLS-SPECIFIED-IN-RULE]",
            "destinationPortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourcePortRange":"[PORT-RANGE-SPECIFIED-IN-RULE]",
            "sourceIP":"[SOURCE-IP-OR-RANGE-SPECIFIED-IN-RULE]",
            "destinationIP":"[DESTINATION-IP-OR-RANGE-SPECIFIED-IN-RULE]"
            }
        }
}
```

### <a name="rule-counter"></a>Contador de regras

O log do contador de regras contém informações sobre cada regra aplicada aos recursos. Os dados de exemplo a seguir são registrados sempre que uma regra é aplicada. No exemplo a seguir, os dados são registrados para uma máquina virtual com o endereço IP 192.168.1.4 e um endereço MAC de 00-0D-3A-92-6A-7C:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "[ID]",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"[ID]",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"[SECURITY-RULE-NAME]",
        "direction":"[DIRECTION-SPECIFIED-IN-RULE]",
        "type":"[ALLOW-OR-DENY-AS-SPECIFIED-IN-RULE]",
        "matchedConnections":125
        }
}
```

> [!NOTE]
> O endereço IP de origem para a comunicação não está conectado. Você pode habilitar [o registro de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md) para um NSG no entanto, que registra todas as informações do contador de regra, bem como o endereço IP de origem que iniciou a comunicação. Os dados do log de fluxo do NSG são gravados em uma conta do Armazenamento do Azure. Você pode analisar os dados com o [análise de tráfego](../network-watcher/traffic-analytics.md) capacidade do Network Watcher - Observador de rede do Azure.

## <a name="view-and-analyze-logs"></a>Exibir e analisar os logs

Para saber como visualizar dados de log de diagnóstico, consulte [ Visão geral dos Logs de Diagnóstico do Azure ](../azure-monitor/platform/resource-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Se você enviar dados de diagnóstico para:
- **Logs do Azure Monitor**: Você pode usar a solução de [análise de grupo de segurança de rede](../azure-monitor/insights/azure-networking-analytics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-security-group-analytics-solution-in-azure-monitor
) para obter insights avançados. A solução fornece visualizações para regras NSG que permitem ou negam o tráfego por endereço de MAC, a interface de rede em uma máquina virtual.
- **Conta de Armazenamento do Azure**: Os dados são gravados em um arquivo PT1H.json. Você pode encontrar o:
  - Log de eventos no seguinte caminho: `insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`
  - Regra do contador de regras no seguinte caminho: `insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/[ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME-FOR-NSG]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG NAME]/y=[YEAR]/m=[MONTH/d=[DAY]/h=[HOUR]/m=[MINUTE]`

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [o log de atividades](../azure-monitor/platform/resource-logs-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), anteriormente conhecido como auditoria ou logs operacionais. O log de atividades é habilitado por padrão para os NSGs criados por meio do modelo de implantação do Azure. Para determinar quais operações foram concluídas em NSGs no log de atividades, procure entradas contendo os seguintes tipos de recursos:
  - Microsoft.ClassicNetwork/networkSecurityGroups
  - Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
  - Microsoft.Network/networkSecurityGroups
  - Microsoft.Network/networkSecurityGroups/securityRules
- Para saber como registrar informações de diagnósticas, para incluir o endereço IP de origem para cada fluxo, consulte [o registro de fluxo NSG](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
