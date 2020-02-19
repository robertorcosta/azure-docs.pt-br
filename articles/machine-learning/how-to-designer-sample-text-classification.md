---
title: 'Designer: exemplo de classificação de resenhas de livros'
titleSuffix: Azure Machine Learning
description: Crie um classificador de regressão logística de várias classes para prever a categoria do conjunto de dados SP 500 da Wikipédia usando o designer do Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 02/11/2020
ms.openlocfilehash: f15f50e372d0bfe58018b16ebfa5d5d85644ae1a
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137781"
---
# <a name="build-a-classifier-to-predict-company-category-using-azure-machine-learning-designer"></a>Crie um classificador para prever a categoria da empresa usando o designer do Azure Machine Learning.

**Amostra 7 do designer (versão prévia)**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Esta amostra explica como usar módulos de análise de texto para criar um pipeline de classificação de textos no designer do Azure Machine Learning (versão prévia).

O objetivo da classificação de textos é atribuir um texto a uma ou mais classes ou categorias predefinidas. O texto pode ser um documento, um artigo de notícias, uma consulta de pesquisa, um email, um tweet, tíquetes de suporte, comentários do cliente, resenhas de produto do usuário etc. Aplicativos de classificação de textos incluem categorização de artigos de jornal e conteúdo de notícias em tópicos, organização de páginas da Web em categorias hierárquicas, filtragem de email de spam, análise de sentimento, previsão da intenção do usuário com base em consultas de pesquisa, roteamento de tíquetes de suporte e análise dos comentários do cliente. 

Este pipeline treina um **classificador de regressão logística de várias classes** para prever a categoria da empresa com o **conjunto de dados SP 500 da Wikipédia obtido na Wikipédia**.  

As etapas fundamentais de um modelo de machine learning de treinamento com os dados de texto são:

1. Obter os dados

1. Pré-processar os dados de texto

1. Engenharia de recursos

   Converta o recurso de texto no recurso numérico com o módulo de extração de recursos, como hash de recurso, e extraia o recurso n-gram dos dados de texto.

1. Treinar o modelo

1. Pontuar o conjunto de dados

1. Avalie o modelo

Este é o grafo final e concluído do pipeline no qual trabalharemos. Forneceremos a justificativa para todos os módulos, de modo que você possa tomar decisões semelhantes por conta própria.

