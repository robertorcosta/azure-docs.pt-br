---
title: Crie conjuntos de dados de aprendizado de máquina do Azure para acessar dados
titleSuffix: Azure Machine Learning
description: Saiba como criar conjuntos de dados de Aprendizado de Máquina do Azure para acessar seus dados para testes de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 02/10/2020
ms.openlocfilehash: feaa0c22ec98d170a65e5c9bee119ba3904a95cf
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673736"
---
# <a name="create-azure-machine-learning-datasets"></a>Criar conjuntos de dados de aprendizado de máquina do Azure

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprende como criar conjuntos de dados do Azure Machine Learning para acessar dados para seus experimentos locais ou remotos.

Com os conjuntos de dados azure Machine Learning, você pode:

* Mantenha uma única cópia de dados em seu armazenamento, referenciada por conjuntos de dados.

* Acesse dados sem problemas durante o treinamento do modelo sem se preocupar com seqüências de conexão ou caminhos de dados.

* Compartilhe dados e colabore com outros usuários.

## <a name="prerequisites"></a>Pré-requisitos
Para criar e trabalhar com conjuntos de dados, você precisa:

* Uma assinatura do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho de aprendizado de máquina do Azure.](how-to-manage-workspace.md)

* O [Azure Machine Learning SDK for Python é instalado,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)que inclui o pacote de conjuntos de dados azureml.

> [!NOTE]
> Algumas classes de conjunto de dados têm dependências do pacote [azureml-dataprep.](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) Para usuários de Linux, essas classes são suportadas apenas nas seguintes distribuições: Red Hat Enterprise Linux, Ubuntu, Fedora e CentOS.

## <a name="compute-size-guidance"></a>Orientação de tamanho de computação

Ao criar um conjunto de dados, revise seu poder de processamento de computação e o tamanho de seus dados na memória. O tamanho de seus dados no armazenamento não é o mesmo que o tamanho dos dados em um dataframe. Por exemplo, os dados em arquivos CSV podem expandir até 10x em um dataframe, de modo que um arquivo CSV de 1 GB pode se tornar 10 GB em um dataframe. 

O principal fator é o tamanho do conjunto de dados na memória, ou seja, como um dataframe. Recomendamos que o tamanho e o poder de processamento do seu cálculo contenham 2x do tamanho da RAM. Portanto, se o seu dataframe é de 10GB, você quer um destino de computação com mais de 20 GB de RAM para garantir que o dataframe possa se encaixar na memória e ser processado. Se seus dados forem compactados, ele pode expandir-se ainda mais; 20 GB de dados relativamente esparsos armazenados em formato de parquet compactado podem expandir para ~800 GB na memória. Uma vez que os arquivos Parquet armazenam dados em um formato columnar, se você só precisa de metade das colunas, então você só precisa carregar ~400 GB na memória.
 
Se você está usando Pandas, não há razão para ter mais de 1 vCPU, já que é tudo o que ele vai usar. Você pode facilmente paralelenear a muitas vCPUs em uma única instância/nó de computação de Aprendizado de Máquina do Azure via Modin e Dask/Ray, e escalar para um grande cluster, se necessário, simplesmente mudando `import pandas as pd` para `import modin.pandas as pd`. 
 
Se você não conseguir obter um virtual grande o suficiente para os dados, você tem duas opções: usar uma estrutura como Spark ou Dask para executar o processamento nos dados 'fora da memória', ou seja, o dataframe é carregado na partição RAM por partição e processado, com o resultado final sendo coletado no final. Se isso for muito lento, Spark ou Dask permitem que você dimensione para um cluster que ainda pode ser usado interativamente. 

## <a name="dataset-types"></a>Tipos de conjunto de dados

