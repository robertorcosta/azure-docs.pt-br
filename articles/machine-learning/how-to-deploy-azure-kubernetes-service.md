---
title: Implantar modelos de ML no serviço kubernetes
titleSuffix: Azure Machine Learning
description: Saiba como implantar seus modelos de Azure Machine Learning como um serviço Web usando o serviço kubernetes do Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 58a8bd6b8e5594f36bf27a3ad76bee137fdd1160
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87433231"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implantar um modelo em um cluster do serviço kubernetes do Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Saiba como usar Azure Machine Learning para implantar um modelo como um serviço Web no AKS (serviço kubernetes do Azure). O serviço kubernetes do Azure é bom para implantações de produção de grande escala. Use o serviço kubernetes do Azure se você precisar de um ou mais dos seguintes recursos:

- __Tempo de resposta rápido__.
- __Dimensionamento__ automático do serviço implantado.
- Opções de __aceleração de hardware__ , como a GPU e as FPGA (matrizes de porta programável por campo).

> [!IMPORTANT]
> O dimensionamento do cluster não é fornecido por meio do SDK do Azure Machine Learning. Para obter mais informações sobre como dimensionar os nós em um cluster AKS, consulte [dimensionar a contagem de nós em um cluster AKs](../aks/scale-cluster.md).

Ao implantar no serviço kubernetes do Azure, você implanta em um cluster AKS que está __conectado ao seu espaço de trabalho__. Há duas maneiras de conectar um cluster AKS ao seu espaço de trabalho:

