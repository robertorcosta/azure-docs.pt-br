---
title: Dados de entrada e saída de gasodutos ML
titleSuffix: Azure Machine Learning
description: Preparar, consumir e gerar dados em pipelines de Machine Learning do Azure
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 04/01/2020
ms.openlocfilehash: 3dd1a82bf7fad1f201f5c0f52af944ef44a3fdf9
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879759"
---
# <a name="moving-data-into-and-between-ml-pipeline-steps-python"></a>Movendo dados para dentro e entre os passos do gasoduto ML (Python)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Os dados são centrais para os pipelines de aprendizagem de máquina. Este artigo fornece código para importar, transformar e mover dados entre etapas em um pipeline de Machine Learning do Azure. Para obter uma visão geral de como os dados funcionam no Azure Machine Learning, consulte [dados de acesso nos serviços de armazenamento do Azure](how-to-access-data.md). Para os benefícios e a estrutura dos gasodutos de Machine Learning do Azure, veja quais são os [pipelines de Machine Learning do Azure?](concept-ml-pipelines.md).

Esta artigo mostrará como:

- Use `Dataset` objetos para dados pré-existentes
- Acesse dados dentro de suas etapas
- Divida `Dataset` dados em subconjuntos, como subconjuntos de treinamento e validação
- Criar `PipelineData` objetos para transferir dados para a próxima etapa do pipeline
- Use `PipelineData` objetos como entrada para etapas de pipeline
- Crie `Dataset` novos `PipelineData` objetos a partir de você deseja persistir

## <a name="prerequisites"></a>Pré-requisitos

Você precisará de:

- Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

