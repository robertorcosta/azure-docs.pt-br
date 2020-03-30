---
title: 'Regressão da rede neural: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo de regressão de rede neural no Azure Machine Learning para criar um modelo de regressão usando um algoritmo de rede neural personalizável..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a591badab29a1669d109f01f8a93732704d2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456091"
---
# <a name="neural-network-regression-module"></a>Módulo de regressão de rede neural

*Cria um modelo de regressão usando um algoritmo de rede neural*  
  
 Categoria: Machine Learning / Modelo de Inicialização / Regressão
  
## <a name="module-overview"></a>Visão geral do módulo  

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Use este módulo para criar um modelo de regressão usando um algoritmo de rede neural personalizável.
  
 Embora as redes neurais sejam amplamente conhecidas para uso em problemas complexos de aprendizado e modelagem, como reconhecimento de imagem, elas são facilmente adaptadas para problemas de regressão. Qualquer classe de modelos estatísticos pode ser chamada de rede neural se ela usar pesos adaptáveis e puder aproximar funções não lineares de suas entradas. Assim, a regressão de rede neural é adequada para onde um modelo de regressão mais tradicional não tiver uma solução.
  
 A regressão da rede neural é um método de aprendizagem supervisionado e, portanto, requer um *conjunto de dados marcado,* que inclui uma coluna de rótulos. Como um modelo de regressão prevê um valor numérico, a coluna de rótulo deve ser um tipo de dado numérico.  
  
 Você pode treinar o modelo fornecendo o modelo e o conjunto de dados marcado como uma entrada para [o Train Model](./train-model.md). O modelo treinado pode então ser usado para prever valores para os novos exemplos de entrada.  
  
## <a name="configure-neural-network-regression"></a>Configurar regressão de rede neural 

Redes neurais podem ser extensivamente personalizadas. Esta seção descreve como criar um modelo usando dois métodos:
  
+ [Crie um modelo de rede neural usando a arquitetura padrão](#bkmk_DefaultArchitecture)  
  
    Se você aceitar a arquitetura de rede neural padrão, use o painel **Propriedades** para definir parâmetros que controlam o comportamento da rede neural, como o número de nódulos na camada oculta, taxa de aprendizado e normalização.

    Comece aqui se você é novo em redes neurais. O módulo suporta muitas personalizações, bem como ajuste de modelo, sem profundo conhecimento de redes neurais. 

+ Defina uma arquitetura personalizada para uma rede neural 

    Use esta opção se quiser adicionar camadas ocultas extras ou personalizar totalmente a arquitetura da rede, suas conexões e funções de ativação.
    
    Esta opção é melhor se você já estiver um pouco familiarizado com redes neurais. Você usa a linguagem Net# para definir a arquitetura de rede.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Crie um modelo de rede neural usando a arquitetura padrão

1.  Adicione o módulo **de regressão da rede neural** ao seu pipeline no designer. Você pode encontrar este módulo em **Machine Learning**, **Initialize**, na categoria **Regressão.** 
  
2. Indique como deseja que o modelo seja treinado, definindo a opção **Criar modo treinador.**  
  
    -   **Parâmetro Único :** Escolha esta opção se você já sabe como deseja configurar o modelo.

    -   **Intervalo de parâmetros**: Selecione esta opção se você não tiver certeza dos melhores parâmetros e deseja executar uma varredura de parâmetros. Selecione uma gama de valores para iterar, e o [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterates sobre todas as combinações possíveis das configurações fornecidas para determinar os hiperparâmetros que produzem os resultados ideais.   

3.  Na **especificação da camada oculta,** selecione **O estojo Totalmente conectado**. Essa opção cria um modelo usando a arquitetura de rede neural padrão, que para um modelo de regressão de rede neural, tem esses atributos:  
  
    + A rede tem exatamente uma camada oculta.
    + A camada de saída é totalmente conectada à camada oculta e a camada oculta é totalmente conectada à camada de entrada.
    + O número de nós na camada oculta pode ser definido pelo usuário (o valor padrão é 100).  
  
    Como o número de nós na camada de entrada é determinado pelo número de características nos dados de treinamento, em um modelo de regressão pode haver apenas um nó na camada de saída.  
  
4. Para **número de nódulos ocultos,** digite o número de nódulos ocultos. O padrão é uma camada oculta com 100 nós. (Esta opção não está disponível se você definir uma arquitetura personalizada usando o Net#.)
  
5.  Para **taxa de aprendizagem**, digite um valor que define o passo dado em cada iteração, antes da correção. Um valor maior para a taxa de aprendizagem pode fazer o modelo convergir mais rapidamente, mas com o risco mínimo de errar o alvo verdadeiro.

6.  Para **número de iterações de aprendizagem,** especifique o número máximo de vezes que o algoritmo processa os casos de treinamento.


8.  Para **o momento**, digite um valor a ser aplicado durante o aprendizado como um peso sobre os nódulos de iterações anteriores.

10. Selecione a **opção, Exemplos de Shuffle,** para alterar a ordem dos casos entre iterações. Se você desmarcar essa opção, os casos serão processados exatamente na mesma ordem cada vez que você executar o pipeline.
  
11. Para **sementes de número aleatório,** você pode digitar opcionalmente um valor para usar como a semente. Especificar um valor de semente é útil quando você deseja garantir a repetibilidade em todas as corridas do mesmo pipeline.
  
13. Conecte um conjunto de dados de treinamento e um dos módulos de [treinamento:](module-reference.md) 
  
    -   Se você definir **Criar o modo treinador** para um único **parâmetro,** use [O Modelo de Trem](./train-model.md).  
  
   
14. Envie o oleoduto.  

## <a name="results"></a>Resultados

Após o treinamento ser concluído:

- Para salvar um instantâneo do modelo treinado, selecione a guia **Saídas** no painel direito do módulo **modelo Train.** Selecione o ícone **do conjunto de dados Registrar** para salvar o modelo como um módulo reutilizável.

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 