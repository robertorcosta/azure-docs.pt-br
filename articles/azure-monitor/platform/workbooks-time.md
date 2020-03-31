---
title: Parâmetros de tempo das regras de trabalho do Azure Monitor
description: Simplifique a emissão de relatórios complexos com pastas de trabalho parametrizadas predefinidas e personalizadas
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 380b8a7ce286ab06b6935bf63bf3a0e82f371c2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658006"
---
# <a name="workbook-time-parameters"></a>Parâmetros de tempo da carteira de trabalho

Os parâmetros de tempo permitem que os usuários definam o contexto de tempo da análise e são usados por quase todos os relatórios. É relativamente simples de configurar e usar - permitindo que os autores especifiquem os intervalos de tempo a serem exibidos na lista de itens de ida e para baixo, incluindo a opção de intervalos de tempo personalizados. 

## <a name="creating-a-time-parameter"></a>Criando um parâmetro de tempo
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`TimeRange`
    2. Tipo de parâmetro:`Time range picker`
    3. Necessário:`checked`
    4. Faixas de tempo disponíveis: Última hora, Últimas 12 horas, Últimas 24 horas, Últimas 48 horas, Últimos 3 dias, Últimos 7 dias e Permitir seleção personalizada do intervalo de tempo
5. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de intervalo de tempo](./media/workbooks-time/time-settings.png)

É assim que a caderneta de trabalho ficará no modo de leitura.

![Imagem mostrando um parâmetro de intervalo de tempo no modo de leitura](./media/workbooks-time/parameters-time.png)

## <a name="referencing-a-time-parameter"></a>Referenciando um parâmetro de tempo
### <a name="via-bindings"></a>Via Vinculações
1. Adicione um controle de consulta à caderneta de trabalho e selecione um recurso Application Insights.
2. A maioria dos controles da carteira de trabalho suporta um seletor de escopo de intervalo de _tempo._ Abra a queda do intervalo `{TimeRange}` de _tempo_ e selecione o grupo de parâmetros no tempo de range na parte inferior.
3. Isso liga o parâmetro de intervalo de tempo ao intervalo de tempo do gráfico. O escopo de tempo da consulta de amostra agora é de 24 horas.
4. Executar consulta para ver os resultados

    ![Imagem mostrando um parâmetro de intervalo de tempo referenciado através de vinculações](./media/workbooks-time/time-binding.png)

### <a name="in-kql"></a>Em KQL
1. Adicione um controle de consulta à caderneta de trabalho e selecione um recurso Application Insights.
2. No KQL, digite um filtro de escopo de tempo usando o parâmetro:`| where timestamp {TimeRange}`
3. Isso se expande no `| where timestamp > ago(1d)`tempo de avaliação de consulta para , que é o valor do intervalo de tempo do parâmetro.
4. Executar consulta para ver os resultados

    ![Imagem mostrando um intervalo de tempo referenciado em KQL](./media/workbooks-time/time-in-code.png)

### <a name="in-text"></a>Em Texto 
1. Adicione um controle de texto à caderneta de trabalho.
2. Na marcação, digite`The chosen time range is {TimeRange:label}`
3. Escolha _a edição feita_
4. O controle de texto mostrará texto: _O intervalo de tempo escolhido é de 24 horas_

## <a name="time-parameter-options"></a>Opções de parâmetros de tempo
| Parâmetro | Explicação | Exemplo |
| ------------- |:-------------|:-------------|
| `{TimeRange}` | Etiqueta de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:label}` | Etiqueta de intervalo de tempo | Últimas 24 horas |
| `{TimeRange:value}` | Valor do intervalo de tempo | > atrás(1d) |
| `{TimeRange:query}` | Consulta de intervalo de tempo | > atrás(1d) |
| `{TimeRange:start}` | Tempo de início do intervalo de tempo | 20/03/2019 16:18 |
| `{TimeRange:end}` | Tempo final do intervalo de tempo | 21/03/2019 16:18 |
| `{TimeRange:grain}` | Grão de faixa de tempo | 30 m |


### <a name="using-parameter-options-in-a-query"></a>Usando opções de parâmetros em uma consulta
```kusto
requests
| make-series Requests = count() default = 0 on timestamp from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain}
```

## <a name="next-steps"></a>Próximas etapas

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
