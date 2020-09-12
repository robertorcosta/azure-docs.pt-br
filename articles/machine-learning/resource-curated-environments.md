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
ms.topic: reference
ms.date: 09/03/2020
ms.openlocfilehash: 4ae96976f81aab9a0949594551c82d3a3fec4f0a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662085"
---
# <a name="azure-machine-learning-curated-environments"></a>Azure Machine Learning ambientes organizados

Este artigo lista os ambientes organizados em Azure Machine Learning, e os pacotes e canais pré-instalados neles. Os ambientes organizados são fornecidos por Azure Machine Learning e estão disponíveis em seu espaço de trabalho por padrão. Eles são apoiados por imagens do Docker armazenadas em cache, reduzindo o custo de preparação da execução e permitindo um tempo de implantação mais rápido. Use esses ambientes para começar rapidamente com várias estruturas do Machine Learning.

> [!NOTE]
> Essa lista é atualizada a partir de setembro de 2020. Use o SDK do Python para obter a lista mais atualizada. Para obter mais informações, consulte o [artigo ambientes](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azureml-automl"></a>AzureML-AutoML

**Canais do pacote:**

* Anaconda
* Conda-forjar
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* feriados
* setuptools-git
* psutil

**Pacotes Pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrões
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* py-cpuinfo

## <a name="azureml-automl-dnn"></a>AzureML-AutoML-DNN

**Canais do pacote:**

* Anaconda
* Conda-forjar
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* py-xgboost
* fbprophet
* feriados
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pacotes Pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrões
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* pytorch-transformadores
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-gpu"></a>AzureML-AutoML-DNN-GPU

**Canais do pacote:**

* Anaconda
* Conda-forjar
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* feriados
* setuptools-git
* pytorch
* cudatoolkit
* psutil

**Pacotes Pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrões
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* horovod
* pytorch-transformadores
* spacy
* en_core_web_sm
* py-cpuinfo

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-AutoML-DNN-Vision-GPU

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-conjunto de tempo de execução
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-padrões
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-contrib-automl-DNN-Vision

## <a name="azureml-automl-gpu"></a>AzureML-AutoML-GPU

**Canais do pacote:**

* Anaconda
* Conda-forjar
* pytorch

**Pacotes Conda:**

* python
* numpy
* scikit-learn
* pandas
* fbprophet
* feriados
* setuptools-git
* psutil

**Pacotes Pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrões
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* inferência-esquema
* py-cpuinfo

## <a name="azureml-chainer-510-cpu"></a>AzureML-Chainer-5.1.0-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chainer-5.1.0-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Chainer
* cupy-cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Dask-CPU

**Canais do pacote:**

* Conda-forjar
* pytorch
* padrões

**Pacotes Conda:**

* python

**Pacotes Pip:**

* adlfs
* azureml-core
* azureml-conjunto de tempo de execução
* Dask [concluído]
* Dask-ml [concluído]
* distribuídas
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Dask-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python
* matplotlib

**Pacotes Pip:**

* azureml-padrões
* adlfs
* azureml-core
* Dask [concluído]
* Dask-ml [concluído]
* distribuídas
* fastparquet
* fsspec
* joblib
* jupyterlab
* lz4
* mpi4py
* notebook
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-hyperdrive-ForecastDNN

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml-padrões
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-contrib-automl-DNN-previsão

## <a name="azureml-minimal"></a>AzureML-mínimo

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0,15

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1,0-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1,0-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1,1-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1,1-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1,2-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1,2-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1,3-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1,3-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1,4-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1,4-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1,5-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1,5-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1,6-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1,6-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* Torch
* torchvision
* MKL
* horovod
* tensorboard
* amanhã

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-Learn-0.20.3

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* scikit-learn
* scipy
* joblib

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1,10-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1,10-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1,12-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1,12-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1,13-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1,13-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2,0-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2,0-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2,1-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2,1-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2,2-CPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow
* horovod

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2,2-GPU

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* tensorflow-GPU
* horovod

## <a name="azureml-tutorial"></a>AzureML-tutorial

**Canais do pacote:**

* Anaconda
* Conda-forjar

**Pacotes Conda:**

* python
* pandas
* numpy
* tqdm
* scikit-learn
* matplotlib

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-telemetry
* azureml-Train-restclients-hyperdrive
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-treinar
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* mlflow
* sklearn-pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-VowpalWabbit-8.8.0

**Canais do pacote:**

* Conda-forjar

**Pacotes Conda:**

* python

**Pacotes Pip:**

* azureml-core
* azureml-padrões
* azureml-DataSet-Runtime [fusível, pandas]
