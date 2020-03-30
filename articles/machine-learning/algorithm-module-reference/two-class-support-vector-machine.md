---
title: 'Máquina vetorial de suporte de duas classes: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo **de máquina vetorial de suporte de duas classes** no Azure Machine Learning para criar um modelo baseado no algoritmo da máquina vetorial de suporte.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455801"
---
# <a name="two-class-support-vector-machine-module"></a>Módulo da máquina vetorial de suporte de duas classes

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo baseado no algoritmo da máquina vetorial de suporte. 

As máquinas vetoriais de suporte (SVMs) são uma classe bem pesquisada de métodos de aprendizagem supervisionados. Esta implementação em particular é adequada à previsão de dois desfechos possíveis, com base em variáveis contínuas ou categóricas.

Depois de definir os parâmetros do modelo, treine o modelo usando os módulos de treinamento e forneça um *conjunto de dados marcado* que inclua uma coluna de etiqueta ou resultado.

## <a name="about-support-vector-machines"></a>Sobre as máquinas vetoriais de suporte

O suporte às máquinas de vetor estão entre os mais recentes dos algoritmos de aprendizado da máquina e modelos SVM e tem sido usado em muitos aplicativos, da recuperação de informações até a classificação de texto e imagem. SVMs podem ser usados tanto para tarefas de classificação quanto de regressão.

Este modelo SVM é um modelo de aprendizagem supervisionado que requer dados rotulados. No processo de treinamento, o algoritmo analisa dados de entrada e reconhece padrões em um espaço de recursos multidimensional chamado *hiperplano*.  Todos os exemplos de entrada são representados como pontos neste espaço, e são mapeados para categorias de saída de tal forma que as categorias sejam divididas pelo mais amplo e claro possível.

Para previsão, o algoritmo SVM atribui novos exemplos em uma categoria ou outra, mapeando-os nesse mesmo espaço. 

## <a name="how-to-configure"></a>Como configurar 

Para este tipo de modelo, recomenda-se que você normalize o conjunto de dados antes de usá-lo para treinar o classificador.
  
1.  Adicione o módulo **de máquina vetorial de suporte de duas classes** ao seu pipeline.  
  
2.  Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.  

    -   **Intervalo de parâmetros**: Se você não tiver certeza dos melhores parâmetros, você pode encontrar os parâmetros ideais usando o módulo [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Você fornece algumas faixas de valores, e o treinador itera sobre várias combinações das configurações para determinar a combinação de valores que produz o melhor resultado.

3.  Para **Número de iterações,** digite um número que denote o número de iterações usadas na construção do modelo.  
  
     Este parâmetro pode ser usado para controlar o compromisso entre a precisão e a velocidade de treinamento.  
  
4.  Para **Lambda,** digite um valor a ser usado como peso para a regularização L1.  
  
     Esse coeficiente de regularização pode ser usado para ajustar o modelo. Valores maiores penalizam modelos mais complexos.  
  
5.  Selecione a opção, **normalize recursos,** se quiser normalizar os recursos antes do treinamento.
  
     Se você aplicar a normalização, antes do treinamento, os pontos de dados são centrados na média e dimensionados para ter uma unidade de desvio padrão.
  
6.  Selecione a **opção, Projetize para a esfera da unidade,** para normalizar os coeficientes.
  
     Projetar valores para o espaço unitário significa que antes do treinamento, os pontos de dados são centrados em 0 e dimensionados para ter uma unidade de desvio padrão.
  
7.  Em **Sementes de número aleatório, digite**um valor inteiro para usar como semente se você quiser garantir a reprodutibilidade entre as corridas.  Caso contrário, um valor de relógio do sistema é usado como uma semente, o que pode resultar em resultados ligeiramente diferentes em todas as corridas.
  
9. Conecte um conjunto de dados rotulado e um dos módulos de [treinamento:](module-reference.md)
  
    -   Se você definir **Criar modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](train-model.md)
  
10. Envie o oleoduto.

## <a name="results"></a>Resultados

Após o treinamento ser concluído:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo para pontuação, adicione o módulo **Score Model** a um pipeline.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 