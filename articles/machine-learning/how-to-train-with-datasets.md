---
title: Trem com conjuntos de dados azureml
titleSuffix: Azure Machine Learning
description: Saiba como usar conjuntos de dados em treinamento
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/09/2020
ms.openlocfilehash: 401383f2d483836bf725051810d78167869f7b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283492"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Treine com conjuntos de dados no Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende as duas maneiras de consumir [conjuntos de dados azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) em um treinamento de experimento remoto executado sem se preocupar com cadeias de conexão ou caminhos de dados.

- Opção 1: Se você tiver dados estruturados, crie um Conjunto Dedados Tabular e use-os diretamente no seu script de treinamento.

- Opção 2: Se você tiver dados não estruturados, crie um FileDataset e monte ou baixe arquivos para uma computação remota para treinamento.

Os conjuntos de dados Azure Machine Learning fornecem uma integração perfeita com os produtos de treinamento de Machine Learning do Azure, como [scriptrun,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py) [estimador,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) [hyperdrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) e [azure machine learning pipelines](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de dados, você precisa:

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho de aprendizado de máquina do Azure.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK for Python é instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados azureml.

> [!Note]
> Algumas classes dataset têm dependências do pacote [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Para usuários de Linux, essas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="option-1-use-datasets-directly-in-training-scripts"></a>Opção 1: Use conjuntos de dados diretamente em scripts de treinamento

Neste exemplo, você cria um [Conjunto Dedados Tabular](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) e `estimator` o usa como uma entrada direta ao seu objeto para treinamento. 

### <a name="create-a-tabulardataset"></a>Criar um Conjunto de Dados Tabular

O código a seguir cria um Conjunto de Dados Tabular não registrado a partir de uma url da Web. Você também pode criar conjuntos de dados a partir de arquivos locais ou caminhos em datastores. Saiba mais sobre [como criar conjuntos de dados](https://aka.ms/azureml/howto/createdatasets).

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

### <a name="access-the-input-dataset-in-your-training-script"></a>Acesse o conjunto de dados de entrada em seu script de treinamento

Os objetos TabularDataset fornecem a capacidade de carregar os dados em um pandas ou acender o DataFrame para que você possa trabalhar com bibliotecas familiares de preparação e treinamento de dados. Para aproveitar esse recurso, você pode passar um TabularDataset como a entrada na sua configuração de treinamento e, em seguida, recuperá-lo em seu script.

Para isso, acesse o conjunto de [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) dados de entrada através [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) do objeto em seu script de treinamento e use o método. 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']
# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

### <a name="configure-the-estimator"></a>Configure o estimador

Um [objeto estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) é usado para submeter a execução do experimento. O Azure Machine Learning tem estimadores pré-configurados para estruturas comuns de aprendizado de máquina, bem como um estimador genérico.

Este código cria um objeto `est`estimador genérico, que especifica

* Um diretório de roteiro para seus roteiros. Todos os arquivos neste diretório são carregados nos nós do cluster para execução.
* O roteiro de treinamento, *train_titanic.py.*
* O conjunto de dados `titanic`de entrada para treinamento, . `as_named_input()`é necessário para que o conjunto de dados de entrada possa ser referenciado pelo nome atribuído em seu script de treinamento. 
* O alvo da computação para o experimento.
* A definição de ambiente para o experimento.

```Python
est = Estimator(source_directory=script_folder,
                entry_script='train_titanic.py',
                # pass dataset object as an input with name 'titanic'
                inputs=[titanic_ds.as_named_input('titanic')],
                compute_target=compute_target,
                environment_definition= conda_env)

# Submit the estimator as part of your experiment run
experiment_run = experiment.submit(est)
experiment_run.wait_for_completion(show_output=True)
```


## <a name="option-2--mount-files-to-a-remote-compute-target"></a>Opção 2: Montar arquivos para um alvo remoto de computação

Se você quiser disponibilizar seus arquivos de dados no alvo da computação para treinamento, use [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) para montar ou baixar arquivos por ele encaminhados.

### <a name="mount-vs-download"></a>Monte vs. Download

A montagem ou download de arquivos de qualquer formato são suportados para conjuntos de dados criados a partir do armazenamento Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database e Azure Database for PostgreSQL. 

Quando você monta um conjunto de dados, você anexa os arquivos referenciados pelo conjunto de dados a um diretório (ponto de montagem) e disponibiliza-o no destino de computação. A montagem é suportada para computação baseada em Linux, incluindo Azure Machine Learning Compute, máquinas virtuais e HDInsight. Quando você baixar um conjunto de dados, todos os arquivos referenciados pelo conjunto de dados serão baixados para o destino de computação. O download é suportado para todos os tipos de computação. 

Se o seu script processar todos os arquivos referenciados pelo conjunto de dados e seu disco de computação puder se adequar ao seu conjunto de dados completo, o download é recomendado para evitar a sobrecarga de fluxo de dados de serviços de armazenamento. Se o tamanho dos seus dados exceder o tamanho do disco de computação, o download não é possível. Para este cenário, recomendamos a montagem, uma vez que apenas os arquivos de dados usados pelo seu script são carregados no momento do processamento.

O código a `dataset` seguir monta para o diretório temporário em`mounted_path`

```python
import tempfile
mounted_path = tempfile.mkdtemp()

# mount dataset onto the mounted_path of a Linux-based compute
mount_context = dataset.mount(mounted_path)

mount_context.start()

import os
print(os.listdir(mounted_path))
print (mounted_path)
```

### <a name="create-a-filedataset"></a>Criar um FileDataset

O exemplo a seguir cria um FileDataset não registrado a partir de urls da Web. Saiba mais sobre [como criar conjuntos](https://aka.ms/azureml/howto/createdatasets) de dados a partir de outras fontes.

```Python
from azureml.core.dataset import Dataset

web_paths = [
            'http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
            'http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz'
            ]
mnist_ds = Dataset.File.from_files(path = web_paths)
```

### <a name="configure-the-estimator"></a>Configure o estimador

Além de passar o `inputs` conjunto de dados através do parâmetro no estimador, você também pode passar o conjunto de `script_params` dados e obter o caminho de dados (ponto de montagem) em seu script de treinamento através de argumentos. Dessa forma, você pode manter seu script de treinamento independente do azureml-sdk. Em outras palavras, você poderá usar o mesmo script de treinamento para depuração local e treinamento remoto em qualquer plataforma de nuvem.

Um objeto estimador [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) é usado para enviar a execução para experimentos de scikit-learn. Saiba mais sobre o treinamento com o [estimador SKlearn](how-to-train-scikit-learn.md).

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    # mount the dataset on the remote compute and pass the mounted path as an argument to the training script
    '--data-folder': mnist_ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              environment_definition=env,
              entry_script='train_mnist.py')

# Run the experiment
run = experiment.submit(est)
run.wait_for_completion(show_output=True)
```

### <a name="retrieve-the-data-in-your-training-script"></a>Recupere os dados em seu script de treinamento

Depois de enviar a execução, `mnist` os arquivos de dados encaminhados pelo conjunto de dados serão montados no destino de computação. O código a seguir mostra como recuperar os dados em seu script.

```Python
%%writefile $script_folder/train_mnist.py

import argparse
import os
import numpy as np
import glob

from utils import load_data

# retrieve the 2 arguments configured through script_params in estimator
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# get the file paths on the compute
X_train_path = glob.glob(os.path.join(data_folder, '**/train-images-idx3-ubyte.gz'), recursive=True)[0]
X_test_path = glob.glob(os.path.join(data_folder, '**/t10k-images-idx3-ubyte.gz'), recursive=True)[0]
y_train_path = glob.glob(os.path.join(data_folder, '**/train-labels-idx1-ubyte.gz'), recursive=True)[0]
y_test = glob.glob(os.path.join(data_folder, '**/t10k-labels-idx1-ubyte.gz'), recursive=True)[0]

# load train and test set into numpy arrays
X_train = load_data(X_train_path, False) / 255.0
X_test = load_data(X_test_path, False) / 255.0
y_train = load_data(y_train_path, True).reshape(-1)
y_test = load_data(y_test, True).reshape(-1)
```

## <a name="notebook-examples"></a>Exemplos de notebook

Os [cadernos de conjunto de dados](https://aka.ms/dataset-tutorial) demonstram e expandem conceitos neste artigo.

## <a name="next-steps"></a>Próximas etapas

* [Modelos de aprendizagem de máquina de auto trem](how-to-auto-train-remote.md) com TabularDatasets

* [Modelos de classificação de imagem de trem](https://aka.ms/filedataset-samplenotebook) com FileDatasets

* [Treine com conjuntos de dados usando pipelines](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb)
