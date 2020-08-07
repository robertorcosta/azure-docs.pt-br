---
title: Treinar com o azureml – conjuntos de valores
titleSuffix: Azure Machine Learning
description: Saiba como usar conjuntos de valores em treinamento
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/31/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: acd030d8108ef3983be29fe85de6d7b3caf620af
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87849328"
---
# <a name="train-with-datasets-in-azure-machine-learning"></a>Treine com conjuntos de os Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a trabalhar com [conjuntos de Azure Machine Learning](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) de trabalho em seus experimentos de treinamento.  Você pode usar conjuntos de dados em seu destino de computação local ou remoto sem se preocupar com cadeias de conexão ou caminhos de dado.

Azure Machine Learning conjuntos de valores fornecem uma integração direta com Azure Machine Learning produtos de treinamento como [ScriptRun](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrun?view=azure-ml-py), [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py), [HyperDrive](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive?view=azure-ml-py) e [pipelines de Azure Machine Learning](how-to-create-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar e treinar com conjuntos de os, você precisa:

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente hoje mesmo a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

> [!Note]
> Algumas classes de conjunto de objetos têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) . Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="access-and-explore-input-datasets"></a>Acessar e explorar conjuntos de dados de entrada

Você pode acessar um TabularDataset existente do script de treinamento de um experimento em seu espaço de trabalho e carregar esse conjunto de informações em um dataframe do pandas para explorar ainda mais em seu ambiente local.

O código a seguir usa o [`get_context()`]() método na [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py) classe para acessar o TabularDataset de entrada existente, `titanic` , no script de treinamento. Em seguida, o usa o [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) método para carregar o conjunto de dados em um dataframe do pandas para exploração e preparação de mais detalhes antes do treinamento.

> [!Note]
> Se a fonte de dados original contiver NaN, cadeias de caracteres vazias ou valores em branco, quando você usar o to_pandas_dataframe (), esses valores serão substituídos como um valor *nulo* . 

```Python
%%writefile $script_folder/train_titanic.py

from azureml.core import Dataset, Run

run = Run.get_context()
# get the input dataset by name
dataset = run.input_datasets['titanic']

# load the TabularDataset to pandas DataFrame
df = dataset.to_pandas_dataframe()
```

Se você precisar carregar os dados preparados em um novo conjunto de dado a partir de um data frame do pandas na memória, grave os dados em um arquivo local, como um parquet, e crie um novo DataSet a partir desse arquivo. Você também pode criar conjuntos de valores de arquivos locais ou caminhos em repositórios de armazenamento. Saiba mais sobre [como criar conjuntos de](how-to-create-register-datasets.md)os.

## <a name="use-datasets-directly-in-training-scripts"></a>Usar conjuntos de valores diretamente em scripts de treinamento

Se você tiver dados estruturados que ainda não estão registrados como um DataSet, crie um TabularDataset e use-o diretamente no script de treinamento para seu experimento local ou remoto.

