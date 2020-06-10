---
title: Executar previsões em lotes para Big Data
titleSuffix: Azure Machine Learning
description: Saiba como obter inferências de maneira assíncrona em grandes quantidades de dados usando ParallelRunStep no Azure Machine Learning. ParallelRunStep fornece funcionalidades de processamento paralelo prontas para uso e otimiza a inferência de alta taxa de transferência do tipo "disparar e esquecer" para casos de uso de Big Data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr
ms.author: tracych
author: tracychms
ms.date: 04/15/2020
ms.custom: Build2020, tracking-python
ms.openlocfilehash: b26527321cf7fc5ca7fc4b061f11b86f8830ec29
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/09/2020
ms.locfileid: "84552311"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Executar inferência de lote em grandes quantidades de dados usando o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como executar a inferência de lote em grandes quantidades de dados de maneira assíncrona e em paralelo usando o Azure Machine Learning. O ParallelRunStep fornece funcionalidades de paralelismo prontas para uso.

Com ParallelRunStep, é simples dimensionar inferências offline para grandes clusters de computadores em terabytes de estruturados ou não estruturados com maior produtividade e custo otimizado.

Neste artigo, você aprenderá as seguintes tarefas:

> * Configurar recursos de machine learning.
> * Configurar entradas e a saída de dados de inferência de lote.
> * Preparar o modelo de classificação de imagens pré-treinado com base no conjunto de dados do [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/). 
> * Escrever seu script de inferência.
> * Criar um [pipeline de machine learning](concept-ml-pipelines.md) contendo ParallelRunStep e executar a inferência de lote em imagens de teste do MNIST. 
> * Reenviar uma execução de inferência de lote com novos parâmetros e entrada de dados. 

## <a name="prerequisites"></a>Pré-requisitos

* Caso não tenha uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para um início rápido passo a passo, conclua o [tutorial de instalação](tutorial-1st-experiment-sdk-setup.md) caso ainda não tenha um workspace do Azure Machine Learning. 

* Para gerenciar seu próprio ambiente e suas dependências, confira o [guia de instruções](how-to-configure-environment.md) sobre como configurar seu próprio ambiente local.

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

As ações a seguir configuram os recursos de machine learning necessários para executar um pipeline de inferência de lote:

- Conectar-se a um workspace.
- Criar ou anexar um recurso de computação existente.

### <a name="configure-workspace"></a>Configurar o workspace

Crie um objeto de workspace a partir do workspace existente. `Workspace.from_config()` lê o arquivo config.json e carrega os detalhes em um objeto chamado ws.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

