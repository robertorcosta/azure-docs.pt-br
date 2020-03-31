---
title: Referência do módulo hashing de recurso
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Hashing de recursos no Azure Machine Learning para fazer uma featurização de dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7178417a5c20afe5b1ed02bc526ec174704962df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456243"
---
# <a name="feature-hashing-module-reference"></a>Referência do módulo hashing de recurso

Este artigo descreve um módulo incluído no azure Machine Learning designer (visualização).

Use o módulo Hashing de recurso para transformar um fluxo de texto em inglês em um conjunto de recursos inteiros. Em seguida, você pode passar este conjunto de recursos hashed para um algoritmo de aprendizado de máquina para treinar um modelo de análise de texto.

A funcionalidade de hashing de recurso fornecida neste módulo é baseada na estrutura nimbusml. Para obter mais informações, consulte [a classe NgramHash](https://docs.microsoft.com/python/api/nimbusml/nimbusml.feature_extraction.text.extractor.ngramhash?view=nimbusml-py-latest).

## <a name="what-is-feature-hashing"></a>O que é hashing de características?

O hashing de recursos funciona convertendo tokens exclusivos em inteiros. Ele opera nas strings exatas que você fornece como entrada e não realiza nenhuma análise linguística ou pré-processamento. 

Por exemplo, pegue um conjunto de frases simples como essas, seguida de uma pontuação de sentimento. Presuma que você deseja usar este texto para construir um modelo.

|Texto do usuário|Sentimento|
|--------------|---------------|
|Eu amei este livro|3|
|Eu odiava este livro.|1|
|Este livro foi ótimo.|3|
|Eu amo livros|2|

Internamente, o módulo Hashing de recursos cria um dicionário de n-grams. Por exemplo, a lista de bigrams para este conjunto de dados seria algo assim:

|Termo (bigrams)|Frequência|
|------------|---------------|
|Este livro|3|
|Eu amei|1|
|Eu odiava|1|
|Eu amo|1|

Você pode controlar o tamanho dos n-gramas usando a propriedade **N-grams.** Se você escolher bigrams, unigramas também são computados. O dicionário também incluiria termos únicos como estes:

|Termo (unigramas)|Frequência|
|------------|---------------|
|agendar|3|
|I|3|
|livros|1|
|Foi|1|

Depois que o dicionário é construído, o módulo Hashing de recurso converte os termos do dicionário em valores de hash. Em seguida, calcula se um recurso foi usado em cada caso. Para cada linha de dados de texto, o módulo produz um conjunto de colunas, uma coluna para cada recurso hashed.

Por exemplo, após o hashing, as colunas de recursos podem se parecer com algo assim:

|Classificação|Recurso de hash 1|Hashing recurso 2|Recurso de hashing 3|
|-----|-----|-----|-----|
|4|1|1|0|
|5|0|0|0|

* Se o valor na coluna for 0, a linha não continha o recurso hashed.
* Se o valor for 1, a linha continha o recurso.

O hashing do recurso permite que você represente documentos de texto de comprimento variável como vetores de característica numérica de comprimento igual para reduzir a dimensionalidade. Se você tentasse usar a coluna de texto para treinamento como está, ela seria tratada como uma coluna de recursos categórico com muitos valores distintos.

As saídas numéricas também possibilitam o uso de métodos comuns de aprendizado de máquina, incluindo classificação, clustering e recuperação de informações. Como as operações de aparência podem usar hashes inteiros em vez de comparações de cordas, obter os pesos de recurso também é muito mais rápido.

## <a name="configure-the-feature-hashing-module"></a>Configure o módulo hashing de recursos

1.  Adicione o módulo hashing de recursos ao seu pipeline no designer.

1. Conecte o conjunto de dados que contém o texto que deseja analisar.

    > [!TIP]
    > Como o hashing de recurso não executa operações léxicas, como a deter ou truncação, às vezes você pode obter melhores resultados ao pré-processar o texto antes de aplicar hashing de recurso. 

1. Defina **colunas de destino** para as colunas de texto que você deseja converter para recursos hashed. Tenha em mente que:

    * As colunas devem ser o tipo de dados de seqüência.
    
    * A escolha de várias colunas de texto pode ter um impacto significativo na dimensionalidade dos recursos. Por exemplo, o número de colunas para um hash de 10 bits vai de 1.024 para uma única coluna para 2.048 para duas colunas.

1. Use o tamanho de bits do **Hashing** para especificar o número de bits a serem usados quando estiver criando a tabela hash.
    
    O tamanho padrão do bit é de 10. Para muitos problemas, esse valor é adequado. Você pode precisar de mais espaço para evitar colisões, dependendo do tamanho do vocabulário n-grams no texto de treinamento.
    
1. Para **N-grams,** digite um número que define o comprimento máximo dos n-gramas para adicionar ao dicionário de treinamento. Um n-gram é uma seqüência de *n* palavras, tratada como uma unidade única.

    Por exemplo, se você inserir 3, unigramas, bigrams e trigramas serão criados.

1. Envie o oleoduto.

## <a name="results"></a>Resultados

Após o processamento ser concluído, o módulo produz um conjunto de dados transformado no qual a coluna de texto original foi convertida em várias colunas. Cada coluna representa uma característica no texto. Dependendo do quão significativo é o dicionário, o conjunto de dados resultante pode ser grande:

|Nome da coluna 1|Tipo de coluna 2|
|-------------------|-------------------|
|USERTEXT|Coluna de dados original|
|Sentimento|Coluna de dados original|
|USERTEXT - Recurso de hashing 1|Coluna de recursos hashed|
|USERTEXT - Recurso de hashing 2|Coluna de recursos hashed|
|USERTEXT - Recurso de hashing n|Coluna de recursos hashed|
|USERTEXT - Recurso de hashing 1024|Coluna de recursos hashed|

Depois de criar o conjunto de dados transformado, você pode usá-lo como entrada para o módulo Modelo de Trem.
 
## <a name="best-practices"></a>Práticas recomendadas

As seguintes práticas recomendadas podem ajudá-lo a tirar o máximo do módulo hashing de recursos:

* Adicione um módulo de texto de pré-processo antes de usar hashing de recurso para pré-processar o texto de entrada. 

* Adicione um módulo Selecionar colunas após o módulo Hashing de recursos para remover as colunas de texto do conjunto de dados de saída. Você não precisa das colunas de texto depois que os recursos de hash foram gerados.
    
* Considere usar essas opções de pré-processamento de texto, para simplificar resultados e melhorar a precisão:

    * Quebra de palavras
    * Parando a remoção da palavra
    * Normalização de casos
    * Remoção de pontuação e caracteres especiais
    * Lematização  

O conjunto ideal de métodos de pré-processamento para aplicar em qualquer solução depende do domínio, vocabulário e necessidade dos negócios. pipeline com seus dados para ver quais métodos de processamento de texto são mais eficazes.

## <a name="next-steps"></a>Próximas etapas
            
Veja o [conjunto de módulos disponíveis](module-reference.md) para O Zure Machine Learning 