Neste exemplo, você cria um [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) não registrado e o usa como uma entrada direta para o seu `estimator` objeto para treinamento. Se você quiser reutilizar esse TabularDataset com outros experimentos em seu espaço de trabalho, consulte [como registrar conjuntos de registros em seu espaço de trabalho](how-to-create-register-datasets.md#register-datasets).

### <a name="create-a-tabulardataset"></a>Criar um TabularDataset

O código a seguir cria um TabularDataset não registrado de uma URL da Web.  

```Python
from azureml.core.dataset import Dataset

web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path)
```

Os objetos TabularDataset fornecem a capacidade de carregar os dados em seu TabularDataset em um data frame do pandas ou Spark para que você possa trabalhar com bibliotecas de treinamento e preparação de dados familiares sem precisar sair do bloco de anotações. Para aproveitar esse recurso, consulte [acessar e explorar conjuntos de dados de entrada](#access-and-explore-input-datasets).

### <a name="configure-the-estimator"></a>Configurar o estimador

Um objeto [estimador](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator.estimator?view=azure-ml-py) é usado para enviar a execução do experimento. Azure Machine Learning tem estimadores pré-configurados para estruturas de aprendizado de máquina comuns, bem como um estimador genérico.

Esse código cria um objeto estimador genérico, `est` , que especifica

* Um diretório de script para seus scripts. Todos os arquivos neste diretório são carregados nos nós do cluster para execução.
* O script de treinamento, *train_titanic. py*.
* O conjunto de dados de entrada para treinamento, `titanic_ds` . `as_named_input()`é necessário para que o conjunto de dados de entrada possa ser referenciado pelo nome atribuído `titanic` em seu script de treinamento. 
* O destino de computação para o experimento.
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

## <a name="mount-files-to-remote-compute-targets"></a>Montar arquivos para destinos de computação remota

Se você tiver dados não estruturados, crie um [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py) e monte ou baixe seus arquivos de dados para disponibilizá-los para o destino de computação remota para treinamento. Saiba mais sobre quando usar o [Mount vs. download](#mount-vs-download) para seus experimentos de treinamento remoto. 

O exemplo a seguir cria um filedataset e monta o conjunto de dados para o destino de computação, passando-o como um argumento no estimador para treinamento. 

### <a name="create-a-filedataset"></a>Criar um FileDataset

O exemplo a seguir cria um filedataset não registrado a partir de URLs da Web. Saiba mais sobre [como criar conjuntos](https://aka.ms/azureml/howto/createdatasets) de informações de outras fontes.

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

### <a name="configure-the-estimator"></a>Configurar o estimador

É recomendável passar o DataSet como um argumento ao montar. Além de passar o conjunto de dados por meio do `inputs` parâmetro no estimador, você também pode passar o conjunto de dados por meio de `script_params` e obter o caminho do dado (ponto de montagem) em seu script de treinamento por meio de argumentos. Dessa forma, você poderá usar o mesmo script de treinamento para depuração local e treinamento remoto em qualquer plataforma de nuvem.

Um objeto de estimador [SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) é usado para enviar a execução para experimentos de scikit-learn. Depois que você enviar a execução, os arquivos de dados referenciados pelo `mnist` DataSet serão montados no destino de computação. Saiba mais sobre o treinamento com o [estimador do SKlearn](how-to-train-scikit-learn.md).

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

### <a name="retrieve-the-data-in-your-training-script"></a>Recuperar os dados em seu script de treinamento

O código a seguir mostra como recuperar os dados em seu script.

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

## <a name="mount-vs-download"></a>Montagem vs download

Há suporte para a montagem ou o download de arquivos de qualquer formato para conjuntos de dados criados a partir do armazenamento de BLOBs do Azure, arquivos do Azure, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2 do Azure SQL e banco de dados do Azure para PostgreSQL. 

Ao **montar** um conjunto de um DataSet, você anexa os arquivos referenciados pelo conjunto de um diretório (ponto de montagem) e os disponibiliza no destino de computação. A montagem tem suporte para computações baseadas em Linux, incluindo Azure Machine Learning computação, máquinas virtuais e HDInsight. 

Quando você **baixa** um conjunto de um DataSet, todos os arquivos referenciados pelo conjunto de um serão baixados para o destino de computação. O download tem suporte para todos os tipos de computação. 

Se o seu script processa todos os arquivos referenciados pelo conjunto de dados e seu disco de computação pode se ajustar a seu conjunto de dados completo, o download é recomendado para evitar a sobrecarga de streaming de serviços de armazenamento. Se o tamanho dos dados exceder o tamanho do disco de computação, o download não será possível. Para este cenário, recomendamos a montagem, pois apenas os arquivos de dados usados pelo script são carregados no momento do processamento.

O código a seguir é montado no `dataset` diretório Temp em`mounted_path`

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

## <a name="access-datasets-in-your-script"></a>Acessar conjuntos de os em seu script

Os conjuntos de itens registrados são acessíveis localmente e remotamente em clusters de computação, como a Azure Machine Learning computação. Para acessar seu conjunto de seus conjuntos de testes entre experimentos, use o código a seguir para acessar seu espaço de trabalho e o conjunto de código registrado por nome. Por padrão, o [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) método na `Dataset` classe retorna a versão mais recente do conjunto de informações registrado com o espaço de trabalho.

```Python
%%writefile $script_folder/train.py

from azureml.core import Dataset, Run

run = Run.get_context()
workspace = run.experiment.workspace

dataset_name = 'titanic_ds'

# Get a dataset by name
titanic_ds = Dataset.get_by_name(workspace=workspace, name=dataset_name)

# Load a TabularDataset into pandas DataFrame
df = titanic_ds.to_pandas_dataframe()
```

## <a name="accessing-source-code-during-training"></a>Acessando o código-fonte durante o treinamento

O Armazenamento de Blobs do Azure tem velocidades de taxa de transferência mais altas que um compartilhamento de arquivo do Azure e será dimensionado para um grande número de trabalhos iniciados em paralelo. Por esse motivo, recomendamos configurar suas execuções para usar o Armazenamento de Blobs na transferência de arquivos de código-fonte.

O exemplo de código a seguir especifica na configuração de execução qual armazenamento de dados de blob usar em transferências de código-fonte.

```python 
# workspaceblobstore is the default blob storage
run_config.source_directory_data_store = "workspaceblobstore" 
```

## <a name="notebook-examples"></a>Exemplos de notebook

Os [notebooks de conjunto de anotações](https://aka.ms/dataset-tutorial) demonstram e se expandem sobre os conceitos deste artigo.

## <a name="next-steps"></a>Próximas etapas

* [Treine automaticamente os modelos de aprendizado de máquina](how-to-auto-train-remote.md) com o TabularDatasets.

* [Treine modelos de classificação de imagem](https://aka.ms/filedataset-samplenotebook) com DataSets.

* [Treine com conjuntos de valores usando pipelines](how-to-create-your-first-pipeline.md).