[![Grafo do pipeline](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png)](./media/how-to-designer-sample-text-classification/nlp-modules-overall.png#lightbox)

## <a name="data"></a>Dados

Neste pipeline, usamos o conjunto de dados **SP 500 da Wikipédia**. O conjunto de dados foi obtido na Wikipédia https://www.wikipedia.org/), com base em artigos de cada empresa S&P 500. Antes de ser carregado no designer do Azure Machine Learning Studio, o conjunto de dados foi processado da seguinte maneira:

- Extraia o conteúdo do texto para cada empresa específica
- Remova a formatação wiki
- Remova caracteres não alfanuméricos
- Converta todo o texto para minúsculas
- Categorias de empresas conhecidas foram adicionadas

Para algumas empresas, os artigos não foram encontrados e, portanto, o número de registros é menor que 500.

## <a name="pre-process-the-text-data"></a>Pré-processar os dados de texto

Usamos o módulo **Pré-processar Texto** para pré-processar os dados de texto, incluindo detecção das sentenças, criar tokens de sentenças etc. Você encontrará todas as opções compatíveis no artigo [**Pré-processar Texto**](algorithm-module-reference/preprocess-text.md). Após o pré-processamento dos dados de texto, usamos o módulo **Dividir Dados** para dividir aleatoriamente os dados de entrada, de modo que o conjunto de dados de treinamento contenha 50% dos dados originais e o conjunto de dados de teste contenha 50% dos dados originais.

## <a name="feature-engineering"></a>Engenharia de recursos
Nesta amostra, usaremos dois métodos que executam a engenharia de recursos.

### <a name="feature-hashing"></a>Hash de recursos
Usamos o módulo [**Hash de Recurso**](algorithm-module-reference/feature-hashing.md) para converter o texto sem formatação dos artigos em inteiros e usamos os valores inteiros como recursos de entrada para o modelo. 

O módulo **Hash de Recurso** pode ser usado para converter documentos de texto de tamanho variável em vetores de recursos numéricos de tamanho igual, usando o método de hash murmurhash v3 de 32 bits fornecido pela biblioteca Vowpal Wabbit. O objetivo de usar o hash de recurso é a redução da dimensionalidade; o hash de recurso também torna a pesquisa de pesos de recursos mais rápida no tempo de classificação, porque ela usa a comparação de valores de hash em vez da comparação de cadeias de caracteres.

No pipeline de exemplo, definimos o número de bits de hash como 14 e o número de n-grams como 2. Com essas configurações, a tabela de hash pode conter 2 ^ 14 entradas, nas quais cada recurso de hash representa um ou mais recursos de n-gram e o valor representa a frequência de ocorrência desse n-gram na instância de texto. Para muitos problemas, uma tabela de hash desse tamanho é mais do que adequada, mas em alguns casos, um espaço adicional pode ser necessário para evitar colisões. Avalie o desempenho da solução de aprendizado de máquina usando um número diferente de bits. 

### <a name="extract-n-gram-feature-from-text"></a>Extrair recursos n-gram do texto

Um n-gram é uma sequência contígua de n termos de determinada sequência de texto. Um n-gram de tamanho 1 é chamado de unigrama; um n-gram de tamanho 2 é um bigrama; um n-gram de tamanho 3 é um trigrama. N-grams de tamanhos maiores às vezes são chamados pelo valor de n, por exemplo, "quatro-grama", "cinco-grama" etc.

Usamos o módulo [**Extrair Recurso N-Gram de Texto**](algorithm-module-reference/extract-n-gram-features-from-text.md) como outra solução para a engenharia de recursos. Esse módulo primeiro extrai o conjunto de n-grams, além dos n-grams, o número de documentos em que cada n-gram aparece no texto é contado (DF). Neste exemplo, a métrica TF-IDF é usada para calcular valores de recursos. Em seguida, ela converte dados de texto não estruturados em vetores de recursos numéricos de tamanho igual, em que cada recurso representa a TF-IDF de um n-gram em uma instância de texto.

Após a conversão dos dados de texto em vetores de recursos numéricos, um módulo **Selecionar Coluna** é usado para remover os dados de texto do conjunto de dados. 

## <a name="train-the-model"></a>Treinar o modelo

A escolha do algoritmo geralmente depende dos requisitos do caso de uso. Como o objetivo desse pipeline é prever a categoria da empresa, um modelo de classificador de várias classes é uma boa opção. Considerando que o número de recursos é grande e que esses recursos são esparsos, usamos o modelo **Regressão Logística de Várias Classes** para esse pipeline.

## <a name="test-evaluate-and-compare"></a>Teste, avaliação e comparação

 Dividimos o conjunto de dados e usamos conjuntos de dados diferentes para treinar e testar o modelo a fim de tornar a avaliação do modelo mais objetiva.

Depois que o modelo for treinado, usaremos os módulos **Pontuar Modelo** e **Avaliar Modelo** para gerar os resultados previstos e avaliar os modelos. No entanto, antes de usar o módulo **Pontuar Modelo**, é necessário executar a engenharia de recursos como o que fizemos durante o treinamento. 

No módulo **Hash de Recurso**, é fácil executar a engenheira de recursos no fluxo de pontuação como o fluxo de treinamento. Use o módulo de **Hash de Recurso** diretamente para processar os dados de texto de entrada.

Para o módulo **Extrair Recurso de N-Gram do Texto**, conectaremos a **saída de Vocabulário do Resultado** do fluxo de dados de treinamento ao **Vocabulário de Entrada** no fluxo de dados de pontuação e definiremos o parâmetro **Modo de vocabulário** como **ReadOnly**.
[![Grafo da pontuação de n-gram](./media/how-to-designer-sample-text-classification/n-gram.png)](./media/how-to-designer-sample-text-classification/n-gram.png)

Após a conclusão da etapa de engenharia, o módulo **Pontuar Modelo** pode ser usado para gerar previsões para o conjunto de dados de teste usando o modelo treinado. Para verificar o resultado, selecione a porta de saída de **Pontuar Modelo** e, em seguida, selecione **Visualizar**.

Em seguida, transmitimos as pontuações para o módulo **Avaliar Modelo** para gerar métricas de avaliação. O módulo **Avaliar Modelo** tem duas portas de entrada, de modo que possamos avaliar e comparar os conjuntos de dados pontuados que são gerados com métodos diferentes. Nesta amostra, comparamos o desempenho do resultado gerado com o método de hash de recurso e o método de n-gram.
Para verificar o resultado, selecione a porta de saída de **Avaliar Modelo** e, em seguida, selecione **Visualizar**.

## <a name="clean-up-resources"></a>Limpar os recursos

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Explore as outras amostras disponíveis para o designer:
- [Amostra 1: regressão: prever o preço de um automóvel](how-to-designer-sample-regression-automobile-price-basic.md)
- [Amostra 2: regressão: comparar algoritmos para a previsão de preços de automóveis](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Amostra 3: classificação com seleção de recursos: previsão de receita](how-to-designer-sample-classification-predict-income.md)
- [Amostra 4: classificação: prever o risco de crédito (sensível ao custo)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Amostra 5: classificação: prever a rotatividade](how-to-designer-sample-classification-churn.md)
- [Amostra 6: classificação: prever atrasos nos voos](how-to-designer-sample-classification-flight-delay.md)
