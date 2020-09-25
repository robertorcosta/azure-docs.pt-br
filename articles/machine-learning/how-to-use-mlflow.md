---
title: Acompanhamento de MLflow para experimentos de Machine Learning
titleSuffix: Azure Machine Learning
description: Configure o MLflow com Azure Machine Learning para registrar métricas e artefatos de modelos de ML e implante seus modelos de ML como um serviço Web.
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3fb177afa804788632f22d24bbd376d64cbe1c9f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91250684"
---
# <a name="track-experiment-runs-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Acompanhar execuções de experimento e implantar modelos de ML com MLflow e Azure Machine Learning (visualização)

Neste artigo, saiba como habilitar o URI de acompanhamento do MLflow e a API de log, coletivamente conhecido como [acompanhamento de MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), para se conectar Azure Machine Learning como o back-end de seus experimentos de MLflow. 

Os recursos com suporte incluem: 

+ Acompanhe e registre as métricas de experimento e os artefatos em seu [espaço de trabalho Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se você já usa o Acompanhamento do MLflow para os experimentos, o espaço de trabalho fornece um local centralizado, seguro e escalável para armazenar métricas e modelos de treinamento.

+ Envie trabalhos de treinamento com projetos do MLflow com suporte de back-end Azure Machine Learning (versão prévia). Você pode enviar trabalhos localmente com rastreamento de Azure Machine Learning ou migrar suas execuções para a nuvem, como por meio de uma [computação Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute).

+ Acompanhe e gerencie modelos no MLflow e no registro de modelo de Azure Machine Learning.

+ Implante seus experimentos do MLflow como um serviço Web Azure Machine Learning. Ao implantar como um serviço Web, você pode aplicar as funcionalidades de Azure Machine Learning monitoramento e desinstalação de descompasso de dados aos seus modelos de produção. 

O [MLflow](https://www.mlflow.org) é uma biblioteca de open-source para gerenciar o ciclo de vida dos experimentos de aprendizado de máquina. O acompanhamento de MLFlow é um componente de MLflow que registra e rastreia suas métricas de execução de treinamento e artefatos de modelo, independentemente do ambiente de seu experimento, localmente em seu computador, em um destino de computação remota, em uma máquina virtual ou [em um cluster Azure Databricks](how-to-use-mlflow-azure-databricks.md). 

>[!NOTE]
> Como uma biblioteca de software livre, o MLflow muda com frequência. Dessa forma, a funcionalidade disponibilizada por meio da integração de Azure Machine Learning e MLflow deve ser considerada uma versão prévia e não tem suporte total da Microsoft.

O diagrama a seguir ilustra isso com o Acompanhamento do MLflow, você acompanha as métricas de execução de um experimento e armazena os artefatos de modelo no workspace do Azure Machine Learning.

![mlflow com o diagrama do Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Este documento oferece informações principalmente para cientistas de dados e desenvolvedores que querem monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparar clientes do MLflow e do Azure Machine Learning

 A tabela a seguir resume os diferentes clientes que podem usar Azure Machine Learning e seus respectivos recursos de função.

 O Acompanhamento do MLflow oferece funcionalidades de registro de métricas e armazenamento de artefatos que estão disponíveis apenas por meio do [SDK do Python do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true).

| Funcionalidade | Implantação de & de rastreamento de MLflow | SDK do Python do Azure Machine Learning |  CLI do Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gerenciar workspace |   | ✓ | ✓ | ✓ |
| Usar armazenamentos de dados  |   | ✓ | ✓ | |
| Registrar métricas      | ✓ | ✓ |   | |
| Carregar artefatos | ✓ | ✓ |   | |
| Métricas de exibição     | ✓ | ✓ | ✓ | ✓ |
| Gerenciar a computação   |   | ✓ | ✓ | ✓ |
| Implantar modelos    | ✓ | ✓ | ✓ | ✓ |
|Monitorar o desempenho do modelo||✓|  |   |
| Detectar descompasso de dados |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>Pré-requisitos

* Instale o pacote `azureml-mlflow`. 
    * Esse pacote automaticamente traz a `azureml-core` [Azure Machine Learning SDK do Python](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true), que fornece a conectividade para MLflow acessar seu espaço de trabalho.
* [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Acompanhar execuções locais

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções locais no workspace do Azure Machine Learning.

Importe as classes `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true) para acessar o URI de acompanhamento do MLflow e configurar o espaço de trabalho.

No código a seguir, o método `get_mlflow_tracking_uri()` atribui um endereço de URI de acompanhamento exclusivo ao espaço de trabalho, `ws` e `set_tracking_uri()` aponta o URI de acompanhamento do MLflow para esse endereço.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>O URI de acompanhamento é válido até uma hora ou menos. Se você reiniciar o script após algum tempo ocioso, use a API get_mlflow_tracking_uri para obter um novo URI.

Defina o nome do experimento do MLflow com `set_experiment()` e inicie a execução de treinamento com `start_run()`. Use `log_metric()` para ativar a API de registro do MLflow e começar a registrar as métricas de execução de treinamento.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Acompanhar execuções remotas

As execuções remotas permitem treinar os modelos em computações mais eficazes, como máquinas virtuais habilitadas para GPU ou clusters de Computação do Machine Learning. Consulte [usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md) para saber mais sobre diferentes opções de computação.

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções remotas no workspace do Azure Machine Learning. Qualquer execução com o código de rastreamento MLflow em ti terá as métricas registradas automaticamente no espaço de trabalho. 

O ambiente Conda de exemplo a seguir inclui `mlflow` e `azureml-mlflow` como pacotes Pip. 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

Em seu script, configure seu ambiente de computação e de execução de treinamento com a [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) classe. Em seguida, construa  [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) com sua computação remota como o destino de computação.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Com essa configuração de computação e execução de treinamento, use o método `Experiment.submit()` para enviar uma execução. Esse método define automaticamente o URI de acompanhamento do MLflow e direciona o registro do MLflow para o espaço de trabalho.

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>Treinar com projetos do MLflow

Os [projetos MLflow](https://mlflow.org/docs/latest/projects.html) permitem que você organize e descreva seu código para permitir que outros cientistas de dados (ou ferramentas automatizadas) o executem. Os projetos do MLflow com Azure Machine Learning permitem que você acompanhe e gerencie suas execuções de treinamento em seu espaço de trabalho. 

Este exemplo mostra como enviar projetos MLflow localmente com o rastreamento de Azure Machine Learning.

Instale o `azureml-mlflow` pacote para usar o acompanhamento do MLflow com o Azure Machine Learning em seus experimentos localmente. Seus experimentos podem ser executados por meio de um Jupyter notebook ou editor de código.

```shell
pip install azureml-mlflow
```

Importe as classes `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true) para acessar o URI de acompanhamento do MLflow e configurar o espaço de trabalho.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

Defina o nome do experimento do MLflow com `set_experiment()` e inicie a execução de treinamento com `start_run()`. Em seguida, use `log_metric()` para ativar a API de log MLflow e começar a registrar suas métricas de execução de treinamento.

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

Crie o objeto de configuração de back-end para armazenar as informações necessárias para a integração, como o destino de computação e qual tipo de ambiente gerenciado usar.

```python
backend_config = {"USE_CONDA": False}
```
Adicione o `azureml-mlflow` pacote como uma dependência Pip ao seu arquivo de configuração de ambiente para acompanhar métricas e artefatos de chave em seu espaço de trabalho. 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
Envie a execução local e verifique se você definiu o parâmetro `backend = "azureml" ` . Com essa configuração, você pode enviar execuções localmente e obter o suporte adicional de rastreamento de saída automático, arquivos de log, instantâneos e erros impressos em seu espaço de trabalho. 

Exiba suas execuções e métricas no [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md). 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Exibir métricas e artefatos no espaço de trabalho

As métricas e os artefatos no registro do MLflow são mantidos no espaço de trabalho. Para exibi-los a qualquer momento, navegue até o espaço de trabalho e localize o experimento por nome no espaço de trabalho em [Azure Machine Learning Studio](https://ml.azure.com).  Ou execute o código abaixo. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>Gerenciar modelos 

Registre e acompanhe seus modelos com o [registro de modelo de Azure Machine Learning](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) que dá suporte ao registro de modelo MLflow. Os modelos de Azure Machine Learning são alinhados com o esquema de modelo MLflow, facilitando a exportação e a importação desses modelos em diferentes fluxos de trabalho. Os metadados relacionados ao MLflow, como a ID de execução, também são marcados com o modelo registrado para rastreamento. Os usuários podem enviar execuções de treinamento, registrar e implantar modelos produzidos por meio de execuções do MLflow. 

Se você quiser implantar e registrar seu modelo pronto de produção em uma única etapa, consulte [implantar e registrar modelos MLflow](#deploy-and-register-mlflow-models).

Para registrar e exibir um modelo de uma execução, use as seguintes etapas:

1. Quando a execução for concluída, chame o `register_model()` método.

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. Exiba o modelo registrado em seu espaço de trabalho com o [Azure Machine Learning Studio](overview-what-is-machine-learning-studio.md).

    No exemplo a seguir, o modelo registrado `my-model` tem metadados de acompanhamento de MLflow marcados. 

    ![registrar-mlflow-modelo](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. Selecione a guia **artefatos** para ver todos os arquivos de modelo que se alinham com o esquema de modelo MLflow (Conda. YAML, MLmodel, Model. PKL).

    ![modelo-esquema](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. Selecione MLmodel para ver o arquivo MLmodel gerado pela execução.

    ![MLmodel-esquema](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>Implantar e registrar modelos MLflow 

Implantar seus experimentos MLflow como um serviço Web Azure Machine Learning permite que você aproveite e aplique os recursos de detecção de Azure Machine Learning de gerenciamento de modelos e de descompasso de dados aos seus modelos de produção.

Para fazer isso, você precisa

1. Registrar seu modelo.
1. Determine qual configuração de implantação você deseja usar para seu cenário.

    1. A [ACI (instância de contêiner do Azure)](#deploy-to-aci) é uma opção adequada para uma implantação rápida de desenvolvimento/teste.
    1. O [AKs (serviço kubernetes do Azure)](#deploy-to-aks) é adequado para implantações de produção escalonáveis.

O diagrama a seguir demonstra que, com a API de implantação do MLflow, você pode implantar seus modelos existentes do MLflow como um serviço Web Azure Machine Learning, apesar de suas estruturas--PyTorch, Tensorflow, scikit-learn, ONNX, etc., e gerenciar seus modelos de produção em seu espaço de trabalho.

![ implantar modelos mlflow com o Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>Implantar no ACI

Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Em seguida, registre e implante o modelo em uma etapa com o método de [implantação](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>Implantar no AKS

Para implantar no AKS, primeiro crie um cluster AKS. Crie um cluster AKS usando o método [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Pode levar de 20-25 minutos para criar um novo cluster.

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Em seguida, registre e implante o modelo em uma etapa com o SDK do Azure Machine Learning [Deploy ()] (em seguida, registre e implante o modelo usando o método de [implantação](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) do SDK do Azure Machine Learning. 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

A implantação do serviço pode levar vários minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não planeja usar as métricas registradas e os artefatos em seu espaço de trabalho, a capacidade de excluí-los individualmente não estará disponível no momento. Em vez disso, exclua o grupo de recursos que contém a conta de armazenamento e o espaço de trabalho, para que você não incorra nenhum encargo:

1. No portal do Azure, selecione **Grupos de recursos** no canto esquerdo.

   ![Exclusão no portal do Azure](./media/how-to-use-mlflow/delete-resources.png)

1. Selecione o grupo de recursos criado na lista.

1. Selecione **Excluir grupo de recursos**.

1. Insira o nome do grupo de recursos. Em seguida, selecione **Excluir**.

## <a name="example-notebooks"></a>Blocos de anotações de exemplo

O [MLflow com notebooks do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow) demonstra e amplia os conceitos apresentados neste artigo.

## <a name="next-steps"></a>Próximas etapas

* [Gerenciar os modelos](concept-model-management-and-deployment.md).
* Monitore os modelos de produção para [descompasso de dados](how-to-monitor-data-drift.md).
* [Acompanhe as execuções de Azure Databricks com MLflow](how-to-use-mlflow-azure-databricks.md). 
