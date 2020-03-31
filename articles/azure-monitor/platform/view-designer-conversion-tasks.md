---
title: Azure Monitor visualiza designer para planilhas conversão de tarefas comuns
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658737"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Exibir designer para tarefas comuns de conversão de livros de trabalho
[O View designer](view-designer.md) é um recurso do Azure Monitor que permite criar visualizações personalizadas para ajudá-lo a visualizar dados em seu espaço de trabalho do Log Analytics, com gráficos, listas e cronogramas. Eles estão sendo eliminados e substituídos por livros de trabalho que fornecem funcionalidade adicional. Este artigo detalha tarefas comuns na conversão de visualizações em livros de trabalho.


## <a name="quickstart-with-preset-view-designer-templates"></a>Início rápido com modelos de designer de visualização predefinidos

As workbooks nos espaços de trabalho do Log Analytics já possuem modelos feitos para combinar com algumas das visualizações do designer de visualização. Na categoria **Guias** do Designer de exibição, selecione **Exibir guia de transição de designer** para saber sobre suas opções ou selecionar um dos modelos predefinidos.

![Modelos de exemplo](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Ativando o filtro de intervalo de tempo
O designer de visualização tem um filtro de intervalo de tempo padrão incorporado, no entanto, nas regras de trabalho essa configuração não está ativada por padrão. As atos de trabalho permitem que os usuários criem seus próprios filtros de intervalo de tempo que podem ser mais aplicáveis aos seus registros de dados. As etapas para gerar o filtro estão listadas abaixo:

Selecione a opção **Adicionar parâmetros.** O **estilo padrão** é definido como *Pills*.

![Adicionar Param](media/view-designer-conversion-tasks/add-param.png)

 Selecione o botão **Adicionar parâmetro.**

![Adicione o parâmetro](media/view-designer-conversion-tasks/add-parameter.png)

No menu barra lateral, na caixa de texto **nome Parâmetro,** digite *TimeRange*. Definir **o tipo de parâmetro** como *seletor de intervalo de tempo*. Selecione a caixa de seleção **Necessária?**

![Menu de parâmetros](media/view-designer-conversion-tasks/parameter-menu.png)

Guarde o parâmetro no canto superior esquerdo do menu da barra lateral. Você pode deixar a queda como *não definida* por padrão ou selecionar um valor **de TimeRange** padrão, como *24 horas*. Selecione **Edição feita**.

Os parâmetros podem ser usados {} em consultas adicionando chaves em torno do nome do parâmetro. Mais detalhes sobre parâmetros podem ser encontrados na documentação da [Caixa de Trabalho sobre parâmetros](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md).

## <a name="updating-queries-with-the-timerange-parameter"></a>Atualizando consultas com o parâmetro TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Opção 1: Selecione timerange da parada do intervalo de tempo

![Parâmetro de tempo](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Opção 2: Atualize suas consultas de log

Na sua consulta adicione `| where TimeGenerated {TimeRange}` a linha: como no exemplo a seguir:

Consulta original
```KQL
search * 
| summarize count() by Type
```

Consulta atualizada
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Incluindo uma lista
A maioria das visualizações do designer de visualizações incluem uma lista, e você pode reproduzir essa lista padrão em uma carteira de trabalho.

![Lista de azulejos](media/view-designer-conversion-tasks/tile-list.png)

Adicione uma visualização clicando em **Adicionar consulta** das opções de celular.

![Adicionar Param](media/view-designer-conversion-tasks/add-param.png)

O designer de visualização emprega uma consulta padrão que corresponde à sintaxe do exemplo Original. Isso pode ser atualizado alterando a consulta para o formulário atualizado como no exemplo a seguir:

Consulta original
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Consulta atualizada
```KQL
search * 
| summarize Count = count() by Type
```

Isso irá gerar uma lista semelhante à seguinte:

![Exemplo de lista](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Habilitando faíscas
Um recurso comum para grades é adicionar faíscas para resumir vários padrões de dados ao longo do tempo. O designer de visualização oferece o recurso **Habilitar sparklines** para todas as listas, assim como as cadernetas de trabalho. Para incluir linhas de ignição em seus dados que correspondam ao designer de visualização, junte os dados com sua consulta original como no exemplo a seguir:

Consulta original
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Consulta atualizada
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Selecione **Configurações da coluna**.
![Configurações da coluna](media/view-designer-conversion-tasks/column-settings.png)

Atualize a **parada do renderizador de coluna** para ser uma *área de Faísca*.
![Minigráficos](media/view-designer-conversion-tasks/sparkline.png)

Salve as configurações e execute a consulta novamente para atualizar sua tabela para incluir uma linha de ignição.

A grade resultante será semelhante ![ao seguinte: Exemplo sparkline](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Configurações avançadas de células
Para espelhar o designer de visualização, você pode executar tarefas como alterar o tamanho das células da carteira de trabalho ou adicionar pinos e links externos aos logs.

Para acessar **configurações avançadas,** selecione o ícone de engrenagem na parte inferior de cada célula.

![Configurações avançadas](media/view-designer-conversion-tasks/advanced-settings.png)

Isso exibirá um menu com várias opções:

![Configurações avançadas configurações](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Para adicionar um pino e um link para uma consulta externa, selecione as caixas de seleção correspondentes. Para adicionar um título à sua célula, digite o título desejado na seção **de título gráfico.**

Por padrão, qualquer célula de livros de trabalho está configurada para assumir toda a largura da página, mas você pode ajustar isso escalando a célula para baixo na guia **Estilo** do menu **Configurações Avançadas**

![Estilo de configurações avançadas](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Parâmetros adicionais
Selecione **Adicionar parâmetro** para criar um novo parâmetro em sua carteira de trabalho. 

Para selecionar uma assinatura, *digite A assinatura* no campo **nome parâmetro** no menu lateral e selecione *Seletor* de assinaturas no tipo de parada do **tipo Parâmetro**

![Menu de Assinatura](media/view-designer-conversion-tasks/subscription-filter.png)

Para selecionar um recurso, digite *Recurso* no campo **nome Parâmetro** no menu lateral e selecione *Seletor* de recursos no tipo de parada do **parâmetro.**

![Menu de recursos](media/view-designer-conversion-tasks/resource-filter.png)

Isso inserirá as gotas para permitir que você acesse suas várias assinaturas e recursos.

![Dropdown de recursos de assinatura](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Próximas etapas
- [Conversões de azulejos](view-designer-conversion-tiles.md)
