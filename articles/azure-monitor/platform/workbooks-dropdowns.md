---
title: Azure Monitor Workbook derruba parâmetros
description: Simplifique relatórios complexos com livros de trabalho pré-construídos e personalizados contendo parâmetros de suspensão
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: f3220a363025d80fd7636dbfc3af3d2d9d7bc040
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658272"
---
# <a name="workbook-drop-down-parameters"></a>Parâmetros de queda da carteira de trabalho

As quedas permitem que o usuário colete um ou mais valores de entrada de um conjunto conhecido (por exemplo, selecione uma das solicitações do seu aplicativo). As quedas fornecem uma maneira fácil de coletar entradas arbitrárias dos usuários. As quedas são especialmente úteis para ativar a filtragem em seus relatórios interativos. 

A maneira mais fácil de especificar uma lista de parada é fornecendo uma lista estática na configuração do parâmetro. Uma maneira mais interessante é obter a lista dinamicamente através de uma consulta KQL. As configurações de parâmetrotambém permitem especificar se é single ou multi-select, e se é multi-select, como o conjunto de resultados deve ser formatado (delimitador, citação, etc.).

## <a name="creating-a-static-drop-down-parameter"></a>Criando um parâmetro estático para baixo

1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`Environment`
    2. Tipo de parâmetro:`Drop down`
    3. Necessário:`checked`
    4. Permitir: `multiple selection``unchecked`
    5. Obter dados de:`JSON`
5. No bloco de texto de entrada JSON, insira este trecho de json:
    ```json
    [
        { "value":"dev", "label":"Development" },
        { "value":"ppe", "label":"Pre-production" },
        { "value":"prod", "label":"Production", "selected":true }
    ]
    ```
6. Aperte o `Update` botão azul.
7. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.
8. O parâmetro Ambiente será um drop-down com os três valores.

    ![Imagem mostrando a criação de um afogamento estático](./media/workbook-dropdowns/dropdown-create.png)

## <a name="creating-a-static-dropdown-with-groups-of-items"></a>Criando uma gota estática com grupos de itens
Se o resultado da consulta/json contiver um campo "grupo", a queda exibirá grupos de valores. Siga a amostra acima, mas use o seguinte json em vez disso:
```json
[
    { "value":"dev", "label":"Development", "group":"Development" },
    { "value":"dev-cloud", "label":"Development (Cloud)", "group":"Development" },
    { "value":"ppe", "label":"Pre-production", "group":"Test" },
    { "value":"ppe-test", "label":"Pre-production (Test)", "group":"Test" },
    { "value":"prod1", "label":"Prod 1", "selected":true, "group":"Production" },
    { "value":"prod2", "label":"Prod 2", "group":"Production" }
]
```
    ![Image showing an example of a grouped dropdown](./media/workbook-dropdowns/grouped-dropDown.png)


## <a name="creating-a-dynamic-drop-down-parameter"></a>Criando um parâmetro dinâmico de queda
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`RequestName`
    2. Tipo de parâmetro:`Drop down`
    3. Necessário:`checked`
    4. Permitir: `multiple selection``unchecked`
    5. Obter dados de:`Query`
5. No bloco de texto de entrada JSON, insira este trecho de json:

    ```kusto
        requests
        | summarize by name
        | order by name asc
    ```
1. Aperte o `Run Query` botão azul.
2. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.
3. O parâmetro RequestName será um drop-down dos nomes de todas as solicitações no aplicativo.

    ![Imagem mostrando a criação de uma gota dinâmica](./media/workbook-dropdowns/dropdown-dynamic.png)

## <a name="referencing-drop-down-parameter"></a>Referência sem parâmetro
### <a name="in-kql"></a>Em KQL
1. Adicione um controle de consulta à caderneta de trabalho e selecione um recurso Application Insights.
2. No editor KQL, digite este trecho

    ```kusto
        requests
        | where name == '{RequestName}'
        | summarize Requests = count() by bin(timestamp, 1h)

    ```
3. Isso se expande no tempo de avaliação de consulta para:

    ```kusto
        requests
        | where name == 'GET Home/Index'
        | summarize Requests = count() by bin(timestamp, 1h)
    ```

4. Executar consulta para ver os resultados. Opcionalmente, torne-o como um gráfico.

    ![Imagem mostrando um drop-down referenciado no KQL](./media/workbook-dropdowns/dropdown-reference.png)


## <a name="parameter-value-label-selection-and-group"></a>Valor do parâmetro, rótulo, seleção e grupo
A consulta usada no parâmetro suspenso dinâmico acima apenas retorna uma lista de valores que são renderizados fielmente no drop-down. Mas e se você quisesse um nome de exibição diferente, ou um desses para ser selecionado? Os parâmetros de queda permitem isso através das colunas de valor, rótulo, seleção e grupo.

A amostra abaixo mostra como obter uma lista de dependências do Application Insights cujos nomes de exibição são estilizados com um emoji, tem o primeiro selecionado e é agrupado por nomes de operação.

```kusto
dependencies
| summarize by operation_Name, name
| where name !contains ('.')
| order by name asc
| serialize Rank = row_number()
| project value = name, label = strcat('🌐 ', name), selected = iff(Rank == 1, true, false), group = operation_Name
```
    ![Image showing a drop-down parameter using value, label, selection and group options](./media/workbook-dropdowns/dropdown-more-options.png)


## <a name="drop-down-parameter-options"></a>Opção de parâmetro de queda
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{DependencyName}` | O valor selecionado | OBTER fabrikamaccount |
| `{DependencyName:label}` | O rótulo selecionado | 🌐 OBTER fabrikamaccount |
| `{DependencyName:value}` | O valor selecionado | OBTER fabrikamaccount |

## <a name="multiple-selection"></a>Seleção múltipla
Os exemplos até agora definem explicitamente o parâmetro para selecionar apenas um valor na queda. Os parâmetros `multiple selection` de queda também suportam - `Allow multiple selection` habilitar isso é tão simples quanto verificar a opção. 

O usuário também tem a opção de especificar `delimiter` o `quote with` formato do conjunto de resultados através das configurações. O padrão apenas retorna os valores como uma coleção nesta forma: 'a', 'b', 'c'. Eles também têm a opção de limitar o número de seleções.

O KQL que faz referência ao parâmetro precisará ser trocado para trabalhar com o formato do resultado. A maneira mais comum de `in` habilitá-lo é através do operador.

```kusto
dependencies
| where name in ({DependencyName})
| summarize Requests = count() by bin(timestamp, 1h), name
```

Aqui está um exemplo para a lista de paradas multi-selecionadas no trabalho:

![Imagem mostrando um parâmetro de baixa multi-selecionado](./media/workbook-dropdowns/dropdown-multiselect.png)

## <a name="next-steps"></a>Próximas etapas

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
