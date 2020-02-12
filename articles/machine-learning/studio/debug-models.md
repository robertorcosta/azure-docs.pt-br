---
title: Depurar seu modelo
titleSuffix: ML Studio (classic) - Azure
description: Como depurar erros produzidos pelos módulos modelo de treinamento e modelo de pontuação no Azure Machine Learning Studio (clássico).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.custom: seodec18
ms.date: 03/14/2017
ms.openlocfilehash: 320eba16511036df77dcdbb7ddb628eaa34b2450
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153546"
---
# <a name="debug-your-model-in-azure-machine-learning-studio-classic"></a>Depurar seu modelo no Azure Machine Learning Studio (clássico)

Ao executar um modelo, você poderá encontrar os seguintes erros:

* o módulo [modelo de treinamento][train-model] produz um erro 
* o módulo [modelo de Pontuação][score-model] produz resultados incorretos 

Este artigo explica as causas possíveis para esses erros.


## <a name="train-model-module-produces-an-error"></a>O módulo Modelo de Treinamento produz um erro

![image1](./media/debug-models/train_model-1.png)

O módulo [modelo de treinamento][train-model] espera duas entradas:

1. O tipo de modelo de aprendizado de máquina da coleção de modelos fornecida pelo Azure Machine Learning Studio (clássico).
2. Os dados de treinamento com uma coluna Rótulo especificada que especifica a variável a ser prevista (as outras colunas são consideradas Recursos).

Esse módulo pode produzir um erro nos seguintes casos:

1. A coluna Rótulo foi especificada incorretamente. Isso pode ocorrer se mais de uma coluna é selecionada como o Rótulo ou um índice de coluna incorreto está selecionado. Por exemplo, o segundo caso será aplicável se um índice de coluna 30 for usado com um conjunto de dados de entrada que tem apenas 25 colunas.

2. O conjunto de dados não contém nenhuma coluna de Recurso. Por exemplo, se o conjunto de dados de entrada tivesse apenas uma coluna, que é marcada como a coluna Rótulo, não haveria nenhum recurso com o qual compilar o modelo. Nesse caso, o módulo [modelo de treinamento][train-model] produz um erro.

3. O conjunto de dados de entrada (Recursos ou Rótulo) contém Infinito como um valor.

## <a name="score-model-module-produces-incorrect-results"></a>O Módulo Modelo de Pontuação produz resultados incorretos

![image2](./media/debug-models/train_test-2.png)

Em um experimento de treinamento/teste típico para aprendizado supervisionado, o módulo [dividir dados][split] divide o conjunto de dado original em duas partes: uma parte é usada para treinar o modelo e uma parte é usada para pontuar o desempenho do modelo treinado. O modelo treinado, em seguida, é usado para pontuar os dados de teste e, depois, os resultados são avaliados para determinar a precisão do modelo.

O módulo [modelo de Pontuação][score-model] requer duas entradas:

1. Uma saída de modelo treinado do módulo [treinar modelo][train-model] .
2. Um conjunto de dados de pontuação diferente do conjunto de dados usado para treinar o modelo.

É possível que, embora o experimento tenha sucesso, o módulo [modelo de Pontuação][score-model] produza resultados incorretos. Vários cenários podem causar esse problema:

1. Se o rótulo especificado for categórico e um modelo de regressão for treinado nos dados, uma saída incorreta será produzida pelo módulo [modelo de Pontuação][score-model] . Isso ocorre porque a regressão requer uma variável de resposta contínua. Nesse caso, deve ser mais adequado usar um modelo de classificação. 

2. Da mesma forma, se um modelo de classificação for treinado em um conjunto de dados com números de ponto flutuante na coluna Rótulo, ele pode produzir resultados indesejáveis. Isso ocorre porque a classificação exige uma variável de resposta discreta que permite somente valores que variam em um conjunto finito e pequeno de classes.

3. Se o conjunto de pontos de pontuação não contiver todos os recursos usados para treinar o modelo, o [modelo de Pontuação][score-model] produzirá um erro.

4. Se uma linha no conjunto de resultados de Pontuação contiver um valor ausente ou um valor infinito para qualquer um de seus recursos, o [modelo de Pontuação][score-model] não produzirá nenhuma saída correspondente a essa linha.

5. O [modelo de Pontuação][score-model] pode produzir saídas idênticas para todas as linhas no conjunto de registros de pontuação. Isso pode ocorrer, por exemplo, ao tentar fazer a classificação usando Florestas de Decisão se o número mínimo de amostras por nó folha for escolhido como sendo maior que o número de exemplos de treinamento disponível.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/

