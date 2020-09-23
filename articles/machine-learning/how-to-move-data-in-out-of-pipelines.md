---
title: Movendo dados em pipelines de ML
titleSuffix: Azure Machine Learning
description: Saiba mais sobre entrada & saída de dados em pipelines de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 08/20/2020
ms.topic: conceptual
ms.custom: how-to, contperfq4, devx-track-python
ms.openlocfilehash: fa78ab00e337ee6f2a42e23a2899d79a09a7ea3d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904972"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Movendo dados para e entre as etapas de pipeline de ML (Python)



Este artigo fornece código para importação, transformação e movimentação de dados entre etapas em um pipeline de Azure Machine Learning. Para obter uma visão geral de como os dados funcionam no Azure Machine Learning, consulte [acessar dados nos serviços de armazenamento do Azure](how-to-access-data.md). Para obter os benefícios e a estrutura de pipelines de Azure Machine Learning, confira [o que são pipelines de Azure Machine Learning?](concept-ml-pipelines.md).

Esta artigo mostrará como:

- Usar `Dataset` objetos para dados preexistentes
- Acessar dados em suas etapas
- Dividir `Dataset` dados em subconjuntos, como subconjuntos de treinamento e validação
- Criar `PipelineData` objetos para transferir dados para a próxima etapa do pipeline
- Usar `PipelineData` objetos como entrada para etapas de pipeline
- Criar novos `Dataset` objetos a partir dos `PipelineData` quais deseja persistir

