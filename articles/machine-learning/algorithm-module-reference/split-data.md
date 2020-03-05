---
title: 'Dividir dados: referência de módulo'
titleSuffix: Azure Machine Learning
description: Saiba como usar o módulo dividir dados em Azure Machine Learning para dividir um conjunto de dado em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d889cd3325784f564d03e5d75dde1ec760c66804
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268534"
---
# <a name="split-data-module"></a>Módulo dividir dados

Este artigo descreve um módulo no designer de Azure Machine Learning (versão prévia).

Use este módulo para dividir um conjunto de módulos em dois conjuntos distintos.

Esse módulo é particularmente útil quando você precisa separar dados em conjuntos de treinamento e teste. Você pode personalizar a maneira como os dados são divididos também. Algumas opções dão suporte à randomização de dados; outras são adaptadas para um determinado tipo de dados ou tipo de modelo.

## <a name="how-to-configure"></a>Como configurar

> [!TIP]
> Antes de escolher o modo de divisão, Leia todas as opções para determinar o tipo de divisão de que você precisa.
> Se você alterar o modo de divisão, todas as outras opções poderão ser redefinidas.

1. Adicione o módulo **dividir dados** ao seu pipeline no designer. Você pode encontrar esse módulo em **transformação de dados**, na categoria **exemplo e dividir** .

2. **Modo de divisão**: escolha um dos seguintes modos, dependendo do tipo de dados que você tem e como deseja dividi-lo. Cada modo de divisão tem opções diferentes. Clique nos tópicos a seguir para obter instruções e exemplos detalhados. 

    - **Dividir linhas**: Use essa opção se desejar apenas dividir os dados em duas partes. Você pode especificar a porcentagem de dados a serem colocados em cada divisão, mas por padrão, os dados são divididos 50-50.

        Você também pode tornar aleatória a seleção de linhas em cada grupo e usar a amostragem de sobreratificação. Em amostragem de sobreratificação, você deve selecionar uma única coluna de dados para a qual você deseja que os valores sejam distribuídos igualmente entre os dois conjuntos de dado de resultado.  

    - **Divisão de expressão regular**  Escolha esta opção quando desejar dividir o conjunto de um testando uma única coluna para um valor.

        Por exemplo, se você estiver analisando sentimentos, poderá verificar a presença de um nome de produto específico em um campo de texto e, em seguida, dividir o conjunto de dado em linhas com o nome do produto de destino e aqueles sem.

    - **Divisão de expressão relativa**: Use essa opção sempre que desejar aplicar uma condição a uma coluna de número. O número pode ser um campo de data/hora, uma coluna que contém valores de idade ou moeda, ou até mesmo uma porcentagem. Por exemplo, talvez você queira dividir seu conjunto de dados dependendo do custo dos itens, agrupar pessoas por intervalos de idade ou separar dados por uma data de calendário.

### <a name="split-rows"></a>Linhas de divisão

1.  Adicione o módulo [dividir dados](./split-data.md) ao seu pipeline no designer, e conecte o DataSet que você deseja dividir.
  
2.  Para o **modo de divisão**, escolha **dividir linhas**. 

3.  **Fração de linhas no primeiro conjunto de resultados de saída**. Use esta opção para determinar quantas linhas vão para a primeira saída (à esquerda). Todas as outras linhas vão para a segunda saída (à direita).

    A taxa representa a porcentagem de linhas enviadas para o primeiro conjunto de resultados de saída, portanto, você deve digitar um número decimal entre 0 e 1.
     
     Por exemplo, se você digitar 0,75 como o valor, o conjunto de dados deve ser dividido usando a razão 75:25, com 75% das linhas enviadas para o primeiro conjunto de saída, e 25% enviadas para o segundo conjunto de saída.
  
4. Selecione a opção **divisão aleatória** se desejar tornar aleatória a seleção de dados nos dois grupos. Essa é a opção preferida ao criar conjuntos de testes de treinamento e de teste.

5.  **Semente aleatória**: digite um valor inteiro não negativo para inicializar a sequência pseudoaleatória de instâncias a serem usadas. Essa propagação padrão é usada em todos os módulos que geram números aleatórios. 

     A especificação de uma semente torna os resultados geralmente reproduzíveis. Se você precisar repetir os resultados de uma operação de divisão, deverá especificar uma semente para o gerador de número aleatório. Caso contrário, a propagação aleatória é definida por padrão como 0, o que significa que o valor de propagação inicial é obtido a partir do relógio do sistema. Como resultado, a distribuição de dados pode ser ligeiramente diferente cada vez que você executar uma divisão. 

