---
title: 'Dados divididos: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Split Data no Azure Machine Learning para dividir um conjunto de dados em dois conjuntos distintos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455886"
---
# <a name="split-data-module"></a>Módulo de dados divididos

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use o módulo Dados Divididos para dividir um conjunto de dados em dois conjuntos distintos.

Este módulo é útil quando você precisa separar dados em conjuntos de treinamento e testes. Você também pode personalizar a forma como esses dados são divididos. Algumas opções suportam a randomização de dados. Outros são adaptados para um determinado tipo de dados ou tipo de modelo.

## <a name="configure-the-module"></a>Configure o módulo

> [!TIP]
> Antes de escolher o modo de divisão, leia todas as opções para determinar o tipo de divisão que você precisa.
> Se você alterar o modo de divisão, todas as outras opções podem ser redefinidas.

1. Adicione o módulo **Dados Divididos** ao seu pipeline no designer. Você pode encontrar este módulo em **Transformação de Dados,** na categoria **Amostra e Divisão.**

1. **Modo de divisão**: Escolha um dos seguintes modos, dependendo do tipo de dados que você tem e como você deseja dividi-los. Cada modo de divisão tem opções diferentes.

   - **Linhas divididas**: Use esta opção se quiser apenas dividir os dados em duas partes. Você pode especificar a porcentagem de dados a serem colocados em cada divisão. Por padrão, os dados são divididos 50/50.

     Você também pode randomizar a seleção de linhas em cada grupo e usar amostragem estratificada. Na amostragem estratificada, você deve selecionar uma única coluna de dados para a qual deseja que os valores sejam rateados igualmente entre os dois conjuntos de dados de resultado.  

   - **Divisão de expressão regular**: Escolha esta opção quando quiser dividir seu conjunto de dados testando uma única coluna por um valor.

     Por exemplo, se você estiver analisando o sentimento, você pode verificar a presença de um nome de produto específico em um campo de texto. Em seguida, você pode dividir o conjunto de dados em linhas com o nome e as linhas do produto de destino sem o nome do produto de destino.

   - **Divisão de expressão relativa**: Use esta opção sempre que quiser aplicar uma condição a uma coluna numérica. O número pode ser um campo de data/hora, uma coluna que contém valores de idade ou dólar, ou até mesmo uma porcentagem. Por exemplo, você pode querer dividir seu conjunto de dados com base no custo dos itens, agrupar pessoas por faixas etárias ou dados separados por uma data de calendário.

### <a name="split-rows"></a>Linhas divididas

1. Adicione o módulo [Dados Divididos](./split-data.md) ao seu pipeline no designer e conecte o conjunto de dados que você deseja dividir.
  
1. Para **o modo Desmemamento,** **selecione Linhas divididas**. 

1. **Fração de linhas no primeiro conjunto de dados de saída**: Use esta opção para determinar quantas linhas irão para a primeira saída (lado esquerdo). Todas as outras linhas irão para a segunda saída (lado direito).

   A razão representa a porcentagem de linhas enviadas para o primeiro conjunto de dados de saída, então você deve inserir um número decimal entre 0 e 1.
     
   Por exemplo, se você inserir **0,75** como valor, o conjunto de dados será dividido em 75/25. Nesta divisão, 75% das linhas serão enviadas para o primeiro conjunto de dados de saída. Os 25% restantes serão enviados para o segundo conjunto de dados de saída.
  
1. Selecione a opção **divisão randomizada** se quiser randomizar a seleção de dados nos dois grupos. Esta é a opção preferida quando você está criando conjuntos de dados de treinamento e teste.

1. **Sementes aleatórias**: Digite um valor inteiro não negativo para iniciar a seqüência pseudoaleatória de instâncias a serem usadas. Essa propagação padrão é usada em todos os módulos que geram números aleatórios. 

   Especificar uma semente torna os resultados reprodutíveis. Se você precisar repetir os resultados de uma operação dividida, você deve especificar uma semente para o gerador de números aleatórios. Caso contrário, a semente aleatória é definida por padrão como **0**, o que significa que o valor inicial da semente é obtido a partir do relógio do sistema. Como resultado, a distribuição de dados pode ser ligeiramente diferente cada vez que você executar uma divisão. 

