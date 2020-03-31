---
title: Como implantar modelos no Azure Kubernetes Service
titleSuffix: Azure Machine Learning
description: Aprenda a implantar seus modelos de Machine Learning do Azure como um serviço web usando o Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 01/16/2020
ms.openlocfilehash: db2e80ebb6cbe5f31f2d99a1403a15daf38fd877
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722349"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implantar um modelo em um cluster azure Kubernetes Service
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Aprenda a usar o Azure Machine Learning para implantar um modelo como serviço web no Azure Kubernetes Service (AKS). O Azure Kubernetes Service é bom para implantações de produção em alta escala. Use o serviço Azure Kubernetes se você precisar de um ou mais dos seguintes recursos:

- __Tempo de resposta rápido__.
- __Autoscaling__ do serviço implantado.
- __Opções__ de aceleração de hardware, como GPU e matrizes de portão programáveis em campo (FPGA).

> [!IMPORTANT]
> O dimensionamento de cluster não é fornecido através do Azure Machine Learning SDK. Para obter mais informações sobre o dimensionamento dos nós em um cluster AKS, consulte [Dimensionar a contagem de nós em um cluster AKS](../aks/scale-cluster.md).

Ao implantar no Azure Kubernetes Service, você implanta em um cluster AKS __conectado ao seu espaço de trabalho__. Existem duas maneiras de conectar um cluster AKS ao seu espaço de trabalho:

* Crie o cluster AKS usando o Azure Machine Learning SDK, o Machine Learning CLI ou [o estúdio Azure Machine Learning](https://ml.azure.com). Esse processo conecta automaticamente o cluster ao espaço de trabalho.
* Conecte um cluster AKS existente ao seu espaço de trabalho azure Machine Learning. Um cluster pode ser anexado usando o Azure Machine Learning SDK, Machine Learning CLI ou Azure Machine Learning studio.

> [!IMPORTANT]
> O processo de criação ou anexo é uma tarefa única. Uma vez que um cluster AKS esteja conectado ao espaço de trabalho, você pode usá-lo para implantações. Você pode desapegar ou excluir o cluster AKS se não precisar mais dele. Uma vez destacado ou excluído, você não poderá mais implantar no cluster.

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [Criar um espaço de trabalho de aprendizado de máquina do Azure](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se você não tem um modelo registrado, veja [Como e onde implantar modelos](how-to-deploy-and-where.md).

- A [extensão Azure CLI para serviço de aprendizado de máquina,](reference-azure-machine-learning-cli.md) [O Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Os trechos de código __Python__ neste artigo assumem que as seguintes variáveis estão definidas:

    * `ws`- Coloque-se no seu espaço de trabalho.
    * `model`- Ajuste para o seu modelo registrado.
    * `inference_config`- Definir a configuração de inferência para o modelo.

    Para obter mais informações sobre a definição dessas variáveis, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

- Os trechos da __CLI__ neste artigo supõem `inferenceconfig.json` que você criou um documento. Para obter mais informações sobre a criação deste documento, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Crie um novo cluster AKS

**Estimativa de**tempo: Aproximadamente 20 minutos.

Criar ou anexar um cluster AKS é um processo único para o seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, você deve criar um novo cluster na próxima vez que precisar ser implantado. Você pode ter vários clusters AKS conectados ao seu espaço de trabalho.

> [!TIP]
> Se você quiser proteger seu cluster AKS usando uma Rede Virtual Azure, você deve criar a rede virtual primeiro. Para obter mais informações, consulte [A experimentação e inferência segura com a Rede Virtual Do Azure](how-to-enable-virtual-network.md#aksvnet).

Se você quiser criar um cluster AKS para __desenvolvimento,__ __validação__e __teste__ em vez de produção, você pode especificar o propósito de __cluster__ para __testar__.

> [!WARNING]
> Se você `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`definir, o cluster criado não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de dev/teste também não garantem a tolerância a falhas. Recomendamos pelo menos 2 CPUs virtuais para clusters de dev/teste.

Os exemplos a seguir demonstram como criar um novo cluster AKS usando o SDK e o CLI:

**Usar o SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), se você `agent_count` escolher `vm_size`valores `DEV_TEST`personalizados para e `agent_count` , e `vm_size` `cluster_purpose` não é , então você precisa ter certeza multiplicado por é maior ou igual a 12 CPUs virtuais. Por exemplo, se `vm_size` você usa um "Standard_D3_v2", que tem 4 `agent_count` CPUs virtuais, então você deve escolher uma de 3 ou mais.
>
> O Azure Machine Learning SDK não oferece suporte para dimensionar um cluster AKS. Para dimensionar os nódulos no cluster, use a ui para o cluster AKS no estúdio Azure Machine Learning. Você só pode alterar a contagem de nós, não o tamanho do VM do cluster.

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Usando a CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Para obter mais informações, consulte o [alvo de cálculo az ml criar referência aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks)

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Estimativa de tempo:** Aproximadamente 5 minutos.

Se você já tem o cluster AKS em sua assinatura do Azure, e é a versão 1.17 ou inferior, você pode usá-lo para implantar sua imagem.

> [!TIP]
> O cluster AKS existente pode estar em uma região azure diferente do seu espaço de trabalho azure Machine Learning.
>
> Se você quiser proteger seu cluster AKS usando uma Rede Virtual Azure, você deve criar a rede virtual primeiro. Para obter mais informações, consulte [A experimentação e inferência segura com a Rede Virtual Do Azure](how-to-enable-virtual-network.md#aksvnet).

Ao anexar um cluster AKS a um espaço de trabalho, você `cluster_purpose` pode definir como você usará o cluster definindo o parâmetro.

Se você não `cluster_purpose` definir o parâmetro, ou definir, `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`então o cluster deve ter pelo menos 12 CPUs virtuais disponíveis.

Se você `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`definir, então o cluster não precisa ter 12 CPUs virtuais. Recomendamos pelo menos 2 CPUs virtuais para dev/teste. No entanto, um cluster configurado para dev/teste não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de dev/teste também não garantem a tolerância a falhas.

> [!WARNING]
> Não crie anexos múltiplos e simultâneos ao mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo quebrará os anexos existentes anteriores.
>
> Se você quiser reconectar um cluster AKS, por exemplo, para alterar sSL ou outra configuração de configuração de cluster, primeiro você deve remover o anexo existente usando [AksCompute.desacoplado()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Para obter mais informações sobre a criação de um cluster AKS usando o Azure CLI ou portal, consulte os seguintes artigos:

* [Criar um cluster AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

Os exemplos a seguir demonstram como anexar um cluster AKS existente ao seu espaço de trabalho:

**Usar o SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Usando a CLI**

Para anexar um cluster existente usando o CLI, você precisa obter o ID de recurso do cluster existente. Para obter este valor, use o seguinte comando. Substitua pelo `myexistingcluster` nome do seu cluster AKS. Substitua pelo `myresourcegroup` grupo de recursos que contém o cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Esse comando retorna um valor semelhante ao texto a seguir:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para anexar o cluster existente ao seu espaço de trabalho, use o seguinte comando. Substitua pelo `aksresourceid` valor devolvido pelo comando anterior. Substitua pelo `myresourcegroup` grupo de recursos que contém seu espaço de trabalho. Substitua pelo `myworkspace` nome do seu espaço de trabalho.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Para obter mais informações, consulte o [alvo de az ml de atcinência anexar referência aks.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

## <a name="deploy-to-aks"></a>Implantar no AKS

Para implantar um modelo no Azure Kubernetes Service, crie uma __configuração de implantação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Você também precisa de uma __configuração de inferência,__ que descreva o ambiente necessário para hospedar o modelo e o serviço web. Para obter mais informações sobre como criar a configuração de inferência, consulte [Como e onde implantar modelos](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Usar o SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Para obter mais informações sobre as classes, métodos e parâmetros utilizados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando a CLI

Para implantar usando o CLI, use o seguinte comando. Substitua pelo `myaks` nome do alvo de computação AKS. Substitua pelo `mymodel:1` nome e versão do modelo registrado. Substitua pelo `myservice` nome para fornecer este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Para obter mais informações, consulte [o modelo az ml implantar](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) referência.

### <a name="using-vs-code"></a>Usar o VS Code

Para obter informações sobre o uso do Código VS, consulte [implantar em AKS através da extensão VS Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A implantação através do Código VS requer que o cluster AKS seja criado ou conectado ao seu espaço de trabalho com antecedência.

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Implantar modelos para AKS usando rollout controlado (visualização)
Analisar e promover versões de modelos de forma controlada usando endpoints. Implante até 6 versões atrás de um único ponto final e configure o % de tráfego de pontuação para cada versão implantada. Você pode habilitar insights de aplicativos para visualizar métricas operacionais de pontos finais e versões implantadas.

### <a name="create-an-endpoint"></a>Criar um ponto de extremidade
Uma vez que você esteja pronto para implantar seus modelos, crie um ponto final de pontuação e implante sua primeira versão. O passo abaixo mostra como implantar e criar o ponto final usando o SDK. A primeira implantação será definida como a versão padrão, o que significa que o percentil de tráfego não especificado em todas as versões irá para a versão padrão.  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Atualizar e adicionar versões a um ponto final

Adicione outra versão ao seu ponto final e configure o percentil de tráfego de pontuação indo para a versão. Existem dois tipos de versões, um controle e uma versão de tratamento. Pode haver uma versão de tratamento múltipla para ajudar a comparar com uma única versão de controle.

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb"
endpoint.create_version(version_name = version_name_add,
                        inference_config=inference_config,
                        models=[model],
                        tags = {'modelVersion':'b'},
                        description = "my second version",
                        traffic_percentile = 10)
```

Atualize as versões existentes ou exclua-as em um ponto final. Você pode alterar o tipo padrão da versão, o tipo de controle e o percentil de tráfego.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Autenticação de serviço Web

Ao ser implantado no Azure Kubernetes Service, a autenticação __baseada em chaves__ é ativada por padrão. Você também pode habilitar a autenticação __baseada em tokens.__ A autenticação baseada em tokenexige que os clientes usem uma conta do Azure Active Directory para solicitar um token de autenticação, que é usado para fazer solicitações ao serviço implantado.

Para __desativar__ a autenticação, defina o parâmetro ao `auth_enabled=False` criar a configuração de implantação. O exemplo a seguir desativa a autenticação usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obter informações sobre autenticação de um aplicativo cliente, consulte o [modelo Consumir um Aprendizado de Máquina do Azure implantado como um serviço web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticação com chaves

Se a autenticação da chave estiver `get_keys` ativada, você poderá usar o método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisa regenerar uma chave, use[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticação com tokens

Para habilitar a autenticação `token_auth_enabled=True` do token, defina o parâmetro quando estiver criando ou atualizando uma implantação. O exemplo a seguir permite a autenticação de tokens usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se a autenticação do token estiver `get_token` ativada, você poderá usar o método para recuperar um token JWT e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Você precisará solicitar um novo token após `refresh_by` o tempo do token.
>
> A Microsoft recomenda fortemente que você crie seu espaço de trabalho azure Machine Learning na mesma região que o seu cluster Azure Kubernetes Service. Para autenticar com um token, o serviço web fará uma chamada para a região em que seu espaço de trabalho Azure Machine Learning é criado. Se a região do seu espaço de trabalho não estiver disponível, então você não será capaz de buscar um token para o seu serviço web, mesmo se o seu cluster estiver em uma região diferente do seu espaço de trabalho. Isso efetivamente faz com que a Autenticação baseada em Token esteja indisponível até que a região do seu espaço de trabalho esteja disponível novamente. Além disso, quanto maior a distância entre a região do seu cluster e a região do seu espaço de trabalho, mais tempo levará para buscar um token.

## <a name="update-the-web-service"></a>Atualizar o serviço Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Próximas etapas

* [Experimentação segura e inferência em uma rede virtual](how-to-enable-virtual-network.md)
* [Como implantar um modelo usando uma imagem Docker personalizada](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Proteger serviços Web do Azure Machine Learning com SSL](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Aprendizado de Máquina do Azure com insights de aplicativos](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
