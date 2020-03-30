---
title: 'Regressão florestal de decisão: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de Regressão Florestal de Decisão no Azure Machine Learning para criar um modelo de regressão baseado em um conjunto de árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456532"
---
# <a name="decision-forest-regression-module"></a>Módulo de Regressão Florestal de Decisão

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de regressão baseado em um conjunto de árvores de decisão.

Depois de configurar o modelo, você deve treinar o modelo usando um conjunto de dados rotulado e o módulo [Modelo de Trem.](./train-model.md) O modelo treinado pode então ser usado para fazer previsões. 

## <a name="how-it-works"></a>Como ele funciona

Árvores de decisão são modelos não paramétricos que executam uma sequência de testes simples para cada instância, percorrendo uma estrutura de dados de árvore binária até que um nó folha (decisão) seja atingido.

As árvores de decisão têm estas vantagens:

- São eficientes no uso de memória e computação durante o treinamento e a previsão.

- Podem representar limites de decisão não lineares.

- Realizam seleção e classificação de recursos integrados e são resilientes na presença de recursos com ruídos.

Esse modelo de regressão consiste de um ensemble de árvores de decisão. Cada árvore em uma decisão de regressão floresta produz uma distribuição gaussiana como uma previsão. Uma agregação é realizada sobre o conjunto de árvores para encontrar uma distribuição gaussiana mais próxima da distribuição combinada para todas as árvores do modelo.

Para obter mais informações sobre o referencial teórico deste algoritmo e sua implementação, consulte este artigo: [Florestas de Decisão: Um Quadro Unificado de Classificação, Regressão, Estimativa de Densidade, Aprendizagem Despachada e Aprendizagem Semi-Supervisionada](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar modelo de regressão florestal de decisão

1. Adicione o módulo **de regressão florestal** de decisão ao gasoduto. Você pode encontrar o módulo no designer em **Machine Learning,** **Initialize Model**e **Regression**.

2. Abra as propriedades do módulo e, para **o método Resampling,** escolha o método usado para criar as árvores individuais.  Você pode escolher entre **Ensacar** ou **replicar**.

    - **Ensaque**: Ensacado também é chamado *de bootstrap agregando*. Cada árvore em uma decisão de regressão floresta produz uma distribuição gaussiana por meio de previsão. A agregação é encontrar um gaussiano cujos dois primeiros momentos correspondem aos momentos da mistura de distribuições gaussianas dada pela combinação de todas as distribuições devolvidas por árvores individuais.

         Para obter mais informações, consulte a entrada da Wikipédia para [agregação bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicar**: Na replicação, cada árvore é treinada exatamente nos mesmos dados de entrada. A determinação de qual predicado dividido é usado para cada nó de árvore permanece aleatória e as árvores serão diversas.

         Para obter mais informações sobre o processo de treinamento com a opção **Replicar,** consulte [Florestas de Decisão para Visão computacional e Análise de Imagem Médica. Criminisi e J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/). .

3. Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**

    - **Parâmetro único**

      Se você souber como deseja configurar o modelo, é possível fornecer um conjunto específico de valores como argumentos. Você pode ter aprendido esses valores por experimentação ou os recebido como orientação.

    - **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais. 



4. Para **número de árvores de decisão,** indique o número total de árvores de decisão a serem criadas no conjunto. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumentará o tempo de treinamento.

    > [!TIP]
    > Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. se você quiser ver ou imprimir uma única árvore, você pode definir o valor como 1; no entanto, isso significa que apenas uma árvore será produzida (a árvore com o conjunto inicial de parâmetros) e não serão realizadas outras iterações.

5. Para **máxima profundidade das árvores de decisão,** digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, mas você corre o risco de algum tempo de treinamento com overfitting e maior.

6. Para **Número de divisões aleatórias por nó,** digite o número de divisões a serem usados ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

7. Para **O número mínimo de amostras por nó folha,** indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) em uma árvore.

     Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.


9. Conecte um conjunto de dados rotulado, selecione uma única coluna de etiqueta contendo não mais do que dois resultados e [conecte-se](./train-model.md)ao Modelo de Trem .

    - Se você definir criar a opção **de modo de treinador** para Um **Parâmetro,** treine o modelo usando o módulo [Modelo de Trem.](./train-model.md)

   

10. Envie o oleoduto.

### <a name="results"></a>Resultados

Após o treinamento ser concluído:

+ Para salvar um instantâneo do modelo treinado, selecione o módulo de treinamento e mude para a guia **Saídas** no painel direito. Clique no modelo de registro de **ícones**.  Você pode encontrar o modelo salvo como um módulo na árvore do módulo. 

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 