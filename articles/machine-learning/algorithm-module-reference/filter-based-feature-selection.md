---
title: 'Seleção de recursos baseada em filtro: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de seleção de recursos baseado em filtro no Azure Machine Learning para identificar os recursos em um conjunto de dados com maior potência preditiva.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: c009a98931240e92527035e51fdce3f1c92f5212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477588"
---
# <a name="filter-based-feature-selection"></a>Seleção de recursos baseada em filtro

Este artigo descreve como usar o módulo de seleção de recursos baseado em filtro no azure Machine Learning designer (visualização). Este módulo ajuda você a identificar as colunas no conjunto de dados de entrada que têm o maior poder preditivo. 

Em geral, a *seleção de recursos* refere-se ao processo de aplicação de testes estatísticos a entradas, dada uma saída especificada. O objetivo é determinar quais colunas são mais preditivas da saída. O módulo de seleção de recursos baseado em filtro fornece vários algoritmos de seleção de recursos para escolher. O módulo inclui métodos de correlação, como correlação de Pearson e valores qui-quadrados. 

Quando você usa o módulo Desseleção de recursos baseado em filtro, você fornece um conjunto de dados e identifica a coluna que contém o rótulo ou a variável dependente. Em seguida, você especifica um único método para usar na medição da importância do recurso.

O módulo produz um conjunto de dados que contém as melhores colunas de recursos, conforme classificado pela potência preditiva. Ele também produz os nomes dos recursos e suas pontuações da métrica selecionada.  

## <a name="what-filter-based-feature-selection-is"></a>Qual é a seleção de recursos baseada em filtros  

Este módulo para seleção de recursos é chamado de "baseado em filtro" porque você usa a métrica selecionada para encontrar atributos irrelevantes. Em seguida, filtraas colunas redundantes do seu modelo. Você escolhe uma única medida estatística que se adapte aos seus dados, e o módulo calcula uma pontuação para cada coluna de recurso. As colunas são devolvidas classificadas por suas pontuações de recursos. 

Escolhendo os recursos corretos, você melhorará muito a precisão e a eficiência da classificação. 

Você normalmente usa apenas as colunas com as melhores pontuações para construir seu modelo preditivo. Colunas com pontuações de seleção de recursos ruins podem ser deixadas no conjunto de dados e ignoradas quando você constrói um modelo.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Como escolher uma métrica de seleção de recursos

