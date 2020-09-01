---
title: Acompanhamento de MLflow para experimentos de Machine Learning
titleSuffix: Azure Machine Learning
description: Configure o MLflow com Azure Machine Learning para registrar métricas e artefatos de modelos de ML e implante seus modelos de ML como um serviço Web.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 06/04/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: df8a75c2297dc68549b2788a2d78dd2c7f576bc2
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89146837"
---
# <a name="track-model-metrics-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>Acompanhar métricas de modelo e implantar modelos de ML com MLflow e Azure Machine Learning (versão prévia)

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Este artigo demonstra como habilitar o URI de acompanhamento do MLflow e a API de registro, coletivamente conhecidos como [Acompanhamento do MLflow](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api), para conectar os experimentos de MLflow e Azure Machine Learning.  Isso permite que você,

+ Acompanhe e registre as métricas de experimento e os artefatos em seu [espaço de trabalho Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces). Se você já usa o Acompanhamento do MLflow para os experimentos, o espaço de trabalho fornece um local centralizado, seguro e escalável para armazenar métricas e modelos de treinamento.

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

 O Acompanhamento do MLflow oferece funcionalidades de registro de métricas e armazenamento de artefatos que estão disponíveis apenas por meio do [SDK do Python do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).


| Recurso | Implantação de & de rastreamento de MLflow &nbsp; | SDK do Python do Azure Machine Learning |  CLI do Azure Machine Learning | Azure Machine Learning Studio|
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
* [Instale o SDK do Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) no computador local. O SDK fornece a conectividade para que o MLflow acesse o espaço de trabalho.
* [Criar um Workspace do Azure Machine Learning](how-to-manage-workspace.md).

## <a name="track-local-runs"></a>Acompanhar execuções locais

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções locais no workspace do Azure Machine Learning.

Instale o pacote `azureml-mlflow` para usar o Acompanhamento do MLflow com o Azure Machine Learning nos experimentos executados localmente em um Jupyter Notebook ou editor de código.

```shell
pip install azureml-mlflow
```

Importe as classes `mlflow` e [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) para acessar o URI de acompanhamento do MLflow e configurar o espaço de trabalho.

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

Configure o ambiente de computação e execução de treinamento com a classe [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py). Inclua os pacotes de PIP `mlflow` e `azureml-mlflow` na seção [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) do ambiente. Crie [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) usando a computação remota como o destino de computação.

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

Com essa configuração de computação e execução de treinamento, use o método `Experiment.submit('train.py')` para enviar uma execução. Esse método define automaticamente o URI de acompanhamento do MLflow e direciona o registro do MLflow para o espaço de trabalho.

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>Acompanhar as execuções do Azure Databricks

O acompanhamento do MLflow com o Azure Machine Learning permite armazenar as métricas registradas e os artefatos das execuções do Azure Databricks no workspace do Azure Machine Learning.

Para executar os experimentos do Mlflow com o Azure Databricks, primeiro é necessário criar um [espaço de trabalho e cluster do Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). No cluster, instale a biblioteca *azureml-mlflow* no PyPi para garantir que o cluster tenha acesso às funções e classes necessárias.

Neste ponto, importe o notebook de experimento, anexe-o ao cluster do Azure Databricks e execute o experimento. 

### <a name="install-libraries"></a>Instalar bibliotecas

