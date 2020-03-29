---
title: 'Floresta de Decisão de Duas Classes: Referência do Módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo De Duas Classes De Floresta de Decisão no Azure Machine Learning para criar um modelo de aprendizado de máquina baseado no algoritmo de florestas de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916703"
---
# <a name="two-class-decision-forest-module"></a>Módulo florestal de decisão de duas classes

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo de florestas de decisão.  

Florestas de decisão são modelos de conjunto saem rápidos e supervisionados. Este módulo é uma boa escolha se você quiser prever um alvo com um máximo de dois resultados. 

## <a name="understanding-decision-forests"></a>Entendendo florestas de decisão

Este algoritmo florestal de decisão é um método de aprendizagem de conjunto destinado a tarefas de classificação. Os métodos de conjunto baseiam-se no princípio geral de que, em vez de depender de um único modelo, você pode obter melhores resultados e um modelo mais generalizado, criando vários modelos relacionados e combinando-os de alguma forma. Em termos gerais, os modelos de ensemble fornecem melhor cobertura e precisão que árvores de decisão únicas. 

Existem muitas maneiras de criar modelos individuais e combiná-los em um conjunto. Esta implementação particular de uma floresta de decisão funciona construindo várias árvores de decisão e, em seguida, **votando** na classe de produção mais popular. A votação é um dos métodos mais conhecidos para gerar resultados em um modelo de conjunto. 

+ Muitas árvores de classificação individuais são criadas, usando todo o conjunto de dados, mas diferentes pontos de partida (geralmente randomizados). Isso difere da abordagem florestal aleatória, na qual as árvores de decisão individuais só podem usar alguma parte aleatória dos dados ou características.
+ Cada árvore na decisão da árvore florestal produz um histograma de freqüência não normalizado de rótulos. 
+ O processo de agregação soma esses histogramas e normaliza o resultado para obter as "probabilidades" de cada rótulo. 
+ As árvores que têm alta confiança de previsão terão um peso maior na decisão final do conjunto.

As árvores de decisão em geral têm muitas vantagens para as tarefas de classificação:
  
- Eles podem capturar limites de decisão não lineares.
- Você pode treinar e prever em muitos dados, pois eles são eficientes em computação e uso de memória.
- A seleção de recursos é integrada nos processos de treinamento e classificação.  
- As árvores podem acomodar dados barulhentos e muitos recursos.  
- São modelos não paramétricos, o que significa que podem lidar com dados com distribuições variadas. 

No entanto, árvores de decisão simples podem se encaixar demais nos dados, e são menos generalizáveis do que conjuntos de árvores.

Para obter mais informações, consulte [Florestas de Decisão](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Como configurar
  
1.  Adicione o módulo **Forest de decisão de duas classes** ao seu pipeline no Azure Machine Learning e abra o painel **Propriedades** do módulo. 

    Você pode encontrar o módulo em **Machine Learning**. Expandir **inicializar**e, em seguida, **classificação**.  
  
2.  Para **o método reamostragem,** escolha o método utilizado para criar as árvores individuais.  Você pode escolher entre **Ensacar** ou **replicar**.  
  
    -   **Ensaque**: Ensacado também é chamado *de bootstrap agregando*. Neste método, cada árvore é cultivada em uma nova amostra, criada por amostragem aleatória do conjunto de dados original com substituição até que você tenha um conjunto de dados do tamanho do original.  
  
         As saídas dos modelos são combinadas por *votação,* que é uma forma de agregação. Cada árvore em uma decisão de classificação floresta produz um histograma de freqüência não normalizado de rótulos. A agregação é para resumir esses histogramas e normalizar para obter as "probabilidades" para cada rótulo. Desta forma, as árvores que possuem alta confiança de previsão terão um peso maior na decisão final do conjunto.  
  
         Para obter mais informações, consulte a entrada da Wikipédia para agregação bootstrap.  
  
    -   **Replicar**: Na replicação, cada árvore é treinada exatamente nos mesmos dados de entrada. A determinação de qual predicado dividido é usado para cada nó de árvore permanece aleatória e as árvores serão diversas.   
  
3.  Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.

    -   **Intervalo de parâmetros**: Se você não tiver certeza dos melhores parâmetros, você pode encontrar os parâmetros ideais usando o módulo [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Você fornece algumas faixas de valores, e o treinador itera sobre várias combinações das configurações para determinar a combinação de valores que produz o melhor resultado.
  
4.  Para **Número de árvores de decisão,** digite o número máximo de árvores de decisão que podem ser criadas no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treinamento aumenta.  
  
    > [!NOTE]
    >  Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, você pode definir o valor como 1. No entanto, apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros) e não são realizadas outras iterações.
  
5.  Para **máxima profundidade das árvores de decisão,** digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, mas você corre o risco de algum tempo de treinamento com overfitting e maior.
  
6.  Para **Número de divisões aleatórias por nó,** digite o número de divisões a serem usados ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.
  
7.  Para **O número mínimo de amostras por nó folha,** indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) em uma árvore.
  
     Aumentando esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.  
  
8.  Selecione **a opção Permitir valores desconhecidos para** a opção de recursos categóricos para criar um grupo para valores desconhecidos nos conjuntos de treinamento ou validação. O modelo pode ser menos preciso para valores conhecidos, mas pode fornecer melhores previsões para novos valores (desconhecidos). 

     Se você desmarcar essa opção, o modelo poderá aceitar apenas os valores contidos nos dados de treinamento.
  
9. Anexar um conjunto de dados rotulado e um dos módulos de [treinamento:](module-reference.md)  
  
    -   Se você definir **Criar modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](./train-model.md)  
    
## <a name="results"></a>Resultados

Após o treinamento ser concluído:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo para pontuação, adicione o módulo **Score Model** a um pipeline.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 