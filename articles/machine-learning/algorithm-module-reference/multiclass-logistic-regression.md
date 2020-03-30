---
title: 'Regressão Logística Multiclasse: Referência do Módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão logística multiclasse no Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever múltiplos valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ca446b0ab67a8a202c1f4d505262660ac55f42db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456158"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de Regressão Logística Multiclasse

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever múltiplos valores.

A classificação usando a regressão logística é um método de aprendizagem supervisionado e, portanto, requer um conjunto de dados rotulado. Você treina o modelo fornecendo o modelo e o conjunto de dados rotulado como uma entrada para um módulo como [Train Model](./train-model.md). O modelo treinado pode então ser usado para prever valores para novos exemplos de entrada.

O Azure Machine Learning também fornece um módulo [de regressão logística de duas classes,](./two-class-logistic-regression.md) que é adequado para a classificação de variáveis binárias ou dicotômicas.

## <a name="about-multiclass-logistic-regression"></a>Sobre a regressão logística multiclasse

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um desfecho, e é popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, encaixando dados em uma função logística. 

Na regressão logística multiclasse, o classificador pode ser usado para prever múltiplos resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configure uma regressão logística multiclasse

1. Adicione o módulo **de regressão logística multiclasse** ao pipeline.

2. Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**

    + **Parâmetro Único**: Use esta opção se você souber como deseja configurar o modelo e forneça um conjunto específico de valores como argumentos.

    + **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.  

3. **Tolerância à otimização,** especifique o valor limite para convergência de otimizadores. Se a melhoria entre as iterações for menor que o limite, o algoritmo para e retorna o modelo atual.

4. **Peso de regularização L1**, **Peso de regularização L2**: Digite um valor a ser utilizado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.

    A regularização é um método de prevenção de superajustamento, penalizando modelos com valores de coeficiente extremo. A regularização funciona adicionando a penalidade que está associada aos valores de coeficiente ao erro da hipótese. Um modelo preciso com valores de coeficiente extremo seria mais penalizado, mas um modelo menos preciso com valores mais conservadores seria menos penalizado.

     As regularizações L1 e L2 têm efeitos e usos diferentes. L1 pode ser aplicado aos modelos esparsos, que é útil ao trabalhar com dados de grande dimensão. Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  Este algoritmo suporta uma combinação linear de valores de regularização `x = L1` `y = L2`L1 e L2: isto é, se e , `ax + by = c` define o vão linear dos termos de regularização.

     Diferentes combinações lineares dos termos L1 e L2 foram concebidas para modelos de regressão logística, como [regularização líquida elástica.](https://wikipedia.org/wiki/Elastic_net_regularization)

6. **Semente de número aleatório**: Digite um valor inteiro para usar como semente para o algoritmo se você quiser que os resultados sejam repetíveis ao longo das corridas. Caso contrário, um valor de relógio do sistema é usado como a semente, que pode produzir resultados ligeiramente diferentes em corridas do mesmo gasoduto.

8. Conecte um conjunto de dados rotulado e um dos módulos do trem:

    + Se você definir **Criar modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](./train-model.md)

9. Envie o oleoduto.



## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 