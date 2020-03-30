---
title: 'Criar modelo Python: referência do módulo'
titleSuffix: Azure Machine Learning
description: Aprenda a usar o módulo Criar modelo Python no Azure Machine Learning para criar um módulo personalizado de modelagem ou processamento de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371810"
---
# <a name="create-python-model-module"></a>Criar módulo de modelo Python

Este artigo descreve um módulo no azure Machine Learning designer (visualização).

Aprenda a usar o módulo Criar modelo python para criar um modelo não treinado a partir de um script Python. Você pode basear o modelo em qualquer aluno que esteja incluído em um pacote Python no ambiente de designer de Machine Learning do Azure. 

Depois de criar o modelo, você pode usar o [Modelo de Trem](train-model.md) para treinar o modelo em um conjunto de dados, como qualquer outro aluno no Azure Machine Learning. O modelo treinado pode ser passado para [o Score Model](score-model.md) para fazer previsões. Em seguida, você pode salvar o modelo treinado e publicar o fluxo de trabalho de pontuação como um serviço web.

> [!WARNING]
> Atualmente, não é possível passar os resultados pontuais de um modelo Python para [Avaliar Modelo](evaluate-model.md). Se você precisar avaliar um modelo, você pode escrever um script Python personalizado e executá-lo usando o módulo [Execute Python Script.](execute-python-script.md)  


## <a name="configure-the-module"></a>Configure o módulo

O uso deste módulo requer conhecimento intermediário ou especializado de Python. O módulo suporta o uso de qualquer aluno incluído nos pacotes Python já instalados no Azure Machine Learning. Veja a lista de pacotes Python pré-instalados no [Execute Python Script](execute-python-script.md).
  

Este artigo mostra como usar **o Create Python Model** com um pipeline simples. Aqui está um diagrama do oleoduto:

![Diagrama de Criar Modelo Python](./media/module/create-python-model.png)

1. Selecione **Criar modelo python**e edite o script para implementar seu processo de modelagem ou gerenciamento de dados. Você pode basear o modelo em qualquer aluno que esteja incluído em um pacote Python no ambiente Azure Machine Learning.

   O seguinte código de amostra do classificador De Bayes ingênuo de duas classes usa o popular pacote *sklearn:*

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. Conecte o módulo **Criar modelo Python** que você acabou de criar para treinar **modelo** e modelo **de pontuação**.

1. Se você precisar avaliar o modelo, adicione um módulo [Execute Python Script](execute-python-script.md) e edite o script Python.

   O seguinte script é o código de avaliação da amostra:

   ```Python


   # The script MUST contain a function named azureml_main
   # which is the entry point for this module.

   # imports up here can be used to 
   import pandas as pd

   # The entry point function can contain up to two input arguments:
   #   Param<dataframe1>: a pandas.DataFrame
   #   Param<dataframe2>: a pandas.DataFrame
   def azureml_main(dataframe1 = None, dataframe2 = None):
    
       from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
       import pandas as pd
       import numpy as np
    
       scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
       ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
       ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
       probabilities = scores["probabilities"]
    
       accuracy, precision, recall, auc = \
       accuracy_score(ytrue, ypred),\
       precision_score(ytrue, ypred),\
       recall_score(ytrue, ypred),\
       roc_auc_score(ytrue, probabilities)
    
       metrics = pd.DataFrame();
       metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
       metrics["Value"] = [accuracy, precision, recall, auc]
    
       return metrics,

   ```

## <a name="next-steps"></a>Próximas etapas

Veja o [conjunto de módulos disponíveis](module-reference.md) para o Azure Machine Learning. 