6. **Divisão de desratificação**: defina essa opção como **true** para garantir que os dois conjuntos de resultados de saída contenham uma amostra representativa dos valores na coluna *Strata* ou na *coluna de chave estratificação*. 

    Com a amostragem de sobreratificação, os dados são divididos de forma que cada conjunto de dado de saída Obtém aproximadamente a mesma porcentagem de cada valor de destino. Por exemplo, talvez você queira garantir que seus conjuntos de treinamento e teste sejam aproximadamente equilibrados em relação ao resultado, ou em relação a alguma outra coluna, como sexo.

7. Execute o pipeline.


## <a name="regular-expression-split"></a>Divisão de expressão regular

1.  Adicione o módulo [dividir dados](./split-data.md) ao seu pipeline e conecte-o como entrada para o DataSet que você deseja dividir.  
  
2.  Para o **modo de divisão**, selecione divisão de **expressão regular**.

3. Na caixa **expressão regular** , digite uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe de expressão regular do Python.


4. Execute o pipeline.

    Com base na expressão regular que você fornece, o conjunto de registros é dividido em dois conjuntos de linhas: linhas com valores que correspondem à expressão e todas as linhas restantes. 

Os exemplos a seguir demonstram como dividir um conjunto de um DataSet usando a opção de **expressão regular** . 

### <a name="single-whole-word"></a>Única palavra inteira 

Este exemplo coloca no primeiro conjunto de dados todas as linhas que contêm o `Gryphon` de texto na coluna `Text`e coloca outras linhas na segunda saída de **Split data**:

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Subcadeia de caracteres

Este exemplo procura a cadeia de caracteres especificada em qualquer posição dentro da segunda coluna do conjunto de valores, indicado aqui pelo valor de índice de 1. A correspondência diferencia maiúsculas de minúsculas.

```text
(\1) ^[a-f]
```

O primeiro conjunto de resultados contém todas as linhas em que a coluna de índice começa com um destes caracteres: `a`, `b`, `c`, `d`, `e`, `f`. Todas as outras linhas são direcionadas para a segunda saída.

## <a name="relative-expression-split"></a>Divisão de expressão relativa.

1. Adicione o módulo [dividir dados](./split-data.md) ao seu pipeline e conecte-o como entrada para o DataSet que você deseja dividir.
  
2. Para o **modo de divisão**, selecione a divisão de **expressão relativa**.
  
3. Na caixa de texto **expressão relacional** , digite uma expressão que executa uma operação de comparação em uma única coluna:

   Para a **coluna numérica**:
   - A coluna contém números de qualquer tipo de dados numéricos, incluindo tipos de dados de data e hora.
   - A expressão pode fazer referência a um máximo de um nome de coluna.
   - Use o caractere de e comercial `&` para a operação AND. Use o `|` de caractere de pipe para a operação ou.
   - Há suporte para os seguintes operadores: `<`, `>`, `<=`, `>=`, `==`, `!=`.
   - Não é possível agrupar operações usando `(` e `)`.
   
   Para a **coluna de cadeia de caracteres**:
   - Há suporte para os seguintes operadores: `==`, `!=`.

4. Execute o pipeline.

    A expressão divide o conjunto de um em dois conjuntos de linhas: linhas com valores que atendem à condição e todas as linhas restantes.

Os exemplos a seguir demonstram como dividir um DataSet usando a opção de **expressão relativa** no módulo **dividir dados** :  

### <a name="using-calendar-year"></a>Usando o ano civil

Um cenário comum é dividir um conjunto de dados por anos. A expressão a seguir seleciona todas as linhas nas quais os valores na coluna `Year` são maiores que `2010`.

```text
\"Year" > 2010
```

A expressão de data deve considerar todas as partes de data incluídas na coluna de dados e o formato das datas na coluna de dados deve ser consistente. 

Por exemplo, em uma coluna de data usando o formato `mmddyyyy`, a expressão deve ser algo assim:

```text
\"Date" > 1/1/2010
```

### <a name="using-column-indices"></a>Usando índices de coluna

A expressão a seguir demonstra como você pode usar o índice da coluna para selecionar todas as linhas na primeira coluna do conjunto de dados que contém valores menores ou iguais a 30, mas não iguais a 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

Consulte o [conjunto de módulos disponíveis](module-reference.md) para Azure Machine Learning. 
