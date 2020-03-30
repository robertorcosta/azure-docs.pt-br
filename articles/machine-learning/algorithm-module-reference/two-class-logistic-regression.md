---
title: 'Regressão logística de duas classes: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão logística de duas classes no Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever dois (e apenas dois) resultados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455818"
---
# <a name="two-class-logistic-regression-module"></a>Módulo de regressão logística de duas classes

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de regressão logística que possa ser usado para prever dois (e apenas dois) resultados. 

A regressão logística é uma técnica estatística bem conhecida que é usada para modelar muitos tipos de problemas. Este algoritmo é um método *de aprendizagem supervisionado;*  portanto, você deve fornecer um conjunto de dados que já contém os resultados para treinar o modelo.  

### <a name="about-logistic-regression"></a>Sobre a regressão logística  

A regressão logística é um método bem conhecido em estatísticas que é usado para prever a probabilidade de um desfecho, e é especialmente popular para tarefas de classificação. O algoritmo prevê a probabilidade de ocorrência de um evento, encaixando dados em uma função logística.
  
Neste módulo, o algoritmo de classificação é otimizado para variáveis dicotômicas ou binárias. se você precisar classificar vários resultados, use o módulo [de regressão logística multiclasse.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Como configurar  

Para treinar este modelo, você deve fornecer um conjunto de dados que contenha uma etiqueta ou coluna de classe. Como este módulo é destinado a problemas de duas classes, a etiqueta ou coluna de classe deve conter exatamente dois valores. 

Por exemplo, a coluna de rótulos pode ser [Votada] com possíveis valores de "Sim" ou "Não". Ou, pode ser [Risco de Crédito], com possíveis valores de "Alto" ou "Baixo". 
  
1.  Adicione o módulo **de regressão logística de duas classes** ao seu pipeline.  
  
2.  Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.  

    -   **Intervalo de parâmetros**: Se você não tiver certeza dos melhores parâmetros, você pode encontrar os parâmetros ideais usando o módulo [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Você fornece algumas faixas de valores, e o treinador itera sobre várias combinações das configurações para determinar a combinação de valores que produz o melhor resultado.
  
3.  Para **tolerância de otimização,** especifique um valor limite a ser usado ao otimizar o modelo. Se a melhoria entre as iterações ficar abaixo do limite especificado, o algoritmo é considerado convergente em uma solução, e o treinamento pára.  
  
4.  Para **o peso de regularização L1** e peso de **regularização L2,** digite um valor a ser utilizado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.  
     *A regularização* é um método de prevenção de superajustamento, penalizando modelos com valores de coeficiente extremo. A regularização funciona adicionando a penalidade que está associada aos valores de coeficiente ao erro da hipótese. Assim, um modelo preciso com valores de coeficiente extremo seria mais penalizado, mas um modelo menos preciso com valores mais conservadores seria penalizado menos.  
  
     As regularizações L1 e L2 têm efeitos e usos diferentes.  
  
    -   L1 pode ser aplicado aos modelos esparsos, que é útil ao trabalhar com dados de grande dimensão.  
  
    -   Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  
  
     Este algoritmo suporta uma combinação linear de valores de regularização <code>x = L1</code> <code>y = L2</code>L1 <code>ax + by = c</code> e L2: isto é, se e , então define o vão linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre a regularização l1 e L2? O artigo a seguir fornece uma discussão sobre como a regularização L1 e L2 são diferentes e como elas afetam o encaixe do modelo, com amostras de código para regressão logística e modelos de rede neural: [Regularização L1 e L2 para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Diferentes combinações lineares dos termos L1 e L2 foram concebidas para modelos de regressão logística: por exemplo, [regularização líquida elástica](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerimos que você faça referência a essas combinações para definir uma combinação linear que seja eficaz em seu modelo.
      
5.  Para **o tamanho da memória para L-BFGS,** especifique a quantidade de memória a ser usada para otimização *L-BFGS.*  
  
     L-BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é conhecido por estimativa de parâmetro. Esse parâmetro indica o número de posições anteriores e gradientes a armazenar o cálculo da próxima etapa.  
  
     Esse parâmetro de otimização limita a quantidade de memória usada para calcular a próxima etapa e direção. Quando você especifica menos memória, o treinamento é mais rápido, mas menos preciso.  
  
6.  Para **sementes de número aleatório, digite**um valor inteiro. Definir um valor de semente é importante se você quiser que os resultados sejam reprodutíveis ao longo de várias corridas do mesmo pipeline.  
  
  
8. Adicione um conjunto de dados marcado ao pipeline e conecte um dos [módulos](module-reference.md)de treinamento .  
  
    -   Se você definir **Criar modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](./train-model.md)  
  
9. Envie o oleoduto.  
  
## <a name="results"></a>Resultados

Após o treinamento ser concluído:
 
  
+ Para fazer previsões sobre novos dados, use o modelo treinado e novos dados como entrada para o módulo [Score Model.](./score-model.md) 


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 