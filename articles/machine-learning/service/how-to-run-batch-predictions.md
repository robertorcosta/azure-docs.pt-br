---
title: Executar previsões em lotes para Big Data
titleSuffix: Azure Machine Learning
description: Saiba como obter inferências de forma assíncrona em grandes quantidades de dados usando a inferência em lote do Azure Machine Learning. A inferência de lote fornece recursos de processamento paralelo prontos para uso e otimiza a inferência de alta taxa de transferência do tipo “disparar e esquecer” para casos de uso de big data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: trbye, jmartens, larryfr, vaidyas
ms.author: vaidyas
author: vaidya-s
ms.date: 11/04/2019
ms.custom: Ignite2019
ms.openlocfilehash: 207e8def168227cb419d25c8e98aa15c09c72b2c
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851597"
---
# <a name="run-batch-inference-on-large-amounts-of-data-by-using-azure-machine-learning"></a>Executar inferência de lote em grandes quantidades de dados usando o Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como obter inferências sobre grandes quantidades de dados de forma assíncrona e em paralelo usando o Azure Machine Learning. A funcionalidade de inferência de lote descrita aqui está em visualização pública. É uma maneira de gerar inferências e processar dados com alto desempenho e alta taxa de transferência. Ela fornece recursos assíncronos prontos para uso.

Com a inferência de lote, é simples dimensionar inferências offline para grandes clusters de computadores em terabytes de dados de produção, resultando em produtividade aprimorada e custo otimizado.

Neste artigo, você aprenderá as seguintes tarefas:

