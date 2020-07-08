---
title: Alternar para a nova API de alertas do Azure
description: Visão geral do savedSearch herdado com base Log Analytics API de alerta e processo para alternar as regras de alerta para a nova API ScheduledQueryRules, com detalhes que abordam as preocupações comuns dos clientes.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 05/30/2019
ms.subservice: alerts
ms.openlocfilehash: 7950b3f584c36b68a4eff66b05e83ba94c1ec1dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81114361"
---
# <a name="switch-api-preference-for-log-alerts"></a>Mudar preferência de API para os Alertas de Log

> [!NOTE]
> Conteúdo declarado aplicável a usuários somente na nuvem pública do Azure e **não** para o Azure governamental ou para a nuvem do Azure China.  

> [!NOTE]
> Depois que um usuário optar por alternar a preferência para a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) , não será possível reverter para o uso da [API de alerta de log Analytics herdada](api-alerts.md)mais antiga.

Até recentemente, você gerenciava regras de alerta no Portal do Microsoft Operations Management Suite. A nova experiência de alertas foi integrada a vários serviços no Microsoft Azure, incluindo o Log Analytics, e solicitamos [estender as regras de alerta do portal do OMS para o Azure](alerts-extend.md). Mas, para assegurar o mínimo de interrupção para os clientes, o processo não alterou a interface programática do seu consumo – [API de alerta do Log Analytics](api-alerts.md) com base em SavedSearch.

Mas agora você anuncia para os usuários de alerta do Log Analytics uma verdadeira alternativa programática do Azure, [Azure Monitor – API ScheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), que também se reflete em sua [Cobrança do Azure – para alertas de log](alerts-unified-log.md#pricing-and-billing-of-log-alerts). Para saber mais sobre como gerenciar seus alertas de log usando a API, consulte [Gerenciando alertas de log usando o modelo de recursos do Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [Gerenciando alertas de log usando o PowerShell](alerts-log.md#managing-log-alerts-using-powershell).

## <a name="benefits-of-switching-to-new-azure-api"></a>Benefícios de mudar para a nova API do Azure

Há várias vantagens em criar e gerenciar alertas usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) em relação à [API herdada de alerta do Log Analytics](api-alerts.md); listamos algumas das principais abaixo:

- Capacidade de realizar [pesquisa de logs entre workspaces](../log-query/cross-workspace-query.md) em regras de alerta e abranger recursos externos como workspaces do Log Analytics ou até mesmo aplicativos do Application Insights
- Quando vários campos são usados para Agrupar na consulta usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), o usuário pode especificar qual campo agregar no portal do Azure
- Alertas de log criados usando a [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) podem ter um período definido de até 48 horas e buscar dados por um período mais longo que antes
- Criar regras de alerta de uma só vez como um único recurso sem a necessidade de criar três níveis de recursos como ocorre com a [API herdada de alerta do Log Analytics](api-alerts.md)
- Interface de programação única para todas as variantes de alertas de log baseados em consulta no Azure – a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) pode ser usada para gerenciar regras do Log Analytics, bem como do Application Insights
- Gerenciar seus alertas de log usando [cmdlets do PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Todas as novas funcionalidades de alertas de log e de desenvolvimento futuro estarão disponíveis somente por meio da nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules)

## <a name="process-of-switching-from-legacy-log-alerts-api"></a>Processo de mudar da API herdada de alertas de log

