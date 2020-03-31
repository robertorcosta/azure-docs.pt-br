---
title: Deep learning vs. machine learning
titleSuffix: Azure
description: Saiba como o aprendizado profundo se relaciona com aprendizado de máquina e inteligência artificial. O deep learning é usado em cenários como detecção de fraudes, reconhecimento facial & de voz, análise de sentimentos e previsão de séries tempois.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: lazzeri
author: FrancescaLazzeri
ms.date: 03/05/2020
ms.openlocfilehash: bcacf4ff66e114f65ac75f0aadd1564875f15f62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501357"
---
# <a name="deep-learning-vs-machine-learning"></a>Deep learning vs. machine learning

Este artigo ajuda você a comparar o deep learning versus machine learning. Você aprenderá como os dois conceitos se comparam e como eles se encaixam na categoria mais ampla de inteligência artificial. O artigo também descreve como o aprendizado profundo pode ser aplicado a cenários reais, como detecção de fraudes, reconhecimento de voz e facial, análise de sentimentos e previsão de séries tempois.

## <a name="deep-learning-machine-learning-and-ai"></a>Aprendizado profundo, aprendizado de máquina e IA

![Diagrama de relacionamento: IA vs. machine learning vs. deep learning](./media/concept-deep-learning-vs-machine-learning/ai-vs-machine-learning-vs-deep-learning.png)

Considere as seguintes definições para entender o deep learning vs. machine learning vs. AI:

- **O aprendizado profundo** é um subconjunto de aprendizado de máquina baseado em redes neurais artificiais. O _processo de aprendizagem_ é _profundo_ porque a estrutura de redes neurais artificiais consiste em múltiplas camadas de entrada, saída e ocultas. Cada camada contém unidades que transformam os dados de entrada em informações que a próxima camada pode usar para uma determinada tarefa preditiva. Graças a essa estrutura, uma máquina pode aprender através de seu próprio processamento de dados.

- **Machine learning** é um subconjunto de inteligência artificial que usa técnicas (como o deep learning) que permitem que as máquinas usem a experiência para melhorar nas tarefas. O _processo de aprendizagem_ baseia-se nas seguintes etapas:

   1. Inserde dados em um algoritmo. (Nesta etapa, você pode fornecer informações adicionais ao modelo, por exemplo, realizando a extração de recursos.)
   1. Usar esses dados para treinar um modelo.
   1. Testar e implantar o modelo.
   1. Consumir o modelo implantado para fazer uma tarefa preditiva automatizada. (Em outras palavras, ligue e use o modelo implantado para receber as previsões devolvidas pelo modelo.)

- A **inteligência artificial (IA)** é uma técnica que permite aos computadores imitar a inteligência humana. Ela inclui o machine learning. 
 
É importante entender a relação entre IA, machine learning e deep learning. O aprendizado de máquina é uma forma de alcançar a inteligência artificial. Usando técnicas de aprendizado de máquina e aprendizagem profunda, você pode construir sistemas de computador e aplicativos que fazem tarefas comumente associadas à inteligência humana. Essas tarefas incluem reconhecimento de imagem, reconhecimento de fala e tradução de idiomas.

## <a name="techniques-of-deep-learning-vs-machine-learning"></a>Técnicas de deep learning vs. machine learning 

Agora que você tem a visão geral do aprendizado de máquina versus aprendizado profundo, vamos comparar as duas técnicas. No aprendizado de máquina, o algoritmo precisa ser dito como fazer uma previsão precisa consumindo mais informações (por exemplo, realizando extração de recursos). No deep learning, o algoritmo pode aprender a fazer uma previsão precisa através de seu próprio processamento de dados, graças à estrutura artificial da rede neural.

A tabela a seguir compara as duas técnicas com mais detalhes:

| |Todos os aprendizados de máquina |Apenas aprendizado profundo|
|---|---|---|
|  **Número de pontos de dados** | Pode usar pequenas quantidades de dados para fazer previsões. | Precisa usar grandes quantidades de dados de treinamento para fazer previsões. |
|  **Dependências de hardware** | Pode funcionar em máquinas low-end. Não precisa de uma grande quantidade de poder computacional. | Depende de máquinas de ponta. Ele inerentemente faz um grande número de operações de multiplicação matricial. Uma GPU pode otimizar eficientemente essas operações. |
|  **Processo de featurização** | Requer que os recursos sejam identificados com precisão e criados pelos usuários. | Aprende recursos de alto nível a partir de dados e cria novos recursos por si só. |
|  **Abordagem de aprendizagem** | Divide o processo de aprendizagem em etapas menores. Em seguida, combina os resultados de cada passo em uma saída. | Passa pelo processo de aprendizagem resolvendo o problema de ponta a ponta. |
|  **Tempo de execução** | Leva relativamente pouco tempo para treinar, variando de alguns segundos a algumas horas. | Geralmente leva muito tempo para treinar porque um algoritmo de aprendizagem profunda envolve muitas camadas. |
|  **Saída** | A saída é geralmente um valor numérico, como uma pontuação ou uma classificação. | A saída pode ter vários formatos, como um texto, uma partitura ou um som. |

## <a name="deep-learning-use-cases"></a>Casos de uso de aprendizagem profunda

Por causa da estrutura artificial da rede neural, o deep learning se destaca na identificação de padrões em dados não estruturados, como imagens, som, vídeo e texto. Por essa razão, o aprendizado profundo está transformando rapidamente muitas indústrias, incluindo saúde, energia, finanças e transporte. Essas indústrias estão agora repensando os processos de negócios tradicionais. 

Algumas das aplicações mais comuns para o aprendizado profundo são descritas nos seguintes parágrafos.

### <a name="named-entity-recognition"></a>Reconhecimento de entidade nomeada

