---
title: 'Rede Neural Multiclasse: Referência de Módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de Rede Neural Multiclasse no Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um alvo que tenha vários valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920409"
---
# <a name="multiclass-neural-network-module"></a>Módulo de rede neural multiclasse

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de rede neural que possa ser usado para prever um destino que tenha vários valores. 

Por exemplo, redes neurais desse tipo podem ser usadas em tarefas complexas de visão computacional, como reconhecimento de dígitos ou letras, classificação de documentos e reconhecimento de padrões.

A classificação usando redes neurais é um método de aprendizagem supervisionado e, portanto, requer um *conjunto de dados marcado* que inclua uma coluna de rótulos.

Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [o Train Model](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  

## <a name="about-neural-networks"></a>Sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um gráfico acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída, você pode inserir várias camadas ocultas. Mais tarefas de previsão podem ser feitas facilmente com apenas uma ou algumas camadas ocultas. No entanto, pesquisas recentes mostraram que redes neurais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida com o treinamento da rede neural sobre os dados de entrada. A direção do gráfico prossegue das entradas através da camada oculta e da camada de saída. Todos os nós em uma camada são conectados pelas bordas ponderadas aos nós na camada seguinte.

Para calcular a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nódulos da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.

## <a name="configure-multiclass-neural-network"></a>Configurar rede neural multiclasse

1. Adicione o módulo **de rede neural multiclasse** ao seu pipeline no designer. Você pode encontrar este módulo em **Machine Learning**, **Initialize**, na categoria **Classificação.**

2. **Criar modo treinador**: Use esta opção para especificar como você quer que o modelo seja treinado:

    - **Parâmetro Único :** Escolha esta opção se você já sabe como deseja configurar o modelo.

    - **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.  

3. **Especificação da camada oculta**: Selecione o tipo de arquitetura de rede a ser criada.

    - **Caso totalmente conectado**: Selecione esta opção para criar um modelo usando a arquitetura de rede neural padrão. Para modelos de rede neural multiclasse, os padrões são os seguintes:

        - Uma camada oculta
        - A camada de saída está totalmente conectada à camada oculta.
        - A camada oculta está totalmente conectada à camada de entrada.
        - O número de nós na camada de entrada é determinado pelo número de recursos nos dados de treinamento.
        - O número de nódulos na camada oculta pode ser definido pelo usuário. O padrão é 100.
        - O número de nós na camada de saída depende do número de classes.
  
   

5. **Número de nós ocultos**: Esta opção permite personalizar o número de nódulos ocultos na arquitetura padrão. Digite o número de nódulos ocultos. O padrão é uma camada oculta com 100 nós.

6. **A taxa de aprendizagem**: Defina o tamanho da etapa dada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer com que o modelo convergir mais rápido, mas pode superar minima local.

7. **Número de iterações de aprendizagem**: Especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

8. **O diâmetro inicial dos pesos de aprendizagem**: Especifique os pesos do nó no início do processo de aprendizagem.

9. **O momento**: Especifique um peso para aplicar durante o aprendizado aos nódulos de iterações anteriores.
  
11. **Exemplos de embaralhar**: Selecione esta opção para embaralhar casos entre iterações.

    Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem cada vez que você executar o pipeline.

12. **Semente de número aleatório**: Digite um valor para usar como semente, se você quiser garantir a repetibilidade em todas as corridas do mesmo pipeline.

14. Conecte um conjunto de dados de treinamento e um dos módulos de [treinamento:](module-reference.md) 

    - Se você definir **Criar o modo treinador** para um único **parâmetro,** use [O Modelo de Trem](train-model.md).  
  

## <a name="results"></a>Resultados

Após o treinamento ser concluído:

- Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 