> [!IMPORTANT]
> Esse snippet de código espera que a configuração do workspace seja salva no diretório atual ou no pai dele. Para obter mais informações sobre como criar um workspace, confira [Criar e gerenciar workspaces do Azure Machine Learning](how-to-manage-workspace.md). Para obter mais informações sobre como salvar a configuração no arquivo, confira [Criar um arquivo de configuração de workspace](how-to-configure-environment.md#workspace).

### <a name="create-a-compute-target"></a>Criar um destino de computação

No Azure Machine Learning, *computação* (ou *destino de computação*) refere-se a computadores ou clusters que executam as etapas computacionais no pipeline de aprendizado de máquina. Execute o código a seguir para criar um destino [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) baseado em CPU.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

## <a name="configure-inputs-and-output"></a>Configurar entradas e a saída

### <a name="create-a-datastore-with-sample-images"></a>Criar um armazenamento de dados com imagens de amostra

Obtenha o conjunto de avaliação do MNIST do contêiner de blob público `sampledata` em uma conta chamada `pipelinedata`. Crie um armazenamento de dados com o nome `mnist_datastore`, que aponta para esse contêiner. Na chamada a seguir para `register_azure_blob_container`, configurar o sinalizador `overwrite` como `True` substitui qualquer armazenamento de dados criado anteriormente com esse nome. 

Você pode alterar essa etapa para apontar para o contêiner de blob fornecendo seus próprios valores para `datastore_name`, `container_name` e `account_name`.

```python
from azureml.core import Datastore
from azureml.core import Workspace

# Load workspace authorization details from config.json
ws = Workspace.from_config()

mnist_blob = Datastore.register_azure_blob_container(ws, 
                      datastore_name="mnist_datastore", 
                      container_name="sampledata", 
                      account_name="pipelinedata",
                      overwrite=True)
```

Depois, especifique o armazenamento de dados padrão do workspace como o armazenamento de dados de saída. Você o usará para a saída de inferência.

Ao criar seu workspace, [Arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) e [armazenamento de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) são conectados ao workspace por padrão. Arquivos do Azure é o armazenamento de dados padrão para um workspace, mas também é possível usar o armazenamento de Blob como um armazenamento de dados. Para obter mais informações, consulte [Opções de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-decide-blobs-files-disks).

```python
def_data_store = ws.get_default_datastore()
```

### <a name="create-the-data-inputs"></a>Criar as entradas de dados

As entradas para inferência de lote são os dados que você deseja participar para processamento paralelo. Um pipeline de inferência de lote aceita entradas de dados por meio de [`Dataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py).

`Dataset` destina-se a explorar, transformar e gerenciar dados no Azure Machine Learning. Há dois tipos: [`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) e [`FileDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.filedataset?view=azure-ml-py). Neste exemplo, você usará `FileDataset` como as entradas. `FileDataset` fornece a capacidade de baixar ou montar os arquivos no computador. Ao criar um conjunto de dados, você cria uma referência à localização da fonte de dados. Se você tiver aplicado transformações de subconfiguração ao conjunto de dados, elas também serão armazenadas no conjunto de dados. Os dados permanecem na localização existente, portanto, nenhum custo de armazenamento extra é gerado.

Para obter mais informações sobre conjuntos de dados do Azure Machine Learning, confira [Criar e acessar conjuntos de dados (versão prévia)](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
```

Para usar entradas de dados dinâmicos ao executar o pipeline de inferência de lote, você pode definir as entradas `Dataset` como um [`PipelineParameter`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.pipelineparameter?view=azure-ml-py). Você poderá especificar o conjunto de dados de entradas cada vez que reenviar uma execução de pipeline de inferência de lote.

```python
from azureml.data.dataset_consumption_config import DatasetConsumptionConfig
from azureml.pipeline.core import PipelineParameter

pipeline_param = PipelineParameter(name="mnist_param", default_value=input_mnist_ds)
input_mnist_ds_consumption = DatasetConsumptionConfig("minist_param_config", pipeline_param).as_mount()
```

### <a name="create-the-output"></a>Criar a saída

Objetos [`PipelineData`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) são usados para transferir dados intermediários entre as etapas do pipeline. Neste exemplo, eles são usados para a saída de inferência.

```python
from azureml.pipeline.core import Pipeline, PipelineData

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

## <a name="prepare-the-model"></a>Preparar o modelo

[Baixe o modelo de classificação de imagem pré-treinado](https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz) e, em seguida, extraia-o para o diretório `models`.

```python
import os
import tarfile
import urllib.request

model_dir = 'models'
if not os.path.isdir(model_dir):
    os.mkdir(model_dir)

url="https://pipelinedata.blob.core.windows.net/mnist-model/mnist-tf.tar.gz"
response = urllib.request.urlretrieve(url, "model.tar.gz")
tar = tarfile.open("model.tar.gz", "r:gz")
tar.extractall(model_dir)
```

Depois, registre o modelo com seu workspace para que ele esteja disponível para o recurso de computação.

```python
from azureml.core.model import Model

# Register the downloaded model 
model = Model.register(model_path="models/",
                       model_name="mnist",
                       tags={'pretrained': "mnist"},
                       description="Mnist trained tensorflow model",
                       workspace=ws)
```

## <a name="write-your-inference-script"></a>Escrever seu script de inferência

>[!Warning]
>O código a seguir é apenas uma amostra usada pelo [notebook de exemplo](https://aka.ms/batch-inference-notebooks). Será preciso criar seu próprio script para o seu cenário.

O script *deve conter* duas funções:
- `init()`: Use essa função para qualquer preparação dispendiosa ou comum para inferência posterior. Por exemplo, use-a para carregar o modelo em um objeto global. Essa função será chamada apenas uma vez no início do processo.
-  `run(mini_batch)`: A função será executada para cada instância de `mini_batch`.
    -  `mini_batch`: ParallelRunStep invocará o método de execução e transmitirá uma lista ou o DataFrame do Pandas como um argumento para o método. Cada entrada em min_batch será – um caminho de arquivo se a entrada for um FileDataset, um DataFrame do Pandas se a entrada for um TabularDataset.
    -  `response`: o método run() deve retornar um DataFrame Pandas ou uma matriz. Para append_row output_action, esses elementos retornados são acrescentados ao arquivo de saída comum. Para summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída retornado indica uma execução bem-sucedida do elemento de entrada no minilote de entrada. Você deve verificar se dados suficientes foram incluídos no resultado da execução para mapear a entrada para o resultado da saída da execução. A saída de execução será gravada no arquivo de saída e não haverá garantia de que esteja em ordem; você deverá usar uma chave na saída para mapeá-la para a entrada.

```python
# Snippets from a sample script.
# Refer to the accompanying digit_identification.py
# (https://aka.ms/batch-inference-notebooks)
# for the implementation script.

import os
import numpy as np
import tensorflow as tf
from PIL import Image
from azureml.core import Model


def init():
    global g_tf_sess

    # Pull down the model from the workspace
    model_path = Model.get_model_path("mnist")

    # Construct a graph to execute
    tf.reset_default_graph()
    saver = tf.train.import_meta_graph(os.path.join(model_path, 'mnist-tf.model.meta'))
    g_tf_sess = tf.Session()
    saver.restore(g_tf_sess, os.path.join(model_path, 'mnist-tf.model'))


def run(mini_batch):
    print(f'run method start: {__file__}, run({mini_batch})')
    resultList = []
    in_tensor = g_tf_sess.graph.get_tensor_by_name("network/X:0")
    output = g_tf_sess.graph.get_tensor_by_name("network/output/MatMul:0")

    for image in mini_batch:
        # Prepare each image
        data = Image.open(image)
        np_im = np.array(data).reshape((1, 784))
        # Perform inference
        inference_result = output.eval(feed_dict={in_tensor: np_im}, session=g_tf_sess)
        # Find the best probability, and add it to the result list
        best_result = np.argmax(inference_result)
        resultList.append("{}: {}".format(os.path.basename(image), best_result))

    return resultList
```

Se você tiver outro arquivo ou pasta no mesmo diretório que o script de inferência, poderá consultá-lo localizando o diretório de trabalho atual.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-pipeline-containing-parallelrunstep"></a>Compilar e executar o pipeline que contém ParallelRunStep

Agora você tem tudo aquilo de que precisa: as entradas de dados, o modelo, a saída e o script de inferência. Vamos criar o pipeline de inferência de lote que contém ParallelRunStep.

### <a name="prepare-the-environment"></a>Preparar o ambiente

Primeiro, especifique as dependências de seu script. Isso permitirá que você instale pacotes pip e configure o ambiente. Inclua sempre os pacotes **azureml-core** e **azureml-dataprep[pandas, fuse]** .

Se você usar uma imagem personalizada do Docker (user_managed_dependencies=True), também deverá ter o conda instalado.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow",
                                                          "azureml-core", "azureml-dataprep[pandas, fuse]"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
```

### <a name="specify-the-parameters-using-parallelrunconfig"></a>Especificar os parâmetros usando ParallelRunConfig

`ParallelRunConfig` é a principal configuração para a instância `ParallelRunStep` no pipeline do Azure Machine Learning. Use-o para encapsular o script e configurar os parâmetros necessários, incluindo todos os itens a seguir:
- `entry_script`: Um script de usuário como um caminho de arquivo local que será executado em paralelo em vários nós. Se `source_directory` estiver presente, use um caminho relativo. Caso contrário, use qualquer caminho que seja acessível no computador.
- `mini_batch_size`: O tamanho do minilote passado para uma única chamada de `run()`. (opcional; o valor padrão é arquivos `10` para FileDataset e `1MB` para TabularDataset.)
    - Para `FileDataset`, é o número de arquivos com um valor mínimo de `1`. Você pode combinar vários arquivos em um minilote.
    - Para `TabularDataset`, é o tamanho dos dados. Os valores de exemplo são `1024`, `1024KB`, `10MB` e `1GB`. O valor recomendado é `1MB`. O minilote de `TabularDataset` nunca ultrapassará os limites do arquivo. Por exemplo, se você tiver arquivos .csv com vários tamanhos, o menor arquivo será de 100 KB, e o maior será de 10 MB. Se você definir `mini_batch_size = 1MB`, os arquivos com um tamanho menor que 1 MB serão tratados como um minilote. Arquivos com um tamanho maior que 1 MB serão divididos em vários minilotes.
- `error_threshold`: O número de falhas de registro para `TabularDataset` e falhas de arquivo para `FileDataset` que devem ser ignorados durante o processamento. Se a contagem de erros de toda a entrada ficar acima desse valor, o trabalho será anulado. O limite de erro é para toda a entrada, não para um minilote individual enviado ao método `run()`. O intervalo é `[-1, int.max]`. A parte `-1` indica que é para ignorar todas as falhas durante o processamento.
- `output_action`: Um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: O script de usuário armazenará a saída. `ParallelRunStep` usará a saída somente para o cálculo do limite de erro.
    - `append_row`: para todas as entradas, somente um arquivo será criado na pasta de saída para acrescentar todas as saídas separadas por linha.
- `append_row_file_name`: para personalizar o nome do arquivo de saída para append_row output_action (opcional; o valor padrão é `parallel_run_step.txt`).
- `source_directory`: Caminhos para pastas que contêm todos os arquivos a serem executados no destino de computação (opcional).
- `compute_target`: Apenas `AmlCompute` tem suporte.
- `node_count`: O número de nós de computação a serem usados para executar o script do usuário.
- `process_count_per_node`: O número de processos por nó. A melhor prática é definir como o número de GPU ou CPU que um nó tem (opcional; o valor padrão é `1`).
- `environment`: A definição de ambiente Python. Você pode configurá-lo para usar um ambiente Python existente ou para configurar um ambiente temporário. A definição também é responsável por configurar as dependências de aplicativo necessárias (opcional).
- `logging_level`: Detalhamento do log. Os valores no detalhamento crescente são: `WARNING`, `INFO` e `DEBUG`. (opcional; o valor padrão é `INFO`)
- `run_invocation_timeout`: O tempo limite de invocação do método `run()` em segundos. (opcional; o valor padrão é `60`)
- `run_max_try`: contagem máxima de tentativas de `run()` para um minilote. Um `run()` falhará se uma exceção for gerada ou nada será retornado quando `run_invocation_timeout` for atingido (opcional; o valor padrão é `3`). 

Você pode especificar `mini_batch_size`, `node_count`, `process_count_per_node`, `logging_level`, `run_invocation_timeout` e `run_max_try` como `PipelineParameter` para que, ao reenviar uma execução de pipeline, você possa ajustar os valores de parâmetro. Neste exemplo, você usará PipelineParameter para `mini_batch_size` e `Process_count_per_node` e vai alterar esses valores ao reenviar uma execução mais tarde. 

```python
from azureml.pipeline.core import PipelineParameter
from azureml.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size=PipelineParameter(name="batch_size_param", default_value="5"),
    error_threshold=10,
    output_action="append_row",
    append_row_file_name="mnist_outputs.txt",
    environment=batch_env,
    compute_target=compute_target,
    process_count_per_node=PipelineParameter(name="process_count_param", default_value=2),
    node_count=2)
```

### <a name="create-the-parallelrunstep"></a>Criar o ParallelRunStep

Crie o ParallelRunStep usando o script, a configuração do ambiente e os parâmetros. Especifique o destino de computação que você já anexou ao seu workspace como o destino de execução do seu script de inferência. Use `ParallelRunStep` para criar a etapa do pipeline de inferência de lote, que usa todos os seguintes parâmetros:
- `name`: O nome da etapa, com as seguintes restrições de nomenclatura: unique, 3-32 characters e regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `parallel_run_config`: Um objeto `ParallelRunConfig`, conforme definido anteriormente.
- `inputs`: um ou mais conjuntos de dados do Azure Machine Learning de tipo único a serem particionados para processamento paralelo.
- `side_inputs`: um ou mais dados de referência ou conjuntos de dados usados como entradas laterais sem a necessidade de partição.
- `output`: Um objeto `PipelineData` que corresponde ao diretório de saída.
- `arguments`: uma lista de argumentos passados para o script do usuário. Use unknown_args para recuperá-los em seu script de entrada (opcional).
- `allow_reuse`: Se a etapa deve reutilizar os resultados anteriores quando executada com as mesmas configurações/entradas. Se esse parâmetro for `False`, uma nova execução sempre será gerada para essa etapa durante a execução do pipeline. (opcional; o valor padrão é `True`.)

```python
from azureml.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="predict-digits-mnist",
    parallel_run_config=parallel_run_config,
    inputs=[input_mnist_ds_consumption],
    output=output_dir,
    allow_reuse=True
)
```
### <a name="create-and-run-the-pipeline"></a>criar e executar o pipeline

Agora execute o pipeline. Primeiro, crie um objeto [`Pipeline`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline%28class%29?view=azure-ml-py) usando a referência do workspace e a etapa de pipeline que você criou. O parâmetro `steps` é uma matriz de etapas. Nesse caso, há apenas uma etapa para a inferência de lote. Para criar pipelines que têm várias etapas, coloque as etapas em ordem nesta matriz.

Em seguida, use a função `Experiment.submit()` para enviar o pipeline para execução.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
experiment = Experiment(ws, 'digit_identification')
pipeline_run = experiment.submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorar o trabalho de inferência de lote

Um trabalho de inferência de lote pode levar muito tempo para ser concluído. Este exemplo monitora o progresso usando um widget Jupyter. Você também pode monitorar o progresso do trabalho usando:

* Azure Machine Learning Studio. 
* Saída do console do objeto [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py).

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="resubmit-a-run-with-new-data-inputs-and-parameters"></a>Reenviar uma execução com novas entradas de dados e parâmetros

Como você fez as entradas e várias configurações como `PipelineParameter`, você pode reenviar uma execução de inferência de lote com uma entrada de conjunto de dados diferente e ajustar os parâmetros sem precisar criar um pipeline. Você usará o mesmo armazenamento de dados, mas usará apenas uma única imagem como entradas de dados.

```python
path_on_datastore = mnist_data.path('mnist/0.png')
single_image_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
single_image_ds._ensure_saved(ws)

pipeline_run_2 = experiment.submit(pipeline, 
                                   pipeline_parameters={"mnist_param": single_image_ds, 
                                                        "batch_size_param": "1",
                                                        "process_count_param": 1}
)

pipeline_run_2.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Próximas etapas

Para ver esse processo funcionando de ponta a ponta, experimente o [notebook de inferência de lote](https://aka.ms/batch-inference-notebooks). 

Para obter diretrizes de depuração e solução de problemas para ParallelRunStep, confira o [guia de instruções](how-to-debug-parallel-run-step.md).

Para obter diretrizes de depuração e solução de problemas para pipelines, confira o [guia de instruções](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