O reconhecimento de entidade nomeada é um método de aprendizagem profunda que toma um pedaço de texto como entrada e o transforma em uma classe pré-especificada. Essa nova informação pode ser um código postal, uma data, uma id do produto. As informações podem então ser armazenadas em um esquema estruturado para construir uma lista de endereços ou servir como referência para um mecanismo de validação de identidade.

### <a name="object-detection"></a>Detecção de objetos

O aprendizado profundo tem sido aplicado em muitos casos de uso de detecção de objetos. A detecção de objetos compreende duas partes: classificação de imagem e, em seguida, localização da imagem. A _classificação_ da imagem identifica os objetos da imagem, como carros ou pessoas. A _localização da imagem_ fornece a localização específica desses objetos. 

A detecção de objetos já é usada em indústrias como jogos, varejo, turismo e carros autônomos.

### <a name="image-caption-generation"></a>Geração de legendas de imagem

Como o reconhecimento de imagem, na legenda da imagem, para uma determinada imagem, o sistema deve gerar uma legenda que descreva o conteúdo da imagem. Quando você pode detectar e rotular objetos em fotografias, o próximo passo é transformar esses rótulos em frases descritivas. 

Normalmente, os aplicativos de legendagem de imagens usam redes neurais convolucionais para identificar objetos em uma imagem e, em seguida, usam uma rede neural recorrente para transformar os rótulos em frases consistentes.

### <a name="machine-translation"></a>Tradução automática

A tradução automática pega palavras ou frases de um idioma e as traduz automaticamente para outra língua. A tradução automática existe há muito tempo, mas o aprendizado profundo alcança resultados impressionantes em duas áreas específicas: tradução automática de texto (e tradução da fala para texto) e tradução automática de imagens.

Com a transformação adequada dos dados, uma rede neural pode entender sinais de texto, áudio e visual. A tradução automática pode ser usada para identificar trechos de som em arquivos de áudio maiores e transcrever a palavra ou imagem falada como texto.

### <a name="text-analytics"></a>Análise de Texto

A análise de texto baseada em métodos de aprendizagem profunda envolve analisar grandes quantidades de dados de texto (por exemplo, documentos médicos ou recibos de despesas), reconhecer padrões e criar informações organizadas e concisas a partir dele.

As empresas usam o aprendizado profundo para realizar análises de texto para detectar informações privilegiadas e conformidade com as regulamentações governamentais. Outro exemplo comum é a fraude de seguros: a análise de texto tem sido frequentemente usada para analisar grandes quantidades de documentos para reconhecer as chances de uma reivindicação de seguro ser fraude. 

## <a name="artificial-neural-networks"></a>Redes neurais artificiais

Redes neurais artificiais são formadas por camadas de nós conectados. Modelos de aprendizagem profunda usam redes neurais que têm um grande número de camadas. 

As seções a seguir exploram tipologias de rede neural artificial mais populares.

### <a name="feedforward-neural-network"></a>Rede neural feedforward

A rede neural feedforward é o tipo mais básico de rede neural artificial. Em uma rede feedforward, as informações se movem em apenas uma direção, da camada de entrada para a camada de saída. As redes neurais feedforward transformam uma entrada colocando-a através de uma série de camadas ocultas. Cada camada é composta de um conjunto de neurônios, e cada camada está totalmente conectada a todos os neurônios da camada antes. A última camada totalmente conectada (a camada de saída) representa as previsões geradas.

### <a name="recurrent-neural-network"></a>Rede neural recorrente

Redes neurais recorrentes são uma rede neural artificial amplamente utilizada. Essas redes salvam a saída de uma camada e a alimentam de volta à camada de entrada para ajudar a prever o resultado da camada. Redes neurais recorrentes têm grandes habilidades de aprendizado. Eles são amplamente usados para tarefas complexas, como previsão de séries tempois, aprender caligrafia e reconhecer a linguagem.

### <a name="convolutional-neural-networks"></a>Redes neurais convolucionais

Uma rede neural convolucional é uma rede neural artificial particularmente eficaz, e apresenta uma arquitetura única. As camadas são organizadas em três dimensões: largura, altura e profundidade. Os neurônios em uma camada se conectam não a todos os neurônios na próxima camada, mas apenas a uma pequena região dos neurônios da camada. A saída final é reduzida a um único vetor de pontuações de probabilidade, organizadaao longo da dimensão de profundidade. 

Redes neurais convolucionais têm sido usadas em áreas como reconhecimento de vídeo, reconhecimento de imagem e sistemas de recomendação.

## <a name="next-steps"></a>Próximas etapas

Os artigos a seguir mostram como usar a tecnologia de deep learning no [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/?WT.mc_id=docs-article-lazzeri):

- [Classifique os dígitos manuscritos usando um modelo TensorFlow](https://docs.microsoft.com/azure/machine-learning/how-to-train-tensorflow?WT.mc_id=docs-article-lazzeri)

- [Classifique os dígitos manuscritos usando um estimador TensorFlow e keras](https://docs.microsoft.com/azure/machine-learning/how-to-train-keras?WT.mc_id=docs-article-lazzeri)

- [Classificar imagens usando um modelo Pytorch](https://docs.microsoft.com/azure/machine-learning/how-to-train-pytorch?WT.mc_id=docs-article-lazzeri)

- [Classificar dígitos manuscritos usando um modelo Chainer](https://docs.microsoft.com/azure/machine-learning/how-to-train-chainer?WT.mc_id=docs-article-lazzeri)

Além disso, use a Folha de Trapaça do [Algoritmo de Aprendizagem de Máquina](../synapse-analytics/sql-data-warehouse/cheat-sheet.md) para escolher algoritmos para o seu modelo.
