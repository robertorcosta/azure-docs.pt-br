---
title: Criar relatórios interativos com pastas de trabalho do Azure Monitor | Microsoft Docs
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bbbf511286c63acf4a939e0a0e7d9c3dc9efa75b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899383"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Crie relatórios interativos com pastas de trabalho do Azure Monitor

As Pastas de Trabalho combinam texto, [consultas de Análise](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), métricas do Azure e os parâmetros em relatórios interativos avançados. As Pastas de Trabalho são editáveis por qualquer membro da equipe com acesso aos mesmos recursos do Azure.

As pastas de trabalho são úteis para cenários como:

* Explorando o uso de seu aplicativo quando você não conhece as métricas de interesse com antecedência: números de usuários, taxas de retenção, taxas de conversão, etc. Ao contrário de outras ferramentas de análise de uso, as pastas de trabalho permitem combinar vários tipos de visualizações e análises, tornando-as excelentes para esse tipo de exploração de forma livre.
* Explicar para sua equipe sobre o desempenho de um recurso recém-liberado, mostrando contagens de usuário para as principais interações e outras métricas.
* Compartilhar os resultados de um experimento A/B no aplicativo com outros membros de sua equipe. Você pode explicar as metas do experimento com um texto e, depois, mostrar cada métrica de uso e consulta do Analytics usada para avaliar o experimento, junto com textos explicativos claros que indicam se cada métrica estava acima ou abaixo da meta.
* Relatar o impacto de uma interrupção no uso do aplicativo, combinando dados, explicação de texto e uma discussão das próximas etapas para prevenir interrupções no futuro.

## <a name="starting-with-a-template-or-saved-workbook"></a>Começando com um modelo ou pasta de trabalho salva

Uma pasta de trabalho composta por seções consiste em gráficos editáveis de forma independente, tabelas, texto e controles de entrada. Para entender melhor as pastas de trabalho, é melhor abrir uma. 

Selecione **Pastas de trabalho** no menu à esquerda de dentro da experiência do Application Insights para seu aplicativo.

![Captura de tela de navegação para as pastas de trabalho](./media/usage-workbooks/001-workbooks.png)

Isso inicia uma galeria de pasta de trabalho com um número de pastas de trabalho predefinido para ajudar você a começar.

![Captura de tela da galeria da pasta de trabalho](./media/usage-workbooks/002-workbook-gallery.png)

Vamos começar com o **Modelo Padrão**, que está localizado no título **Guia de início rápido**.

![Captura de tela da galeria da pasta de trabalho](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Editando, reorganizando, clonando e excluindo seções da pasta de trabalho

Pastas de Trabalho têm dois modos: **modo de edição**, e **modo de leitura**. Quando a pasta de trabalho padrão é iniciado pela primeira vez, ela é aberta no **modo de edição**. Isso mostra todo o conteúdo da pasta de trabalho, incluindo quaisquer etapas e os parâmetros que estiverem ocultos de outra forma. **Modo de leitura** apresenta uma exibição de estilo do relatório simplificado. Isso permite abstrair a complexidade que deu a criação de um relatório ao mesmo tempo, a mecânica subjacente apenas alguns cliques de distância quando necessário para modificação.

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/editing-controls-new.png)

1. Quando terminar de editar uma seção, clique em **Edição Concluída** no canto inferior esquerdo da seção.

2. Para criar uma duplicata de uma seção, clique no ícone **Clonar esta seção**. Criar seções duplicadas é uma ótima forma de iterar em uma consulta sem perder as iterações anteriores.

3. Para mover uma seção para cima em uma pasta de trabalho, clique no ícone **Mover para cima** ou **Mover para baixo**.

4. Para remover uma seção permanentemente, clique no ícone **Remover**.

## <a name="adding-text-and-markdown-sections"></a>Adicionando texto e seções de Markdown

