---
title: 'Regressão florestal de decisão: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Perceptron médio de duas classes no Azure Machine Learning para criar um modelo de aprendizado de máquina baseado no algoritmo perceptron médio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920766"
---
# <a name="two-class-averaged-perceptron-module"></a>Módulo Perceptron Médio de Duas Classes

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo perceptron médio.  
  
Este algoritmo de classificação é um método de aprendizagem supervisionado e requer um *conjunto de dados marcado,* que inclui uma coluna de rótulo. Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [o Train Model](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

### <a name="about-averaged-perceptron-models"></a>Sobre modelos perceptron médios

O *método perceptron médio* é uma versão antiga e simples de uma rede neural. Nesta abordagem, as entradas são classificadas em várias saídas possíveis com base em uma função linear e, em seguida, combinadas com um conjunto de pesos que são derivados do vetor de recurso — daí o nome "perceptron".

Os modelos perceptron mais simples são adequados ao aprendizado de padrões separáveis linearmente, enquanto as redes neurais (especialmente redes neurais profundas) podem modelar limites de classe mais complexos. No entanto, os perceptrons são mais rápidos e conforme eles processem casos em série, podem ser usados com treinamento contínuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar perceptron médio de duas classes

1.  Adicione o módulo **Perceptron médio de duas classes** ao seu pipeline.  

2.  Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único**: Se você souber como deseja configurar o modelo, forneça um conjunto específico de valores como argumentos.

    -   **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.  
  
3.  Para **taxa de aprendizagem,** especifique um valor para a *taxa de aprendizagem*. Os valores da taxa de aprendizagem controlam o tamanho da etapa que é usada na descida do gradiente estocástico cada vez que o modelo é testado e corrigido.
  
     Ao tornar a taxa menor, você testa o modelo com mais frequência, com o risco de ficar preso em um planalto local. Aumentando a etapa, você pode convergir mais rapidamente, com o risco mínimo de errar o alvo verdadeiro.
  
4.  Para **o número máximo de iterações,** digite o número de vezes que deseja que o algoritmo examine os dados de treinamento.  
  
     Parando no início geralmente fornece melhor generalização. Aumentar o número de iterações melhora o ajuste, com o risco de superajuste.
  
5.  Para **sementes de número aleatório, digite**opcionalmente um valor inteiro para usar como semente. O uso de uma semente é recomendado se você quiser garantir a reprodutibilidade do gasoduto através das corridas.  
  
1.  Conecte um conjunto de dados de treinamento e um dos módulos de treinamento:
  
    -   Se você definir **Criar modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](train-model.md)




## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 