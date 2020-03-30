---
title: O que é treinamento distribuído?
titleSuffix: Azure Machine Learning
description: Saiba mais sobre treinamento distribuído e como o Azure Machine Learning o suporta.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: a0d5bf795e4759a105b9a235770f37aa10bd6751
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385539"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Treinamento distribuído com Aprendizado de Máquina do Azure

Neste artigo, você aprende sobre treinamento distribuído e como o Azure Machine Learning o apoia para modelos de aprendizagem profunda. 

No treinamento distribuído, a carga de trabalho para treinar um modelo é dividida e compartilhada entre vários mini processadores, chamados nós de trabalhadores. Esses nódulos operianos trabalham paralelamente para acelerar o treinamento de modelos. O treinamento distribuído pode ser usado para modelos ml tradicionais, mas é mais adequado para tarefas de computação e tempo intensivo, como [o deep learning](concept-deep-learning-vs-machine-learning.md) para treinar redes neurais profundas.

## <a name="deep-learning-and-distributed-training"></a>Deep learning e treinamento distribuído 

Existem dois tipos principais de treinamento distribuído: [paralelismo de dados](#data-parallelism) e [paralelismo modelo.](#model-parallelism) Para treinamento distribuído em modelos de aprendizagem profunda, o [Azure Machine Learning SDK em Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) suporta integrações com frameworks populares, PyTorch e TensorFlow. Ambas as estruturas empregam paralelismo de dados para treinamento distribuído, e podem alavancar [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html) para otimizar velocidades de computação. 

* [Treinamento distribuído com PyTorch](how-to-train-pytorch.md#distributed-training)

* [Treinamento distribuído com tensorFlow](how-to-train-tensorflow.md#distributed-training)

Para modelos ML que não exigem treinamento distribuído, consulte [modelos de trem com OZure Machine Learning](concept-train-machine-learning-model.md#python-sdk) para as diferentes maneiras de treinar modelos usando o Python SDK.

## <a name="data-parallelism"></a>Paralelismo de dados

O paralelismo de dados é o mais fácil de implementar das duas abordagens de treinamento distribuída, e é suficiente para a maioria dos casos de uso.

Nesta abordagem, os dados são divididos em partições, onde o número de partições é igual ao número total de nódulos disponíveis, no cluster de computação. O modelo é copiado em cada um desses nós operários, e cada trabalhador opera em seu próprio subconjunto de dados. Tenha em mente que cada nó tem que ter a capacidade de suportar o modelo que está sendo treinado, ou seja, o modelo tem que se encaixar inteiramente em cada nó.

Cada nó calcula independentemente os erros entre suas previsões para suas amostras de treinamento e as saídas rotuladas. Por sua vez, cada nó atualiza seu modelo com base nos erros e deve comunicar todas as suas alterações aos outros nós para atualizar seus modelos correspondentes. Isso significa que os nós do trabalhador precisam sincronizar os parâmetros do modelo, ou gradientes, no final da computação em lote para garantir que estão treinando um modelo consistente. 

## <a name="model-parallelism"></a>Modelo de paralelismo

No modelo de paralelismo, também conhecido como paralelismo de rede, o modelo é segmentado em diferentes partes que podem ser executadas simultaneamente em diferentes nados, e cada um será executado nos mesmos dados. A escalabilidade desse método depende do grau de paralelização da tarefa do algoritmo, e é mais complexo de implementar do que o paralelismo de dados. 

No modelo de paralelismo, os nós do trabalhador só precisam sincronizar os parâmetros compartilhados, geralmente uma vez para cada etapa de propagação para frente ou para trás. Além disso, modelos maiores não são uma preocupação, uma vez que cada nó opera em uma subseção do modelo nos mesmos dados de treinamento.

## <a name="next-steps"></a>Próximas etapas

* Aprenda a [configurar ambientes de treinamento](how-to-set-up-training-targets.md) com o Python SDK.
* Para um exemplo técnico, veja o cenário de [arquitetura de referência](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/training-deep-learning).
* [Treine modelos ML com TensorFlow](how-to-train-tensorflow.md).
* [Treine modelos ML com PyTorch](how-to-train-pytorch.md). 