> * Criar um recurso de computação remota.
> * Escrever um script de inferência personalizado.
> * Criar um [pipeline do Machine Learning](concept-ml-pipelines.md) para registrar um modelo de classificação de imagem pré-treinado com base no conjunto de [MNIST](https://publicdataset.azurewebsites.net/dataDetail/mnist/). 
> * Usar o modelo para executar a inferência de lote em imagens de amostra disponíveis em sua conta de armazenamento do Azure Blob. 

## <a name="prerequisites"></a>Pré-requisitos

* Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Azure Machine Learning](https://aka.ms/AMLFree).

* Para um início rápido passo a passo, faça o [tutorial de instalação](tutorial-1st-experiment-sdk-setup.md) caso ainda não tenha uma máquina virtual do notebook ou workspace do Azure Machine Learning. 

* Para gerenciar seu próprio ambiente e suas dependências, confira o [guia de instruções](how-to-configure-environment.md) sobre como configurar seu próprio ambiente. Execute `pip install azureml-sdk[notebooks] azureml-pipeline-core azureml-contrib-pipeline-steps` em seu ambiente para baixar as dependências necessárias.

## <a name="set-up-machine-learning-resources"></a>Configurar recursos de aprendizado de máquina

As ações a seguir configuram os recursos necessários para executar um pipeline de inferência de lote:

- Crie um armazenamento de dados que aponte para um contêiner de blob que tenha imagens para inferência.
- Configure referências de dados como entradas e saídas para a etapa do pipeline de inferência de lote.
- Configure um cluster de cálculo para executar a etapa de inferência de lote.

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

### <a name="configure-data-inputs-and-outputs"></a>Configurar entradas e saídas de dados

Agora, você precisa configurar entradas e saídas de dados, incluindo:

- o diretório que contém as imagens de entrada.
- O diretório em que o modelo pré-treinado está armazenado.
- O diretório que contém os rótulos.
- O diretório de saída.

`Dataset` é uma classe para explorar, transformar e gerenciar dados no Azure Machine Learning. Essa classe tem dois tipos: `TabularDataset` e `FileDataset`. Neste exemplo, você usará `FileDataset` como as entradas para a etapa do pipeline de inferência de lote. 

> [!NOTE] 
> Por enquanto, o suporte de `FileDataset` na inferência de lote é restrito ao armazenamento do Azure Blob. 

Você também pode fazer referência a outros conjuntos de dados em seu script de inferência personalizado. Por exemplo, você pode usá-lo para acessar rótulos em seu script para rotular imagens usando `Dataset.register` e `Dataset.get_by_name`.

Para obter mais informações sobre conjuntos de dados do Azure Machine Learning, confira [Criar e acessar conjuntos de dados (versão prévia)](https://docs.microsoft.com/azure/machine-learning/service/how-to-create-register-datasets).

Objetos `PipelineData` são usados para transferir dados intermediários entre as etapas do pipeline. Neste exemplo, ele é usado para saídas de inferência.

```python
from azureml.core.dataset import Dataset

mnist_ds_name = 'mnist_sample_data'

path_on_datastore = mnist_blob.path('mnist/')
input_mnist_ds = Dataset.File.from_files(path=path_on_datastore, validate=False)
registered_mnist_ds = input_mnist_ds.register(ws, mnist_ds_name, create_new_version=True)
named_mnist_ds = registered_mnist_ds.as_named_input(mnist_ds_name)

output_dir = PipelineData(name="inferences", 
                          datastore=def_data_store, 
                          output_path_on_compute="mnist/results")
```

### <a name="set-up-a-compute-target"></a>Configurar um destino de computação

No Azure Machine Learning, *computação* (ou *destino de computação*) refere-se a computadores ou clusters que executam as etapas computacionais no pipeline de aprendizado de máquina. Execute o código a seguir para criar um destino [AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py) baseado em CPU.

```python
from azureml.core.compute import AmlCompute, ComputeTarget
from azureml.core.compute_target import ComputeTargetException

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
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

Depois, registre o modelo com seu workspace para que ele esteja disponível para o recurso de computação remota.

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
- `init()`: Use essa função para qualquer preparação dispendiosa ou comum para inferência posterior. Por exemplo, use-a para carregar o modelo em um objeto global.
-  `run(mini_batch)`: A função será executada para cada instância de `mini_batch`.
    -  `mini_batch`: A inferência de lote invocará o método de execução e passará uma lista ou o DataFrame Pandas como um argumento para o método. Cada entrada em min_batch será um filepath se a entrada for um FileDataset, um DataFrame Pandas se a entrada for um TabularDataset.
    -  `response`: o método run() deve retornar um DataFrame Pandas ou uma matriz. Para append_row output_action, esses elementos retornados são acrescentados ao arquivo de saída comum. Para summary_only, o conteúdo dos elementos é ignorado. Para todas as ações de saída, cada elemento de saída retornado indica uma inferência bem-sucedida do elemento de entrada no minilote de entrada. O usuário deve garantir que dados suficientes sejam incluídos no resultado da inferência para mapear a entrada para a inferência. A saída de inferência será gravada no arquivo de saída, e não haverá garantia de que esteja em ordem; o usuário deverá usar alguma chave na saída para mapeá-la para entrada.

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

### <a name="how-to-access-other-files-in-init-or-run-functions"></a>Como acessar outros arquivos em funções `init()` ou `run()`

Se você tiver outro arquivo ou pasta no mesmo diretório que o script de inferência, poderá consultá-lo localizando o diretório de trabalho atual.

```python
script_dir = os.path.realpath(os.path.join(__file__, '..',))
file_path = os.path.join(script_dir, "<file_name>")
```

## <a name="build-and-run-the-batch-inference-pipeline"></a>Compilar e executar o pipeline de inferência de lote

Agora, você tem tudo o que precisa para compilar o pipeline.

### <a name="prepare-the-run-environment"></a>Prepare o ambiente de execução

Primeiro, especifique as dependências de seu script. Use este objeto mais tarde quando criar a etapa do pipeline.

```python
from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core.runconfig import DEFAULT_GPU_IMAGE

batch_conda_deps = CondaDependencies.create(pip_packages=["tensorflow==1.13.1", "pillow"])

batch_env = Environment(name="batch_environment")
batch_env.python.conda_dependencies = batch_conda_deps
batch_env.docker.enabled = True
batch_env.docker.base_image = DEFAULT_GPU_IMAGE
batch_env.spark.precache_packages = False
```

### <a name="specify-the-parameters-for-your-batch-inference-pipeline-step"></a>Especificar os parâmetros para a etapa do pipeline de inferência de lote

`ParallelRunConfig` é a principal configuração para a instância `ParallelRunStep` de inferência de lote introduzida recentemente no pipeline do Azure Machine Learning. Use-o para encapsular o script e configurar os parâmetros necessários, incluindo todos os parâmetros a seguir:
- `entry_script`: Um script de usuário como um caminho de arquivo local que será executado em paralelo em vários nós. Se `source_directly` estiver presente, use um caminho relativo. Caso contrário, use qualquer caminho que seja acessível no computador.
- `mini_batch_size`: O tamanho do minilote passado para uma única chamada de `run()`. (Opcional; o valor padrão é `1`.)
    - Para `FileDataset`, é o número de arquivos com um valor mínimo de `1`. Você pode combinar vários arquivos em um minilote.
    - Para `TabularDataset`, é o tamanho dos dados. Os valores de exemplo são `1024`, `1024KB`, `10MB` e `1GB`. O valor recomendado é `1MB`. O minilote de `TabularDataset` nunca ultrapassará os limites do arquivo. Por exemplo, se você tiver arquivos .csv com vários tamanhos, o menor arquivo será de 100 KB, e o maior será de 10 MB. Se você definir `mini_batch_size = 1MB`, os arquivos com um tamanho menor que 1 MB serão tratados como um minilote. Arquivos com um tamanho maior que 1 MB serão divididos em vários minilotes.
- `error_threshold`: O número de falhas de registro para `TabularDataset` e falhas de arquivo para `FileDataset` que devem ser ignorados durante o processamento. Se a contagem de erros de toda a entrada for acima desse valor, o trabalho será interrompido. O limite de erro é para toda a entrada, e não para minilotes individuais enviados ao método `run()`. O intervalo é `[-1, int.max]`. A parte `-1` indica que é para ignorar todas as falhas durante o processamento.
- `output_action`: Um dos seguintes valores indica como a saída será organizada:
    - `summary_only`: O script de usuário armazenará a saída. `ParallelRunStep` usará a saída somente para o cálculo do limite de erro.
    - `append_row`: Para todos os arquivos de entrada, somente um arquivo será criado na pasta de saída para acrescentar todas as saídas separadas por linha. O nome do arquivo será parallel_run_step.txt.
- `source_directory`: Caminhos para pastas que contêm todos os arquivos a serem executados no destino de computação (opcional).
- `compute_target`: Apenas `AmlCompute` tem suporte.
- `node_count`: O número de nós de computação a serem usados para executar o script do usuário.
- `process_count_per_node`: O número de processos por nó.
- `environment`: A definição de ambiente Python. Você pode configurá-lo para usar um ambiente Python existente ou para configurar um ambiente temporário para o experimento. A definição também é responsável por configurar as dependências de aplicativo necessárias (opcional).
- `logging_level`: Detalhamento do log. Os valores no detalhamento crescente são: `WARNING`, `INFO` e `DEBUG`. O padrão é `INFO` (opcional).
- `run_invocation_timeout`: O tempo limite de invocação do método `run()` em segundos. O valor padrão é `60`.

```python
from azureml.contrib.pipeline.steps import ParallelRunConfig

parallel_run_config = ParallelRunConfig(
    source_directory=scripts_folder,
    entry_script="digit_identification.py",
    mini_batch_size="5",
    error_threshold=10,
    output_action="append_row",
    environment=batch_env,
    compute_target=compute_target,
    node_count=4)
```

### <a name="create-the-pipeline-step"></a>Criar a etapa do pipeline

Crie a etapa do pipeline usando o script, a configuração do ambiente e os parâmetros. Especifique o destino de computação que você já anexou ao seu workspace como o destino de execução do script. Use `ParallelRunStep` para criar a etapa do pipeline de inferência de lote, que usa todos os seguintes parâmetros:
- `name`: O nome da etapa, com as seguintes restrições de nomenclatura: unique, 3-32 characters e regex ^\[a-z\]([-a-z0-9]*[a-z0-9])?$.
- `models`: Zero ou mais nomes de modelo já registrados no registro de modelo do Azure Machine Learning.
- `parallel_run_config`: Um objeto `ParallelRunConfig`, conforme definido anteriormente.
- `inputs`: Um ou mais conjuntos de dados do Azure Machine Learning de tipo único.
- `output`: Um objeto `PipelineData` que corresponde ao diretório de saída.
- `arguments`: Uma lista de argumentos passados para o script do usuário (opcional).
- `allow_reuse`: Se a etapa deve reutilizar os resultados anteriores quando executada com as mesmas configurações/entradas. Se esse parâmetro for `False`, uma nova execução sempre será gerada para essa etapa durante a execução do pipeline. (Opcional; o valor padrão é `True`.)

```python
from azureml.contrib.pipeline.steps import ParallelRunStep

parallelrun_step = ParallelRunStep(
    name="batch-mnist",
    models=[model],
    parallel_run_config=parallel_run_config,
    inputs=[named_mnist_ds],
    output=output_dir,
    arguments=[],
    allow_reuse=True
)
```

### <a name="run-the-pipeline"></a>Executar o pipeline

Agora execute o pipeline. Primeiro, crie um objeto `Pipeline` usando a referência do workspace e a etapa de pipeline que você criou. O parâmetro `steps` é uma matriz de etapas. Nesse caso, há apenas uma etapa para a pontuação do lote. Para criar pipelines que têm várias etapas, coloque as etapas em ordem nesta matriz.

Em seguida, use a função `Experiment.submit()` para enviar o pipeline para execução.

```python
from azureml.pipeline.core import Pipeline
from azureml.core.experiment import Experiment

pipeline = Pipeline(workspace=ws, steps=[parallelrun_step])
pipeline_run = Experiment(ws, 'digit_identification').submit(pipeline)
```

## <a name="monitor-the-batch-inference-job"></a>Monitorar o trabalho de inferência de lote

Um trabalho de inferência de lote pode levar muito tempo para ser concluído. Este exemplo monitora o progresso usando um widget Jupyter. Você também pode gerenciar o progresso do trabalho usando:

* Azure Machine Learning Studio. 
* Saída do console do objeto [`PipelineRun`](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.run.pipelinerun?view=azure-ml-py).

```python
from azureml.widgets import RunDetails
RunDetails(pipeline_run).show()

pipeline_run.wait_for_completion(show_output=True)
```

## <a name="next-steps"></a>Próximas etapas

Para ver esse processo funcionando de ponta a ponta, experimente o [notebook de inferência de lote](https://aka.ms/batch-inference-notebooks). 

Para obter diretrizes de depuração e solução de problemas para ParallelRunStep, confira o [guia de instruções](how-to-debug-batch-predictions.md).

Para obter diretrizes de depuração e solução de problemas para pipelines, confira o [guia de instruções](how-to-debug-pipelines.md).

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

