---
title: 'Floresta de decisão multiclasse: Referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Floresta de Decisão multiclasse no Azure Machine Learning para criar um modelo de aprendizado de máquina baseado no algoritmo *da floresta de decisão.*
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477537"
---
# <a name="multiclass-decision-forest-module"></a>Módulo Floresta de Decisão Multiclasse

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de aprendizado de máquina baseado no algoritmo *da floresta de decisão.* Uma floresta de decisão é um modelo conjunto que rapidamente constrói uma série de árvores de decisão, enquanto aprende com dados marcados.

## <a name="more-about-decision-forests"></a>Mais sobre florestas de decisão

O algoritmo da floresta de decisão é um método de aprendizagem conjunto para classificação. O algoritmo funciona construindo várias árvores de decisão e, em seguida, *votando* na classe de saída mais popular. O voto é uma forma de agregação, na qual cada árvore em uma decisão de classificação produz um histograma de frequência não normalizado de rótulos. O processo de agregação soma esses histogramas e normaliza o resultado para obter as "probabilidades" de cada rótulo. As árvores que têm alta confiança de previsão têm um peso maior na decisão final do conjunto.

As árvores de decisão em geral são modelos não paramétricos, o que significa que suportam dados com distribuições variadas. Em cada árvore, uma sequência de testes simples é executada para cada classe, aumentando os níveis de uma estrutura de árvore até que um nó folha (decisão) seja atingido.

Árvores de decisão têm muitas vantagens:

+ Podem representar limites de decisão não lineares.
+ São eficientes no uso de memória e computação durante o treinamento e a previsão.
+ Elas executam a seleção de recursos integrados e classificação.
+ Elas são flexíveis na presença de recursos com ruídos.

O classificador de floresta de decisão no Azure Machine Learning consiste em um conjunto de árvores de decisão. Em termos gerais, os modelos de ensemble fornecem melhor cobertura e precisão que árvores de decisão únicas. Para obter mais informações, consulte [Árvores de decisão](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>Como configurar a Floresta de Decisão Multiclasse

1. Adicione o módulo **Floresta de Decisão multiclasse** ao seu pipeline no designer. Você pode encontrar este módulo em **Machine Learning,** **Initialize Model**e **Classification**.

2. Clique duas vezes no módulo para abrir o painel **Propriedades.**

3. Para **o método reamostragem,** escolha o método utilizado para criar as árvores individuais.  Você pode escolher entre bagging ou replicação.

    + **Ensaque**: Ensacado também é chamado *de bootstrap agregando*. Neste método, cada árvore é cultivada em uma nova amostra, criada por amostragem aleatória do conjunto de dados original com substituição até que você tenha um conjunto de dados do tamanho do original. As saídas dos modelos são combinadas por *votação,* que é uma forma de agregação. Para obter mais informações, consulte a entrada da Wikipédia para agregação bootstrap.

    + **Replicar**: Na replicação, cada árvore é treinada exatamente nos mesmos dados de entrada. A determinação de qual predicado dividido é usado para cada nó de árvore permanece aleatória, criando árvores diversas.

   

4. Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**

    + **Parâmetro Único :** Selecione esta opção se você souber como deseja configurar o modelo e forneça um conjunto de valores como argumentos.

    + **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.   

5. **Número de árvores de decisão**: Digite o número máximo de árvores de decisão que podem ser criadas no conjunto. Ao criar mais árvores de decisão, você pode potencialmente obter uma melhor cobertura, mas o tempo de treinamento pode aumentar.

    Esse valor também controla o número de árvores exibidas nos resultados, ao visualizar o modelo treinado. Para ver ou imprimir uma única árvore, você pode definir o valor como 1; no entanto, isso significa que apenas uma árvore pode ser produzida (a árvore com o conjunto inicial de parâmetros), e não são realizadas outras iterações.

6. **Profundidade máxima das árvores de decisão**: Digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, mas você corre o risco de algum tempo de treinamento com overfitting e maior.

7. **Número de divisões aleatórias por nó**: Digite o número de divisões a serem usados ao construir cada nó da árvore. Uma *divisão* significa que as características em cada nível da árvore (nó) são divididas aleatoriamente.

8. **Número mínimo de amostras por nó folha**: Indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) em uma árvore. Aumentando esse valor, você aumenta o limite para a criação de novas regras.

    Por exemplo, com o valor padrão de 1, um único caso pode causar uma nova regra a ser criada. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.



10. Conecte um conjunto de dados rotulado e um dos módulos de treinamento:

    + Se você definir **Criar modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](./train-model.md)

11. Envie o oleoduto.



## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 