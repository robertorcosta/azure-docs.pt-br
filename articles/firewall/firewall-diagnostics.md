---
title: Monitorar os logs e as métricas do Firewall do Azure
description: Neste artigo, você aprende a habilitar e a gerenciar os logs e as métricas do firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 11/04/2020
ms.author: victorh
ms.openlocfilehash: 2899121db4b6a3f202be4860e2e4f43027cdef7c
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348756"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Monitorar os logs e as métricas do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure. Usando as métricas, você pode exibir contadores de desempenho no portal.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para [Azure monitor logs](../azure-monitor/insights/azure-networking-analytics.md), armazenamento e hubs de eventos e analisados em logs de Azure monitor ou ferramentas diferentes, como Excel e Power bi.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, você deve ler [os logs e as métricas do firewall do Azure](logs-and-metrics.md) para obter uma visão geral dos logs de diagnóstico e das métricas disponíveis para o Firewall do Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Habilitar o log de diagnósticos por meio do portal do Azure

Pode levar alguns minutos até que os dados sejam exibidos em seus logs depois de concluir este procedimento para ativar o log de diagnósticos. Se você não vir algo a princípio, verifique novamente depois de alguns minutos.

1. No portal do Azure, abra o grupo de recursos de firewall e selecione o firewall.
2. Em **Monitoramento** , selecione **Configurações de diagnóstico**.

   Para o Firewall do Azure, quatro logs específicos do serviço estão disponíveis:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule
   * AzureFirewallThreatIntelLog
   * AzureFirewallDnsProxy


3. Selecione **Adicionar configuração de diagnóstico**. A página **Configurações de diagnóstico** fornece as configurações dos logs de diagnóstico.
5. Neste exemplo, os logs do Azure Monitor armazenam os logs; portanto, digite **Log Analytics do Firewall** para o nome.
6. Em **log** , selecione **AzureFirewallApplicationRule** , **AzureFirewallNetworkRule** , **AzureFirewallThreatIntelLog** e **AzureFirewallDnsProxy** para coletar os logs.
7. Selecione **Enviar para log Analytics** para configurar seu espaço de trabalho.
8. Selecione sua assinatura.
9. Selecione **Salvar**.

## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. O log de diagnósticos deve ser habilitado para começar a coletar os dados disponíveis por meio desses logs.

Para habilitar os logs de diagnóstico, realize as seguintes etapas:

1. Anote a ID do recurso da conta de armazenamento, na qual os dados de log são armazenados. Esse valor está no formato: */subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /Providers/Microsoft.Storage/storageAccounts/ \<storage account name\>*.

   Use qualquer conta de armazenamento em sua assinatura. Use o portal do Azure para encontrar essas informações. As informações estão localizadas na página do recurso **Propriedades**.

2. Anote a ID do recurso Firewall para o qual o log está habilitado. Esse valor está no formato: */subscriptions/ \<subscriptionId\> /resourceGroups/ \<resource group name\> /Providers/Microsoft.Network/azureFirewalls/ \<Firewall name\>*.

   Use o portal para encontrar essas informações.

3. Habilite o log de diagnóstico usando o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Os logs de diagnóstico não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de acesso e de desempenho gera encargos de serviço.

## <a name="enable-diagnostic-logging-by-using-azure-cli"></a>Habilitar o log de diagnóstico usando CLI do Azure

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. O log de diagnósticos deve ser habilitado para começar a coletar os dados disponíveis por meio desses logs.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="enable-diagnostic-logging"></a>Habilitar registro em log de diagnóstico

Use os comandos a seguir para habilitar o log de diagnóstico.

1. Execute o comando [AZ monitor Diagnostics-Settings Create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) para habilitar o log de diagnóstico:

   ```azurecli
   az monitor diagnostic-settings create –name AzureFirewallApplicationRule \
     --resource Firewall07 --storage-account MyStorageAccount
   ```

   Execute o comando [AZ monitor Diagnostics-Settings List](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_list) para ver as configurações de diagnóstico de um recurso:

   ```azurecli
   az monitor diagnostic-settings list --resource Firewall07
   ```

   Use a [tela AZ monitor Diagnostic-Settings](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_show) para ver as configurações de diagnóstico ativas de um recurso:

   ```azurecli
   az monitor diagnostic-settings show --name AzureFirewallApplicationRule --resource Firewall07
   ```

1. Execute o comando [AZ monitor Diagnostic-Settings Update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) para atualizar as configurações.

   ```azurecli
   az monitor diagnostic-settings update --name AzureFirewallApplicationRule --resource Firewall07 --set retentionPolicy.days=365
   ```

   Use o comando [AZ monitor Diagnostics-Settings Delete](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_delete) para excluir uma configuração de diagnóstico.

   ```azurecli
   az monitor diagnostic-settings delete --name AzureFirewallApplicationRule --resource Firewall07
   ```

> [!TIP]
>Os logs de diagnóstico não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de acesso e de desempenho gera encargos de serviço.

## <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar os dados do log de atividades usando um dos seguintes métodos:

* **Ferramentas do Azure** : recupere informações do log de atividades por meio do Azure PowerShell, da CLI do Azure, da API REST do Azure ou do portal do Azure. As instruções passo a passo para cada método são detalhadas no artigo [Activity operations with Resource Manager](../azure-resource-manager/management/view-activity-logs.md) (Operações de atividade com o Resource Manager).
* **Power BI** : se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), experimente uma gratuitamente. Com o [pacote de conteúdo dos Logs de Atividades do Azure para Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados que podem ser usados no estado em que se encontram ou ser personalizados.
* **Azure Sentinel** : você pode conectar os logs de firewall do Azure ao Azure Sentinel, permitindo que você exiba dados de log em pastas de trabalho, use-os para criar alertas personalizados e incorporá-los para melhorar sua investigação. O conector de dados do firewall do Azure no Azure Sentinel está atualmente em visualização pública. Para obter mais informações, consulte [conectar dados do firewall do Azure](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Exibir e analisar os logs de regra de aplicativo e de rede

Os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) coletam os arquivos de log de eventos e de contador. Ele inclui visualizações e funcionalidades de pesquisa avançadas para analisar os logs.

Para consultas de exemplo do Log Analytics do Firewall do Azure, confira [Amostras do Log Analytics do Firewall do Azure](log-analytics-samples.md).

Você também pode se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de desempenho e acesso. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, no Power BI ou em qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="view-metrics"></a>Métricas de exibição
Navegue até um firewall do Azure, em **monitoramento** selecione **métricas**. Para exibir os valores disponíveis, selecione a lista suspensa **MÉTRICA**.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o firewall para coletar logs, poderá explorar os logs do Azure Monitor para exibir seus dados.

[Soluções de monitoramento de rede nos logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)
