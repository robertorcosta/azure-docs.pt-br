---
title: Pipeline de aprendizado de máquina YAML
titleSuffix: Azure Machine Learning
description: Aprenda a definir um pipeline de aprendizado de máquina usando um arquivo YAML. As definições de pipeline YAML são usadas com a extensão de aprendizado de máquina para o Azure CLI.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: sanpil
author: sanpil
ms.date: 11/11/2019
ms.openlocfilehash: 40e6d7f3d9c28708c5adec26ddc3c0463e75adc0
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81529698"
---
# <a name="define-machine-learning-pipelines-in-yaml"></a>Definir pipelines de aprendizado de máquina em YAML

Saiba como definir seus pipelines de aprendizado de máquina no [YAML](https://yaml.org/). Ao usar a extensão de aprendizado de máquina para o Azure CLI, muitos dos comandos relacionados ao pipeline esperam um arquivo YAML que define o pipeline.

A tabela a seguir lista o que é e não é suportado atualmente ao definir um pipeline em YAML:

| Tipo de etapa | Compatível? |
| ----- | :-----: |
| PythonScriptStep | Sim |
| AdlaStep | Sim |
| AzureBatchStep | Sim |
| DatabricksStep | Sim |
| DataTransferStep | Sim |
| Passo Automático | Não |
| HyperDriveStep | Não |
| MóduloStep | Sim |
| MPIStep | Não |
| EstimatorStep | Não |

## <a name="pipeline-definition"></a>Definição de pipeline

Uma definição de pipeline usa as seguintes teclas, que correspondem à classe [Pipelines:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline.pipeline?view=azure-ml-py)

| Tecla YAML | Descrição |
| ----- | ----- |
| `name` | A descrição do oleoduto. |
| `parameters` | Parâmetro(s) para o oleoduto. |
| `data_reference` | Define como e onde os dados devem ser disponibilizados em uma corrida. |
| `default_compute` | Alvo de computação padrão onde todas as etapas do pipeline são executadas. |
| `steps` | As etapas usadas no oleoduto. |

## <a name="parameters"></a>Parâmetros

A `parameters` seção usa as seguintes teclas, que correspondem à classe [PipelineParameter:](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py)

| Tecla YAML | Descrição |
| ---- | ---- |
| `type` | O tipo de valor do parâmetro. Os tipos `string` `int`válidos são, , `float`, `bool`, ou `datapath`. |
| `default` | O valor padrão. |

Cada parâmetro é nomeado. Por exemplo, o seguinte trecho yaml define `NumIterationsParameter` `DataPathParameter`três `NodeCountParameter`parâmetros nomeados , e :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        NumIterationsParameter:
            type: int
            default: 40
        DataPathParameter:
            type: datapath
            default:
                datastore: workspaceblobstore
                path_on_datastore: sample2.txt
        NodeCountParameter:
            type: int
            default: 4
```

## <a name="data-reference"></a>Referência de dados

A `data_references` seção usa as seguintes teclas, que correspondem à [Referência de Dados](https://docs.microsoft.com/python/api/azureml-core/azureml.data.data_reference.datareference?view=azure-ml-py):

| Tecla YAML | Descrição |
| ----- | ----- |
| `datastore` | O datastore para referência. |
| `path_on_datastore` | O caminho relativo no armazenamento de backup para a referência de dados. |

Cada referência de dados está contida em uma chave. Por exemplo, o seguinte trecho YAML define uma referência `employee_data`de dados armazenada na chave denominada :

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
```

## <a name="steps"></a>Etapas

As etapas definem um ambiente computacional, juntamente com os arquivos a serem executados no ambiente. Para definir o tipo de `type` passo, use a chave:

| Tipo de etapa | Descrição |
| ----- | ----- |
| `AdlaStep` | Executa um script U-SQL com o Azure Data Lake Analytics. Corresponde à classe [AdlaStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.adlastep?view=azure-ml-py) |
| `AzureBatchStep` | Executa trabalhos usando o Azure Batch. Corresponde à classe [AzureBatchStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.azurebatchstep?view=azure-ml-py) |
| `DatabricsStep` | Adiciona um notebook Databricks, script Python ou JAR. Corresponde à classe [DatabricksStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.databricksstep?view=azure-ml-py) |
| `DataTransferStep` | Transfere dados entre opções de armazenamento. Corresponde à classe [DataTransferStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.datatransferstep?view=azure-ml-py) |
| `PythonScriptStep` | Executa um script Python. Corresponde à classe [PythonScriptStep.](https://docs.microsoft.com/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep?view=azure-ml-py) |

### <a name="adla-step"></a>Passo ADLA

| Tecla YAML | Descrição |
| ----- | ----- |
| `script_name` | O nome do script U-SQL `source_directory`(em relação ao ). |
| `compute_target` | O alvo de computação do Azure Data Lake para usar nesta etapa. |
| `parameters` | [Parâmetros](#parameters) para o oleoduto. |
| `inputs` | As entradas podem ser [InputPortBinding,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference,](#data-reference) [PortDataReference,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [Dataset,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [Dataset, DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [pipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Diretório que contém o script, assembléias, etc. |
| `priority` | O valor prioritário a ser usado para o trabalho atual. |
| `params` | Dicionário de pares de valor de nome. |
| `degree_of_parallelism` | O grau de paralelismo para usar neste trabalho. |
| `runtime_version` | A versão em tempo de execução do mecanismo Data Lake Analytics. |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando for executada novamente com as mesmas configurações. |

O exemplo a seguir contém uma definição de Passo ADLA:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        employee_data:
            datastore: adftestadla
            path_on_datastore: "adla_sample/sample_input.csv"
    default_compute: adlacomp
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AdlaStep"
            name: "MyAdlaStep"
            script_name: "sample_script.usql"
            source_directory: "D:\\scripts\\Adla"
            inputs:
                employee_data:
                    source: employee_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: adftestadla
                    bind_mode: mount
```

### <a name="azure-batch-step"></a>Etapa do lote azure

| Tecla YAML | Descrição |
| ----- | ----- |
| `compute_target` | O destino de computação do Azure Batch para usar nesta etapa. |
| `inputs` | As entradas podem ser [InputPortBinding,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference,](#data-reference) [PortDataReference,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [Dataset,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [Dataset, DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [pipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `source_directory` | Diretório que contém binários de módulo, executáveis, montagens, etc. |
| `executable` | Nome do comando/executável que será executado como parte deste trabalho. |
| `create_pool` | Bandeira booleana para indicar se criar a piscina antes de executar o trabalho. |
| `delete_batch_job_after_finish` | Bandeira booleana para indicar se deve excluir o trabalho da conta Batch depois que ele estiver concluído. |
| `delete_batch_pool_after_finish` | Bandeira booleana para indicar se deve excluir a piscina após o término do trabalho. |
| `is_positive_exit_code_failure` | Bandeira booleana para indicar se o trabalho falhar se a tarefa sair com um código positivo. |
| `vm_image_urn` | Se `create_pool` `True`for , e `VirtualMachineConfiguration`usar VM . |
| `pool_id` | A id da piscina onde o trabalho será executado. |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando for executada novamente com as mesmas configurações. |

O exemplo a seguir contém uma definição de etapa do Azure Batch:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        input:
            datastore: workspaceblobstore
            path_on_datastore: "input.txt"
    default_compute: testbatch
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "AzureBatchStep"
            name: "MyAzureBatchStep"
            pool_id: "MyPoolName"
            create_pool: true
            executable: "azurebatch.cmd"
            source_directory: "D:\\scripts\\AureBatch"
            allow_reuse: false
            inputs:
                input:
                    source: input
            outputs:
                output:
                    destination: output
                    datastore: workspaceblobstore
```

### <a name="databricks-step"></a>Passo de Databricks

| Tecla YAML | Descrição |
| ----- | ----- |
| `compute_target` | O alvo de computação do Azure Databricks para usar nesta etapa. |
| `inputs` | As entradas podem ser [InputPortBinding,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference,](#data-reference) [PortDataReference,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [Dataset,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [Dataset, DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [pipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `run_name` | O nome em Databricks para esta corrida. |
| `source_directory` | Diretório que contém o script e outros arquivos. |
| `num_workers` | O número estático de trabalhadores para o cluster de execução databricks. |
| `runconfig` | O caminho `.runconfig` para um arquivo. Este arquivo é uma representação YAML da classe [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Para obter mais informações sobre a estrutura deste arquivo, consulte [runconfigschema.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando for executada novamente com as mesmas configurações. |

O exemplo a seguir contém uma etapa databricks:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "dbtest"
    default_compute: mydatabricks
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DatabricksStep"
            name: "MyDatabrickStep"
            run_name: "DatabricksRun"
            python_script_name: "train-db-local.py"
            source_directory: "D:\\scripts\\Databricks"
            num_workers: 1
            allow_reuse: true
            inputs:
                blob_test_data:
                    source: blob_test_data
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="data-transfer-step"></a>Etapa de transferência de dados

| Tecla YAML | Descrição |
| ----- | ----- |
| `compute_target` | O destino de computação da Fábrica de Dados do Azure deve ser usado para esta etapa. |
| `source_data_reference` | Conexão de entrada que serve como fonte de operações de transferência de dados. Os valores suportados são [InputPortBinding,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference,](#data-reference) [PortDataReference,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [Dataset,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [Dataset, DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `destination_data_reference` | Conexão de entrada que serve como destino das operações de transferência de dados. Os valores suportados são [PipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) e [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando for executada novamente com as mesmas configurações. |

O exemplo a seguir contém uma etapa de transferência de dados:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        adls_test_data:
            datastore: adftestadla
            path_on_datastore: "testdata"
        blob_test_data:
            datastore: workspaceblobstore
            path_on_datastore: "testdata"
    default_compute: adftest
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "DataTransferStep"
            name: "MyDataTransferStep"
            adla_compute_name: adftest
            source_data_reference:
                adls_test_data:
                    source: adls_test_data
            destination_data_reference:
                blob_test_data:
                    source: blob_test_data
```

### <a name="python-script-step"></a>Etapa do script python

| Tecla YAML | Descrição |
| ----- | ----- |
| `inputs` | As entradas podem ser [InputPortBinding,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.inputportbinding?view=azure-ml-py) [DataReference,](#data-reference) [PortDataReference,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.portdatareference?view=azure-ml-py) [PipelineData,](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) [Dataset,](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset%28class%29?view=azure-ml-py) [Dataset, DatasetDefinition](https://docs.microsoft.com/python/api/azureml-core/azureml.data.dataset_definition.datasetdefinition?view=azure-ml-py)ou [PipelineDataset](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedataset?view=azure-ml-py). |
| `outputs` | As saídas podem ser [pipelineData](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinedata?view=azure-ml-py) ou [OutputPortBinding](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.outputportbinding?view=azure-ml-py). |
| `script_name` | O nome do script Python `source_directory`(em relação a ). |
| `source_directory` | Diretório que contém o script, ambiente Conda, etc. |
| `runconfig` | O caminho `.runconfig` para um arquivo. Este arquivo é uma representação YAML da classe [RunConfiguration.](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py) Para obter mais informações sobre a estrutura deste arquivo, consulte [runconfig.json](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json). |
| `allow_reuse` | Determina se a etapa deve reutilizar os resultados anteriores quando for executada novamente com as mesmas configurações. |

O exemplo a seguir contém uma etapa de script Python:

```yaml
pipeline:
    name: SamplePipelineFromYaml
    parameters:
        PipelineParam1:
            type: int
            default: 3
    data_references:
        DataReference1:
            datastore: workspaceblobstore
            path_on_datastore: testfolder/sample.txt
    default_compute: cpu-cluster
    steps:
        Step1:
            runconfig: "D:\\Yaml\\default_runconfig.yml"
            parameters:
                NUM_ITERATIONS_2:
                    source: PipelineParam1
                NUM_ITERATIONS_1: 7
            type: "PythonScriptStep"
            name: "MyPythonScriptStep"
            script_name: "train.py"
            allow_reuse: True
            source_directory: "D:\\scripts\\PythonScript"
            inputs:
                InputData:
                    source: DataReference1
            outputs:
                OutputData:
                    destination: Output4
                    datastore: workspaceblobstore
                    bind_mode: mount
```

### <a name="pipeline-with-multiple-steps"></a>Pipeline com várias etapas 

| Tecla YAML | Descrição |
| ----- | ----- |
| `steps` | Seqüência de uma ou mais definições pipelineStep. Note que `destination` o de `outputs` um passo se `inputs` tornou as chaves para o .| 

```yaml
pipeline:
    name: SamplePipelineFromYAML
    description: Sample multistep YAML pipeline
    data_references:
        TitanicDS:
            dataset_name: 'titanic_ds'
            bind_mode: download
    default_compute: cpu-cluster
    steps:
        Dataprep:
            type: "PythonScriptStep"
            name: "DataPrep Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "prep.py"
            arguments:
            - '--train_path'
            - output:train_path
            - '--test_path'
            - output:test_path
            allow_reuse: True
            inputs:
                titanic_ds:
                    source: TitanicDS
                    bind_mode: download
            outputs:
                train_path:
                    destination: train_csv
                    datastore: workspaceblobstore
                test_path:
                    destination: test_csv
        Training:
            type: "PythonScriptStep"
            name: "Training Step"
            compute: cpu-cluster
            runconfig: ".\\default_runconfig.yml"
            script_name: "train.py"
            arguments:
            - "--train_path"
            - input:train_path
            - "--test_path"
            - input:test_path
            inputs:
                train_path:
                    source: train_csv
                    bind_mode: download
                test_path:
                    source: test_csv
                    bind_mode: download

```

## <a name="schedules"></a>Agendas

Ao definir o cronograma de um pipeline, ele pode ser acionado pelo datastore ou recorrente com base em um intervalo de tempo. A seguir estão as chaves usadas para definir um cronograma:

| Tecla YAML | Descrição |
| ----- | ----- |
| `description` | Uma descrição da agenda. |
| `recurrence` | Contém configurações de recorrência, se o cronograma for recorrente. |
| `pipeline_parameters` | Quaisquer parâmetros exigidos pelo oleoduto. |
| `wait_for_provisioning` | Quer esperar o provisionamento do cronograma para ser concluído. |
| `wait_timeout` | O número de segundos para esperar antes de cronometrar. |
| `datastore_name` | O datastore para monitorar as bolhas modificadas/adicionadas. |
| `polling_interval` | Quanto tempo, em minutos, entre a votação para bolhas modificadas/adicionadas. Valor padrão: 5 minutos. Apenas suportado para agendamentos de datastore. |
| `data_path_parameter_name` | O nome do parâmetro de pipeline de caminho de dados a ser definido com o caminho de bolha alterado. Apenas suportado para agendamentos de datastore. |
| `continue_on_step_failure` | Se deve continuar a execução de outras etapas no PipelineRun submetido se uma etapa falhar. Se fornecido, substituirá `continue_on_step_failure` a configuração do gasoduto.
| `path_on_datastore` | Opcional. O caminho no datastore para monitorar as bolhas modificadas/adicionadas. O caminho está sob o contêiner para o datastore, de modo`path_on_datastore`que o caminho real que o cronograma monitora é o contêiner/ . Se não houver, o contêiner do armazenamento de dados é monitorado. As adições/modificações feitas em `path_on_datastore` uma subpasta não são monitoradas. Apenas suportado para agendamentos de datastore. |

O exemplo a seguir contém a definição de um cronograma acionado pelo datastore:

```yaml
Schedule: 
      description: "Test create with datastore" 
      recurrence: ~ 
      pipeline_parameters: {} 
      wait_for_provisioning: True 
      wait_timeout: 3600 
      datastore_name: "workspaceblobstore" 
      polling_interval: 5 
      data_path_parameter_name: "input_data" 
      continue_on_step_failure: None 
      path_on_datastore: "file/path" 
```

Ao definir um **cronograma recorrente,** `recurrence`use as seguintes teclas em:

| Tecla YAML | Descrição |
| ----- | ----- |
| `frequency` | Quantas vezes o horário se repete. Os valores `"Hour"` `"Day"`válidos `"Month"`são, `"Minute"`ou `"Week"`. |
| `interval` | Quantas vezes o horário dispara. O valor inteiro é o número de unidades de tempo para esperar até que o cronograma seja acionado novamente. |
| `start_time` | A hora de início da programação. O formato de string `YYYY-MM-DDThh:mm:ss`do valor é . Se não houver tempo de início, a primeira carga de trabalho será executada instantaneamente e as futuras cargas de trabalho serão executadas com base no cronograma. Se o tempo de início estiver no passado, a primeira carga de trabalho será executada no próximo tempo de execução calculado. |
| `time_zone` | O fuso horário para a hora de início. Se nenhum fuso horário for fornecido, UTC será usado. |
| `hours` | Se `frequency` `"Day"` for `"Week"`ou , você pode especificar um ou mais inteiros de 0 a 23, separados por commas, como as horas do dia em que o gasoduto deve funcionar. Só `time_of_day` `hours` ou `minutes` pode ser usado. |
| `minutes` | Se `frequency` `"Day"` for `"Week"`ou , você pode especificar um ou mais inteiros de 0 a 59, separados por commas, como os minutos da hora em que o gasoduto deve funcionar. Só `time_of_day` `hours` ou `minutes` pode ser usado. |
| `time_of_day` | Se `frequency` `"Day"` for `"Week"`ou , você pode especificar uma hora do dia para o cronograma ser executado. O formato de string `hh:mm`do valor é . Só `time_of_day` `hours` ou `minutes` pode ser usado. |
| `week_days` | Se `frequency` `"Week"`for, você pode especificar um ou mais dias, separados por commas, quando o cronograma deve ser executado. Os valores `"Tuesday"` `"Wednesday"`válidos `"Friday"` `"Saturday"` `"Sunday"`são, `"Monday"`e `"Thursday"`. |

O exemplo a seguir contém a definição de um cronograma recorrente:

```yaml
Schedule: 
    description: "Test create with recurrence" 
    recurrence: 
        frequency: Week # Can be "Minute", "Hour", "Day", "Week", or "Month". 
        interval: 1 # how often fires 
        start_time: 2019-06-07T10:50:00 
        time_zone: UTC 
        hours: 
        - 1 
        minutes: 
        - 0 
        time_of_day: null 
        week_days: 
        - Friday 
    pipeline_parameters: 
        'a': 1 
    wait_for_provisioning: True 
    wait_timeout: 3600 
    datastore_name: ~ 
    polling_interval: ~ 
    data_path_parameter_name: ~ 
    continue_on_step_failure: None 
    path_on_datastore: ~ 
```

## <a name="next-steps"></a>Próximas etapas

Aprenda a [usar a extensão CLI para Aprendizado de Máquina do Azure](reference-azure-machine-learning-cli.md).
