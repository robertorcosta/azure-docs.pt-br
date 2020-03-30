---
title: Extrair recursos n-gram da referência do módulo de texto
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Extrato N-Gram no Azure Machine Learning para fazer a featurização de dados de texto.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: efe09c1d516b37c23b024e07ae387772fa7e5992
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477605"
---
# <a name="extract-n-gram-features-from-text-module-reference"></a>Extrair recursos n-gram da referência do módulo de texto

Este artigo descreve um módulo no azure Machine Learning designer (visualização). Use os Recursos de Extrato N-Gram do módulo Texto para *fazer uma featurização de* dados de texto não estruturados. 

## <a name="configuration-of-the-extract-n-gram-features-from-text-module"></a>Configuração dos recursos de extrato N-Gram do módulo texto

O módulo suporta os seguintes cenários para usar um dicionário n-gram:

* [Crie um novo dicionário n-gram](#create-a-new-n-gram-dictionary) a partir de uma coluna de texto livre.

* [Use um conjunto de recursos de texto existentes](#use-an-existing-n-gram-dictionary) para fazer uma coluna de texto livre.

* [Marque ou publique um modelo](#score-or-publish-a-model-that-uses-n-grams) que use n-grams.

### <a name="create-a-new-n-gram-dictionary"></a>Crie um novo dicionário n-gram

1.  Adicione os Recursos do Extrato N-Gram do módulo Texto ao seu pipeline e conecte o conjunto de dados que tem o texto que você deseja processar.

1.  Use **a coluna Texto** para escolher uma coluna de tipo de string que contenha o texto que deseja extrair. Como os resultados são verbosos, você pode processar apenas uma única coluna de cada vez.

1. Defina **o modo Vocabulário** para **Criar** para indicar que você está criando uma nova lista de recursos n-gram. 

1. Defina **o tamanho de N-Grams** para indicar o tamanho *máximo* dos n-gramas para extrair e armazenar. 

    Por exemplo, se você inserir 3, unigramas, bigrams e trigramas serão criados.

1. **A função de ponderação** especifica como construir o vetor de recurso do documento e como extrair vocabulário de documentos.

    * **Peso Binário**: Atribui um valor de presença binário aos n-gramas extraídos. O valor para cada n-grama é 1 quando ele existe no documento, e 0 de outra forma.

    * **Peso TF**: Atribui uma pontuação de freqüência de termo (TF) aos n-gramas extraídos. O valor para cada n-grama é sua freqüência de ocorrência no documento.

    * **Peso do IDF**: Atribui uma pontuação de freqüência de documento inverso (IDF) aos n-gramas extraídos. O valor para cada n-grama é o registro do tamanho do corpus dividido por sua freqüência de ocorrência em todo o corpus.
    
      `IDF = log of corpus_size / document_frequency`
 
    *  **Peso TF-IDF**: Atribui uma freqüência de termo/freqüência de documento inverso (TF/IDF) ao n-grams extraído. O valor para cada n-gram é sua pontuação de TF multiplicada pela pontuação do IDF.

1. Defina **o comprimento mínimo** da palavra para o número mínimo de letras que podem ser usadas em qualquer *palavra* em um n-grama.

1. Use **comprimento máximo de palavras** para definir o número máximo de letras que podem ser usadas em qualquer *palavra* em um n-grama.

    Por padrão, até 25 caracteres por palavra ou token são permitidos.

1. Use **a freqüência absoluta do documento n-gram mínimo** para definir as ocorrências mínimas necessárias para que qualquer n-gram seja incluído no dicionário n-gram. 

    Por exemplo, se você usar o valor padrão de 5, qualquer n-gram deve aparecer pelo menos cinco vezes no corpus para ser incluído no dicionário n-gram. 

1.  Definir **a razão máxima de documento n-grama** para a razão máxima do número de linhas que contêm um n-grama particular, sobre o número de linhas no corpus geral.

    Por exemplo, uma razão de 1 indicaria que, mesmo que um n-gram específico esteja presente em cada linha, o n-grama pode ser adicionado ao dicionário n-gram. Mais tipicamente, uma palavra que ocorre em cada linha seria considerada uma palavra de ruído e seria removida. Para filtrar palavras de ruído dependentes do domínio, tente reduzir essa proporção.

    > [!IMPORTANT]
    > A taxa de ocorrência de palavras específicas não é uniforme. Varia de documento para documento. Por exemplo, se você está analisando comentários de clientes sobre um produto específico, o nome do produto pode ser de alta frequência e perto de uma palavra de ruído, mas ser um termo significativo em outros contextos.

1. Selecione a opção **Normalize vetores de recurso n-gram** para normalizar os vetores de recurso. Se esta opção estiver habilitada, cada vetor de recurso n-gram será dividido pela norma L2.

1. Envie o oleoduto.

### <a name="use-an-existing-n-gram-dictionary"></a>Use um dicionário n-gram existente

1.  Adicione os Recursos do Extrato N-Gram do módulo Texto ao seu pipeline e conecte o conjunto de dados que tem o texto que deseja processar à porta **Dataset.**

1.  Use **a coluna Texto** para selecionar a coluna de texto que contém o texto que deseja fazer featurize. Por padrão, o módulo seleciona todas as colunas de **string**tipo . Para obter melhores resultados, processe uma única coluna de cada vez.

1. Adicione o conjunto de dados salvo que contém um dicionário n-gram gerado anteriormente e conecte-o à porta **de vocabulário de entrada.** Você também pode conectar a saída **de vocabulário de resultado** de uma instância upstream dos Recursos Extrato N-Gram do módulo Texto.

1. Para **o modo Vocabulário,** selecione a opção **ReadOnly** update na lista de baixa.

   A opção **ReadOnly** representa o corpus de entrada para o vocabulário de entrada. Em vez de calcular as freqüências do termo de computação do novo conjunto de dados de texto (na entrada esquerda), os pesos de n-grama do vocabulário de entrada são aplicados como está.

   > [!TIP]
   > Use esta opção quando estiver marcando um classificador de texto.

1.  Para todas as outras opções, consulte as descrições da propriedade na [seção anterior](#create-a-new-n-gram-dictionary).

1.  Envie o oleoduto.

### <a name="score-or-publish-a-model-that-uses-n-grams"></a>Marcar ou publicar um modelo que usa n-grams

1.  Copie os **Recursos do Extrato N-Gram do** módulo Texto do fluxo de dados de treinamento para o fluxo de dados de pontuação.

1.  Conecte a saída do Vocabulário de **Resultado** do fluxo de dados de treinamento ao **Vocabulário de Entrada** no fluxo de dados de pontuação.

1.  No fluxo de trabalho de pontuação, modifique os Recursos de Extrato N-Gram do módulo Texto e defina o parâmetro **de modo vocabulário** para **ReadOnly**. Deixe tudo igual.

1.  Para publicar o pipeline, salve **o Vocabulário de Resultado** como um conjunto de dados.

1.  Conecte o conjunto de dados salvo aos Recursos de Extrato N-Gram do módulo Texto em seu gráfico de pontuação.

## <a name="results"></a>Resultados

O Extract N-Gram Features from Text cria dois tipos de saída: 

* **Conjunto de dados de resultados**: Esta saída é um resumo do texto analisado combinado com os n-gramas que foram extraídos. As colunas que você não selecionou na opção **Texto da coluna** são passadas para a saída. Para cada coluna de texto que você analisa, o módulo gera estas colunas:

  * **Matriz de ocorrências n-gram:** O módulo gera uma coluna para cada n-grama encontrado no corpus total e adiciona uma pontuação em cada coluna para indicar o peso do n-grama para essa linha. 

* **Vocabulário de resultados**: O vocabulário contém o dicionário n-gram real, juntamente com os escores de freqüência de termo que são gerados como parte da análise. Você pode salvar o conjunto de dados para reutilização com um conjunto diferente de entradas ou para uma atualização posterior. Você também pode reutilizar o vocabulário para modelagem e pontuação.

### <a name="result-vocabulary"></a>Vocabulário de resultados

O vocabulário contém o dicionário n-gram com os escores de freqüência de termo que são gerados como parte da análise. As pontuações do DF e do IDF são geradas independentemente de outras opções.

+ **ID**: Um identificador gerado para cada n-grama único.
+ **NGram**: O n-grama. Espaços ou outros separadores de palavras são substituídos pelo caractere sublinhado.
+ **DF**: A pontuação de freqüência do termo para o n-gram no corpus original.
+ **IDF**: A pontuação de freqüência de documento inverso para o n-gram no corpus original.

Você pode atualizar manualmente este conjunto de dados, mas você pode introduzir erros. Por exemplo: 

* Um erro é levantado se o módulo encontrar linhas duplicadas com a mesma chave no vocabulário de entrada. Certifique-se de que não há duas linhas no vocabulário que tenham a mesma palavra.
* O esquema de entrada dos conjuntos de dados do vocabulário deve corresponder exatamente, incluindo nomes de colunas e tipos de colunas. 
* A coluna **ID** e a coluna **DF** devem ser do tipo inteiro. 
* A coluna **IDF** deve ser do tipo flutuante.

> [!Note]
> Não conecte a saída de dados diretamente ao módulo Modelo de Trem. Você deve remover colunas de texto grátis antes que elas sejam alimentadas no Modelo de Trem. Caso contrário, as colunas de texto livre serão tratadas como características categóricas.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning.
