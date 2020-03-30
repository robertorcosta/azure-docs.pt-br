---
title: 'Selecionar colunas no conjunto de dados: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Select Columns in Dataset no Azure Machine Learning para escolher um subconjunto de colunas para usar em operações a jusante.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 714148815fa4ff543ecbf86ab21e8d51fe775543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153767"
---
# <a name="select-columns-in-dataset-module"></a>Selecione Colunas no módulo Dataset

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para escolher um subconjunto de colunas para usar em operações a jusante. O módulo não remove fisicamente as colunas do conjunto de dados de origem; em vez disso, cria um subconjunto de colunas, muito parecido com uma *exibição* ou *projeção*de banco de dados.

Este módulo é útil quando você precisa limitar as colunas disponíveis para uma operação a jusante ou se você quiser reduzir o tamanho do conjunto de dados removendo colunas desnecessárias.

As colunas no conjunto de dados são saídas na mesma ordem que nos dados originais, mesmo se você especificá-las em uma ordem diferente.

## <a name="how-to-use"></a>Como usar

Este módulo não tem parâmetros. Você usa o seletor de colunas para escolher as colunas para incluir ou excluir.

### <a name="choose-columns-by-name"></a>Escolha colunas pelo nome

Existem várias opções no módulo para escolher colunas pelo nome: 

+ Filtrar e pesquisar

    Clique na opção **POR NOME.**

    Se você tiver conectado um conjunto de dados que já está preenchido, uma lista de colunas disponíveis deve aparecer. Se nenhuma coluna aparecer, talvez seja necessário executar módulos upstream para visualizar a lista de colunas.

    Para filtrar a lista, digite a caixa de pesquisa. Por exemplo, se você `w` digitar a letra na caixa de pesquisa, a lista `w`será filtrada para mostrar os nomes da coluna que contêm a letra .

    Selecione colunas e clique no botão de seta direita para mover as colunas selecionadas para a lista no painel à direita.

    + Para selecionar uma gama contínua de nomes de colunas, **pressione Shift + Click**.
    + Para adicionar colunas individuais à seleção, **pressione Ctrl + Click**.

    Clique no botão marca de seleção para salvar e fechar.

+ Use nomes em combinação com outras regras

    Clique na opção **COM REGRAS.**
    
    Escolha uma regra, como mostrar colunas de um tipo de dados específico.

    Em seguida, clique em colunas individuais desse tipo por nome, para adicioná-las à lista de seleção.

+ Digite ou cole uma lista separada por comma de nomes de colunas

    Se o conjunto de dados for amplo, pode ser mais fácil usar índices ou listas geradas de nomes, em vez de selecionar colunas individualmente. Supondo que você tenha preparado a lista com antecedência:

    1. Clique na opção **COM REGRAS.** 
    2. Selecione **Nenhuma coluna,** selecione **Incluir**e clique em dentro da caixa de texto com o ponto de exclamação vermelho. 
    3. Cole ou digite uma lista separada por comma de nomes de coluna previamente validados. Não é possível salvar o módulo se alguma coluna tiver um nome inválido, por isso certifique-se de verificar os nomes com antecedência.
    
    Você também pode usar este método para especificar uma lista de colunas usando seus valores de índice. 

### <a name="choose-by-type"></a>Escolha por tipo

Se você usar a opção **COM REGRAS,** você pode aplicar várias condições nas seleções de colunas. Por exemplo, talvez seja necessário obter apenas colunas de recurso de um tipo de dados numérico.

A opção **START WITH** determina seu ponto de partida e é importante para entender os resultados. 

+ Se você selecionar a opção **TODAS AS COLUNAS,** todas as colunas serão adicionadas à lista. Em seguida, você deve usar a opção **Excluir** para *remover* colunas que atendam a certas condições. 

    Por exemplo, você pode começar com todas as colunas e, em seguida, remover colunas por nome ou por tipo.

+ Se você selecionar a opção **SEM COLUNAS,** a lista de colunas será iniciada vazia. Em seguida, você especifica as condições para *adicionar* colunas à lista. 

    Se você aplicar várias regras, cada condição é **aditiva**. Por exemplo, digamos que você comece sem colunas e, em seguida, adicione uma regra para obter todas as colunas numéricas. No conjunto de dados de preços do Automóvel, isso resulta em 16 colunas. Em seguida, **+** clique no sinal para adicionar uma nova condição e selecione **Incluir todos os recursos**. O conjunto de dados resultante inclui todas as colunas numéricas, além de todas as colunas de recursos, incluindo algumas colunas de recurso de string.

### <a name="choose-by-column-index"></a>Escolha por índice de coluna

O índice da coluna refere-se à ordem da coluna dentro do conjunto de dados original.

+ As colunas são numeradas sequencialmente a partir de 1.  
+ Para obter uma gama de colunas, use um hífen. 
+ Especificações abertas, `1-` como `-3` ou não são permitidas.
+ Valores de índice duplicados (ou nomes de coluna) não são permitidos e podem resultar em um erro.

Por exemplo, assumindo que seu conjunto de dados tenha pelo menos oito colunas, você pode colar em qualquer um dos seguintes exemplos para retornar várias colunas não contíguas: 

+ `8,1-4,6`
+ `1,3-8`
+ `1,3-6,4` 

o exemplo final não resulta em um erro; no entanto, ele retorna `4`uma única instância de coluna .



### <a name="change-order-of-columns"></a>Alterar a ordem das colunas

A opção **Permitir duplicatas e preservar a ordem da coluna na seleção** começa com uma lista vazia e adiciona colunas que você especifica pelo nome ou por índice. Ao contrário de outras opções, que sempre retornam colunas em sua "ordem natural", essa opção produz as colunas na ordem que você nomeia ou liste. 

Por exemplo, em um conjunto de dados com as colunas Col1, Col2, Col3 e Col4, você pode inverter a ordem das colunas e deixar de fora a coluna 2, especificando qualquer uma das seguintes listas:

+ `Col4, Col3, Col1`
+ `4,3,1`


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 