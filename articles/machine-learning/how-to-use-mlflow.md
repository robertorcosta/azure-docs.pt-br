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
ms.openlocfilehash: 116faae1bc0a93ce2007fcf809a8c96475289036
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897184"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Acompanhar métricas de modelo e implantar modelos de ML com MLflow e Azure Machine Learning (versão prévia)

Este artigo demonstra como habilitar o URI de acompanhamento do MLflow e a API de registro, coletivamente conhecidos como [Acompanhamento do MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), para conectar os experimentos de MLflow e Azure Machine Learning. 

Com Azure Machine Learning suporte nativo para MLflow você pode,

+ Acompanhe e registre as métricas de experimento e os artefatos em seu [espaço de trabalho Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se você já usa o Acompanhamento do MLflow para os experimentos, o espaço de trabalho fornece um local centralizado, seguro e escalável para armazenar métricas e modelos de treinamento.

+ Envie trabalhos de treinamento com projetos do MLflow com suporte de back-end Azure Machine Learning (versão prévia). Você pode enviar trabalhos localmente com rastreamento de Azure Machine Learning ou migrar suas execuções para a nuvem, como por meio de uma [computação Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute).

+ Acompanhe e gerencie modelos no MLflow e no registro de modelo de Azure Machine Learning.

+ Implante seus experimentos do MLflow como um serviço Web Azure Machine Learning. Ao implantar como um serviço Web, você pode aplicar as funcionalidades de Azure Machine Learning monitoramento e desinstalação de descompasso de dados aos seus modelos de produção. 

O [MLflow](https://www.mlflow.org) é uma biblioteca de open-source para gerenciar o ciclo de vida dos experimentos de aprendizado de máquina. O Acompanhamento do MLFlow é um componente do MLflow que registra e acompanha as métricas de execução de treinamento e os artefatos de modelo, independentemente do ambiente do experimento, localmente no computador, em um destino de computação remoto, em uma máquina virtual ou em um cluster do Azure Databricks. 

>[!NOTE]
> Como uma biblioteca de software livre, o MLflow muda com frequência. Dessa forma, a funcionalidade disponibilizada por meio da integração de Azure Machine Learning e MLflow deve ser considerada uma versão prévia e não tem suporte total da Microsoft.

O diagrama a seguir ilustra isso com o Acompanhamento do MLflow, você acompanha as métricas de execução de um experimento e armazena os artefatos de modelo no workspace do Azure Machine Learning.

![mlflow com o diagrama do Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> Este documento oferece informações principalmente para cientistas de dados e desenvolvedores que querem monitorar o processo de treinamento de modelo. Se você for um administrador interessado em monitorar o uso de recursos e os eventos do Azure Machine Learning, como cotas, execuções de treinamento concluídas ou implantações de modelo concluídas, consulte [Monitoramento do Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>Comparar clientes do MLflow e do Azure Machine Learning

 A tabela abaixo resume os diferentes clientes que podem usar o Azure Machine Learning e seus respectivos recursos de função.

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

* [Instalar MLflow.](https://mlflow.org/docs/latest/quickstart.html)
* [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true) no computador local. O SDK fornece a conectividade para que o MLflow acesse o espaço de trabalho.
* [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Acompanhar execuções locais

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções locais no workspace do Azure Machine Learning.

Instale o pacote `azureml-mlflow` para usar o Acompanhamento do MLflow com o Azure Machine Learning nos experimentos executados localmente em um Jupyter Notebook ou editor de código.

```shell
pip install azureml-mlflow
```

Importe as classes `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py&preserve-view=true) para acessar o URI de acompanhamento do MLflow e configurar o espaço de trabalho.

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

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções remotas no workspace do Azure Machine Learning.

As execuções remotas permitem treinar os modelos em computações mais eficazes, como máquinas virtuais habilitadas para GPU ou clusters de Computação do Machine Learning. Consulte [usar destinos de computação para treinamento de modelo](how-to-set-up-training-targets.md) para saber mais sobre diferentes opções de computação.

Configure o ambiente de computação e execução de treinamento com a classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true). Inclua os pacotes de PIP `mlflow` e `azureml-mlflow` na seção [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py&preserve-view=true) do ambiente. Crie [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) usando a computação remota como o destino de computação.

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

No script de treinamento, importe `mlflow` para usar as APIs de registro do MLflow e comece a registrar as métricas de execução.

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

Importe as classes `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) para acessar o URI de acompanhamento do MLflow e configurar o espaço de trabalho.

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

## <a name="track-azure-databricks-runs"></a>Acompanhar as execuções do Azure Databricks

O acompanhamento de MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos do seu Azure Databricks é executado em todas as três áreas a seguir ao mesmo tempo: 

* Workspace do Azure Machine Learning
* Workspace do Azure Databricks.
* MLflow

Para executar os experimentos do Mlflow com o Azure Databricks, primeiro é necessário criar um [espaço de trabalho e cluster do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). No cluster, instale a biblioteca *azureml-mlflow* de PyPi, para garantir que o cluster tenha acesso às funções e classes necessárias.

> [!NOTE]
> O `azureml.core` pacote inclui `azureml-mlflow` . Se você já tiver instalado `azureml.core` o, poderá ignorar a `azureml-mlflow` etapa de instalação. 

Neste ponto, importe o notebook de experimento, anexe-o ao cluster do Azure Databricks e execute o experimento. 

### <a name="install-libraries"></a>Instalar bibliotecas

Para instalar as bibliotecas no cluster, navegue até a guia **Bibliotecas** e clique em **Instalar novo**

 ![mlflow com o Azure databricks](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

No campo **Pacote**, digite azureml-mlflow e clique em instalar. Repita essa etapa conforme necessário para instalar outros pacotes no cluster do experimento.

 ![Biblioteca mlflow de instalação do BD do Azure](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>Configurar o notebook e o espaço de trabalho

Depois de configurar o cluster, importe o notebook do experimento, abra-o e anexe o cluster a ele.

O código a seguir deve estar no notebook do experimento. Esse código obtém os detalhes da assinatura do Azure para criar uma instância do espaço de trabalho. Esse código pressupõe que você tenha um grupo de recursos existente e o workspace do Azure Machine Learning, caso contrário, é possível [criá-los](how-to-manage-workspace.md). 

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

### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Conectar os espaços de trabalho do Azure Databricks e do Azure Machine Learning

No [portal do Azure](https://ms.portal.azure.com), você pode vincular o espaço de trabalho do Azure Databricks (ADB) a um workspace do Azure Machine Learning novo ou existente. Para fazer isso, navegue até o espaço de trabalho do ADB e selecione o botão **Vincular workspace do Azure Machine Learning** no canto inferior direito. Vincular os espaços de trabalho permite acompanhar os dados do experimento no workspace do Azure Machine Learning. 

### <a name="mlflow-tracking-in-your-workspaces"></a>Acompanhamento de MLflow em seus espaços de trabalho

Depois de instanciar seu espaço de trabalho, o acompanhamento de MLflow é definido automaticamente para ser acompanhado em todos os seguintes locais:

* O espaço de trabalho Azure Machine Learning vinculado.
* Seu espaço de trabalho do ADB original. 
* MLflow. 

Todos os experimentos vão parar o serviço de controle de Azure Machine Learning gerenciado.

#### <a name="set-mlflow-tracking-to-only-track-in-your-azure-machine-learning-workspace"></a>Definir o acompanhamento de MLflow para rastrear somente em seu espaço de trabalho Azure Machine Learning

Se preferir gerenciar seus experimentos controlados em um local centralizado, você poderá definir o acompanhamento de MLflow para rastrear **somente** em seu espaço de trabalho Azure Machine Learning. 


```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

No script de treinamento, importe `mlflow` para usar as APIs de registro do MLflow e comece a registrar as métricas de execução. O exemplo a seguir registra a métrica de perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
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

    ![registrado-mlflow-Model](./media/how-to-use-mlflow/registered-mlflow-model.png)

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

Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

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

Em seguida, registre e implante o modelo em uma etapa com o método de [implantação](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

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

Para implantar no AKS, primeiro crie um cluster AKS. Crie um cluster AKS usando o método [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#&preserve-view=truecreate-workspace--name--provisioning-configuration-) . Pode levar de 20-25 minutos para criar um novo cluster.

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
Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

Em seguida, registre e implante o modelo em uma etapa com o SDK do Azure Machine Learning [Deploy ()] (em seguida, registre e implante o modelo usando o método de [implantação](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) do SDK do Azure Machine Learning. 

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
