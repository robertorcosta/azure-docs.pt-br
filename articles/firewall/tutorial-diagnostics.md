---
title: 'Tutorial: Monitorar os logs e as métricas do Firewall do Azure'
description: Neste tutorial, você aprenderá a habilitar e a gerenciar os logs do Firewall do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: da46cf826da40658883d22692e5038b09d222907
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75974521"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Tutorial: Monitorar os logs e as métricas do Firewall do Azure

Você pode monitorar o Firewall do Azure usando os logs de firewall. Você também pode usar os logs de atividades para auditar operações nos recursos do Firewall do Azure. Usando as métricas, você pode exibir contadores de desempenho no portal.

Você pode acessar alguns desses logs por meio do portal. Os logs podem ser enviados para os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), o Armazenamento e os Hubs de Eventos e analisados nos logs do Azure Monitor ou por ferramentas diferentes, como Excel e Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Habilitar o log por meio do portal do Azure
> * Habilitar o registro em log com o PowerShell
> * Exibir e analisar o log de atividades
> * Exibir e analisar os logs de regra de aplicativo e de rede
> * Métricas de exibição


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisites

Antes de iniciar este tutorial, você deve ler [métricas e logs do Firewall do Azure](logs-and-metrics.md) para obter uma visão geral dos logs e métricas de diagnóstico disponíveis para o Firewall do Azure.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Habilitar o log de diagnósticos por meio do portal do Azure

Pode levar alguns minutos até que os dados sejam exibidos em seus logs depois de concluir este procedimento para ativar o log de diagnósticos. Se você não vir algo a princípio, verifique novamente depois de alguns minutos.

1. No portal do Azure, abra seu grupo de recursos de firewall e clique no firewall.
2. Em **Monitoramento**, clique em **Configurações de diagnóstico**.

   Para o Firewall do Azure, dois logs específicos de serviço estão disponíveis:

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Para iniciar a coleta de dados., clique em **Ativar diagnóstico**.
4. A página **Configurações de diagnóstico** fornece as configurações dos logs de diagnóstico.
5. Neste exemplo, os logs do Azure Monitor armazenam os logs; portanto, digite **Log Analytics do Firewall** para o nome.
6. Clique em **Enviar para o Log Analytics** para configurar seu workspace. Você também pode usar os hubs de eventos e uma conta de armazenamento para salvar os logs de diagnóstico.
7. Em **Log Analytics**, clique em **Configurar**.
8. Na página Workspaces do Log Analytics, clique em **Criar Novo Workspace**.
9. Na página **Espaço de Trablho do Log Analytics**, digite **firewall-oms** como o novo nome do **Espaço de Trablho do Log Analytics**.
10. Selecione sua assinatura, use o grupo de recursos de firewall existente (**Test-FW-RG**), selecione **Leste dos EUA** para o local e selecione o tipo de preço **gratuito**.
11. Clique em **OK**.
   ![Iniciando o processo de configuração][1] Os workspaces do OMS agora são chamados de workspaces do Log Analytics.  
12. Em **Log**, clique em **AzureFirewallApplicationRule** e **AzureFirewallNetworkRule** para coletar logs de regras de aplicativo e de rede.
   ![Salvar configurações de diagnóstico][2]
13. Clique em **Save** (Salvar).

## <a name="enable-logging-with-powershell"></a>Habilitar o registro em log com o PowerShell

O log de atividade é habilitado automaticamente para todos os recursos do Resource Manager. O log de diagnósticos deve ser habilitado para começar a coletar os dados disponíveis por meio desses logs.

Para habilitar os logs de diagnóstico, realize as seguintes etapas:

1. Anote a ID do recurso da conta de armazenamento, na qual os dados de log são armazenados. Esse valor tem o formato: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Storage/storageAccounts/\<nome da conta de armazenamento\>* .

   Use qualquer conta de armazenamento em sua assinatura. Use o portal do Azure para encontrar essas informações. As informações estão localizadas na página do recurso **Propriedades**.

2. Anote a ID do recurso Firewall para o qual o log está habilitado. Esse valor tem o formato: */subscriptions/\<subscriptionId\>/resourceGroups/\<nome do grupo de recursos\>/providers/Microsoft.Network/azureFirewalls/\<nome do Firewall\>* .

   Use o portal para encontrar essas informações.

3. Habilite o log de diagnóstico usando o seguinte cmdlet do PowerShell:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Os logs de diagnóstico não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de acesso e de desempenho gera encargos de serviço.

## <a name="view-and-analyze-the-activity-log"></a>Exibir e analisar o log de atividades

Você pode exibir e analisar os dados do log de atividades usando um dos seguintes métodos:

* **Ferramentas do Azure**: Recupere informações do log de atividades por meio do Azure PowerShell, da CLI do Azure, da API REST do Azure ou do portal do Azure. As instruções passo a passo para cada método são detalhadas no artigo [Activity operations with Resource Manager](../azure-resource-manager/management/view-activity-logs.md) (Operações de atividade com o Resource Manager).
* **Power BI**: Se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), experimente uma gratuitamente. Com o [pacote de conteúdo dos Logs de Atividades do Azure para Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados que podem ser usados no estado em que se encontram ou ser personalizados.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Exibir e analisar os logs de regra de aplicativo e de rede

Os [logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) coletam os arquivos de log de eventos e de contador. Ele inclui visualizações e funcionalidades de pesquisa avançadas para analisar os logs.

Para consultas de exemplo do Log Analytics do Firewall do Azure, confira [Amostras do Log Analytics do Firewall do Azure](log-analytics-samples.md).

Você também pode se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de desempenho e acesso. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-los no Excel, no Power BI ou em qualquer outra ferramenta de visualização de dados.

> [!TIP]
> Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C#, você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no GitHub.

## <a name="view-metrics"></a>Métricas de exibição
Navegue até um Firewall do Azure e, em **Monitoramento**, clique em **Métricas**. Para exibir os valores disponíveis, selecione a lista suspensa **MÉTRICA**.

## <a name="next-steps"></a>Próximas etapas

Agora que você configurou o firewall para coletar logs, poderá explorar os logs do Azure Monitor para exibir seus dados.

> [!div class="nextstepaction"]
> [Soluções de monitoramento de rede nos logs do Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
