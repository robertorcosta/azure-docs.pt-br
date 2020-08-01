---
title: Criar conjuntos de dados de Azure Machine Learning para acessar o dado
titleSuffix: Azure Machine Learning
description: Saiba como criar conjuntos de dados de Azure Machine Learning para acessar seus dados para execuções de experimento de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 07/22/2020
ms.openlocfilehash: 5f58698de289efc0b74550260c2229f2a08d798d
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461367"
---
# <a name="create-azure-machine-learning-datasets"></a>Criar conjuntos de Azure Machine Learning de os

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Neste artigo, você aprenderá a criar conjuntos de dados Azure Machine Learnings para acessar os dados para seus experimentos locais ou remotos. Para entender onde os conjuntos de dados se encaixam no fluxo de trabalho de acesso de data de Azure Machine Learning geral, confira o artigo [dados de acesso seguro](concept-data.md#data-workflow) .

Com os conjuntos de Azure Machine Learning, você pode:

* Mantenha uma única cópia de dados em seu armazenamento, referenciada por conjuntos.

* Acesse dados diretamente durante o treinamento do modelo sem se preocupar com cadeias de conexão ou caminhos de dados.

* Compartilhe dados e colabore com outros usuários.

[Saiba mais sobre como treinar com conjuntos de dados](how-to-train-with-datasets.md).

## <a name="prerequisites"></a>Pré-requisitos

Para criar e trabalhar com conjuntos de os, você precisa de:

* Uma assinatura do Azure. Se você não tiver uma, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Um [espaço de trabalho Azure Machine Learning](how-to-manage-workspace.md).

* O [SDK do Azure Machine Learning para Python instalado](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), que inclui o pacote de conjuntos de linhas do azureml.

> [!NOTE]
> Algumas classes de conjunto de objetos têm dependências no pacote [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , que é compatível apenas com Python de 64 bits. Para usuários do Linux, essas classes têm suporte apenas nas seguintes distribuições: Red Hat Enterprise Linux (7, 8), Ubuntu (14, 4, 16, 4, 18, 4), Fedora (27, 28), Debian (8, 9) e CentOS (7).

## <a name="compute-size-guidance"></a>Diretrizes de tamanho de computação

Ao criar um conjunto de dados, revise sua capacidade de processamento de computação e o tamanho dos seus dados na memória. O tamanho dos dados no armazenamento não é igual ao tamanho dos dados em um dataframe. Por exemplo, os dados em arquivos CSV podem expandir até 10 vezes em um dataframe, de modo que um arquivo CSV de 1 GB possa se tornar 10 GB em um dataframe. 

Se os dados estiverem compactados, eles poderão ser expandidos. 20 GB de dados relativamente esparsos armazenados no formato parquet compactado podem ser expandidos para aproximadamente 800 GB de memória. Como os arquivos parquet armazenam dados em um formato de coluna, se você precisar apenas de metade das colunas, precisará carregar apenas ~ 400 GB de memória.
 
Se você estiver usando o pandas, não há motivo para ter mais de 1 vCPU, pois isso é tudo o que ele usará. Você pode facilmente paralelizar para muitos vCPUs em um único Azure Machine Learning instância/nó de computação por meio de Modin e Dask/Ray e escalar horizontalmente para um cluster grande, se necessário, simplesmente alterando `import pandas as pd` para `import modin.pandas as pd` . 
 
[Saiba mais sobre como otimizar o processamento de dados no Azure Machine Learning](concept-optimize-data-processing.md)

## <a name="dataset-types"></a>Tipos de conjunto de dados

Há dois tipos de conjuntos de conjunto de DataSet, com base em como os usuários os consomem no treinamento:

* A classe [Filedataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.file_dataset.filedataset?view=azure-ml-py) faz referência a um ou vários arquivos em seus armazenamentos de dados ou URLs públicas. Por esse método, você pode baixar ou montar os arquivos em sua computação como um objeto filedataset. Os arquivos podem estar em qualquer formato, o que permite uma ampla gama de cenários de aprendizado de máquina, incluindo aprendizado profundo. 

* [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representa dados em um formato tabular analisando o arquivo fornecido ou a lista de arquivos. Isso fornece a capacidade de materializar os dados em um data frame pandas ou Spark. Você pode criar um `TabularDataset` objeto a partir de arquivos. csv,. TSV,. parquet,. jsonl e dos resultados da consulta SQL. Para obter uma lista completa, consulte [classe TabularDatasetFactory](https://aka.ms/tabulardataset-api-reference).

## <a name="create-datasets-via-the-sdk"></a>Criar conjuntos de itens por meio do SDK

Ao criar um conjunto de dados, você cria uma referência para o local da fonte de dados com uma cópia de seus metadados. Como os dados permanecem na localização existente, nenhum custo de armazenamento extra é gerado para você. Para que os dados sejam acessíveis por Azure Machine Learning, os conjuntos de dados devem ser criados a partir de caminhos em [repositórios de dados do Azure](how-to-access-data.md) ou URLs da Web públicas. 

Para criar conjuntos de itens de um [repositório de armazenamento do Azure](how-to-access-data.md) usando o SDK do Python:

1. Verifique se você tem `contributor` ou tem `owner` acesso ao repositório de armazenamento do Azure registrado.

2. Crie o conjunto de um referenciando caminhos no repositório de armazenamento.

> [!Note]
> Você pode criar um conjunto de um DataSet a partir de vários caminhos em vários repositórios de armazenamento. Não há nenhum limite rígido para o número de arquivos ou o tamanho de dados do qual você pode criar um DataSet. No entanto, para cada caminho de dados, algumas solicitações serão enviadas ao serviço de armazenamento para verificar se ele aponta para um arquivo ou uma pasta. Essa sobrecarga pode levar à degradação do desempenho ou falha. Um conjunto de dados que faz referência a uma pasta com 1000 arquivos dentro é considerado fazendo referência a um caminho de dado. É recomendável criar um conjunto de consulta que referencie menos de 100 caminhos em repositórios de armazenamento para um desempenho ideal.

#### <a name="create-a-filedataset"></a>Criar um FileDataset

Use o [`from_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.filedatasetfactory?view=azure-ml-py#from-files-path--validate-true-) método na `FileDatasetFactory` classe para carregar arquivos em qualquer formato e para criar um filedataset não registrado. Se o armazenamento estiver protegido por uma rede virtual ou firewall, defina o parâmetro `validate=False` em seu `from_files()` método. Isso ignora a etapa de validação inicial e garante que você possa criar seu conjunto de seus arquivos seguros.

```Python
# create a FileDataset pointing to files in 'animals' folder and its subfolders recursively
datastore_paths = [(datastore, 'animals')]
animal_ds = Dataset.File.from_files(path=datastore_paths)

# create a FileDataset from image and label files behind public web urls
web_paths = ['https://azureopendatastorage.blob.core.windows.net/mnist/train-images-idx3-ubyte.gz',
             'https://azureopendatastorage.blob.core.windows.net/mnist/train-labels-idx1-ubyte.gz']
mnist_ds = Dataset.File.from_files(path=web_paths)
```

#### <a name="create-a-tabulardataset"></a>Criar um TabularDataset

Use o [`from_delimited_files()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory) método na `TabularDatasetFactory` classe para ler os arquivos no formato. csv ou. tsv e para criar um TabularDataset não registrado. Se você estiver lendo de vários arquivos, os resultados serão agregados em uma representação tabular. 

O código a seguir obtém o espaço de trabalho existente e o repositório de armazenamento desejado pelo nome. E, em seguida, passa o repositório de armazenamento e os locais de arquivo para o `path` parâmetro para criar um novo TabularDataset, `weather_ds` .

```Python
from azureml.core import Workspace, Datastore, Dataset

datastore_name = 'your datastore name'

# get existing workspace
workspace = Workspace.from_config()
    
# retrieve an existing datastore in the workspace by name
datastore = Datastore.get(workspace, datastore_name)

# create a TabularDataset from 3 file paths in datastore
datastore_paths = [(datastore, 'weather/2018/11.csv'),
                   (datastore, 'weather/2018/12.csv'),
                   (datastore, 'weather/2019/*.csv')]

weather_ds = Dataset.Tabular.from_delimited_files(path=datastore_paths)
```

Por padrão, quando você cria um TabularDataset, os tipos de dados de coluna são inferidos automaticamente. Se os tipos deduzidos não corresponderem às suas expectativas, você poderá especificar tipos de coluna usando o código a seguir. O parâmetro `infer_column_type` só é aplicável a conjuntos de valores criados a partir de arquivos delimitados. Você também pode [saber mais sobre os tipos de dados com suporte](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.datatype?view=azure-ml-py).

> [!IMPORTANT] 
> Se o armazenamento estiver protegido por uma rede virtual ou firewall, haverá suporte para a criação apenas de um conjunto de uma por meio do SDK. Para criar seu conjunto de seus conjuntos de seus, certifique-se de incluir os parâmetros `validate=False` e `infer_column_types=False` em seu `from_delimited_files()` método. Isso ignora a verificação de validação inicial e garante que você possa criar seu conjunto de seus arquivos seguros. 

```Python
from azureml.core import Dataset
from azureml.data.dataset_factory import DataType

# create a TabularDataset from a delimited file behind a public web url and convert column "Survived" to boolean
web_path ='https://dprepdata.blob.core.windows.net/demo/Titanic.csv'
titanic_ds = Dataset.Tabular.from_delimited_files(path=web_path, set_column_types={'Survived': DataType.to_bool()})

# preview the first 3 rows of titanic_ds
titanic_ds.take(3).to_pandas_dataframe()
```

|Index|Passageiroid|Survived|Pclass|Name|Sexo|Idade|SibSp|Parch|Tíquete|Tarifa|Cabine|Embarcou
-|-----------|--------|------|----|---|---|-----|-----|------|----|-----|--------|
0|1|Falso|3|Braund, Sr. Owen Harris|masculino|22,0|1|0|A/5 21171|7,2500||S
1|2|Verdadeiro|1|Cumings, Sra. John Bradley (Florence Briggs th...|feminino|38,0|1|0|PC 17599|71,2833|C85|C
2|3|Verdadeiro|3|Heikkinen, erro. Laina|feminino|26,0|0|0|STON/O2. 3101282|7,9250||S

Para criar um conjunto de dados a partir de um dataframe do pandas na memória, grave-os em um arquivo local, como um CSV, e crie seu conjunto de dado a partir desse arquivo. O código a seguir demonstra esse fluxo de trabalho.

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

Use o [`from_sql_query()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory?view=azure-ml-py#from-sql-query-query--validate-true--set-column-types-none--query-timeout-30-) método na `TabularDatasetFactory` classe para ler do banco de dados SQL do Azure:

```Python

from azureml.core import Dataset, Datastore

# create tabular dataset from a SQL database in datastore. Take note of double parenthesis.
sql_datastore = Datastore.get(workspace, 'mssql')
sql_ds = Dataset.Tabular.from_sql_query((sql_datastore, 'SELECT * FROM my_table'))
```

No TabularDatasets, você pode especificar um carimbo de data/hora de uma coluna nos dados ou de onde quer que os dados de padrão de caminho sejam armazenados para habilitar uma característica de série temporal. Essa especificação permite uma filtragem fácil e eficiente por tempo.

Use o [`with_timestamp_columns()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#with-timestamp-columns-timestamp-none--partition-timestamp-none--validate-false----kwargs-) método na `TabularDataset` classe para especificar a coluna de carimbo de data/hora e habilitar a filtragem por tempo. Para obter mais informações, consulte [tabela de demonstração de API relacionada à série temporal com dados meteorológicos NOAA](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb).

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
### <a name="register-datasets"></a>Registrar conjuntos de os

Para concluir o processo de criação, registre seus conjuntos de registros com um espaço de trabalho. Use o [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) método para registrar conjuntos de registros com seu espaço de trabalho a fim de compartilhá-los com outras pessoas e reutilizá-los entre experimentos em seu espaço de trabalho:

```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-in-the-studio"></a>Criar conjuntos de valores no estúdio

As etapas e a animação a seguir mostram como criar um conjunto de um DataSet no [Azure Machine Learning Studio](https://ml.azure.com).

> [!Note]
> Os conjuntos de valores criados por meio do Azure Machine Learning Studio são automaticamente registrados no espaço de trabalho.

![Criar um conjunto de uma com a interface do usuário](./media/how-to-create-register-datasets/create-dataset-ui.gif)

Para criar um conjunto de um DataSet no estúdio:
1. Entre em https://ml.azure.com .
1. Selecione **conjuntos** de itens na seção **ativos** do painel esquerdo. 
1. Selecione **criar conjunto** de um para escolher a origem do conjunto de seus conjuntos de um. Essa fonte pode ser arquivos locais, um repositório de armazenamento ou URLs públicas.
1. Selecione **tabela** ou **arquivo** para o tipo de conjunto de texto.
1. Selecione **Avançar** para abrir o formulário **repositório de armazenamento e seleção de arquivo** . Neste formulário, você seleciona onde deseja manter o conjunto de dados após a criação, bem como selecionar quais arquivos que deseja usar para o conjunto. 
    1. Habilite ignorar validação se os dados estiverem em uma rede virtual. Saiba mais sobre o [isolamento e a privacidade da rede virtual](how-to-enable-virtual-network.md#machine-learning-studio).
1. Selecione **Avançar** para popular as **configurações e** os formulários de visualização e **esquema** ; Eles são populados de forma inteligente com base no tipo de arquivo e você pode configurar ainda mais seu conjunto de informações antes da criação nesses formulários. 
1. Selecione **Avançar** para examinar o formulário **confirmar detalhes** . Verifique suas seleções e crie um perfil de dados opcional para seu conjunto de dados. Saiba mais sobre a [criação de perfil de dados](how-to-use-automated-ml-for-ml-models.md#profile). 
1. Selecione **criar** para concluir a criação do conjunto de seus conjuntos de os.

## <a name="create-datasets-with-azure-open-datasets"></a>Criar conjuntos de itens com os conjuntos de valores abertos do Azure

Os [Conjuntos de dados abertos do Azure](https://azure.microsoft.com/services/open-datasets/) são conjuntos de dados públicos coletados que você pode usar para adicionar recursos específicos do cenário para soluções de aprendizado de máquina para obter modelos mais precisos. Os conjuntos de dados incluem dados de domínio público de clima, censo, feriados, segurança pública e localização que ajudam você a treinar os modelos de machine learning e aprimorar as soluções de previsão. Os conjuntos de itens abertos estão na nuvem em Microsoft Azure e são incluídos no SDK e na interface do usuário do espaço de trabalho.

### <a name="use-the-sdk"></a>Usar o SDK

Para criar conjuntos de valores com os conjuntos de itens abertos do Azure do SDK, verifique se você instalou o pacote com o `pip install azureml-opendatasets` . Cada conjunto de dados discretos é representado por sua própria classe no SDK, e determinadas classes estão disponíveis como um `TabularDataset` , `FileDataset` ou ambos. Consulte a [documentação de referência](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) para obter uma lista completa de classes.

Você pode recuperar determinadas classes como um `TabularDataset` ou o `FileDataset` , que permite manipular e/ou baixar os arquivos diretamente. Outras classes **só** podem obter um conjunto de um DataSet usando uma das `get_tabular_dataset()` `get_file_dataset()` funções ou. O exemplo de código a seguir mostra alguns exemplos desses tipos de classes.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando você registra um conjunto de dados criado a partir de DataSets abertos, nenhum dado é baixado imediatamente, mas os dados serão acessados posteriormente quando solicitado (durante o treinamento, por exemplo) de um local de armazenamento central.

### <a name="use-the-ui"></a>Usar a interface do usuário

Você também pode criar conjuntos de valores de classes de conjuntos de itens abertos por meio da interface do usuário. Em seu espaço de trabalho, selecione a guia **conjuntos de valores** em **ativos**. No menu suspenso **criar conjunto** de linhas, selecione **de conjuntos de valores abertos**.

![Abrir conjunto de um com a interface do usuário](./media/how-to-create-register-datasets/open-datasets-1.png)

Selecione um conjunto de um DataSet selecionando seu bloco. (Você tem a opção de filtrar usando a barra de pesquisa.) Selecione **Avançar**.

![Escolher conjunto de um](./media/how-to-create-register-datasets/open-datasets-2.png)

Escolha um nome sob o qual registrar o conjunto de dados e, opcionalmente, filtre-os usando os filtros disponíveis. Nesse caso, para o conjunto de data de feriados públicos, você filtra o período de tempo para um ano e o código do país apenas para os EUA. Selecione **Criar**.

![Definir parâmetros de conjunto de DataSet e criar conjunto de um](./media/how-to-create-register-datasets/open-datasets-3.png)

Agora, o conjunto de linhas está disponível em seu espaço de trabalho em **conjuntos**de os. Você pode usá-lo da mesma maneira que outros conjuntos de os que você criou.

## <a name="version-datasets"></a>Conjuntos de itens de versão

Você pode registrar um novo conjunto de registros com o mesmo nome criando uma nova versão. Uma versão de conjunto de dados é uma maneira de marcar o estado de seus dados de forma que você possa aplicar uma versão específica do conjunto para experimentação ou reprodução futura. Saiba mais sobre [as versões do conjunto](how-to-version-track-datasets.md)de informações.
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

## <a name="access-datasets-in-a-virtual-network"></a>Acessar conjuntos de os em uma rede virtual

Se o seu espaço de trabalho estiver em uma rede virtual, você deverá configurar o conjunto de um para ignorar a validação. Para obter mais informações sobre como usar armazenamentos de dados e conjuntos de dados em uma rede virtual, consulte [isolamento de rede durante o treinamento & inferência com redes virtuais privadas](how-to-enable-virtual-network.md#use-datastores-and-datasets).

## <a name="next-steps"></a>Próximas etapas

* Saiba [como treinar com conjuntos de](how-to-train-with-datasets.md)informações.
* Use o Machine Learning automatizado para [treinar com o TabularDatasets](https://aka.ms/automl-dataset).
* Para obter mais exemplos de treinamento de conjunto de informações, consulte os [blocos de anotações de exemplo](https://aka.ms/dataset-tutorial).
