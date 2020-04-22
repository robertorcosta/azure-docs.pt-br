---
title: Parâmetros de texto das atos de texto do Azure Monitor
description: Simplifique relatórios complexos com livros de trabalho pré-construídos e personalizados. Saiba mais sobre os parâmetros de texto da carteira de trabalho.
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: c804cc8942a40e2f30c980636194daa82e0fb0e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687335"
---
# <a name="workbook-text-parameters"></a>Parâmetros de texto da carteira de trabalho

Os parâmetros da caixa de texto fornecem uma maneira simples de coletar a entrada de texto dos usuários da carteira de trabalho. Eles são usados quando não é prático usar um drop-down para coletar a entrada (por exemplo, um limiar arbitrário ou filtros genéricos). As câmaras de trabalho permitem que os autores obtenham o valor padrão da caixa de texto de uma consulta. Isso permite cenários interessantes como definir o limite padrão com base no p95 da métrica.

Um uso comum de caixas de texto é como variáveis internas usadas por outros controles da carteira de trabalho. Isso é feito aproveitando uma consulta para valores padrão e tornando o controle de entrada invisível no modo de leitura. Por exemplo, um usuário pode querer que um limite venha de uma fórmula (não um usuário) e, em seguida, use o limiar em consultas subseqüentes.

## <a name="creating-a-text-parameter"></a>Criando um parâmetro de texto
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`SlowRequestThreshold`
    2. Tipo de parâmetro:`Text`
    3. Necessário:`checked`
    4. Obter valor padrão da consulta:`unchecked`
5. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando a criação de um parâmetro de texto](./media/workbooks-text/text-create.png)

É assim que a caderneta de trabalho ficará no modo de leitura.

![Imagem mostrando um parâmetro de texto no modo de leitura](./media/workbooks-text/text-readmode.png)

## <a name="referencing-a-text-parameter"></a>Fazendo referência a um parâmetro de texto
1. Adicione um controle de consulta à carteira `Add query` de trabalho selecionando o link azul e selecione um recurso Dosacção de aplicativos.
2. Na caixa KQL, adicione este trecho:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= {SlowRequestThreshold}) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
3. Usando o parâmetro de texto com um valor de 500 juntamente com o controle de consulta, você executa efetivamente a consulta abaixo:
    ```kusto
    requests
    | summarize AllRequests = count(), SlowRequests = countif(duration >= 500) by name
    | extend SlowRequestPercent = 100.0 * SlowRequests / AllRequests
    | order by SlowRequests desc
    ```
4. Executar consulta para ver os resultados

    ![Imagem mostrando um parâmetro de texto referenciado em KQL](./media/workbooks-text/text-reference.png)

> [!NOTE]
> No exemplo acima, `{SlowRequestThreshold}` representa um valor inteiro. Se você estivesse consultando uma `{ComputerName}` string como você precisaria modificar sua consulta `"{ComputerName}"` kusto para adicionar cotações para que o campo de parâmetros para uma entrada de aceitação sem aspas.

## <a name="setting-default-values"></a>Definindo valores padrão
1. Comece com uma carteira de trabalho vazia no modo de edição.
2. Escolha _Adicionar parâmetros_ dos links dentro da caderneta de trabalho.
3. Clique no botão adicionar _parâmetro_ azul.
4. No novo painel de parâmetros que aparece digite:
    1. Nome do parâmetro:`SlowRequestThreshold`
    2. Tipo de parâmetro:`Text`
    3. Necessário:`checked`
    4. Obter valor padrão da consulta:`checked`
5. Na caixa KQL, adicione este trecho:
    ```kusto
    requests
    | summarize round(percentile(duration, 95), 2)
    ```
    Esta consulta define o valor padrão da caixa de texto para a duração do percentil 95 para todas as solicitações no aplicativo.
6. Executar consulta para ver o resultado
7. Escolha 'Salvar' na barra de ferramentas para criar o parâmetro.

    ![Imagem mostrando um parâmetro de texto com valor padrão do KQL](./media/workbooks-text/text-default-value.png)

> [!NOTE]
> Embora este exemplo consulte dados do Application Insights, a abordagem pode ser usada para qualquer fonte de dados baseada em log - Log Analytics, Azure Resource Graph, etc.

## <a name="next-steps"></a>Próximas etapas

* [Comece a](workbooks-visualizations.md) aprender mais sobre livros de trabalho muitas opções ricas de visualizações.
* [Controle](workbooks-access-control.md) e compartilhe o acesso aos recursos da sua carteira de trabalho.
