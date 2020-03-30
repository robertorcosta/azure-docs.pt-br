---
title: 'Rede neural de duas classes: referência de módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de rede neural de duas classes no Azure Machine Learning para criar um modelo de rede neural que pode ser usado para prever um alvo que tenha apenas dois valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a5cf2b210781d43ddd1a7aa87736a53df222cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477384"
---
# <a name="two-class-neural-network-module"></a>Módulo de rede neural de duas classes

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de rede neural que possa ser usado para prever um destino que tenha somente dois valores.

A classificação usando redes neurais é um método de aprendizagem supervisionado e, portanto, requer um *conjunto de dados marcado,* que inclui uma coluna de rótulos. Por exemplo, você poderia usar este modelo de rede neural para prever resultados binários, como se um paciente tem ou não uma determinada doença, ou se uma máquina provavelmente falhará dentro de uma janela de tempo especificada.  

Depois de definir o modelo, treine-o fornecendo um conjunto de dados marcado e o modelo como uma entrada para [o Modelo de Trem](./train-model.md). O modelo treinado pode então ser usado para prever valores para novas entradas.

### <a name="more-about-neural-networks"></a>Mais sobre redes neurais

Uma rede neural é um conjunto de camadas interconectadas. As entradas são a primeira camada e são conectadas a uma camada de saída por um gráfico acíclico composto por bordas e nós ponderados.

Entre as camadas de entrada e saída, você pode inserir várias camadas ocultas. Mais tarefas de previsão podem ser feitas facilmente com apenas uma ou algumas camadas ocultas. No entanto, pesquisas recentes mostraram que redes neurais profundas (DNN) com muitas camadas podem ser eficazes em tarefas complexas, como reconhecimento de imagem ou fala. As camadas sucessivas são usadas para modelar níveis crescentes de profundidade semântica.

A relação entre entradas e saídas é aprendida com o treinamento da rede neural sobre os dados de entrada. A direção do gráfico prossegue das entradas através da camada oculta e da camada de saída. Todos os nós em uma camada são conectados pelas bordas ponderadas aos nós na camada seguinte.

Para calcular a saída da rede para uma entrada específica, um valor é calculado em cada nó nas camadas ocultas e na camada de saída. O valor é definido calculando a soma ponderada dos valores dos nódulos da camada anterior. Uma função de ativação é então aplicada a essa soma ponderada.
  
## <a name="how-to-configure"></a>Como configurar

1.  Adicione o módulo **de rede neural de duas classes** ao seu pipeline. Você pode encontrar este módulo em **Machine Learning**, **Initialize**, na categoria **Classificação.**  
  
2.  Especifique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único :** Escolha esta opção se você já sabe como deseja configurar o modelo.

    -   **Intervalo de parâmetros**: Se você não tiver certeza dos melhores parâmetros, você pode encontrar os parâmetros ideais usando o módulo [Tune Model Hyperparameters.](tune-model-hyperparameters.md) Você fornece algumas faixas de valores, e o treinador itera sobre várias combinações das configurações para determinar a combinação de valores que produz o melhor resultado.  

3.  Para **especificação de camada oculta,** selecione o tipo de arquitetura de rede a ser criada.  
  
    -   **Caso totalmente conectado**: Usa a arquitetura de rede neural padrão, definida para redes neurais de duas classes da seguinte forma:
  
        -   Tem uma camada escondida.
  
        -   A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
  
        -   O número de nódulos na camada de entrada é igual ao número de recursos nos dados de treinamento.
  
        -   O número de nós na camada oculta é definido pelo usuário. O valor padrão é 100.
  
        -   O número de nódulos é igual ao número de classes. Para uma rede neural de duas classes, isso significa que todas as entradas devem mapear para um dos dois nós na camada de saída.

5.  Para **taxa de aprendizagem,** defina o tamanho da etapa dada em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer o modelo convergir mais rapidamente, mas com o risco mínimo de errar o alvo verdadeiro.

6.  Para **número de iterações de aprendizagem,** especifique o número máximo de vezes que o algoritmo deve processar os casos de treinamento.

7.  Para **o diâmetro inicial dos pesos de aprendizagem,** especifique os pesos do nó no início do processo de aprendizagem.

8.  Para **o momento,** especifique um peso para aplicar durante o aprendizado de nódulos de iterações anteriores  

10. Selecione a opção **Exemplos Shuffle** para embaralhar casos entre iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem cada vez que você executar o pipeline.
  
11. Para **sementes de número aleatório, digite**um valor para usar como semente.
  
     Especificar um valor de semente é útil quando você deseja garantir a repetibilidade em todas as corridas do mesmo pipeline.  Caso contrário, um valor de relógio do sistema é usado como a semente, o que pode causar resultados ligeiramente diferentes cada vez que você executar o pipeline.
  
13. Adicione um conjunto de dados marcado ao pipeline e conecte um dos [módulos](module-reference.md)de treinamento .  
  
    -   Se você definir **Criar modo de treinador** para Um **Parâmetro,** use o módulo [Modelo de Trem.](train-model.md)  
  
14. Envie o oleoduto.

## <a name="results"></a>Resultados

Após o treinamento ser concluído:

+ Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.

+ Para usar o modelo para pontuação, adicione o módulo **Score Model** a um pipeline.


## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 
