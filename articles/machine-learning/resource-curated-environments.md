---
title: Ambientes organizados
titleSuffix: Azure Machine Learning
description: Coleção de ambientes organizados disponíveis no Azure Machine Learning
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 9d0964fb28451abe0a77276d6f9d605fcaac2514
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86156410"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning ambientes organizados

Este artigo lista os ambientes organizados em Azure Machine Learning, e os pacotes e canais pré-instalados neles.

> [!NOTE]
> Essa lista é atualizada a partir de junho de 2020. Use o SDK do Python para obter a lista mais atualizada. Para obter mais informações, consulte o [artigo ambientes](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azure-automl"></a>Azure AutoML

- [AutoML do AzureML](#azureml-automl)
- [GPU AutoML do AzureML](#azureml-automl-gpu)
- [DNN AutoML do AzureML](#azureml-automl-dnn)
- [AutoML DNN da GPU do AzureML](#azureml-automl-dnn-gpu)
- [GPU de visão DNN do Azure AutoML](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AutoML do AzureML

canais de pacotes:
- Anaconda
- Conda-forjar
- pytorch

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-explique-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferência-esquema
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- pandas>= 0.21.0, <= 0.23.4
- py-xgboost<= 0,90
- fbprophet = = 0,5
- setuptools-git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>GPU AutoML do AzureML

canais de pacotes:
- Anaconda
- Conda-forjar
- pytorch

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-explique-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferência-esquema
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- numpy>= 1.16.0, <= 1.16.2
- pandas>= 0.21.0, <= 0.23.4
- fbprophet = = 0,5
- setuptools-git
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>DNN AutoML do AzureML

canais de pacotes:
- Anaconda
- Conda-forjar
- pytorch

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-explique-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferência-esquema
  - pytorch-transformadores = = 1.0.0
  - Spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- py-xgboost<= 0,90
- fbprophet = = 0,5
- setuptools-git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>AutoML DNN da GPU do AzureML

canais de pacotes:
- Anaconda
- Conda-forjar
- pytorch

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-explique-Model = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - inferência-esquema
  - horovod = = 0.19.4
  - pytorch-transformadores = = 1.0.0
  - Spacy = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - py-cpuinfo = = 5.0.0
- pandas>= 0.21.0, <= 0.23.4
- numpy>= 1.16.0, <= 1.16.2
- fbprophet = = 0,5
- setuptools-git
- pytorch = 1.4.0
- cudatoolkit = 10.0.130
- psutil>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>GPU de visão DNN do AzureML AutoML

depend
- Python = 3.7
- pontos
  - azureml-automl-Core = = 1.8.0
  - azureml-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-explique-Model = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - azureml-Train-automl = = 1.8.0
  - azureml-contrib-DataSet = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml-pipeline-Steps = = 1.8.0
  - azureml-pipeline = = 1.8.0
  - azureml-Train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-contrib-automl-DNN-Vision = = 1.8.0

## <a name="azure-ml-designer"></a>Designer de ML do Azure

- [Designer do AzureML](#azureml-designer)
- [CV do designer do AzureML](#azureml-designer-cv)
- [Transformação de CV do designer do AzureML](#azureml-designer-cv-transform)
- [E/s do designer do AzureML](#azureml-designer-io)
- [NLP do designer do AzureML](#azureml-designer-nlp)
- [PyTorch do designer do AzureML](#azureml-designer-pytorch)
- [Treinamento de PyTorch do designer do AzureML](#azureml-designer-pytorch-train)
- [O designer do AzureML R](#azureml-designer-r)
- [Recomendador do AzureML designer](#azureml-designer-recommender)
- [Pontuação do designer do AzureML](#azureml-designer-score)
- [Transformação do designer do AzureML](#azureml-designer-transform)

### <a name="azureml-designer"></a>Designer do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.8
- scikit-surpresa = 1.0.6
- pontos
  - azureml-designer-clássico-modules = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Spacy = = 2.1.7

### <a name="azureml-designer-cv"></a>CV do designer do AzureML

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-designer-CV-modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>Transformação de CV do designer do AzureML

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-designer-CV-Modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>E/s do designer do AzureML

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-dataprep>= 1,6
  - azureml-designer-dataio-modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>NLP do designer do AzureML

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-designer-clássico-modules = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - Spacy = = 2.1.7

### <a name="azureml-designer-pytorch"></a>PyTorch do designer do AzureML

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>Treinamento de PyTorch do designer do AzureML

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-r"></a>O designer do AzureML R

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.8
- r-circunflexo = 6.0
- r-catools = 1.17.1
- r-cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-Forcats = 0.5.0
- r-previsão = 8.12
- r-glmnet = 2.0
- r-igraph = 1.2.4
- r-matriz = 1.2
- r-mclust = 5.4.6
- r-mgcv = 1.8
- r-nlme = 3.1
- r-nnet = 7.3
- r-plyr = 1.8.6
- r-randomforest = 4.6
- r-reticulate = 1.12
- r-rocr = 1,0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-StringType = 1.4.0
- r-tidyverse = 1.2.1
- r-timedate = 3043.102
- r-Tseries = 0,10
- r = 3.5.1
- pontos
  - azureml-designer-clássico-modules = = 0.0.124

### <a name="azureml-designer-recommender"></a>Recomendador do AzureML designer

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-designer-recomendável-modules = = 0.0.5

### <a name="azureml-designer-score"></a>Pontuação do designer do AzureML

canais de pacotes:
- padrões

depend
- Conda
- Python = 3.6.8
- pontos
  - azureml-designer-Score-modules = = 0.0.5

### <a name="azureml-designer-transform"></a>Transformação do designer do AzureML

canais de pacotes:
- padrões

depend
- Python = 3.6.8
- pontos
  - azureml-designer-DataTransform-modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>ForecastDNN hyperdrive do AzureML

depend
- Python = 3.7
- pontos
  - azureml-Core = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1
  - azureml-contrib-automl-DNN-previsão = = 1.8.0

## <a name="azureml-minimal"></a>Mínimo do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0

## <a name="azureml-sidecar"></a>Sidecar do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2

## <a name="azureml-tutorial"></a>Tutorial do AzureML

canais de pacotes:
- Anaconda
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - azureml-widgets = = 1.8.0
  - azureml-pipeline-Core = = 1.8.0
  - azureml-pipeline-Steps = = 1.8.0
  - azureml-opendatasets = = 1.8.0
  - azureml-automl-Core = = 1.8.0
  - azureml-automl-Runtime = = 1.8.0
  - azureml-Train-automl-Client = = 1.8.0
  - azureml-Train-automl-Runtime = = 1.8.0. post1  
  - azureml-Train-automl = = 1.8.0
  - azureml-Train = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-interpret = = 1.8.0
  - azureml-tensorboard = = 1.8.0
  - azureml-mlflow = = 1.8.0
  - mlflow
  - sklearn-pandas
- pandas
- numpy
- tqdm
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>8.8.0 VowpalWabbit do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-dataprep [fusível, pandas]

## <a name="dask"></a>Dask

- [CPU Dask do AzureML](#azureml-dask-cpu)
- [GPU Dask do AzureML](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>CPU Dask do AzureML

canais de pacotes:
- Conda-forjar
- pytorch
- padrões

depend
- Python = 3.6.9
- pontos
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - Dask [concluído]
  - Dask-ml [concluído]
  - distribuídas
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notebook
  - pyarrow

### <a name="azureml-dask-gpu"></a>GPU Dask do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.9
- pontos
  - azureml-padrões = = 1.8.0
  - adlfs
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - Dask [concluído]
  - Dask-ml [concluído]
  - distribuídas
  - fastparquet
  - fsspec
  - joblib
  - jupyterlab
  - lz4
  - mpi4py
  - notebook
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [CPU do encadeamento do AzureML 5.1.0](#azureml-chainer-510-cpu)
- [GPU do sequenciador do AzureML 5.1.0](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>CPU do encadeamento do AzureML 5.1.0

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Chainr = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>GPU do sequenciador do AzureML 5.1.0

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Chainr = = 5.1.0
  - Cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>PySpark do AzureML MmlSpark 0,15

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [CPU do AzureML PyTorch 1,0](#azureml-pytorch-10-cpu)
- [GPU PyTorch 1,0 do AzureML](#azureml-pytorch-10-gpu)
- [CPU do AzureML PyTorch 1,1](#azureml-pytorch-11-cpu)
- [GPU PyTorch 1,1 do AzureML](#azureml-pytorch-11-gpu)
- [CPU do AzureML PyTorch 1,2](#azureml-pytorch-12-cpu)
- [GPU PyTorch 1,2 do AzureML](#azureml-pytorch-12-gpu)
- [CPU do AzureML PyTorch 1,3](#azureml-pytorch-13-cpu)
- [GPU PyTorch 1,3 do AzureML](#azureml-pytorch-13-gpu)
- [CPU do AzureML PyTorch 1,4](#azureml-pytorch-14-cpu)
- [GPU PyTorch 1,4 do AzureML](#azureml-pytorch-14-gpu)
- [CPU do AzureML PyTorch 1,5](#azureml-pytorch-15-cpu)
- [GPU PyTorch 1,5 do AzureML](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>CPU do AzureML PyTorch 1,0

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1,0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>GPU PyTorch 1,0 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1,0
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>PyTorch de CPU do AzureML 1,1

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>GPU PyTorch 1,1 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.1
  - torchvision = = 0.2.1
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>CPU do AzureML PyTorch 1,2

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>GPU PyTorch 1,2 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.2
  - torchvision = = 0.4.0
  - MKL = = 2018.0.3
  - horovod = = 0.16.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>CPU do AzureML PyTorch 1,3

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>GPU PyTorch 1,3 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.3
  - torchvision = = 0.4.1
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>CPU do AzureML PyTorch 1,4

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>GPU PyTorch 1,4 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.4.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.18.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>CPU do AzureML PyTorch 1,5

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>GPU PyTorch 1,5 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - Torch = = 1.5.0
  - torchvision = = 0.5.0
  - MKL = = 2018.0.3
  - horovod = = 0.19.1
  - tensorboard==1.14.0
  - futuro = = 0.17.1

## <a name="scikit-learn"></a>Saiba mais sobre o Scikit

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit-saiba 0.20.3

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - scikit-learn==0.20.3
  - SciPy = = 1.2.1
  - joblib = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [CPU do Azure ML TensorFlow 1,10](#azureml-tensorflow-110-cpu)
- [GPU do Azure ML TensorFlow 1,10](#azureml-tensorflow-110-gpu)
- [CPU do Azure ML TensorFlow 1,12](#azureml-tensorflow-112-cpu)
- [GPU do Azure ML TensorFlow 1,12](#azureml-tensorflow-112-gpu)
- [CPU do Azure ML TensorFlow 1,13](#azureml-tensorflow-113-cpu)
- [GPU do Azure ML TensorFlow 1,13](#azureml-tensorflow-113-gpu)
- [CPU do Azure ML TensorFlow 2,0](#azureml-tensorflow-20-cpu)
- [GPU do Azure ML TensorFlow 2,0](#azureml-tensorflow-20-gpu)
- [CPU do Azure ML TensorFlow 2,1](#azureml-tensorflow-21-cpu)
- [GPU do Azure ML TensorFlow 2,1](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>CPU do AzureML TensorFlow 1,10

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1,10
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>GPU TensorFlow 1,10 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 1.10.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>CPU do AzureML TensorFlow 1,12

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1.12
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>GPU TensorFlow 1,12 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 1.12.0
  - horovod = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>TensorFlow de CPU do AzureML 1,13

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>GPU TensorFlow 1,13 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 1.13.1
  - horovod = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>CPU do AzureML TensorFlow 2,0

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 2.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>GPU TensorFlow 2,0 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 2.0.0
  - horovod = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>CPU do AzureML TensorFlow 2,1

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow = = 2.1.0
  - horovod = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>GPU TensorFlow 2,1 do AzureML

canais de pacotes:
- Conda-forjar

depend
- Python = 3.6.2
- pontos
  - azureml-Core = = 1.8.0
  - azureml-padrões = = 1.8.0
  - azureml-telemetria = = 1.8.0
  - azureml-Train-restclients-hyperdrive = = 1.8.0
  - azureml-Train-Core = = 1.8.0
  - tensorflow-GPU = = 2.1.0
  - horovod = = 0.19.1