Para instalar as bibliotecas no cluster, navegue até a guia **Bibliotecas** e clique em **Instalar novo**

 ![mlflow com o diagrama do Azure Machine Learning](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

No campo **Pacote**, digite azureml-mlflow e clique em instalar. Repita essa etapa conforme necessário para instalar outros pacotes no cluster do experimento.

 ![mlflow com o diagrama do Azure Machine Learning](./media/how-to-use-mlflow/install-libraries.png)

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

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>Conectar os espaços de trabalho do Azure Databricks e do Azure Machine Learning

No [portal do Azure](https://ms.portal.azure.com), você pode vincular o espaço de trabalho do Azure Databricks (ADB) a um workspace do Azure Machine Learning novo ou existente. Para fazer isso, navegue até o espaço de trabalho do ADB e selecione o botão **Vincular workspace do Azure Machine Learning** no canto inferior direito. Vincular os espaços de trabalho permite acompanhar os dados do experimento no workspace do Azure Machine Learning. 

### <a name="link-mlflow-tracking-to-your-workspace"></a>Vincular acompanhamento do MLflow ao espaço de trabalho

Depois de instanciar o espaço de trabalho, defina o URI de acompanhamento do MLflow. Ao fazer isso, você vincula o acompanhamento do MLflow ao workspace do Azure Machine Learning. Após vincular, todos os experimentos vão para o serviço de acompanhamento gerenciado do Azure Machine Learning.

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>Definir diretamente o Acompanhamento do MLflow no notebook

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

No script de treinamento, importe o mlflow para usar as APIs de registro do MLflow e comece a registrar as métricas de execução. O exemplo a seguir registra a métrica de perda de época. 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>Automatizar a configuração do acompanhamento do MLflow

Em vez de definir manualmente o URI de rastreamento em todas as sessões subsequentes do notebook do experimento nos clusters, faça isso automaticamente usando este [script Init do Cluster de Acompanhamento do Azure Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md).

Quando configurado corretamente, é possível ver os dados do acompanhamento do MLflow na API REST do Azure Machine Learning e em todos os clientes, bem como no Azure Databricks por meio da interface do usuário do MLflow ou usando o cliente MLflow.

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>Exibir métricas e artefatos no espaço de trabalho

As métricas e os artefatos no registro do MLflow são mantidos no espaço de trabalho. Para exibi-los a qualquer momento, navegue até o espaço de trabalho e localize o experimento por nome no espaço de trabalho em [Azure Machine Learning Studio](https://ml.azure.com).  Ou execute o código abaixo. 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>Implantar modelos MLflow como um serviço Web

Implantar seus experimentos MLflow como um serviço Web Azure Machine Learning permite aproveitar os recursos de detecção de Azure Machine Learning de gerenciamento de modelos e descompasso de dados e aplicá-los aos seus modelos de produção.

O diagrama a seguir demonstra que, com a API de implantação do MLflow, você pode implantar seus modelos existentes do MLflow como um serviço Web Azure Machine Learning, apesar de suas estruturas--PyTorch, Tensorflow, scikit-learn, ONNX, etc., e gerenciar seus modelos de produção em seu espaço de trabalho.

![mlflow com o diagrama do Azure Machine Learning](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>Registrar seu modelo

Antes de implantar o, certifique-se de que seu modelo seja salvo para que você possa referenciá-lo e seu local de caminho para implantação. No script de treinamento, deve haver um código semelhante ao seguinte método [mlflow. sklearn. log_model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) , que salva o modelo no diretório de saídas especificado. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Inclua o `conda_env` parâmetro para passar uma representação de dicionário das dependências e do ambiente em que esse modelo deve ser executado.

### <a name="retrieve-model-from-previous-run"></a>Recuperar modelo da execução anterior

Para recuperar a execução, você precisa da ID de execução e do caminho no histórico de execução de onde o modelo foi salvo. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="deploy-the-model"></a>Implantar o modelo

Use o SDK do Azure Machine Learning para implantar o modelo como um serviço Web.

Primeiro, especifique a configuração de implantação. A ACI (instância de contêiner do Azure) é uma opção adequada para uma implantação rápida de desenvolvimento/teste, enquanto o AKS (serviço kubernetes do Azure) é adequado para implantações de produção escalonáveis.

#### <a name="deploy-to-aci"></a>Implantar no ACI

Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Em seguida, registre e implante o modelo usando o método de [implantação](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) Azure Machine Learning SDK. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>Implantar no AKS

Para implantar no AKS, primeiro crie um cluster AKS. Crie um cluster AKS usando o método [ComputeTarget. Create ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) . Pode levar de 20-25 minutos para criar um novo cluster.

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
Configure sua configuração de implantação com o método [deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) . Você também pode adicionar marcas e descrições para ajudar a manter o controle do seu serviço Web.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
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

O [MLflow com notebooks do Azure Machine Learning](https://aka.ms/azureml-mlflow-examples) demonstra e amplia os conceitos apresentados neste artigo.

## <a name="next-steps"></a>Próximas etapas
* [Gerenciar os modelos](concept-model-management-and-deployment.md).
* Monitore os modelos de produção para [descompasso de dados](how-to-monitor-data-drift.md).
