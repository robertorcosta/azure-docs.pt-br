---
title: Coletar log de atividades do Azure com configurações de diagnóstico (versão prévia) – Azure Monitor | Microsoft Docs
description: Use as configurações de diagnóstico para encaminhar logs de atividades do Azure para Azure Monitor logs, armazenamento do Azure ou hubs de eventos do Azure.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 01/21/2020
ms.openlocfilehash: dff4901f1488406ed1259d1411a6b05b949382cb
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76715849"
---
# <a name="update-to-azure-activity-log-collection-and-export"></a>Atualizar para a coleta e a exportação do log de atividades do Azure
O [log de atividades do Azure](platform-logs-overview.md) é um [log de plataforma](platform-logs-overview.md) que fornece informações sobre eventos de nível de assinatura que ocorreram no Azure. O método para enviar entradas do log de atividades para [um hub de eventos ou uma conta de armazenamento](activity-log-export.md) ou para um [log Analytics espaço de trabalho](activity-log-collect.md) foi alterado para usar [as configurações de diagnóstico](diagnostic-settings.md). Este artigo descreve a diferença entre os métodos e como limpar as configurações herdadas na preparação para alterar as configurações de diagnóstico.


## <a name="differences-between-methods"></a>Diferenças entre métodos

### <a name="advantages"></a>Vantagens
O uso de configurações de diagnóstico tem as seguintes vantagens em relação aos métodos atuais:

- Método consistente para coletar todos os logs de plataforma.
- Coletar log de atividades entre várias assinaturas e locatários.
- Filtrar coleção para coletar somente logs para categorias específicas.
- Coletar todas as categorias de log de atividades. Algumas categorias não são coletadas usando o método herdado.
- Latência mais rápida para a ingestão de logs. O método anterior tem aproximadamente 15 minutos de latência enquanto as configurações de diagnóstico adicionam apenas cerca de 1 minuto.

### <a name="considerations"></a>Considerações
Considere os seguintes detalhes da coleta de log de atividades usando configurações de diagnóstico antes de habilitar esse recurso.

- A configuração de retenção para coletar o log de atividades para o armazenamento do Azure foi removida, o que significa que os dados serão armazenados indefinidamente até que você o remova.
- No momento, você só pode criar uma configuração de diagnóstico de nível de assinatura usando o portal do Azure. Para usar outros métodos, como o PowerShell ou a CLI, você pode criar um modelo do Resource Manager.


### <a name="differences-in-data"></a>Diferenças nos dados
As configurações de diagnóstico coletam os mesmos dados que os métodos anteriores usados para coletar o log de atividades com as seguintes diferenças atuais:

As colunas a seguir foram removidas. A substituição dessas colunas está em um formato diferente, portanto, talvez seja necessário modificar as consultas de log que as utilizam. Você ainda pode ver colunas removidas no esquema, mas elas não serão preenchidas com dados.

| Coluna removida | Coluna de substituição |
|:---|:---|
| ActivityStatus    | ActivityStatusValue    |
| ActivitySubstatus | ActivitySubstatusValue |
| OperationName     | OperationNamevalue     |
| ResourceProvider  | ResourceProviderValue  |

A seguinte coluna foi adicionada:

- Authorization_d
- Claims_d
- Properties_d

## <a name="work-with-legacy-settings"></a>Trabalhar com configurações herdadas
As configurações herdadas para coletar o log de atividades continuarão a funcionar se você não optar por substituir por uma configuração de diagnóstico. Use o método a seguir para gerenciar o perfil de log para uma assinatura.

1. No menu **Azure monitor** na portal do Azure, selecione log de **atividades**.
3. Clique em **Configurações do Diagnóstico**.

   ![Configurações de Diagnóstico](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Clique na faixa roxa para a experiência herdada.

    ![Experiência herdada](media/diagnostic-settings-subscription/legacy-experience.png)


Consulte os artigos a seguir para obter detalhes sobre como usar os métodos de coleção herdados.

- [Coletar e analisar os logs de atividades do Azure no espaço de trabalho Log Analytics no Azure Monitor](activity-log-collect.md)
- [Coletar logs de atividades do Azure em Azure Monitor entre locatários Azure Active Directory](activity-log-collect-tenants.md)
- [Exportar o log de atividades do Azure para o armazenamento ou hubs de eventos do Azure](activity-log-export.md)

## <a name="disable-existing-settings"></a>Desabilitar configurações existentes
Você deve desabilitar a coleta existente da atividade antes de habilitá-la usando as configurações de diagnóstico. Ter ambos habilitados pode resultar em dados duplicados.

### <a name="disable-collection-into-log-analytics-workspace"></a>Desabilitar a coleta no espaço de trabalho Log Analytics

1. Abra o menu **log Analytics espaços de trabalho** na portal do Azure e selecione o espaço de trabalho para coletar o log de atividades.
2. Na seção **fontes de dados de espaço de trabalho** do menu do espaço de trabalho, selecione **log de atividades do Azure**.
3. Clique na assinatura que você deseja desconectar.
4. Clique em **Desconectar** e em **Sim** quando solicitado a confirmar sua escolha.

### <a name="disable-log-profile"></a>Desabilitar perfil de log

1. Use o procedimento descrito em [trabalhar com configurações herdadas](#work-with-legacy-settings) para abrir as configurações herdadas.
2. Desabilite qualquer coleção atual para o armazenamento ou hubs de eventos.



## <a name="activity-log-monitoring-solution"></a>Solução de monitoramento do log de atividades
A solução de monitoramento de Log Analytics do Azure inclui várias consultas de log e exibições para analisar os registros de log de atividades em seu espaço de trabalho do Log Analytics. Essa solução usa os dados de log coletados em um espaço de trabalho Log Analytics e continuará a funcionar sem nenhuma alteração se você coletar o log de atividades usando as configurações de diagnóstico. Consulte [solução de monitoramento de análise de logs de atividade](activity-log-collect.md#activity-logs-analytics-monitoring-solution) para obter detalhes sobre esta solução.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Leia mais sobre o Log de Atividades](../../azure-resource-manager/management/view-activity-logs.md)
* [Saiba mais sobre as configurações de diagnóstico](diagnostic-settings.md)