Existem dois tipos de conjunto de dados, baseados em como os usuários os consomem no treinamento:

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular analisando o arquivo fornecido ou lista de arquivos. Isso fornece a você a capacidade de materializar os dados em um Pandas ou Spark DataFrame. Você pode `TabularDataset` criar um objeto a partir de arquivos de consulta .csv, .tsv, .parquet, .jsonl e a partir de resultados de consulta SQL. Para obter uma lista completa, consulte [TabularDatasetFactory class](https://aka.ms/tabulardataset-api-reference).

* A classe [FileDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a arquivos únicos ou múltiplos em seus datastores ou URLs públicos. Por este método, você pode baixar ou montar os arquivos para o seu cálculo como um objeto FileDataset. Os arquivos podem estar em qualquer formato, o que permite uma gama mais ampla de cenários de aprendizado de máquina, incluindo o deep learning.

Para saber mais sobre as próximas alterações da API, consulte [aviso de alteração da API dataset](https://aka.ms/tabular-dataset).

## <a name="create-datasets"></a>Criar conjuntos de dados

Ao criar um conjunto de dados, você cria uma referência à localização da fonte de dados, juntamente com uma cópia de seus metadados. Como os dados permanecem em sua localização existente, você não incorre em nenhum custo extra de armazenamento. Você pode `TabularDataset` criar `FileDataset` ambos e conjuntos de dados https://ml.azure.comusando o Python SDK ou em .

Para que os dados sejam acessíveis pelo Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em [datastores do Azure](how-to-access-data.md) ou URLs da web pública. 

### <a name="use-the-sdk"></a>Use o SDK

Para criar conjuntos de dados a partir de um armazenamento de [dados do Azure](how-to-access-data.md) usando o Python SDK:

1. Verifique se `contributor` você `owner` tem ou acesso ao armazenamento de dados azure registrado.

2. Crie o conjunto de dados fazendo referência a caminhos no datastore.
> [!Note]
> Você pode criar um conjunto de dados a partir de vários caminhos em vários datastores. Não há limite rígido no número de arquivos ou tamanho de dados do que você pode criar um conjunto de dados. No entanto, para cada caminho de dados, algumas solicitações serão enviadas ao serviço de armazenamento para verificar se ele aponta para um arquivo ou uma pasta. Essa sobrecarga pode levar a um desempenho ou falha degradado. Um conjunto de dados que faz referência a uma pasta com 1000 arquivos no interior é considerado referenciando um caminho de dados. Recomendamos a criação de conjunto de dados com menos de 100 caminhos em datastores para obter um desempenho ideal.

#### <a name="create-a-tabulardataset"></a>Criar um Conjunto de Dados Tabular

Use [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-delimited-files-path--validate-true--include-path-false--infer-column-types-true--set-column-types-none--separator------header-true--partition-format-none--support-multi-line-false-) o método `TabularDatasetFactory` na classe para ler arquivos no formato .csv ou .tsv e para criar um Conjunto de Dados Tabular não registrado. Se você estiver lendo de vários arquivos, os resultados serão agregados em uma representação tabular. 

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]
weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Por padrão, quando você cria um Conjunto TabularDataset, os tipos de dados da coluna são inferidos automaticamente. Se os tipos inferidos não corresponderem às suas expectativas, você pode especificar os tipos de coluna usando o seguinte código. O parâmetro `infer_column_type` só é aplicável para conjuntos de dados criados a partir de arquivos delimitados. Você também pode [aprender mais sobre os tipos de dados suportados.](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py)

> [!IMPORTANT] 
> Se o seu armazenamento estiver por trás de uma rede virtual ou firewall, somente a criação de um conjunto de dados através do SDK é suportada. Para criar seu conjunto de dados, `validate=False` `infer_column_types=False` certifique-se de incluir os parâmetros e em seu `from_delimited_files()` método. Isso ignora a verificação inicial de validação e garante que você pode criar seu conjunto de dados a partir desses arquivos seguros. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

| |PassengerId|Survived|Pclass|Nome|Sexo|Idade|SibSp|Rio Parch|Tíquete|Tarifa|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris.|masculino|22.0|1|0|A/5 21171|7.2500||S
1|2|True|1|Cumings, Sra. John Bradley (Florence Briggs Th...|feminino|38.0|1|0|PC 17599|71.2833|C85|C
2|3|True|3|Heikkinen, senhorita. Laina|feminino|26.0|0|0|STON/O2. 3101282|7.9250||S


Para criar um conjunto de dados a partir de um dataframe de pandas de memória, escreva os dados em um arquivo local, como um csv, e crie seu conjunto de dados a partir desse arquivo. O código a seguir demonstra esse fluxo de trabalho.

```python
local_path = 'data/prepared.csv'
dataframe.to_csv(local_path)
upload the local file to a datastore on the cloud
# azureml-core of version 1.0.72 or higher is required
# azureml-dataprep[pandas] of version 1.1.34 or higher is required
from azureml.core import Workspace, Dataset

subscription_id = 'xxxxxxxxxxxxxxxxxxxxx'
resource_group = 'xxxxxx'
workspace_name = 'xxxxxxxxxxxxxxxx'

workspace = Workspace(subscription_id, resource_group, workspace_name)

# get the datastore to upload prepared data
datastore = workspace.get_default_datastore()

# upload the local file from src_dir to the target_path in datastore
datastore.upload(src_dir='data', target_path='data')
# create a dataset referencing the cloud location
dataset = Dataset.Tabular.from_delimited_files(datastore.path('data/prepared.csv'))
```

Use [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) o método `TabularDatasetFactory` na classe para ler no Banco de Dados SQL do Azure:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

Em TabularDatasets, você pode especificar um carimbo de tempo de uma coluna nos dados ou de onde os dados do padrão de caminho forem armazenados para habilitar um traço de série socada. Esta especificação permite uma filtragem fácil e eficiente pelo tempo.

Use [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) o método`TabularDataset` na classe para especificar sua coluna de carimbo de tempo e para permitir a filtragem por tempo. Para obter mais informações, consulte [a demonstração da API relacionada à série temporal tabular com dados meteorológicos NOAA](https://aka.ms/azureml-tsd-notebook).

```Python
# create a TabularDataset with time series trait
datastore_paths = [(datastore, 'weather/*/*/*/data.parquet')]

# get a coarse timestamp column from the path pattern
dataset = Dataset.Tabular.from_parquet_files(path=datastore_path, partition_format='weather/{coarse_time:yyy/MM/dd}/data.parquet')

# set coarse timestamp to the virtual column created, and fine grain timestamp from a column in the data
dataset = dataset.with_timestamp_columns(fine_grain_timestamp='datetime', coarse_grain_timestamp='coarse_time')

# filter with time-series-trait-specific methods
data_slice = dataset.time_before(datetime(2019, 1, 1))
data_slice = dataset.time_after(datetime(2019, 1, 1))
data_slice = dataset.time_between(datetime(2019, 1, 1), datetime(2019, 2, 1))
data_slice = dataset.time_recent(timedelta(weeks=1, days=1))
```

#### <a name="create-a-filedataset"></a>Criar um FileDataset

Use [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) o método `FileDatasetFactory` na classe para carregar arquivos em qualquer formato e para criar um conjunto de Datados de arquivo não registrado. Se o seu armazenamento estiver por trás de `validate =False` uma `from_files()` rede virtual ou firewall, defina o parâmetro em seu método. Isso ignora a etapa inicial de validação e garante que você possa criar seu conjunto de dados a partir desses arquivos seguros.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="on-the-web"></a>Na Web 
As etapas a seguir e animação mostram como criar um https://ml.azure.comconjunto de dados no estúdio Azure Machine Learning, .

![Crie um conjunto de dados com a ui](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Para criar um conjunto de dados no estúdio:
1. Faça login https://ml.azure.comem .
1. Selecione **Conjuntos de dados** na seção **Ativos** do painel esquerdo. 
1. Selecione **Criar conjunto de dados** para escolher a origem do seu conjunto de dados. Essa fonte pode ser arquivos locais, um datastore ou URLs públicos.
1. Selecione **Tabular** ou **Arquivo** para tipo Conjunto de dados.
1. Selecione **Ao lado** para abrir o **formulário de seleção de dados e arquivos.** Neste formulário, você seleciona onde manter seu conjunto de dados após a criação, bem como selecione quais arquivos de dados usar para o seu conjunto de dados. 
1. Selecione **Ao lado** para preencher as **configurações e os formulários de visualização** e **esquema;** eles são preenchidos de forma inteligente com base no tipo de arquivo e você pode configurar ainda mais seu conjunto de dados antes da criação nesses formulários. 
1. Selecione **A seguir** para revisar o formulário **Confirmar detalhes.** Verifique suas seleções e crie um perfil de dados opcional para o seu conjunto de dados. Saiba mais sobre a [criação de perfil de dados](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Selecione **Criar** para concluir sua criação do conjunto de dados.

## <a name="register-datasets"></a>Registrar conjuntos de dados

Para concluir o processo de criação, registre seus conjuntos de dados com um espaço de trabalho. Use [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) o método para registrar conjuntos de dados com seu espaço de trabalho para compartilhá-los com outros e reutilizá-los em vários experimentos:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

> [!Note]
> Os conjuntos de dados criados através do estúdio Azure Machine Learning são automaticamente registrados no espaço de trabalho.

## <a name="create-datasets-with-azure-open-datasets"></a>Criar conjuntos de dados com conjuntos de dados abertos do Azure

Os [Conjuntos de dados abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos coletados que você pode usar para adicionar recursos específicos do cenário para soluções de aprendizado de máquina para obter modelos mais precisos. Os conjuntos de dados incluem dados de domínio público de clima, censo, feriados, segurança pública e localização que ajudam você a treinar os modelos de machine learning e aprimorar as soluções de previsão. Os Conjuntos de Dados Abertos estão na nuvem no Microsoft Azure e estão incluídos tanto no SDK quanto na UI do espaço de trabalho.

### <a name="use-the-sdk"></a>Use o SDK

Para criar conjuntos de dados com o Azure Open Datasets a partir `pip install azureml-opendatasets`do SDK, certifique-se de que você instalou o pacote com . Cada conjunto de dados discreto é representado por sua própria classe no SDK, e certas classes estão disponíveis como a `TabularDataset`, `FileDataset`ou ambas. Consulte a [documentação de referência](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) para uma lista completa de classes.

Você pode recuperar certas `TabularDataset` classes `FileDataset`como a ou , o que permite manipular e/ou baixar os arquivos diretamente. Outras classes podem obter um conjunto `get_tabular_dataset()` `get_file_dataset()` de dados **apenas** usando uma das funções. A amostra de código a seguir mostra alguns exemplos desses tipos de classes.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando você registra um conjunto de dados criado a partir de Conjuntos de Dados Abertos, nenhum dado é imediatamente baixado, mas os dados serão acessados mais tarde quando solicitados (durante o treinamento, por exemplo) a partir de um local central de armazenamento.

### <a name="use-the-ui"></a>Use a ui

Você também pode criar conjuntos de dados a partir de classes Open Datasets através da ui. Em seu espaço de trabalho, selecione a guia **Conjuntos de dados** em **Ativos**. No **menu suspenso do conjunto** de dados Create, selecione De **Conjuntos de dados abertos**.

![Abrir conjunto de dados com a ui](./media/how-to-create-register-datasets/open-datasets-1.png)

Selecione um conjunto de dados selecionando seu azulejo. (Você tem a opção de filtrar usando a barra de pesquisa.) Selecione **Next**.

![Escolha conjunto de dados](./media/how-to-create-register-datasets/open-datasets-2.png)

Escolha um nome sob o qual registrar o conjunto de dados e, opcionalmente, filtre os dados usando os filtros disponíveis. Neste caso, para o conjunto de dados de feriados, você filtra o período de tempo para um ano e o código do país apenas para os EUA. Selecione **Criar**.

![Defina params de conjunto de dados e crie conjunto de dados](./media/how-to-create-register-datasets/open-datasets-3.png)

O conjunto de dados está agora disponível em seu espaço de trabalho em **Conjuntos de dados**. Você pode usá-lo da mesma forma que outros conjuntos de dados que você criou.

## <a name="version-datasets"></a>Conjuntos de dados de versão

Você pode registrar um novo conjunto de dados com o mesmo nome criando uma nova versão. Uma versão do conjunto de dados é uma maneira de marcar o estado de seus dados para que você possa aplicar uma versão específica do conjunto de dados para experimentação ou reprodução futura. Saiba mais sobre [as versões do conjunto de dados](how-to-version-track-datasets.md).
```Python
# create a TabularDataset from Titanic training data
web_paths = ['https://dprepdata.blob.core.windows.net/demo/Titanic.csv',
             'https://dprepdata.blob.core.windows.net/demo/Titanic2.csv']
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_paths)

# create a new version of titanic_ds
titanic_ds = titanic_ds.register(workspace = workspace,
                                 name = 'titanic_ds',
                                 description = 'new titanic training data',
                                 create_new_version = True)
```

## <a name="access-datasets-in-your-script"></a>Acesse conjuntos de dados em seu script

Os conjuntos de dados registrados são acessíveis local e remotamente em clusters computacionais, como o aputação Azure Machine Learning. Para acessar seu conjunto de dados registrado em experimentos, use o seguinte código para acessar seu espaço de trabalho e conjunto de dados registrados por nome. Por padrão, [`get_by_name()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#get-by-name-workspace--name--version--latest--) o `Dataset` método na classe retorna a versão mais recente do conjunto de dados que está registrado no espaço de trabalho.

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

## <a name="next-steps"></a>Próximas etapas

* Aprenda [a treinar com conjuntos de dados.](how-to-train-with-datasets.md)
* Use aprendizado de máquina automatizado para [treinar com TabularDatasets](https://aka.ms/automl-dataset).
* Para obter mais exemplos de treinamento de conjuntos de dados, consulte os [cadernos de amostra](https://aka.ms/dataset-tutorial).