Adicionar cabeçalhos, explicações e comentários às pastas de trabalho ajuda a transformar um conjunto de tabelas e gráficos em uma narrativa. As seções de texto nas pastas de trabalho dão suporte à [sintaxe de Markdown](https://daringfireball.net/projects/markdown/) na formatação de texto, como cabeçalhos, negrito, itálico e listas com marcadores.

Para adicionar uma seção de texto à pasta de trabalho, use o botão **Adicionar texto** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

## <a name="adding-query-sections"></a>Adicionando seções de consulta

![Seção de consulta no Workbooks](./media/usage-workbooks/analytics-section-new.png)

Para adicionar uma seção de consulta à pasta de trabalho, use o botão **Adicionar consulta** na parte inferior da pasta de trabalho ou na parte inferior de qualquer seção.

Seções de consulta são altamente flexíveis e podem ser usadas para responder perguntas como:

* Quantas exceções seu site gerou durante o mesmo período como um declínio no uso?
* Qual foi a distribuição dos tempos de carregamento de página para os usuários que exibiram alguma página?
* Quantos usuários exibiram algum conjunto de páginas no site, mas não algum outro conjunto de páginas? Pode ser útil entender isso se você tiver clusters de usuários que usam diferentes subconjuntos da funcionalidade do site (use o operador `join` com o modificador `kind=leftanti` na [linguagem de consulta Kusto](/azure/kusto/query/)).

Você também não é limitado apenas ao consultar a partir do contexto do aplicativo iniciado da pasta de trabalho. Você pode consultar em vários aplicativos monitorados do Application Insights, bem como os espaços de trabalho do Log Analytics, desde que você tem permissão de acesso a esses recursos.

A consulta dos recursos externos do Application Insights usa o **identificador** do aplicativo.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Essa consulta é combinar solicitações de três aplicativos diferentes. Um aplicativo chamado app01, um aplicativo chamado app02 e as solicitações de recurso do Application Insights local.

Para extrair dados de um uso de espaço de trabalho do Log Analytics use o**identificador** do espaço de trabalho.

Para saber mais sobre consultas entre recursos, consulte as [diretrizes oficiais](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Configurações avançadas de consulta analítica

Cada seção tem suas próprias configurações avançadas, que podem ser acessadas por meio do ícone de configurações ![controle de edição de seção Application Insights Workbooks](./media/usage-workbooks/005-settings.png) localizado à direita do botão **Adicionar Parâmetros**.

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Largura personalizada**    | Defina isso para tornar um item um tamanho arbitrário, portanto, você pode ajustar vários itens em uma única linha que lhe permite organizar melhor seus gráficos e tabelas em relatórios interativos avançados.  |
   | **Condicionalmente visível** | Use isto para ocultar etapas com base em um valor de parâmetro. As etapas só ficam ocultas no modo de leitura. |
   | **Exportar um parâmetro**| Isso permite que uma linha selecionada na grade ou gráfico faça com que as etapas posteriores alterarem os valores ou se tornem visíveis.  |
   | **Mostrar consulta quando não estiver em edição** | Isso exibe a consulta acima do gráfico ou uma tabela, mesma quando no modo de leitura.
   | **Mostrar abrir no botão análise ao não editar** | Isso adiciona o ícone azul do Analytics para o canto direito do gráfico para permitir o acesso por um clique.|

A maioria dessas configurações é bastante intuitiva, mas para entender **um parâmetro de exportação** é melhor examinar uma pasta de trabalho que usa essa funcionalidade.

Uma das pastas de trabalho predefinidas fornece informações sobre usuários ativos.

A primeira seção da pasta de trabalho se baseia em dados de consulta analítica:

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/003-active-users.png)

A segunda seção também se baseia nos dados de consulta analítica, mas selecionar uma linha na primeira tabela interativamente atualizará o conteúdo do gráfico:

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/004-active-users-trend.png)

 Isso é possível por meio do uso do **Quando um item é selecionado, exporte um parâmetro** configurações avançadas que estão habilitadas na consulta do Analytics da tabela.

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/007-settings-export.png)

A segunda consulta do Analytics, em seguida, utiliza os valores exportados quando uma linha está selecionada. Se nenhuma linha estiver selecionada, o padrão será a linha que representa os valores gerais. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Adicionando seções de métricas

As seções métricas dão acesso completo ao incorporar dados de métricas do Azure Monitor em seus relatórios interativos. Muitas das pastas de trabalho predefinidas conterão dados de consulta analítica e dados de métrica, permitindo que você possa aproveitar o melhor de ambos os recursos em um só lugar. Você também tem a capacidade de extrair dados de métrica de recursos em qualquer uma das assinaturas que você tem acesso.

Aqui está um exemplo de dados da máquina Virtual que está sendo extraído para uma pasta de trabalho para fornecer uma visualização em grade de desempenho da CPU:

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Adicionando seções de parâmetro