O módulo de seleção de recursos baseado em filtro fornece uma variedade de métricas para avaliar o valor das informações em cada coluna. Esta seção fornece uma descrição geral de cada métrica, e como ela é aplicada. Você pode encontrar requisitos adicionais para usar cada métrica nas [notas técnicas](#technical-notes) e nas [instruções](#how-to-configure-filter-based-feature-selection) para configurar cada módulo.

-   **Correlação de Pearson**  

    A estatística de correlação de Pearson, ou coeficiente de correlação de Pearson, também é conhecida em modelos estatísticos como o `r` valor. Para duas variáveis quaisquer, ele retorna um valor que indica a intensidade da correlação.

    O coeficiente de correlação de Pearson é calculado usando a covariância de duas variáveis e dividindo-se pelo produto de seus desvios padrão. Mudanças de escala nas duas variáveis não afetam o coeficiente.  

-   **Chi ao quadrado**  

    O teste qui-quadrado bidirecional é um método estatístico que mede o quão distante os valores esperados estão dos resultados reais. O método pressupõe que variáveis são aleatórias e desenha a partir de um exemplo adequado das variáveis independentes. A estatística qui-quadrada resultante indica o quão longe os resultados estão do resultado esperado (aleatório).  


> [!TIP]
> Se você precisar de uma opção diferente para o método de seleção de recursos personalizado, use o módulo [Execute R Script.](execute-r-script.md) 

## <a name="how-to-configure-filter-based-feature-selection"></a>Como configurar a seleção de recursos baseados em filtros

Você escolhe uma métrica estatística padrão. O módulo calcula a correlação entre um par de colunas: a coluna de rótulos e uma coluna de recursos.

1.  Adicione o módulo de seleção de recursos baseado em filtro ao seu pipeline. Você pode encontrá-lo na categoria **Seleção de Recursos** no designer.

2. Conecte um conjunto de dados de entrada que contenha pelo menos duas colunas que são recursos potenciais.  

    Para garantir que uma coluna seja analisada e uma pontuação de recurso seja gerada, use o módulo [Editar metadados](edit-metadata.md) para definir o atributo **IsFeature.** 

    > [!IMPORTANT]
    > Certifique-se de que as colunas que você está fornecendo como entrada são recursos potenciais. Por exemplo, uma coluna que contém um único valor não tem valor de informação.
    >
    > Se você sabe que algumas colunas fariam recursos ruins, você pode removê-los da seleção da coluna. Você também pode usar o módulo [Editar metadados](edit-metadata.md) para sinalizar como **Categórico**. 
3.  Para **o método de pontuação de**recursos, escolha um dos seguintes métodos estatísticos estabelecidos para usar no cálculo dos escores.  

    | Método              | Requisitos                             |
    | ------------------- | ---------------------------------------- |
    | Correlação de Pearson | O rótulo pode ser texto ou numérico. As características devem ser numéricas. |
    Chi ao quadrado| Rótulos e recursos podem ser texto ou numérico. Use este método para calcular a importância do recurso para duas colunas categóricas.|

    > [!TIP]
    > Se você alterar a métrica selecionada, todas as outras seleções serão redefinidas. Por isso, certifique-se de definir esta opção primeiro.
4.  Selecione a opção **Operar em colunas de recurso apenas** para gerar uma pontuação apenas para colunas que foram previamente marcadas como recursos. 

    Se você limpar essa opção, o módulo criará uma pontuação para qualquer coluna que de outra forma atenda aos critérios, até o número de colunas especificadas em **Número de recursos desejados**.  

5.  Para **a coluna Destino,** selecione **O seletor de coluna Iniciar** para escolher a coluna de rótulos pelo nome ou pelo seu índice. (Os índices são baseados em uma base.)  
    Uma coluna de rótulos é necessária para todos os métodos que envolvem correlação estatística. O módulo retorna um erro de tempo de projeto se você escolher nenhuma coluna de rótulo ou várias colunas de rótulo. 

6.  Em **Caso de número de recursos desejados,** digite o número de colunas de recurso que você deseja retornar como resultado:  

    - O número mínimo de recursos que você pode especificar é um, mas recomendamos que você aumente esse valor.  

    - Se o número de recursos desejados especificado for maior que o número de colunas no conjunto de dados, todos os recursos serão retornados. Até mesmo características com zero pontuação são devolvidas.  

    - Se você especificar menos colunas de resultado do que há colunas de recursos, os recursos serão classificados por pontuação descendente. Apenas os recursos superiores são devolvidos. 

7.  Envie o pipeline ou selecione o módulo Desseleção de recursos baseado em filtro e, em seguida, **selecione Executar selecionado**.


## <a name="results"></a>Resultados

Após a conclusão do processamento:

+ Para ver uma lista completa das colunas de recursos analisadas e suas pontuações, clique com o botão direito do mouse no módulo e selecione **Visualizar**.  

+ Para exibir o conjunto de dados com base nos critérios de seleção de recursos, clique com o botão direito do mouse no módulo e selecione **Visualizar**. 

Se o conjunto de dados contiver menos colunas do que você esperava, verifique as configurações do módulo. Verifique também os tipos de dados das colunas fornecidas como entrada. Por exemplo, se você definir **número de recursos desejados** como 1, o conjunto de dados de saída contém apenas duas colunas: a coluna de rótulos e a coluna de recursos mais bem ranqueada.


##  <a name="technical-notes"></a>Observações técnicas  

### <a name="implementation-details"></a>Detalhes de implementação

Se você usar a correlação Pearson em um recurso numérico e um rótulo categórico, a pontuação do recurso será calculada da seguinte forma:  

1.  Para cada nível na coluna categórica, calcule a média condicional da coluna numérica.  

2.  Correlacionar a coluna de meios condicionais com a coluna numérica.  

### <a name="requirements"></a>Requisitos  

-   Uma pontuação de seleção de recursos não pode ser gerada para qualquer coluna designada como uma coluna **Rótulo** ou **Pontuação.**  

-   Se você tentar usar um método de pontuação com uma coluna de um tipo de dados que o método não suporta, o módulo levantará um erro. Ou, uma pontuação zero será atribuída à coluna.  

-   Se uma coluna contém valores lógicos (verdadeiros/falsos), eles são processados como `True = 1` e `False = 0`.  

-   Uma coluna não pode ser um recurso se tiver sido designada como **um Rótulo** ou uma **Pontuação**.  

### <a name="how-missing-values-are-handled"></a>Como os valores perdidos são tratados  

-   Você não pode especificar como uma coluna de destino (rótulo) qualquer coluna que tenha todos os valores ausentes.  

-   Se uma coluna contiver valores ausentes, o módulo os ignorará quando estiver calculando a pontuação da coluna.  

-   Se uma coluna designada como uma coluna de recurso tiver todos os valores perdidos, o módulo atribuirá uma pontuação zero.   


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 

