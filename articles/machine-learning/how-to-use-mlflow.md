---
title: MLflow Tracking para experimentos ML
titleSuffix: Azure Machine Learning
description: Configure o MLflow com o Azure Machine Learning para registrar métricas e artefatos de modelos ML criados em clusters Databricks, seu ambiente local ou ambiente VM.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: dce7db9fc508c70d79be62a7e97b3bf52a316b22
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76983691"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>Rastrear métricas de modelos com MLflow e Azure Machine Learning (visualização)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo demonstra como habilitar o URI de rastreamento e a API de registro do MLflow, coletivamente conhecida como [MLflow Tracking,](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)para conectar seus experimentos de MLflow e Azure Machine Learning. Isso permite rastrear e registrar métricas e artefatos de experimentos em seu [espaço de trabalho azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se você já usa o MLflow Tracking para seus experimentos, o espaço de trabalho fornece um local centralizado, seguro e escalável para armazenar métricas e modelos de treinamento.

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[O MLflow](https://www.mlflow.org) é uma biblioteca de código aberto para gerenciar o ciclo de vida de seus experimentos de aprendizado de máquina. O MLFlow Tracking é um componente do MLflow que registra e rastreia as métricas de execução de treinamento e artefatos de modelo, não importa o ambiente do seu experimento — localmente no seu computador, em um alvo de computação remota, em uma máquina virtual ou em um cluster Azure Databricks. 

O diagrama a seguir ilustra que, com o MLflow Tracking, você rastreia as métricas de execução de um experimento e armazena artefatos de modelo em seu espaço de trabalho Azure Machine Learning.

![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> As informações deste documento são principalmente para cientistas de dados e desenvolvedores que desejam monitorar o processo de treinamento do modelo. Se você é um administrador interessado em monitorar o uso de recursos e eventos do Azure Machine Learning, como cotas, corridas de treinamento concluídas ou implantações de modelos concluídas, consulte Monitorando o Aprendizado de [Máquina do Azure](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Compare clientes de MLflow e Machine Learning do Azure

 A tabela abaixo resume os diferentes clientes que podem usar o Azure Machine Learning e suas respectivas capacidades de função.

 O MLflow Tracking oferece funcionalidades de registro métrico e armazenamento de artefatos que só estão disponíveis de outra forma através do [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| | Rastreamento de&nbsp;fluxo de MLflow <!--& Deployment--> | Azure Machine Learning Python SDK |  CLI do Azure Machine Learning | Azure Machine Learning Studio|
|---|---|---|---|---|
| Gerenciar workspace |   | ✓ | ✓ | ✓ |
| Use data stores  |   | ✓ | ✓ | |
| Métricas de log      | ✓ | ✓ |   | |
| Carregar artefatos | ✓ | ✓ |   | |
| Métricas de exibição     | ✓ | ✓ | ✓ | ✓ |
| Gerenciar a computação   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>Pré-requisitos

* [Instale o MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Instale o Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) em seu computador local O SDK fornece a conectividade para o MLflow acessar seu espaço de trabalho.
* [Crie um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Acompanhe as corridas locais

O MLflow Tracking com o Azure Machine Learning permite que você armazene as métricas e artefatos registrados do seu local em seu espaço de trabalho azure Machine Learning.

Instale `azureml-mlflow` o pacote para usar o MLflow Tracking com o Azure Machine Learning em seus experimentos executados localmente em um Notebook Jupyter ou editor de código.

```shell
pip install azureml-mlflow
```

>[!NOTE]
>O espaço de nome azureml.contrib muda com freqüência, à medida que trabalhamos para melhorar o serviço. Assim, tudo nesse namespace deve ser considerado como uma versão prévia e sem o suporte total da Microsoft.

Importe `mlflow` [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) as classes e as classes para acessar o URI de rastreamento do MLflow e configure seu espaço de trabalho.

No código a `get_mlflow_tracking_uri()` seguir, o método atribui um endereço `ws`URI `set_tracking_uri()` de rastreamento exclusivo ao espaço de trabalho e aponta o URI de rastreamento MLflow para esse endereço.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>O URI de rastreamento é válido até uma hora ou menos. Se você reiniciar seu script após algum tempo ocioso, use a API get_mlflow_tracking_uri para obter um novo URI.

Defina o nome do `set_experiment()` experimento MLflow `start_run()`e inicie sua corrida de treinamento com . Em `log_metric()` seguida, use para ativar a API de registro de registro de MLflow e começar a registrar suas métricas de execução de treinamento.

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>Rastrear corridas remotas

O MLflow Tracking com o Azure Machine Learning permite que você armazene as métricas e artefatos registrados do seu controle remoto no espaço de trabalho do Azure Machine Learning.

As corridas remotas permitem que você treine seus modelos em cálculos mais poderosos, como máquinas virtuais habilitadas para GPU ou clusters de Machine Learning Compute. Consulte [Configurar metas de computação para treinamento de modelos](how-to-set-up-training-targets.md) para aprender sobre diferentes opções de computação.

Configure seu ambiente de corrida [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) de computação e treinamento com a classe. Inclua `mlflow` `azureml-mlflow` e pip pacotes [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) na seção do ambiente. Em [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) seguida, construa com sua computação remota como o alvo da computação.

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

Em seu script `mlflow` de treinamento, importe para usar as APIs de registro de mlflow e comece a registrar suas métricas de execução.

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

Com esta configuração de execução `Experiment.submit('train.py')` computacional e de treinamento, use o método para enviar uma corrida. Este método define automaticamente o URI de rastreamento de mLflow e direciona o registro do MLflow para o seu Espaço de Trabalho.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Track Azure Databricks é executado

O MLflow Tracking com o Azure Machine Learning permite que você armazene as métricas e artefatos registrados de seus Databricks do Azure executados no seu espaço de trabalho azure Machine Learning.

Para executar seus experimentos do Mlflow com o Azure Databricks, você precisa primeiro criar um [espaço de trabalho e cluster do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Em seu cluster, certifique-se de instalar a biblioteca *de fluxo azureml-ml* do PyPi, para garantir que seu cluster tenha acesso às funções e classes necessárias.

A partir daqui, importe seu notebook de experimento, anexe-o ao seu cluster Azure Databricks e execute seu experimento. 

### <a name="install-libraries"></a>Instalar bibliotecas

Para instalar bibliotecas em seu cluster, navegue até a guia **Bibliotecas** e clique **em Instalar novo**

 ![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

No **campo Pacote,** digite azureml-mlflow e clique em instalar. Repita esta etapa conforme necessário para instalar outros pacotes adicionais ao seu cluster para o seu experimento.

 ![mlflow com diagrama de aprendizagem de máquina azul](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configure seu notebook e espaço de trabalho

Uma vez configurado o cluster, importe seu caderno de experimentos, abra-o e conecte seu cluster a ele.

O código a seguir deve estar no seu caderno de experiências. Este código obtém os detalhes da sua assinatura do Azure para instanciar seu espaço de trabalho. Este código pressupõe que você tem um grupo de recursos existente e um espaço de trabalho de Aprendizado de Máquina do Azure, caso contrário, você pode [criá-los](how-to-manage-workspace.md). 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Conecte seus databricks do Azure e espaços de trabalho de aprendizado de máquina do Azure

No [portal Azure,](https://ms.portal.azure.com)você pode vincular seu espaço de trabalho Azure Databricks (ADB) a um novo ou existente espaço de trabalho de Aprendizado de Máquina do Azure. Para isso, navegue até o seu espaço de trabalho ADB e selecione o botão **link azure machine learning** no canto inferior direito. A vinculação de seus espaços de trabalho permite rastrear os dados do experimento no espaço de trabalho azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Link mLflow tracking para seu espaço de trabalho

Depois de instanciar seu espaço de trabalho, defina o URI de rastreamento de mLflow. Ao fazer isso, você vincula o rastreamento do MLflow ao espaço de trabalho do Azure Machine Learning. Após a ligação, todos os seus experimentos desembarcarão no serviço de rastreamento de aprendizado de máquina azure gerenciado.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Defina diretamente o rastreamento de fluxo de ML em seu notebook

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

Em seu script de treinamento, importe mlflow para usar as APIs de registro de mLflow e comece a registrar suas métricas de execução. O exemplo a seguir registra a métrica de perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatize a configuração do rastreamento de fluxo de ML

Em vez de definir manualmente o URI de rastreamento em cada sessão de notebook de experimento subseqüente em seus clusters, faça-o automaticamente usando este [script Azure Machine Learning Tracking Cluster Init](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Quando configurado corretamente, você pode ver seus dados de rastreamento de MLflow na API azure Machine Learning REST e todos os clientes, e no Azure Databricks através da interface de usuário Do MLflow ou usando o cliente MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Veja métricas e artefatos em seu espaço de trabalho

As métricas e artefatos do registro de MLflow são mantidos em seu espaço de trabalho. Para visualizá-los a qualquer momento, navegue até o seu espaço de trabalho e encontre o experimento pelo nome em seu espaço de trabalho no [estúdio Azure Machine Learning](https://ml.azure.com).  Ou execute o código abaixo. 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>Blocos de anotações de exemplo

O [MLflow com notebooks Azure ML](https://aka.ms/azureml-mlflow-examples) demonstra e expande os conceitos apresentados neste artigo.

## <a name="next-steps"></a>Próximas etapas
* [Gerencie seus modelos](concept-model-management-and-deployment.md).
* Monitore seus modelos de produção para [deriva de dados](how-to-monitor-data-drift.md).