Os parâmetros de pasta de trabalho permitem que você altere os valores na pasta de trabalho sem a necessidade de editar manualmente as seções de consulta ou texto.  Isso elimina a necessidade de precisar compreender a linguagem de consulta do Analytics subjacente e expandindo imensamente o público-alvo potencial de relatórios baseados na pasta de trabalho.

Os valores dos parâmetros são substituídos na consulta, texto ou outras seções de parâmetro, colocando o nome do parâmetro entre chaves, como ``{parameterName}``.  Nomes de parâmetro são limitados a regras semelhantes como identificadores de JavaScript, basicamente alfabéticos ou sublinhados, seguidos por caracteres alfanuméricos ou sublinhados. Por exemplo, **a1** é permitido, mas **1a** não é permitido.

Os parâmetros são lineares, começando na parte superior de uma pasta de trabalho e que fluem para baixo para etapas posteriores.  Parâmetros declarados posteriormente em uma pasta de trabalho podem substituir aqueles que foram declarados mais acima.  Isso também permite usar consultas para acessar os valores de parâmetros definidos mais acima de parâmetros.  Dentro de etapa de um parâmetro em si, parâmetros também são lineares, da esquerda para a direita, onde parâmetros à direita podem depender de um parâmetro declarado anteriormente na mesma etapa.
 
Há quatro tipos diferentes de parâmetros que têm suporte no momento:

  |         |          |
   | ---------------- |:-----|
   | **Texto**    | o usuário editará uma caixa de texto e, opcionalmente, você pode fornecer uma consulta para preencher o valor padrão. |
   | **Lista suspensa** | O usuário escolherá de um conjunto de valores. |
   | **Seletor de intervalo de tempo**| O usuário escolherá um conjunto predefinido de valores de intervalo de tempo ou escolherá um intervalo de tempo personalizado.|
   | **Seletor de recurso** | O usuário irá escolher dentre os recursos selecionados para a pasta de trabalho.|

### <a name="using-a-text-parameter"></a>Usando um parâmetro de texto

O valor que um usuário digita na caixa de texto é substituído diretamente na consulta, sem escape ou aspas. Se o valor que você precisa é uma cadeia de caracteres, a consulta deve ter aspas ao redor do parâmetro (como **'{parameter}'** ).

Isso permite que o valor em uma caixa de texto seja usado em qualquer lugar. Pode ser um nome de tabela, o nome da coluna, nome da função, operador, etc.

O tipo de parâmetro de texto tem uma configuração **Obter o valor padrão de consulta do Analytics**, que permite ao autor da pasta de trabalho usar uma consulta para preencher o valor padrão para essa caixa de texto.

Ao usar o valor padrão de uma consulta do Analytics, apenas o primeiro valor da primeira linha (linha 0, a coluna 0) é usado como o valor padrão. Portanto, é recomendável limitar sua consulta para retornar apenas uma linha e uma coluna. Quaisquer outros dados retornados pela consulta serão ignorados. 

Qualquer valor que a consulta retorna será substituído diretamente por nenhum escape ou aspas. Se a consulta não retornar nenhuma linha, o resultado do parâmetro é uma cadeia de caracteres vazia (se o parâmetro não for necessário) ou indefinido (se o parâmetro for necessário).

### <a name="using-a-dropdown"></a>Usando uma lista suspensa

O tipo de parâmetro de lista suspensa permite criar um controle de lista suspensa, permitindo a seleção de um ou vários valores.

A lista suspensa é preenchida por uma consulta do Analytics. Se a consulta retorna uma coluna, os valores nessa coluna são ambos os **valor** e o **rótulo** no controle suspenso. Se a consulta retorna duas colunas, a primeira coluna é o **valor**, e a segunda coluna é a **rótulo** mostrado na lista suspensa.  Se a consulta retorna três colunas, a terceira coluna é usada para indicar a seleção padrão nessa lista suspensa.  Esta coluna pode ser qualquer tipo, mas o mais simples é usar o bool ou tipos numéricos, onde 0 é falso e 1 é verdadeiro.

 Se a coluna for um tipo de cadeia de caracteres, cadeia de caracteres nula ou está vazia, é considerada falsa, e qualquer outro valor será considerado verdadeiro. Para listas suspensas de seleção única, o primeiro valor com um valor true é usado como a seleção padrão.  Para listas suspensas de seleção única, o primeiro valor com um valor true é usado como a seleção padrão. Os itens na lista suspensa são mostrados na mesma ordem em linhas retornadas pela consulta. 