* Crie o cluster AKS usando o SDK do Azure Machine Learning, a CLI do Machine Learning ou o [Azure Machine Learning Studio](https://ml.azure.com). Esse processo conecta automaticamente o cluster ao espaço de trabalho.
* Anexe um cluster AKS existente ao seu espaço de trabalho Azure Machine Learning. Um cluster pode ser anexado usando o SDK do Azure Machine Learning, Machine Learning CLI ou Azure Machine Learning Studio.

O cluster AKS e o espaço de trabalho AML podem estar em grupos de recursos diferentes.

> [!IMPORTANT]
> O processo de criação ou anexo é uma tarefa ocasional. Depois que um cluster AKS estiver conectado ao espaço de trabalho, você poderá usá-lo para implantações. Você pode desanexar ou excluir o cluster AKS se não precisar mais dele. Depois de desanexado ou excluído, você não poderá mais implantar no cluster.

> [!IMPORTANT]
> É altamente recomendável depurar localmente antes de implantar no serviço Web, para obter mais informações, consulte [depurar localmente](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> Você também pode consultar a Azure Machine Learning- [implantar no bloco de anotações local](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>Pré-requisitos

- Um Workspace do Azure Machine Learning. Para obter mais informações, consulte [criar um Azure Machine Learning espaço de trabalho](how-to-manage-workspace.md).

- Um modelo de aprendizado de máquina registrado em seu espaço de trabalho. Se você não tiver um modelo registrado, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- A [extensão CLI do Azure para o serviço Machine Learning](reference-azure-machine-learning-cli.md), o [SDK do Azure Machine Learning Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)ou a [extensão Azure Machine Learning Visual Studio Code](tutorial-setup-vscode-extension.md).

- Os trechos de código __Python__ neste artigo pressupõem que as seguintes variáveis sejam definidas:

    * `ws`-Defina para seu espaço de trabalho.
    * `model`-Defina para o modelo registrado.
    * `inference_config`– Defina para a configuração de inferência para o modelo.

    Para obter mais informações sobre como definir essas variáveis, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- Os trechos de código da __CLI__ neste artigo pressupõem que você criou um `inferenceconfig.json` documento. Para obter mais informações sobre como criar este documento, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

- Se você precisar de um SLB (Standard Load Balancer) implantado em seu cluster em vez de um Load Balancer básico (BLB), crie um cluster no portal do AKS/CLI/SDK e anexe-o ao espaço de trabalho AML.

- Se você anexar um cluster AKS, que tem um [intervalo de IP autorizado habilitado para acessar o servidor de API](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), habilite os intervalos de IP do plano controle AML para o cluster AKs. O plano de controle AML é implantado em regiões emparelhadas e implanta inferência pods no cluster AKS. Sem acesso ao servidor de API, os pods inferência não podem ser implantados. Use os [intervalos de IP](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519) para ambas as [regiões emparelhadas]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions) ao habilitar os intervalos de IP em um cluster AKs.

__Os intervalos de IP Authroized funcionam apenas com Standard Load Balancer.__
 
 - O nome de computação deve ser exclusivo em um espaço de trabalho
   - O nome é obrigatório e deve ter entre 3 e 24 caracteres.
   - Os caracteres válidos são letras maiúsculas e minúsculas, dígitos e caractere.
   - O nome deve começar com uma letra
   - O nome deve ser exclusivo em todas as computações existentes em uma região do Azure. Você verá um alerta se o nome escolhido não for exclusivo
   
 - Se você quiser implantar modelos em nós de GPU ou nós FPGA (ou qualquer SKU específico), deverá criar um cluster com o SKU específico. Não há suporte para a criação de um pool de nós secundário em um cluster existente e a implantação de modelos no pool de nós secundário.
 
 



## <a name="create-a-new-aks-cluster"></a>Criar um novo cluster AKS

**Tempo estimado**: aproximadamente 10 minutos.

Criar ou anexar um cluster AKS é um processo de uma vez para seu espaço de trabalho. Você pode reutilizar esse cluster para várias implantações. Se você excluir o cluster ou o grupo de recursos que o contém, deverá criar um novo cluster na próxima vez que precisar implantá-lo. Você pode ter vários clusters AKS anexados ao seu espaço de trabalho.
 
Azure Machine Learning agora dá suporte ao uso de um serviço kubernetes do Azure que tem o link privado habilitado.
Para criar um cluster AKS privado, siga os documentos [aqui](https://docs.microsoft.com/azure/aks/private-clusters)

> [!TIP]
> Se você quiser proteger o cluster AKS usando uma rede virtual do Azure, primeiro deverá criar a rede virtual. Para obter mais informações, consulte [experimentação segura e inferência com a rede virtual do Azure](how-to-enable-virtual-network.md#aksvnet).

Se você quiser criar um cluster AKS para __desenvolvimento__, __validação__e __teste__ em vez de produção, poderá especificar a finalidade do __cluster__ para o __teste de desenvolvimento__.

> [!WARNING]
> Se você definir `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , o cluster criado não será adequado para o tráfego de nível de produção e poderá aumentar os tempos de inferência. Os clusters de desenvolvimento/teste também não garantem a tolerância a falhas. Recomendamos pelo menos duas CPUs virtuais para clusters de desenvolvimento/teste.

Os exemplos a seguir demonstram como criar um novo cluster AKS usando o SDK e a CLI:

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
> Para o [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py) , se você escolher valores personalizados para `agent_count` e `vm_size` , e `cluster_purpose` não for `DEV_TEST` , precisará ter certeza de que `agent_count` multiplicado pelo `vm_size` é maior ou igual a 12 CPUs virtuais. Por exemplo, se você usar um `vm_size` de "Standard_D3_v2", que tem 4 CPUs virtuais, deverá escolher um `agent_count` de 3 ou mais.
>
> O SDK do Azure Machine Learning não fornece suporte para dimensionar um cluster AKS. Para dimensionar os nós no cluster, use a interface do usuário para o cluster AKS no Azure Machine Learning Studio. Você só pode alterar a contagem de nós, não o tamanho da VM do cluster.

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget. Create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget. wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Usando a CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Para obter mais informações, consulte a referência do [AZ ml computetarget Create AKs](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) .

## <a name="attach-an-existing-aks-cluster"></a>Anexar um cluster AKS existente

**Tempo estimado:** Aproximadamente 5 minutos.

Se você já tiver o cluster AKS em sua assinatura do Azure e for a versão 1,17 ou inferior, poderá usá-lo para implantar a imagem.

> [!TIP]
> O cluster AKS existente pode estar em uma região do Azure que não seja seu espaço de trabalho Azure Machine Learning.
>
> Se você quiser proteger o cluster AKS usando uma rede virtual do Azure, primeiro deverá criar a rede virtual. Para obter mais informações, consulte [experimentação segura e inferência com a rede virtual do Azure](how-to-enable-virtual-network.md#aksvnet).

Ao anexar um cluster AKS a um espaço de trabalho, você pode definir como usará o cluster definindo o `cluster_purpose` parâmetro.

Se você não definir o `cluster_purpose` parâmetro ou definido `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD` , o cluster deverá ter pelo menos 12 CPUs virtuais disponíveis.

Se você definir `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST` , o cluster não precisará ter 12 CPUs virtuais. Recomendamos pelo menos duas CPUs virtuais para desenvolvimento/teste. No entanto, um cluster configurado para desenvolvimento/teste não é adequado para o tráfego de nível de produção e pode aumentar os tempos de inferência. Os clusters de desenvolvimento/teste também não garantem a tolerância a falhas.

> [!WARNING]
> Não crie vários anexos simultâneos para o mesmo cluster AKS do seu espaço de trabalho. Por exemplo, anexar um cluster AKS a um espaço de trabalho usando dois nomes diferentes. Cada novo anexo interromperá os anexos existentes anteriores.
>
> Se você quiser anexar novamente um cluster AKS, por exemplo, para alterar o TLS ou outra configuração de cluster, primeiro você deve remover o anexo existente usando [AksCompute. Detach ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--).

Para obter mais informações sobre como criar um cluster AKS usando o CLI do Azure ou o portal, consulte os seguintes artigos:

* [Criar um cluster do AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Criar um cluster AKS (Portal)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [Criar um cluster AKS (modelo ARM nos modelos de início rápido do Azure)](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

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

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute. attach_configuration ()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute. Attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Usando a CLI**

Para anexar um cluster existente usando a CLI, você precisa obter a ID de recurso do cluster existente. Para obter esse valor, use o comando a seguir. Substitua `myexistingcluster` pelo nome do seu cluster AKs. Substitua `myresourcegroup` pelo grupo de recursos que contém o cluster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Esse comando retorna um valor semelhante ao texto a seguir:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para anexar o cluster existente ao seu espaço de trabalho, use o comando a seguir. Substituir pelo `aksresourceid` valor retornado pelo comando anterior. Substituir `myresourcegroup` pelo grupo de recursos que contém seu espaço de trabalho. Substituir `myworkspace` pelo nome do espaço de trabalho.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Para obter mais informações, consulte a referência de [AKs AZ ml computetarget Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) .

## <a name="deploy-to-aks"></a>Implantar no AKS

Para implantar um modelo no serviço kubernetes do Azure, crie uma __configuração de implantação__ que descreva os recursos de computação necessários. Por exemplo, número de núcleos e memória. Você também precisa de uma __configuração de inferência__, que descreve o ambiente necessário para hospedar o modelo e o serviço Web. Para obter mais informações sobre como criar a configuração de inferência, consulte [como e onde implantar modelos](how-to-deploy-and-where.md).

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

Para obter mais informações sobre as classes, os métodos e os parâmetros usados neste exemplo, consulte os seguintes documentos de referência:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Modelo. implantar](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [WebService. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Usando a CLI

Para implantar usando a CLI, use o comando a seguir. Substitua `myaks` pelo nome do destino de computação AKs. Substituir `mymodel:1` pelo nome e versão do modelo registrado. Substitua `myservice` pelo nome para fornecer este serviço:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

Para obter mais informações, consulte a referência de [implantação do modelo AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) .

### <a name="using-vs-code"></a>Usar o VS Code

Para obter informações sobre como usar VS Code, consulte [implantar no AKs por meio da extensão vs Code](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model).

> [!IMPORTANT]
> A implantação por meio do VS Code requer que o cluster AKS seja criado ou anexado ao seu espaço de trabalho com antecedência.

### <a name="understand-the-deployment-processes"></a>Entender os processos de implantação

A palavra "implantação" é usada em kubernetes e em Azure Machine Learning. "Implantação" tem significados muito diferentes nesses dois contextos. No kubernetes, uma `Deployment` é uma entidade concreta, especificada com um arquivo YAML declarativo. Um kubernetes `Deployment` tem um ciclo de vida definido e relações concretas com outras entidades kubernetes, como `Pods` e `ReplicaSets` . Você pode aprender sobre o kubernetes de documentos e vídeos em [o que é o kubernetes?](https://aka.ms/k8slearning).

No Azure Machine Learning, a "implantação" é usada no sentido mais geral de disponibilizar e limpar os recursos do projeto. As etapas que Azure Machine Learning consideram parte da implantação são:

1. Compactando os arquivos na pasta do projeto, ignorando aqueles especificados em. amlignore ou. gitignore
1. Escalar verticalmente o cluster de cálculo (relacionado a kubernetes)
1. Criando ou baixando o dockerfile para o nó de computação (relacionado ao kubernetes)
    1. O sistema calcula um hash de: 
        - A imagem base 
        - Etapas personalizadas do Docker (consulte [implantar um modelo usando uma imagem de base do Docker personalizada](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image))
        - A definição de Conda YAML (consulte [criar & usar ambientes de software em Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments))
    1. O sistema usa esse hash como a chave em uma pesquisa do ACR (registro de contêiner do Azure) do espaço de trabalho
    1. Se não for encontrado, ele procurará uma correspondência no ACR global
    1. Se não for encontrado, o sistema criará uma nova imagem (que será armazenada em cache e registrada com o ACR do espaço de trabalho)
1. Baixando seu arquivo de projeto compactado para armazenamento temporário no nó de computação
1. Descompactando o arquivo de projeto
1. O nó de computação em execução`python <entry script> <arguments>`
1. Salvando logs, arquivos de modelo e outros arquivos gravados na `./outputs` conta de armazenamento associada ao espaço de trabalho
1. Reduzindo a computação, incluindo a remoção de armazenamento temporário (relacionado a kubernetes)

Quando você estiver usando o AKS, a expansão e a redução da computação serão controladas pelo kubernetes, usando o dockerfile compilado ou encontrado conforme descrito acima. 

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>Implantar modelos no AKS usando a distribuição controlada (versão prévia)

Analise e promova versões de modelo de maneira controlada usando pontos de extremidade. Você pode implantar até seis versões por trás de um único ponto de extremidade. Os pontos de extremidade fornecem os seguintes recursos:

* Configure a __porcentagem de tráfego de pontuação enviada para cada ponto de extremidade__. Por exemplo, encaminhe 20% do tráfego para o ponto de extremidade ' test ' e 80% para ' Production '.

    > [!NOTE]
    > Se você não considerar 100% do tráfego, qualquer porcentagem restante será roteada para a versão do ponto de extremidade __padrão__ . Por exemplo, se você configurar a versão de ponto de extremidade ' teste ' para obter 10% do tráfego e ' prod ' para 30%, o% de 60% restante será enviado para a versão de ponto de extremidade padrão.
    >
    > A primeira versão de ponto de extremidade criada é configurada automaticamente como o padrão. Você pode alterar isso definindo `is_default=True` ao criar ou atualizar uma versão de ponto de extremidade.
     
* Marque uma versão de ponto de extremidade como __controle__ ou __tratamento__. Por exemplo, a versão do ponto de extremidade de produção atual pode ser o controle, enquanto novos modelos potenciais são implantados como versões de tratamento. Depois de avaliar o desempenho das versões de tratamento, se um supera o controle atual, ele pode ser promovido para a nova produção/controle.

    > [!NOTE]
    > Você só pode ter __um__ controle. Você pode ter vários tratamentos.

Você pode habilitar o app insights para exibir as métricas operacionais de pontos de extremidade e versões implantadas.

### <a name="create-an-endpoint"></a>Criar um ponto de extremidade
Quando você estiver pronto para implantar seus modelos, crie um ponto de extremidade de Pontuação e implante sua primeira versão. O exemplo a seguir mostra como implantar e criar o ponto de extremidade usando o SDK. A primeira implantação será definida como a versão padrão, o que significa que o percentil de tráfego não especificado em todas as versões vai para a versão padrão.  

> [!TIP]
> No exemplo a seguir, a configuração define a versão inicial do ponto de extremidade para lidar com 20% do tráfego. Como esse é o primeiro ponto de extremidade, também é a versão padrão. E, como não temos outras versões para o outro% de 80% do tráfego, ele também é roteado para o padrão. Até que outras versões que usam um percentual de tráfego sejam implantadas, isso recebe efetivamente 100% do tráfego.

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>Atualizar e adicionar versões a um ponto de extremidade

Adicione outra versão ao ponto de extremidade e configure o percentual de tráfego de Pontuação indo para a versão. Há dois tipos de versões, um controle e uma versão de tratamento. Pode haver várias versões de tratamento para ajudar a comparar com uma única versão de controle.

> [!TIP]
> A segunda versão, criada pelo trecho de código a seguir, aceita 10% do tráfego. A primeira versão está configurada para 20%, portanto, somente 30% do tráfego está configurado para versões específicas. O restante de 70% é enviado para a primeira versão de ponto de extremidade, porque também é a versão padrão.

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
endpoint.wait_for_deployment(True)
```

Atualize as versões existentes ou exclua-as em um ponto de extremidade. Você pode alterar o tipo padrão da versão, o tipo de controle e o percentual de tráfego. No exemplo a seguir, a segunda versão aumenta seu tráfego para 40% e agora é o padrão.

> [!TIP]
> Após o trecho de código a seguir, a segunda versão é agora padrão. Agora, ele está configurado para 40%, enquanto a versão original ainda está configurada para 20%. Isso significa que 40% do tráfego não é contabilizado por configurações de versão. O tráfego restante será roteado para a segunda versão, porque agora ele é o padrão. Ele recebe efetivamente 80% do tráfego.

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Autenticação de serviço Web

Ao implantar no serviço kubernetes do Azure, a autenticação __baseada em chave__ é habilitada por padrão. Você também pode habilitar __a autenticação baseada em token__ . A autenticação baseada em token exige que os clientes usem uma conta de Azure Active Directory para solicitar um token de autenticação, que é usado para fazer solicitações ao serviço implantado.

Para __desabilitar__ a autenticação, defina o `auth_enabled=False` parâmetro ao criar a configuração de implantação. O exemplo a seguir desabilita a autenticação usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obter informações sobre como autenticar de um aplicativo cliente, consulte o [modelo consumir um Azure Machine Learning implantado como um serviço Web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticação com chaves

Se a autenticação de chave estiver habilitada, você poderá usar o `get_keys` método para recuperar uma chave de autenticação primária e secundária:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Se você precisar regenerar uma chave, use[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>Autenticação com tokens

Para habilitar a autenticação de token, defina o `token_auth_enabled=True` parâmetro ao criar ou atualizar uma implantação. O exemplo a seguir habilita a autenticação de token usando o SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Se a autenticação de token estiver habilitada, você poderá usar o `get_token` método para recuperar um token JWT e o tempo de expiração desse token:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Será necessário solicitar um novo token após a hora do token `refresh_by` .
>
> A Microsoft recomenda enfaticamente que você crie seu espaço de trabalho Azure Machine Learning na mesma região que o cluster do serviço kubernetes do Azure. Para autenticar com um token, o serviço Web fará uma chamada para a região em que o workspace do Azure Machine Learning foi criado. Se a região do seu espaço de trabalho estiver indisponível, você não poderá buscar um token para o serviço Web mesmo que o cluster esteja em uma região diferente do seu espaço de trabalho. Isso efetivamente resulta na indisponibilidade da autenticação baseada em token até que a região do seu espaço de trabalho esteja disponível novamente. Além disso, quanto maior a distância entre a região do cluster e a região do seu espaço de trabalho, mais tempo será levado para buscar um token.
>
> Para recuperar um token, você deve usar o SDK do Azure Machine Learning ou o comando [AZ ml Service Get-Access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) .

## <a name="update-the-web-service"></a>Atualizar o serviço Web

[!INCLUDE [aml-update-web-service](../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Próximas etapas

* [Experimentação e inferência de segurança em uma rede virtual](how-to-enable-virtual-network.md)
* [Como implantar um modelo usando uma imagem personalizada do Docker](how-to-deploy-custom-docker-image.md)
* [Solução de problemas de implantação](how-to-troubleshoot-deployment.md)
* [Use o TLS para proteger um serviço Web por meio do Azure Machine Learning](how-to-secure-web-service.md)
* [Consumir um modelo de ML implantado como um serviço Web](how-to-consume-web-service.md)
* [Monitore seus modelos de Azure Machine Learning com Application Insights](how-to-enable-app-insights.md)
* [Coletar dados para modelos em produção](how-to-enable-data-collection.md)
