---
title: Exibir e analisar dados no Azure Log Analytics | Microsoft Docs
description: Assistência para usuários da pesquisa de logs do Log Analytics à experiência de consulta de log do Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2019
ms.openlocfilehash: 6cc7fc94162ed101d06fbbbc4722867be46987b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "77670127"
---
# <a name="transition-from-log-analytics-log-search-to-azure-monitor-logs"></a>Transição da pesquisa de logs do Log Analytics para os logs do Azure Monitor
Recentemente, a pesquisa de logs do Log Analytics foi substituída por uma nova experiência de análise de logs do Azure Monitor. Atualmente, a página Pesquisa de logs ainda pode ser acessada por meio do item de menu **Logs (clássico)** na página **Workspaces do Log Analytics** no portal do Azure, mas será removida em 15 de fevereiro de 2019. Este artigo descreve as diferenças entre as duas experiências para ajudar você a fazer a transição da pesquisa de logs. 

## <a name="filter-results-of-a-query"></a>Filtrar os resultados de uma consulta
Na Pesquisa de Logs, uma lista de filtros é exibida conforme os resultados da pesquisa são entregues. Selecione um filtro e clique em **Aplicar** para executar a consulta com o filtro selecionado.

![Filtro da Pesquisa de Logs](media/log-search-transition/filter-log-search.png)

Nos logs do Azure Monitor, selecione *Filtro (versão prévia)* para exibir os filtros. Clique no ícone de filtro para exibir os filtros de adição. Selecione um filtro e clique em **Aplicar e Executar** para executar a consulta com o filtro selecionado.

![Filtro de logs](media/log-search-transition/filter-logs.png)

## <a name="extract-custom-fields"></a>Extrair campos personalizados 
Na Pesquisa de Logs, você extrai [campos personalizados](../platform/custom-fields.md) da exibição de lista, em que o menu de um campo inclui a ação _Extrair campos da Tabela_.

![Extração de campos da Pesquisa de Logs](media/log-search-transition/extract-fields-log-search.png)

Nos logs do Azure Monitor, você extrai campos personalizados da exibição de tabela. Expanda um registro clicando na seta à esquerda e, em seguida, clique nas reticências para acessar a ação _Extrair campos_.

![Extração de campos dos logs](media/log-search-transition/extract-fields-logs.png)

## <a name="functions-and-computer-groups"></a>Funções e grupos de computadores
Para salvar uma pesquisa na Pesquisa de Logs, selecione **Pesquisas salvas** e **Adicionar** para fornecer um nome, uma categoria e um texto de consulta. Crie um [grupo de computadores](../platform/computer-groups.md) adicionando um alias da função.

![Salvar a pesquisa de logs](media/log-search-transition/save-search-log-search.png)

Para salvar a consulta atual nos logs do Azure Monitor, selecione **Salvar**. Altere **Salvar como** para _Função_ e forneça um **Alias da Função** para criar uma [Função](functions.md). Selecione _Salvar essa consulta como um grupo de computadores_ para usar o alias da função para um [grupo de computadores](../platform/computer-groups.md).

![Salvar a consulta de log](media/log-search-transition/save-query-logs.png)

## <a name="saved-queries"></a>Consultas salvas
Na Pesquisa de Logs, as consultas salvas ficam disponíveis por meio do item da barra de ações **Pesquisas salvas**. Nos logs do Azure Monitor, acesse as consultas salvas no [Gerenciador de Consultas](../log-query/get-started-portal.md#save-queries).

![Gerenciador de consultas](media/log-search-transition/query-explorer.png)

## <a name="drill-down-on-summarized-rows"></a>Busca detalhada em linhas resumidas
Na Pesquisa de Logs, você pode clicar em uma linha em uma consulta resumida para iniciar outra consulta que liste os registros detalhados nessa linha.

![Busca detalhada de Pesquisa de Log](media/log-search-transition/drilldown-search.png)

Nos logs do Azure Monitor, você deve modificar a consulta para retornar esses registros. Expanda uma das linhas nos resultados e clique em ao **+** lado do valor para adicioná-lo à consulta. Em seguida, comente o comando **resumir** e execute a consulta novamente.

![Busca detalhada de logs do Azure Monitor](media/log-search-transition/drilldown-logs.png)

## <a name="take-action"></a>Executar ação
Na Pesquisa de Logs, [inicie um runbook](take-action.md) a partir de um resultado de pesquisa selecionando **Executar ação**.

![Executar ação](media/log-search-transition/take-action-log-search.png)

Nos logs do Azure Monitor, [crie um alerta com base na consulta de log](../platform/alerts-log.md). Configure um grupo de ações com uma ou mais ações que serão executadas em resposta ao alerta.

![Grupo de ações](media/log-search-transition/action-group.png)

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre a nova [experiência de logs do Azure Monitor](get-started-portal.md).