Os usuários são livres para usar a [API herdada de alertas do Log Analytics](api-alerts.md) ou a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). As regras de alerta criadas por qualquer API *serão gerenciáveis somente pela mesma API* – bem como do portal do Azure. Por padrão, Azure Monitor continuará a usar a [API de alerta log Analytics herdada](api-alerts.md) para criar qualquer nova regra de alerta de portal do Azure para os espaços de trabalho existentes do log Analytics. Conforme [anunciado novo espaço de trabalho de log criado em ou após 1º de junho de 2019,](https://azure.microsoft.com/updates/switch-api-preference-log-alerts/) o usará automaticamente a nova [API scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) por padrão, incluindo em portal do Azure.

Os impactos da mudança de preferência para a API scheduledQueryRules são compilados abaixo:

- Todas as interações feitas para gerenciar alertas de log por meio de interfaces programáticas agora devem ser feitas usando a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Para obter mais informações, consulte [exemplo de uso por meio do modelo de recurso do Azure](alerts-log.md#managing-log-alerts-using-azure-resource-template) e [uso de exemplo por meio do PowerShell](alerts-log.md#managing-log-alerts-using-powershell)
- Qualquer regra de alerta de log nova criada no portal do Azure será criada usando somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e permitirá que os usuários usem a [funcionalidade adicional da nova API](#benefits-of-switching-to-new-azure-api) por meio do portal do Azure também
- A severidade das regras de alerta de log mudará de: *crítico, aviso & informativo*, para *valores de severidade de 0, 1 & 2*. Junto com a opção de criar/atualizar regras de alerta com a severidade 3 e 4 também.

O processo de mudar as regras de alerta da [API herdada de alertas do Log Analytics](api-alerts.md) não envolve a mudança da definição, consulta ou configuração do alerta de nenhuma maneira. Suas regras de alerta e o monitoramento não são afetados e os alertas não serão interrompidos ou não serão interrompidos durante ou após o comutador. As únicas alterações são:

- Uma alteração na preferência de API e o acesso às suas regras por meio de uma nova API.
- Um URI de recurso de regra de alerta modificado que contém as IDs usadas na [API de alerta de log Analytics herdada](api-alerts.md) em vez do nome da regra de alerta nesta estrutura `<WorkspaceName>|<savedSearchId>|<scheduleId>|<ActionId>` . O nome de exibição da regra de alerta permanecerá inalterado.

Qualquer cliente que desejar mudar voluntariamente para a nova [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) e bloquear o uso da [API herdada de alertas do Log Analytics](api-alerts.md) poderá fazer isso executando uma chamada PUT na API abaixo para mudar todas as regras de alerta associadas ao espaço de trabalho do Log Analytics específico.

```
PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Com o corpo da solicitação contendo o JSON abaixo.

```json
{
    "scheduledQueryRulesEnabled" : true
}
```

A API também pode ser acessada por meio de uma linha de comando do PowerShell usando o [ARMClient](https://github.com/projectkudu/ARMClient), uma ferramenta de linha de comando de software livre que simplifica a invocação da API do Azure Resource Manager. Conforme ilustrado abaixo, na chamada PUT de exemplo, a ferramenta ARMclient é usada para alternar todas as regras de alerta associadas ao espaço de trabalho do Log Analytics específico.

```powershell
$switchJSON = '{"scheduledQueryRulesEnabled": "true"}'
armclient PUT /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview $switchJSON
```

Se a mudança de todas as regras de alerta no espaço de trabalho do Log Analytics para usar a nova [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules) for bem-sucedida, a seguinte resposta será fornecida.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```

Os usuários também podem verificar o status atual do espaço de trabalho do Log Analytics e ver se a mudança foi realizada ou não para ele usar somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules). Para verificar, os usuários podem realizar uma chamada GET na API abaixo.

```
GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Para executar os itens acima usando a linha de comando do PowerShell com a ferramenta [ARMClient](https://github.com/projectkudu/ARMClient), confira o exemplo a seguir.

```powershell
armclient GET /subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>/alertsversion?api-version=2017-04-26-preview
```

Se o espaço de trabalho do Log Analytics especificado tiver mudado para usar somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), o JSON de resposta será conforme listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : true
}
```
Além do mais, se o workspace do Log Analytics especificado ainda não tiver sido mudado para usar somente a [scheduledQueryRules](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules), o JSON de resposta será conforme listado abaixo.

```json
{
    "version": 2,
    "scheduledQueryRulesEnabled" : false
}
```

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Monitor – Alertas de log](alerts-unified-log.md).
- Saiba como criar [alertas de log nos alertas do Azure](alerts-log.md).
- Saiba mais sobre a [experiência de alertas do Azure](../../azure-monitor/platform/alerts-overview.md).