Vamos examinar os parâmetros presentes no relatório de usuários ativos. Clique no símbolo de edição ao lado **TimeRange**.

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/009-time-range.png)

Isso iniciará o item de menu Editar Parâmetro:

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/010-time-range-edit.png)

A consulta usa um recurso da linguagem de consulta do analytics chamado um **datatable** que permite gerar uma tabela arbitrária, cheia de conteúdo, fora do ar! Por exemplo, a consulta do Analytics a seguir:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Gera o resultado:

![Controles de edição da seção Pastas de Trabalho do Application Insights](./media/usage-workbooks/011-data-table.png)

Um exemplo mais aplicável é usar uma lista suspensa para escolher um conjunto de países/regiões por nome:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

A consulta exibirá os resultados da seguinte maneira:

![Menu suspenso de país/região](./media/usage-workbooks/012-country-dropdown.png)

Os menus suspensos são ferramentas incrivelmente poderosas para personalizar e criar relatórios interativos.

### <a name="time-range-parameters"></a>Parâmetros de intervalo de tempo

Embora você possa fazer seu próprio parâmetro de intervalo de tempo personalizado por meio do tipo de parâmetro de lista suspensa, você também pode usar o tipo de parâmetro de intervalo de tempo de uso, se você não precisa do mesmo grau de flexibilidade. 

Tipos de parâmetro de intervalo de tempo têm intervalos padrão de 15 que vão de cinco minutos para os últimos 90 dias. Também é uma opção para permitir a seleção de intervalo de tempo personalizado, que permite que o operador de relatório escolha iniciar explicitamente e parar os valores para o intervalo de tempo.

### <a name="resource-picker"></a>Seletor de recursos

O tipo de parâmetro de seletor de recurso fornece a capacidade de definir o escopo de seu relatório para determinados tipos de recursos. Um exemplo de pasta de trabalho predefinido que aproveita o tipo de seletor de recurso é a pasta de trabalho do **Failure Insights**.

![Menu suspenso de país/região](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvando e compartilhando pastas de trabalho com sua equipe

As pastas de trabalho são salvas em um recurso do Application Insights, na seção **Meus Relatórios**, que é particular a você ou na seção **Relatórios Compartilhados**, que é acessível a todos com acesso ao recurso do Application Insights. Para exibir todas as pastas de trabalho no recurso, clique no botão **Abrir** na barra de ações.

Para compartilhar uma pasta de trabalho que atualmente está em **Meus Relatórios**:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja compartilhar
3. Clique em **Mover para Relatórios Compartilhados**.

Para compartilhar uma pasta de trabalho com um link ou por email, clique em **Compartilhar** na barra de ações. Tenha em mente que os destinatários do link precisam ter acesso a esse recurso no portal do Azure para exibir a pasta de trabalho. Para fazer edições, os destinatários precisam de, pelo menos, permissões de Colaborador ao recurso.

Para fixar um link em uma pasta de trabalho em um Painel do Azure:

1. Clique em **Abrir** na barra de ações
2. Clique no botão “...” ao lado da pasta de trabalho que você deseja fixar
3. Clique em **Fixar no painel**.

## <a name="contributing-workbook-templates"></a>Contribuir com modelos de pasta de trabalho

Você criou um modelo de pasta de trabalho incrível e quer compartilhá-lo com a comunidade? Para saber mais, visite nosso [repositório GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md).

## <a name="next-steps"></a>Próximos passos
- Para habilitar as experiências de uso, comece enviando [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) ou [exibições de página](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se você já envia eventos personalizados ou exibições de página, explore as ferramentas de uso para saber como os usuários utilizam o seu serviço.
    - [Usuários, Sessões, Eventos](../../azure-monitor/app/usage-segmentation.md)
    - [Funis](../../azure-monitor/app/usage-funnels.md)
    - [Retenção](../../azure-monitor/app/usage-retention.md)
    - [Fluxos de Usuário](../../azure-monitor/app/usage-flows.md)
    - [Adicionar contexto de usuário](../../azure-monitor/app/usage-send-user-context.md)