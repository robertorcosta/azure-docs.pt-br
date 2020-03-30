---
title: Análise no HDInsight Spark com PySpark, Scala – Processo de Ciência de Dados da Equipe
description: Exemplos do Processo de Ciência de Dados da Equipe que percorrem o uso de PySpark e Scala em uma Faísca Azure HDInsight.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864138"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>Passo a passo de ciência de dados do HDInsight Spark usando PySpark e Scala no Azure

Esses passo a passos usam PySpark e Scala em um cluster Azure Spark para executar a análise preditiva. Eles seguem as etapas descritas no Processo de Ciência de Dados de Equipe. Para obter uma visão geral do Processo de Ciência de Dados de Equipe, veja [Processo de Ciência de Dados](overview.md). Para obter uma visão geral do Spark no HDInsight, confira [Introdução ao Spark no HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Outras instruções passo a passo de ciência de dados que executam o Processo de ciência de dados de equipe estão agrupadas pela **plataforma** que elas usam. Consulte [Instruções passo a passo que executam o Processo de ciência de dados de equipe](walkthroughs.md) para obter uma discriminação desses exemplos.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Prever gorjetas de táxi usando PySpark no Azure Spark

Usando dados de táxi de Nova York, o passo a passo do [Use Spark no Azure HDInsight](spark-overview.md) prevê se uma gorjeta é paga e a faixa de valores esperados. Este exemplo usa o Processo de Ciência de Dados da equipe em um cenário usando um [cluster Azure HDInsight Spark](https://azure.microsoft.com/services/hdinsight/) para armazenar, explorar e apresentar dados de engenharia do conjunto de dados de viagem de táxi e tarifa selada publicamente disponíveis em NYC. Este tópico de visão geral usa um cluster HDInsight Spark e notebooks Jupyter PySpark. Esses notebooks mostram como explorar os dados e como criar e consumir modelos. Onotebook de exploração e modelagem de dados avançadas mostra como incluir validação cruzada, limpeza de hiperparâmetro e avaliação de modelo.

### <a name="data-exploration-and-modeling-with-spark"></a>Modelagem e exploração de dados com Spark 
Explore o conjunto de dados, crie, pontue e avalie os modelos de aprendizado de máquina usando o tópico [Criar modelos de regressão e classificação binária para dados com o kit de ferramentas MLlib do Spark](spark-data-exploration-modeling.md).

### <a name="model-consumption"></a>Consumo do modelo
Para saber como pontuar os modelos de classificação e regressão criados neste tópico, confira [Pontuar modelos de aprendizado de máquina criados no Spark](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Validação cruzada e limpeza de hiperparâmetro
Consulte [A exploração e modelagem avançada de dados com a Spark](spark-advanced-data-exploration-modeling.md) sobre como os modelos podem ser treinados usando validação cruzada e varredura de hiperparâmetros.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Prever gorjetas de táxi usando Scala no Azure Spark

O passe de Use Scala with Spark no passo a passo [do Azure](scala-walkthrough.md) prevê se uma gorjeta é paga e a faixa de valores esperada para ser paga. Ele mostra como usar o Scala para tarefas de Machine Learning supervisionado com a biblioteca de Machine Learning do Spark (MLlib) e pacotes SparkML em um cluster Spark do Azure HDInsight. Ele orienta você sobre as tarefas que constituem o [Processo de Ciência de Dados](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): ingestão e exploração de dados, visualização, engenharia de recursos, modelagem e consumo de modelos. Os modelos criados incluem a regressão logística e linear, florestas aleatórias e árvores aumentadas gradientes.


## <a name="next-steps"></a>Próximas etapas

Para obter uma visão geral do Processo de Ciência de Dados da Equipe, consulte [visão geral do Processo](overview.md)de Ciência de Dados da Equipe .

Para uma discussão sobre o ciclo de vida do Processo de Ciência de Dados da Equipe, consulte o ciclo de vida do Processo de [Ciência de Dados da Equipe](lifecycle.md). Este ciclo de vida descreve as etapas, do início ao fim, que os projetos geralmente seguem quando são executados. 

