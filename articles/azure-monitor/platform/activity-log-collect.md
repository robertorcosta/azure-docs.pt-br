---
title: Coletar o login de atividades do Azure no espaço de trabalho do Log Analytics
description: Colete o Log de Atividades do Azure no Azure Monitor Logs e use a solução de monitoramento para analisar e pesquisar o registro de atividades do Azure em todas as suas assinaturas do Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055322"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Coletar e analisar os logs de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor

> [!WARNING]
> Agora você pode coletar o login do Activity em um espaço de trabalho do Log Analytics usando uma configuração de diagnóstico semelhante à forma como você coleta logs de recursos. Consulte [Coletar e analisar os logs de atividades do Azure no espaço de trabalho do Log Analytics no Azure Monitor](diagnostic-settings-legacy.md).

O [Azure Activity Log](platform-logs-overview.md) fornece informações sobre eventos de nível de assinatura que ocorreram em sua assinatura do Azure. Este artigo descreve como coletar o Registro de Atividades em um espaço de trabalho do Log Analytics e como usar a solução de [monitoramento](../insights/solutions.md)do Activity Log Analytics , que fornece consultas e visualizações de log para analisar esses dados. 

Conectar o Registro de Atividades a um espaço de trabalho do Log Analytics oferece os seguintes benefícios:

- Consolide o Registro de Atividades a partir de várias assinaturas do Azure em um local para análise.
- Entradas do Registro de Atividades da Loja por mais de 90 dias.
- Correlacionar dados do Registro de Atividades com outros dados de monitoramento coletados pelo Azure Monitor.
- Use consultas de log para realizar [análises](../log-query/log-query-overview.md) complexas e obter insights profundos sobre entradas do Registro de Atividades.

## <a name="connect-to-log-analytics-workspace"></a>Conecte-se ao espaço de trabalho do Log Analytics
Um único espaço de trabalho pode ser conectado ao Registro de Atividades para várias assinaturas no mesmo inquilino do Azure. Para coletar em vários inquilinos, consulte [Collect Azure Activity Logs em um espaço de trabalho do Log Analytics através de assinaturas em diferentes inquilinos do Azure Active Directory](activity-log-collect-tenants.md).

> [!IMPORTANT]
> Você pode receber um erro com o seguinte procedimento se os provedores de recursos Microsoft.OperationalInsights e Microsoft.OperationsManagement não forem registrados para sua assinatura. Consulte [os provedores de recursos e tipos do Azure](../../azure-resource-manager/management/resource-providers-and-types.md) para registrar esses provedores.

Use o procedimento a seguir para conectar o Registro de atividades ao espaço de trabalho do Log Analytics:

1. No menu de espaços de trabalho do Log Analytics no portal Azure, selecione o espaço de trabalho para coletar o Registro de **atividades.**
1. Na seção Fontes de dados do espaço de **trabalho** do menu do espaço de trabalho, selecione O log de atividade **do Azure**.
1. Clique na assinatura que deseja conectar.

    ![Workspaces](media/activity-log-export/workspaces.png)

1. Clique **em Conectar** para conectar o login atividade na assinatura do espaço de trabalho selecionado. Se a assinatura já estiver conectada a outro espaço de trabalho, clique **em Desconectar** primeiro para desconectá-la.

    ![Conectar espaços de trabalho](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>Analisar no espaço de trabalho do Log Analytics
Quando você conecta um registro de atividades a um espaço de trabalho do Log Analytics, as entradas serão escritas no espaço de trabalho em uma tabela chamada **AzureActivity** que você pode recuperar com uma [consulta de log](../log-query/log-query-overview.md). A estrutura desta tabela varia dependendo da [categoria de entrada de registro](activity-log-view.md#categories-in-the-activity-log). Consulte [o esquema de eventos do Azure Activity Log](activity-log-schema.md) para obter uma descrição de cada categoria.

## <a name="activity-logs-analytics-monitoring-solution"></a>Solução de monitoramento do Activity Logs Analytics
A solução de monitoramento do Azure Log Analytics inclui várias consultas de log e visualizações para analisar os registros do Registro de Atividades em seu espaço de trabalho do Log Analytics.

### <a name="install-the-solution"></a>Instalar a solução
Use o procedimento em [Instalar uma solução de monitoramento](../insights/solutions.md#install-a-monitoring-solution) para instalar a solução Activity Log **Analytics.** Não é necessária uma configuração adicional.

### <a name="use-the-solution"></a>Usar a solução
As soluções de monitoramento são acessadas a partir do menu **Monitor** no portal Azure. Selecione **Mais** na seção **'Insights'** para abrir a página **Visão geral** com os ladrilhos da solução. O **azulejo Azure Activity Logs** exibe uma contagem do número de registros do **AzureActivity** em seu espaço de trabalho.

![Bloco de logs de atividade do Azure](media/collect-activity-logs/azure-activity-logs-tile.png)


Clique no **bloco Azure Activity Logs** para abrir a exibição **Logs de atividade do Azure.** A visualização inclui as partes de visualização na tabela a seguir. Cada peça lista até 10 itens que correspondem aos critérios das peças para o intervalo de tempo especificado. Você pode executar uma consulta de log que retorna todos os registros correspondentes clicando em **Ver tudo** na parte inferior da peça.

![Painel Logs de Atividade do Azure](media/collect-activity-logs/activity-log-dash.png)

| Parte de visualização | Descrição |
| --- | --- |
| Entradas de log de atividades do Azure | Mostra um gráfico de barras dos principais totais de registro de entrada do Azure Activity Log para a faixa de data que você selecionou e mostra uma lista dos 10 principais chamadores de atividades. Clique no gráfico de barras para executar uma pesquisa de logs para `AzureActivity`. Clique em um item de chamada para executar uma pesquisa de log retornando todas as entradas do Registro de Atividades para esse item. |
| Logs de atividade por status | Mostra um gráfico de rosquinhas para o status do Azure Activity Log para a faixa de data selecionada e uma lista dos dez principais registros de status. Clique no gráfico para executar `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`uma consulta de log para . Clique em um item de status para executar uma pesquisa de log retornando todas as entradas do Registro de atividades para esse registro de status. |
| Logs de atividade por recurso | Mostra o número total de recursos com Registros de Atividades e lista os dez principais recursos com contagem de registros para cada recurso. Clique na área total para executar uma pesquisa de logs de `AzureActivity | summarize AggregatedValue = count() by Resource`, que mostra todos os recursos do Azure disponíveis para a solução. Clique em um recurso para executar uma consulta de log retornando todos os registros de atividades para esse recurso. |
| Logs de atividade por provedor de recursos | Mostra o número total de provedores de recursos que produzem Registros de Atividades e lista os dez primeiros. Clique na área total para executar `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`uma consulta de log para , que mostra todos os provedores de recursos do Azure. Clique em um provedor de recursos para executar uma consulta de log retornando todos os registros de atividades para o provedor. |

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Registro de Atividades](platform-logs-overview.md).
- Saiba mais sobre a [plataforma de dados do Azure Monitor](data-platform.md).
- Use [consultas de log](../log-query/log-query-overview.md) para exibir informações detalhadas do seu Registro de Atividades.