> [!TIP]
> Uma experiência aprimorada para passar dados temporários entre etapas de pipeline e persistir seus dados após a execução do pipeline está disponível nas classes de visualização pública  [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) e [`OutputTabularDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.output_dataset_config.outputtabulardatasetconfig?view=azure-ml-py&preserve-view=true) .  Essas classes são recursos de visualização [experimental](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py#&preserve-view=truestable-vs-experimental) e podem mudar a qualquer momento.


## <a name="prerequisites"></a>Pré-requisitos

Você precisará de:

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- O [SDK do Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) ou acesso ao [Azure Machine Learning Studio](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning.
  
  [Crie um workspace do Azure Machine Learning](how-to-manage-workspace.md) ou use um existente por meio do SDK do Python. Importe as classes `Workspace` e `Datastore` e carregue as informações de assinatura do arquivo `config.json` usando a função `from_config()`. Essa função procura o arquivo JSON no diretório atual por padrão, mas você também pode especificar um parâmetro de caminho para apontar para o arquivo usando `from_config(path="your/file/path")` .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alguns dados já existentes. Este artigo mostra resumidamente o uso de um [contêiner de blob do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Opcional: um pipeline de Machine Learning existente, como o descrito em [criar e executar pipelines de Machine Learning com Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Usar `Dataset` objetos para dados preexistentes 

A maneira preferida de ingerir dados em um pipeline é usar um objeto [DataSet](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py&preserve-view=true) . `Dataset` os objetos representam dados persistentes disponíveis em um espaço de trabalho.

Há várias maneiras de criar e registrar `Dataset` objetos. Os conjuntos de dados tabulares são para a disponibilidade delimitada em um ou mais arquivos. Os conjuntos de dados de arquivo são para dado binário (como imagens) ou para dados que você analisará. As maneiras programáticas mais simples de criar `Dataset` objetos são usar BLOBs existentes no armazenamento do espaço de trabalho ou URLs públicas:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Para obter mais opções sobre como criar conjuntos de dados com opções diferentes e de fontes diferentes, registrá-los e analisá-los na interface do usuário do Azure Machine Learning, entender como o tamanho dos dados interage com a capacidade de computação e o controle de versão deles, consulte [criar conjuntos de Azure Machine Learning](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Passe conjuntos de valores para seu script

Para passar o caminho do conjunto de scripts para o script, use o `Dataset` método do objeto `as_named_input()` . Você pode passar o objeto resultante `DatasetConsumptionConfig` para o script como um argumento ou, usando o `inputs` argumento para o script de pipeline, você pode recuperar o conjunto de resultados usando `Run.get_context().input_datasets[]` .

Depois de criar uma entrada nomeada, você pode escolher seu modo de acesso: `as_mount()` ou `as_download()` . Se o seu script processar todos os arquivos em seu conjunto de recursos e o disco em seu recurso de computação for grande o suficiente para o conjunto de espaço, o modo de acesso de download será a melhor opção. O modo de acesso de download evitará a sobrecarga de transmitir os dados em tempo de execução. Se o script acessar um subconjunto do conjunto de um ou for muito grande para sua computação, use o modo de acesso de montagem. Para obter mais informações, leia [montar versus baixar](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Para passar um conjunto de um DataSet para a etapa de pipeline:

1. Use `TabularDataset.as_named_input()` ou `FileDataset.as_named_input()` (não está ' no final) para criar um `DatasetConsumptionConfig` objeto
1. Use `as_mount()` ou `as_download()` para definir o modo de acesso
1. Passe os conjuntos de itens para suas etapas de pipeline usando o `arguments` argumento ou o `inputs`

O trecho a seguir mostra o padrão comum de combinar essas etapas dentro do `PythonScriptStep` Construtor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_input('iris').as_mount()]
)
```

Você também pode usar métodos como `random_split()` e `take_sample()` para criar várias entradas ou reduzir a quantidade de dados passados para a etapa do pipeline:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_input('train').as_download(), test.as_named_input('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Acessar conjuntos de os em seu script

As entradas nomeadas para o script de etapa de pipeline estão disponíveis como um dicionário dentro do `Run` objeto. Recupere o `Run` objeto ativo usando `Run.get_context()` e, em seguida, recupere o dicionário de entradas nomeadas usando `input_datasets` . Se você passou o `DatasetConsumptionConfig` objeto usando o `arguments` argumento em vez do `inputs` argumento, acesse os dados usando o `ArgParser` código. As duas técnicas são demonstradas no trecho a seguir.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_input('train').as_download()]
    inputs=[test.as_named_input('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

O valor transmitido será o caminho para os arquivos do conjunto de valores.

Também é possível acessar um registrado `Dataset` diretamente. Como os conjuntos de dado registrados são persistentes e compartilhados em um espaço de trabalho, você pode recuperá-los diretamente:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Usar `PipelineData` para dados intermediários

Embora `Dataset` os objetos representem dados persistentes, os objetos [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) são usados para dados temporários que são gerados de etapas de pipeline. Como o tempo de vida de um `PipelineData` objeto é maior do que uma única etapa de pipeline, você os define no script de definição de pipeline. Ao criar um `PipelineData` objeto, você deve fornecer um nome e um repositório de dados no qual os mesmos serão armazenados. Passe seus `PipelineData` objetos para o `PythonScriptStep` usando _os_ `arguments` `outputs` argumentos e:

```python

default_datastore = workspace.get_default_datastore()
dataprep_output = PipelineData("clean_data", datastore=default_datastore)

dataprep_step = PythonScriptStep(
    name="prep_data",
    script_name="dataprep.py",
    compute_target=cluster,
    arguments=["--output-path", dataprep_output]
    inputs=[Dataset.get_by_name(workspace, 'raw_data')],
    outputs=[dataprep_output]
)

```

Você pode optar por criar o `PipelineData` objeto usando um modo de acesso que forneça um carregamento imediato. Nesse caso, ao criar seu `PipelineData` , defina o `upload_mode` como `"upload"` e use o `output_path_on_compute` argumento para especificar o caminho para o qual você irá gravar os dados:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

> [!TIP]
> Uma experiência aprimorada para passar dados intermediários entre etapas de pipeline está disponível com a classe de visualização pública, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Para obter um exemplo de código usando `OutputFileDatasetConfig` , consulte como [criar um pipeline de ml de duas etapas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).


### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Usar `PipelineData` como saídas de uma etapa de treinamento
Dentro de seu pipeline `PythonScriptStep` , você pode recuperar os caminhos de saída disponíveis usando os argumentos do programa. Se esta etapa for a primeira e iniciará os dados de saída, você deverá criar o diretório no caminho especificado. Em seguida, você pode gravar quaisquer arquivos que queira que estejam contidos no `PipelineData` .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()
# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Se você criou seu `PipelineData` com o `is_directory` argumento definido como `True` , seria suficiente apenas executar a `os.makedirs()` chamada e, em seguida, você estaria livre para escrever quaisquer arquivos que desejar no caminho. Para obter mais detalhes, consulte a documentação de referência do [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py&preserve-view=true) .


### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Ler `PipelineData` como entradas para etapas não iniciais

Depois que a etapa inicial do pipeline grava alguns dados no `PipelineData` caminho e se torna uma saída dessa etapa inicial, ela pode ser usada como uma entrada para uma etapa posterior:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")
# get adls gen 2 datastore already registered with the workspace
datastore = workspace.datastores['my_adlsgen2']

step1 = PythonScriptStep(
    name="generate_data",
    script_name="step1.py",
    runconfig = aml_run_config,
    arguments = ["--output_path", step1_output_data],
    inputs=[],
    outputs=[step1_output_data]
)

step2 = PythonScriptStep(
    name="read_pipeline_data",
    script_name="step2.py",
    compute_target=compute,
    runconfig = aml_run_config,
    arguments = ["--pd", step1_output_data],
    inputs=[step1_output_data]
)
pipeline = Pipeline(workspace=ws, steps=[step1, step2])
```

O valor de uma `PipelineData` entrada é o caminho para a saída anterior. 

> [!TIP]
> Uma experiência aprimorada para passar dados intermediários entre etapas de pipeline está disponível com a classe de visualização pública, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Para obter um exemplo de código usando `OutputFileDatasetConfig` , consulte como [criar um pipeline de ml de duas etapas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

Se, como mostrado anteriormente, a primeira etapa escreveu um único arquivo, consumi-lo pode ser semelhante a: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()
with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Converter `PipelineData` objetos em `Dataset` s

Se você quiser disponibilizar sua `PipelineData` disponibilidade por mais tempo do que a duração de uma execução, use sua `as_dataset()` função para convertê-la em um `Dataset` . Em seguida, você pode registrar o `Dataset` , tornando-o um cidadão de primeira classe em seu espaço de trabalho. Como seu `PipelineData` objeto terá um caminho diferente toda vez que o pipeline for executado, é altamente recomendável que você defina `create_new_version` como `True` ao registrar um `Dataset` criado a partir de um `PipelineData` objeto.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)

```
> [!TIP]
> Uma experiência aprimorada para persistir seus dados intermediários fora de suas execuções de pipeline está disponível com a classe de visualização pública, [`OutputFileDatasetConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.outputfiledatasetconfig?view=azure-ml-py&preserve-view=true) . Para obter um exemplo de código usando `OutputFileDatasetConfig` , consulte como [criar um pipeline de ml de duas etapas](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/pipeline-with-datasets/pipeline-for-image-classification.ipynb).

## <a name="next-steps"></a>Próximas etapas

* [Criar um conjunto de dados do Azure Machine Learning](how-to-create-register-datasets.md)
* [Crie e execute pipelines de machine learning com o SDK do Azure Machine Learning](how-to-create-your-first-pipeline.md)