- O [Azure Machine Learning SDK para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou acesso ao estúdio [Azure Machine Learning](https://ml.azure.com/).

- Um Workspace do Azure Machine Learning.
  
  [Crie um espaço de trabalho de Aprendizado de Máquina do Azure](how-to-manage-workspace.md) ou use um existente através do Python SDK. Importe `Workspace` `Datastore` a classe e carregue suas `config.json` informações de `from_config()`assinatura do arquivo usando a função . Esta função procura o arquivo JSON no diretório atual por padrão, mas você também pode `from_config(path="your/file/path")`especificar um parâmetro de caminho para apontar para o arquivo usando .

   ```python
   import azureml.core
   from azureml.core import Workspace, Datastore
        
   ws = Workspace.from_config()
   ```

- Alguns dados pré-existentes. Este artigo mostra brevemente o uso de [um recipiente de blob Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview).

- Opcional: Um pipeline de aprendizado de máquina existente, como o descrito em Criar e executar pipelines de [aprendizado de máquina com o Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="use-dataset-objects-for-pre-existing-data"></a>Use `Dataset` objetos para dados pré-existentes 

A maneira preferida de ingerir dados em um pipeline é usar um objeto [Dataset.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) `Dataset`objetos representam dados persistentes disponíveis em todo o espaço de trabalho.

Existem muitas maneiras `Dataset` de criar e registrar objetos. Os conjuntos de dados tabulares são para dados delimitados disponíveis em um ou mais arquivos. Os conjuntos de dados de arquivo são para dados binários (como imagens) ou para dados que você analisará. As formas programáticas `Dataset` mais simples de criar objetos são usar blobs existentes no armazenamento de espaço de trabalho ou URLs públicas:

```python
datastore = Datastore.get(workspace, 'training_data')
iris_dataset = Dataset.Tabular.from_delimited_files(DataPath(datastore, 'iris.csv'))

cats_dogs_dataset = Dataset.File.from_files(
    paths='https://download.microsoft.com/download/3/E/1/3E1C3F21-ECDB-4869-8368-6DEBA77B919F/kagglecatsanddogs_3367a.zip',
    archive_options=ArchiveOptions(archive_type=ArchiveType.ZIP, entry_glob='**/*.jpg')
)
```

Para obter mais opções sobre a criação de conjuntos de dados com diferentes opções e de diferentes fontes, registrá-los e revisá-los na UI de Machine Learning do Azure, entender como o tamanho dos dados interage com a capacidade de computação e versão deles, consulte [Criar conjuntos de dados do Azure Machine Learning](how-to-create-register-datasets.md). 

### <a name="pass-datasets-to-your-script"></a>Passe conjuntos de dados para o seu script

Para passar o caminho do conjunto de `Dataset` dados para `as_named_input()` o seu script, use o método do objeto. Você pode passar o `DatasetConsumptionConfig` objeto resultante para o seu script `inputs` como um argumento ou, usando o `Run.get_context().input_datasets[]`argumento para o script do pipeline, você pode recuperar o conjunto de dados usando .

Depois de criar uma entrada nomeada, você pode `as_mount()` escolher `as_download()`seu modo de acesso: ou . Se o seu script processar todos os arquivos do seu conjunto de dados e o disco em seu recurso de computação for grande o suficiente para o conjunto de dados, o modo de acesso ao download é a melhor escolha. O modo de acesso ao download evitará a sobrecarga de transmissão dos dados em tempo de execução. Se o seu script acessar um subconjunto do conjunto de dados ou for muito grande para sua computação, use o modo de acesso de montagem. Para obter mais informações, leia [Mount vs. Download](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets#mount-vs-download)

Para passar um conjunto de dados para a etapa do pipeline:

1. Use `TabularDataset.as_named_inputs()` `FileDataset.as_named_input()` ou (sem 's' no `DatasetConsumptionConfig` final) para criar um objeto
1. Use `as_mount()` `as_download()` ou para definir o modo de acesso
1. Passe os conjuntos de dados `arguments` para `inputs` as etapas do pipeline usando o argumento ou o argumento

O trecho a seguir mostra o padrão comum `PythonScriptStep` de combinar essas etapas dentro do construtor: 

```python

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[iris_dataset.as_named_inputs('iris').as_mount()]
)
```

Você também pode usar `random_split()` métodos como e `take_sample()` criar várias entradas ou reduzir a quantidade de dados passados para a etapa do pipeline:

```python
seed = 42 # PRNG seed
smaller_dataset = iris_dataset.take_sample(0.1, seed=seed) # 10%
train, test = smaller_dataset.random_split(percentage=0.8, seed=seed)

train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    inputs=[train.as_named_inputs('train').as_download(), test.as_named_inputs('test').as_download()]
)
```

### <a name="access-datasets-within-your-script"></a>Acesse conjuntos de dados dentro do seu script

Entradas nomeadas no script de passo do `Run` pipeline estão disponíveis como um dicionário dentro do objeto. Recupere `Run` o `Run.get_context()` objeto ativo usando e, em `input_datasets`seguida, recupere o dicionário de entradas nomeadas usando . Se você `DatasetConsumptionConfig` passou o `arguments` objeto usando `inputs` o argumento em `ArgParser` vez do argumento, acesse os dados usando código. Ambas as técnicas são demonstradas no trecho a seguir.

```python
# In pipeline definition script:
# Code for demonstration only: It would be very confusing to split datasets between `arguments` and `inputs`
train_step = PythonScriptStep(
    name="train_data",
    script_name="train.py",
    compute_target=cluster,
    arguments=['--training-folder', train.as_named_inputs('train').as_download()]
    inputs=[test.as_named_inputs('test').as_download()]
)

# In pipeline script
parser = argparse.ArgumentParser()
parser.add_argument('--training-folder', type=str, dest='train_folder', help='training data folder mounting point')
args = parser.parse_args()
training_data_folder = args.train_folder

testing_data_folder = Run.get_context().input_datasets['test']
```

O valor passado será o caminho para os arquivos do conjunto de dados.

Também é possível acessar um `Dataset` registrado diretamente. Uma vez que os conjuntos de dados registrados são persistentes e compartilhados em um espaço de trabalho, você pode recuperá-los diretamente:

```python
run = Run.get_context()
ws = run.experiment.workspace
ds = Dataset.get_by_name(workspace=ws, name='mnist_opendataset')
```

## <a name="use-pipelinedata-for-intermediate-data"></a>Uso `PipelineData` para dados intermediários

Embora `Dataset` os objetos representem dados persistentes, os objetos [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) são usados para dados temporários que são produzidos a partir de etapas do pipeline. Como a vida `PipelineData` útil de um objeto é maior do que um único passo de pipeline, você os define no script de definição de pipeline. Quando você `PipelineData` cria um objeto, você deve fornecer um nome e um datastore no qual os dados residirão. Passe `PipelineData` seu objeto para `PythonScriptStep` o seu `arguments` uso `outputs` _tanto_ dos argumentos quanto dos argumentos:

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

Você pode optar `PipelineData` por criar seu objeto usando um modo de acesso que fornece um upload imediato. Nesse caso, quando você `PipelineData`criar `upload_mode` o `"upload"` seu `output_path_on_compute` , defina o e use o argumento para especificar o caminho para o qual você estará escrevendo os dados:

```python
PipelineData("clean_data", datastore=def_blob_store, output_mode="upload", output_path_on_compute="clean_data_output/")
```

### <a name="use-pipelinedata-as-outputs-of-a-training-step"></a>Use `PipelineData` como saídas de uma etapa de treinamento

Dentro do `PythonScriptStep`pipeline, você pode recuperar os caminhos de saída disponíveis usando os argumentos do programa. Se esta etapa for a primeira e inicializar os dados de saída, você deve criar o diretório no caminho especificado. Você pode então escrever os arquivos que `PipelineData`deseja estar contidos no .

```python
parser = argparse.ArgumentParser()
parser.add_argument('--output_path', dest='output_path', required=True)
args = parser.parse_args()

# Make directory for file
os.makedirs(os.path.dirname(args.output_path), exist_ok=True)
with open(args.output_path, 'w') as f:
    f.write("Step 1's output")
```

Se você criou `PipelineData` o `is_directory` seu `True`com o argumento definido, `os.makedirs()` seria suficiente apenas para executar a chamada e, em seguida, você estaria livre para escrever os arquivos que quisesse para o caminho. Para obter mais detalhes, consulte a documentação de referência [pipelineData.](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py)

### <a name="read-pipelinedata-as-inputs-to-non-initial-steps"></a>Leia `PipelineData` como entradas para etapas não iniciais

Depois que a etapa inicial `PipelineData` do pipeline grava alguns dados para o caminho e torna-se uma saída dessa etapa inicial, ele pode ser usado como uma entrada para uma etapa posterior:

```python
step1_output_data = PipelineData("processed_data", datastore=def_blob_store, output_mode="upload")

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

O valor `PipelineData` de uma entrada é o caminho para a saída anterior. Se, como mostrado anteriormente, o primeiro passo escreveu um único arquivo, consumindo-o pode parecer: 

```python
parser = argparse.ArgumentParser()
parser.add_argument('--pd', dest='pd', required=True)
args = parser.parse_args()

with open(args.pd) as f:
    print(f.read())
```

## <a name="convert-pipelinedata-objects-to-datasets"></a>Converter `PipelineData` objetos para `Dataset`s

Se você quiser disponibilizar `PipelineData` o seu disponível por mais tempo `as_dataset()` do que a `Dataset`duração de uma execução, use sua função para convertê-la em um . Você pode então `Dataset`registrar o , tornando-o um cidadão de primeira classe em seu espaço de trabalho. Uma `PipelineData` vez que seu objeto terá um caminho diferente cada vez que `create_new_version` `True` o pipeline `Dataset` for executado, `PipelineData` é altamente recomendável que você defina ao registrar um criado a partir de um objeto.

```python
step1_output_ds = step1_output_data.as_dataset()
step1_output_ds.register(name="processed_data", create_new_version=True)
```

## <a name="next-steps"></a>Próximas etapas

* [Crie um conjunto de dados de aprendizado de máquina do Azure](how-to-create-register-datasets.md)
* [Crie e execute pipelines de aprendizado de máquina com o Azure Machine Learning SDK](how-to-create-your-first-pipeline.md)
