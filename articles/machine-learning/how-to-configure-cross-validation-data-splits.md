---
title: Configurar a validação cruzada e as divisões de dados em experiências automatizadas de aprendizado de máquina
titleSuffix: Azure Machine Learning
description: Saiba como configurar a validação cruzada e as divisões de conjunto de informações para experiências automatizadas de aprendizado de máquina
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 900d5cd435a913c0859c862d176fd30130e0a079
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321489"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Configurar divisões de dados e validação cruzada no Machine Learning automatizado

Neste artigo, você aprende as diferentes opções para configurar as divisões de dados de treinamento/validação e a validação cruzada para o aprendizado de máquina automatizado, AutoML, experimentos.

Em Azure Machine Learning, quando você usa AutoML para criar vários modelos de ML, cada execução filho precisa validar o modelo relacionado calculando as métricas de qualidade para esse modelo, como exatidão ou AUC ponderado. Essas métricas são calculadas comparando as previsões feitas com cada modelo com rótulos reais das observações anteriores nos dados de validação. 

Os experimentos de AutoML executam automaticamente a validação de modelo. As seções a seguir descrevem como você pode personalizar ainda mais as configurações de validação com o [SDK Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py). 

Para uma experiência de baixo código ou sem código, confira [criar experiências automatizadas de aprendizado de máquina no Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> Atualmente, o estúdio dá suporte a divisões de dados de treinamento/validação e opções de validação cruzada, mas não oferece suporte à especificação de arquivos de dados individuais para seu conjunto de validação. 

## <a name="prerequisites"></a>Pré-requisitos

Para este artigo, você precisa,

* Um Workspace do Azure Machine Learning. Para criar o workspace, consulte [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

* Familiaridade com a configuração de um experimento de aprendizado de máquina automatizado com o SDK do Azure Machine Learning. Siga o [tutorial](tutorial-auto-train-models.md) ou as [instruções](how-to-configure-auto-train.md) para ver os padrões básicos de design de experimentos de machine learning automatizado.

* Uma compreensão da validação cruzada e dos dados de treinamento/validação são divididas como conceitos de ML. Para obter uma explicação de alto nível,

    * [Sobre os conjuntos de treinamento, validação e teste em Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Compreendendo a validação cruzada](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>Divisões de dados padrão e validação cruzada

Use o objeto [AutoMLConfig](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) para definir suas configurações de teste e treinamento. No trecho de código a seguir, observe que apenas os parâmetros necessários são definidos, que são os parâmetros `n_cross_validation` para `validation_ data` ou **não** estão incluídos.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Se você não especificar explicitamente um `validation_data` `n_cross_validation` parâmetro ou, o AutoML aplicará as técnicas padrão dependendo do número de linhas no único conjunto de registros `training_data` fornecido:

|&nbsp;Tamanho dos dados de treinamento &nbsp;| Técnica de validação |
|---|-----|
|**Mais &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A divisão de dados de treinamento/validação é aplicada. O padrão é levar 10% do conjunto de dados de treinamento inicial como o conjunto de validação. Por sua vez, esse conjunto de validação é usado para cálculo de métricas.
|**Menos &nbsp; de &nbsp; 20.000 &nbsp; linhas**| A abordagem de validação cruzada é aplicada. O número padrão de dobras depende do número de linhas. <br> **Se o conjunto de registros for menor que 1.000 linhas**, serão usadas 10 dobras. <br> **Se as linhas estiverem entre 1.000 e 20.000**, serão usadas três dobras.

## <a name="provide-validation-data"></a>Fornecer dados de validação

Nesse caso, você pode começar com um único arquivo de dados e dividi-lo em conjuntos de treinamento e validação ou pode fornecer um arquivo de dados separado para o conjunto de validação. De qualquer forma, o `validation_data` parâmetro em seu `AutoMLConfig` objeto atribui os dados a serem usados como seu conjunto de validação. Esse parâmetro só aceita conjuntos de dados na forma de um [conjunto](how-to-create-register-datasets.md) de dados Azure Machine Learning ou pandas dataframe.   

O exemplo de código a seguir define explicitamente qual parte dos dados fornecidos no `dataset` usar para treinamento e validação.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Fornecer tamanho do conjunto de validação

Nesse caso, apenas um único conjunto de um é fornecido para o experimento. Ou seja, o `validation_data` parâmetro **não** é especificado e o conjunto de e fornecido é atribuído ao `training_data` parâmetro.  Em seu `AutoMLConfig` objeto, você pode definir o `validation_size` parâmetro para manter uma parte dos dados de treinamento para validação. Isso significa que o conjunto de validação será dividido por AutoML do inicial `training_data` fornecido. Esse valor deve estar entre 0,0 e 1,0 não inclusivo (por exemplo, 0,2 significa que 20% dos dados são mantidos para dados de validação).

Consulte o exemplo de código a seguir:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>Definir o número de validações cruzadas

Para executar a validação cruzada, inclua o `n_cross_validations` parâmetro e defina-o como um valor. Esse parâmetro define quantas validações cruzadas executar, com base no mesmo número de dobras.

No código a seguir, são definidas cinco dobras para validação cruzada. Portanto, cinco treinamentos diferentes, cada um treinando usando 4/5 dos dados e cada validação usando 1/5 dos dados com uma dobra de controle diferente a cada vez.

Como resultado, as métricas são calculadas com a média das 5 métricas de validação.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Especificar partições de dados de validação cruzada personalizadas

Você também pode fornecer suas próprias dobras de dados de validação cruzada (CV). Isso é considerado um cenário mais avançado porque você está especificando quais colunas devem ser divididas e usadas para validação.  Inclua colunas de divisão de CV personalizadas em seus dados de treinamento e especifique quais colunas preenchendo os nomes de coluna no `cv_split_column_names` parâmetro. Cada coluna representa uma divisão de validação cruzada e é preenchida com valores inteiros 1 ou 0--em que 1 indica que a linha deve ser usada para treinamento e 0 indica que a linha deve ser usada para validação.

O trecho de código a seguir contém dados de marketing bancário com duas colunas de divisão de CV ' CV1 ' e ' CV2 '.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Para usar `cv_split_column_names` `training_data` o com `label_column_name` o e o, atualize seu Azure Machine Learning SDK do Python versão 1.6.0 ou posterior. Para versões anteriores do SDK, consulte usando `cv_splits_indices` , mas observe que ela é usada somente com `X` e `y` entrada de conjunto de dados. 

## <a name="next-steps"></a>Próximas etapas

* [Evitar dados desequilibrados e sobreajuste](concept-manage-ml-pitfalls.md).
* [Tutorial: usar o Machine Learning automatizado para prever as tarifas de táxi – seção dividir dados](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Como [treinar automaticamente um modelo de previsão de série temporal](how-to-auto-train-forecast.md).