1. **Divisão estratificada**: Defina esta opção como **True** para garantir que os dois conjuntos de dados de saída contenham uma amostra representativa dos valores na *coluna estratos* ou *na coluna de tecla estratificação*. 

   Com a amostragem estratificada, os dados são divididos de forma que cada conjunto de dados de saída receba aproximadamente a mesma porcentagem de cada valor-alvo. Por exemplo, você pode querer garantir que seus conjuntos de treinamento e testes sejam aproximadamente equilibrados em relação ao resultado ou a alguma outra coluna (como sexo).

1. Envie o oleoduto.


## <a name="select-a-regular-expression"></a>Selecione uma expressão regular

1. Adicione o módulo [Dados Divididos](./split-data.md) ao seu pipeline e conecte-o como entrada ao conjunto de dados que você deseja dividir.  
  
1. Para **o modo Desmemamento,** selecione **Divisão de expressão regular**.

1. Na **caixa de expressão Regular,** digite uma expressão regular válida. 
  
   A expressão regular deve seguir a sintaxe Python para expressões regulares.

1. Envie o oleoduto.

   Com base na expressão regular que você fornece, o conjunto de dados é dividido em dois conjuntos de linhas: linhas com valores que correspondem à expressão e todas as linhas restantes. 

Os exemplos a seguir demonstram como dividir um conjunto de dados usando a opção **de expressão Regular.** 

### <a name="single-whole-word"></a>Única palavra inteira 

Este exemplo coloca no primeiro conjunto de dados `Gryphon` todas `Text`as linhas que contêm o texto na coluna . Ele coloca outras linhas na segunda saída de **Dados Divididos**.

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Subcadeia de caracteres

Este exemplo procura a seqüência especificada em qualquer posição dentro da segunda coluna do conjunto de dados. A posição é denotada aqui pelo valor do índice de 1. A correspondência diferencia maiúsculas e minúsculas.

```text
(\1) ^[a-f]
```

O primeiro conjunto de dados de resultados contém todas as `a`linhas `b` `c`onde `d` `e`a coluna de índice começa com um desses caracteres: , , , , . . `f` Todas as outras linhas são direcionadas para a segunda saída.

## <a name="select-a-relative-expression"></a>Selecione uma expressão relativa

1. Adicione o módulo [Dados Divididos](./split-data.md) ao seu pipeline e conecte-o como entrada ao conjunto de dados que você deseja dividir.
  
1. Para **o modo Desmemoização,** selecione **Expressão relativa**.
  
1. Na **caixa de expressão Relacional,** digite uma expressão que executa uma operação de comparação em uma única coluna.

   Para **a coluna numérica:**
   - A coluna contém números de qualquer tipo de dados numérico, incluindo tipos de dados de data e hora.
   - A expressão pode fazer referência a um máximo de um nome de coluna.
   - Use o caractere ampersand, `&`para a operação E. Use o caractere `|`pipe, para a operação OR.
   - Os seguintes operadores `<`são `>` `<=`suportados: , , , `>=`, `==`. . `!=`
   - Você não pode agrupar `(` `)`operações usando e .
   
   Para **coluna String:**
   - Os seguintes operadores `==`são `!=`suportados: . .

1. Envie o oleoduto.

   A expressão divide o conjunto de dados em dois conjuntos de linhas: linhas com valores que atendam à condição e todas as linhas restantes.

Os exemplos a seguir demonstram como dividir um conjunto de dados usando a opção **Expressão Relativa** no módulo **Dados Divididos.**  

### <a name="calendar-year"></a>Ano calendário

Um cenário comum é dividir um conjunto de dados por anos. A expressão a seguir seleciona todas as `Year` linhas `2010`onde os valores na coluna são maiores que .

```text
\"Year" > 2010
```

A expressão de data deve explicar todas as partes de data incluídas na coluna de dados. O formato das datas na coluna de dados deve ser consistente. 

Por exemplo, em uma coluna `mmddyyyy`de datas que usa o formato, a expressão deve ser algo assim:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Índice da coluna

A expressão a seguir demonstra como você pode usar o índice da coluna para selecionar todas as linhas na primeira coluna do conjunto de dados que contém valores menores ou iguais a 30, mas não iguais a 20.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
