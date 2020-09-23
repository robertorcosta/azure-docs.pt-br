---
title: Proteger ambientes inferência com redes virtuais
titleSuffix: Azure Machine Learning
description: Use uma rede virtual do Azure isolada para proteger seu ambiente de Azure Machine Learning inferência.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 07/16/2020
ms.custom: contperfq4, tracking-python
ms.openlocfilehash: 359c2a27099ca298076edc255b8c30e226af0a18
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90882955"
---
# <a name="secure-an-azure-machine-learning-inferencing-environment-with-virtual-networks"></a>Proteger um ambiente Azure Machine Learning inferência com redes virtuais

Neste artigo, você aprende a proteger ambientes inferência com uma rede virtual no Azure Machine Learning.

Este artigo é a parte quatro de uma série de cinco partes que orienta você pela proteção de um fluxo de trabalho Azure Machine Learning. É altamente recomendável que você leia a [parte um: visão geral da VNet](how-to-network-security-overview.md) para entender a arquitetura geral primeiro. 

Consulte os outros artigos desta série:

[1. visão geral](how-to-network-security-overview.md)  >  [da VNet proteger o espaço de trabalho](how-to-secure-workspace-vnet.md)  >  [3. Proteja o ambiente de treinamento](how-to-secure-training-vnet.md)  >  **4. Proteja o ambiente do inferência**  >  [5. Habilitar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)

Neste artigo, você aprenderá a proteger os seguintes recursos do inferência em uma rede virtual:
> [!div class="checklist"]
> - Cluster padrão do AKS (serviço kubernetes do Azure)
> - Cluster AKS privado
> - ACI (Instâncias de Contêiner do Azure)


## <a name="prerequisites"></a>Pré-requisitos

+ Leia o artigo [visão geral de segurança de rede](how-to-network-security-overview.md) para entender cenários comuns de rede virtual e a arquitetura de rede virtual geral.

+ Uma rede virtual e sub-rede existentes para usar com seus recursos de computação.

+ Para implantar recursos em uma rede virtual ou sub-rede, sua conta de usuário deve ter permissões para as seguintes ações nos controles de acesso baseado em função (RBAC) do Azure:

    - "Microsoft. Network/virtualNetworks/Join/Action" no recurso de rede virtual.
    - "Microsoft. Network/virtualNetworks/sub-rede/junção/ação" no recurso de sub-rede.

    Para obter mais informações sobre o RBAC com rede, consulte [funções internas de rede](/azure/role-based-access-control/built-in-roles#networking)

<a id="aksvnet"></a>

## <a name="azure-kubernetes-service"></a>Serviço de Kubernetes do Azure

Para usar um cluster AKS em uma rede virtual, os requisitos de rede a seguir devem ser atendidos:

> [!div class="checklist"]
> * Siga os pré-requisitos em [Configurar a rede avançada no AKs (serviço de kubernetes do Azure)](../aks/configure-azure-cni.md#prerequisites).
> * A instância AKS e a rede virtual devem estar na mesma região. Se você proteger as contas de armazenamento do Azure usadas pelo espaço de trabalho em uma rede virtual, elas deverão estar na mesma rede virtual que a instância AKS também.


Para adicionar o AKS em uma rede virtual ao seu espaço de trabalho, use as seguintes etapas:

1. Entre no [estúdio do Azure Machine Learning](https://ml.azure.com/) e selecione sua assinatura e workspace.

1. Selecione __Computação__ à esquerda.

1. Selecione __Clusters de inferência__ da central e, em seguida, selecione __+__ .

1. Na caixa de diálogo __Novo cluster de inferência__, selecione __Avançado__ em __Configuração de rede__.

1. Para configurar esse recurso de computação para usar uma rede virtual, execute as seguintes ações:

    1. Na lista suspensa __Grupo de recursos__, selecione o grupo de recursos que contém a rede virtual.
    1. Na lista suspensa __Rede virtual__, selecione a rede virtual que contém a sub-rede.
    1. Na lista suspensa __Sub-rede__, selecione a sub-rede.
    1. Na caixa __Intervalo de endereços do serviço Kubernetes__ , insira o intervalo de endereços do serviço Kubernetes. Esse intervalo de endereços usa um intervalo de IP de notação de Roteamento entre Domínios sem Classificação (CIDR) para definir os endereços IP disponíveis para o cluster. Ele não deve se sobrepor a nenhum intervalo de IP de sub-rede (por exemplo, 10.0.0.0/16).
    1. Na caixa __Endereço IP do serviço DNS do Kubernetes__ , insira o endereço IP do serviço de DNS do Kubernetes. Esse endereço IP é atribuído ao serviço DNS do Kubernetes. Ele deve estar dentro do intervalo de endereços do serviço Kubernetes (por exemplo, 10.0.0.10).
    1. Na caixa __Endereço de ponte do Docker__, insira o endereço da ponte do Docker. Esse endereço IP é atribuído à ponte Docker. Ele não deve estar em nenhum intervalo de IP de sub-rede nem no intervalo de endereços do serviço Kubernetes (por exemplo, 172.17.0.1/16).

   ![Azure Machine Learning: Configurações de rede virtual de Computação do Machine Learning](./media/how-to-enable-virtual-network/aks-virtual-network-screen.png)

1. Verifique se o grupo de NSG que controla a rede virtual tem uma regra de segurança de entrada habilitada para o ponto de extremidade de pontuação para que ela possa ser chamada de fora da rede virtual.
   > [!IMPORTANT]
   > Mantenha as regras de saída padrão para o NSG. Para obter mais informações, consulte as regras de segurança padrão em [Grupos de segurança](https://docs.microsoft.com/azure/virtual-network/security-overview#default-security-rules).

   [![Uma regra de segurança de entrada](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png)](./media/how-to-enable-virtual-network/aks-vnet-inbound-nsg-scoring.png#lightbox)

Também é possível usar o SDK do Azure Machine Learning para adicionar o Serviço de Kubernetes do Azure a uma rede virtual. Se você já tem um cluster do AKS em uma rede virtual, anexe-o ao workspace conforme descrito em [Como implantar no AKS](how-to-deploy-and-where.md). O código a seguir cria uma nova instância de AKS na `default`sub-rede de uma rede virtual nomeada`mynetwork`:

```python
from azureml.core.compute import ComputeTarget, AksCompute

# Create the compute configuration and set virtual network information
config = AksCompute.provisioning_configuration(location="eastus2")
config.vnet_resourcegroup_name = "mygroup"
config.vnet_name = "mynetwork"
config.subnet_name = "default"
config.service_cidr = "10.0.0.0/16"
config.dns_service_ip = "10.0.0.10"
config.docker_bridge_cidr = "172.17.0.1/16"

# Create the compute target
aks_target = ComputeTarget.create(workspace=ws,
                                  name="myaks",
                                  provisioning_configuration=config)
```

Quando o processo de criação for concluído, você pode executar inferência ou pontuação de modelo em um cluster de AKS atrás de uma rede virtual. Para obter mais informações, consulte [Como implantar no AKS](how-to-deploy-and-where.md).

## <a name="private-aks-cluster"></a>Cluster AKS privado

Por padrão, os clusters AKS têm um plano de controle, ou servidor de API, com endereços IP públicos. Você pode configurar o AKS para usar um plano de controle privado criando um cluster AKS privado. Para obter mais informações, consulte [criar um cluster particular do serviço kubernetes do Azure](../aks/private-clusters.md).

Depois de criar o cluster AKS privado, [anexe o cluster à rede virtual](how-to-create-attach-kubernetes.md) para usar com Azure Machine Learning.

## <a name="internal-aks-load-balancer"></a>Balanceador de carga AKS interno

Por padrão, as implantações do AKS usam um [balanceador de carga público](../aks/load-balancer-standard.md). Nesta seção, você aprenderá a configurar o AKS para usar um balanceador de carga interno. Um balanceador de carga interno (ou privado) é usado onde somente os IPs privados são permitidos como front-end. Balanceadores de carga internos são usados para balancear a carga do tráfego dentro de uma rede virtual

Um balanceador de carga privado é habilitado Configurando AKS para usar um _balanceador de carga interno_. 

### <a name="network-contributor-role"></a>Função de colaborador de rede

> [!IMPORTANT]
> Se você criar ou anexar um cluster AKS fornecendo uma rede virtual criada anteriormente, deverá conceder a entidade de serviço (SP) ou a identidade gerenciada para seu cluster AKS a função de _colaborador de rede_ para o grupo de recursos que contém a rede virtual. Isso deve ser feito antes de tentar alterar o balanceador de carga interno para o IP privado.
>
> Para adicionar a identidade como colaborador de rede, use as seguintes etapas:

1. Para localizar a entidade de serviço ou a ID de identidade gerenciada para AKS, use os seguintes comandos de CLI do Azure. Substitua `<aks-cluster-name>` pelo nome do cluster. Substitua `<resource-group-name>` pelo nome do grupo de recursos que _contém o cluster AKs_:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query servicePrincipalProfile.clientId
    ``` 

    Se esse comando retornar um valor de `msi` , use o seguinte comando para identificar a ID da entidade de segurança para a identidade gerenciada:

    ```azurecli-interactive
    az aks show -n <aks-cluster-name> --resource-group <resource-group-name> --query identity.principalId
    ```

1. Para localizar a ID do grupo de recursos que contém sua rede virtual, use o comando a seguir. Substitua `<resource-group-name>` pelo nome do grupo de recursos que _contém a rede virtual_:

    ```azurecli-interactive
    az group show -n <resource-group-name> --query id
    ```

1. Para adicionar a entidade de serviço ou a identidade gerenciada como um colaborador de rede, use o comando a seguir. Substituir `<SP-or-managed-identity>` pela ID retornada para a entidade de serviço ou identidade gerenciada. Substituir `<resource-group-id>` pela ID retornada para o grupo de recursos que contém a rede virtual:

    ```azurecli-interactive
    az role assignment create --assignee <SP-or-managed-identity> --role 'Network Contributor' --scope <resource-group-id>
    ```
Para obter mais informações sobre como usar o balanceador de carga interno com o AKS, consulte [Usar o balanceador de carga interno com o Serviço de Kubernetes do Azure](/azure/aks/internal-lb).

### <a name="enable-private-load-balancer"></a>Habilitar o balanceador de carga privado

> [!IMPORTANT]
> Você não pode habilitar o IP particular ao criar o cluster do Serviço de Kubernetes do Azure. Ele deve estar habilitado como uma atualização de um cluster existente.

O trecho de código a seguir demonstra como __criar um novo cluster de AKS__ e, em seguida, atualizá-lo para usar um balanceador de carga interno/IP particular:

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute, ComputeTarget

# Verify that cluster does not exist already
try:
    aks_target = AksCompute(workspace=ws, name=aks_cluster_name)
    print("Found existing aks cluster")

except:
    print("Creating new aks cluster")

    # Subnet to use for AKS
    subnet_name = "default"
    # Create AKS configuration
    prov_config = AksCompute.provisioning_configuration(location = "eastus2")
    # Set info for existing virtual network to create the cluster in
    prov_config.vnet_resourcegroup_name = "myvnetresourcegroup"
    prov_config.vnet_name = "myvnetname"
    prov_config.service_cidr = "10.0.0.0/16"
    prov_config.dns_service_ip = "10.0.0.10"
    prov_config.subnet_name = subnet_name
    prov_config.docker_bridge_cidr = "172.17.0.1/16"

    # Create compute target
    aks_target = ComputeTarget.create(workspace = ws, name = "myaks", provisioning_configuration = prov_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
    
    # Update AKS configuration to use an internal load balancer
    update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
    aks_target.update(update_config)
    # Wait for the operation to complete
    aks_target.wait_for_completion(show_output = True)
```

__CLI do Azure__

```azurecli-interactive
az rest --method put --uri https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.MachineLearningServices/workspaces/<workspace>/computes/<compute>?api-version=2018-11-19 --body @body.json
```

O conteúdo do arquivo `body.json` referenciado pelo comando é semelhante ao seguinte documento JSON:

```json
{ 
    "location": "<region>", 
    "properties": { 
        "resourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group>/providers/Microsoft.ContainerService/managedClusters/<aks-resource-name>", 
        "computeType": "AKS", 
        "provisioningState": "Succeeded", 
        "properties": { 
            "loadBalancerType": "InternalLoadBalancer", 
            "agentCount": <agent-count>, 
            "agentVmSize": "vm-size", 
            "clusterFqdn": "<cluster-fqdn>" 
        } 
    } 
} 
```

Ao __anexar um cluster existente__ ao seu espaço de trabalho, você deve aguardar até que a operação de anexação configure o balanceador de carga.

Para obter informações sobre como anexar um cluster, consulte [anexar um cluster AKs existente](how-to-create-attach-kubernetes.md).

Depois de anexar o cluster existente, você pode atualizar o cluster para usar um IP privado.

```python
import azureml.core
from azureml.core.compute.aks import AksUpdateConfiguration
from azureml.core.compute import AksCompute

# ws = workspace object. Creation not shown in this snippet
aks_target = AksCompute(ws,"myaks")

# Change to the name of the subnet that contains AKS
subnet_name = "default"
# Update AKS configuration to use an internal load balancer
update_config = AksUpdateConfiguration(None, "InternalLoadBalancer", subnet_name)
aks_target.update(update_config)
# Wait for the operation to complete
aks_target.wait_for_completion(show_output = True)
```

## <a name="enable-azure-container-instances-aci"></a>Habilitar ACI (instâncias de contêiner do Azure)

As Instâncias de Contêiner do Azure são criadas dinamicamente durante a implantação de um modelo. Para habilitar a Azure Machine Learning para criar ACI dentro da rede virtual, você deve habilitar a __delegação de sub-rede__ para a sub-rede usada pela implantação.

> [!WARNING]
> Ao usar instâncias de contêiner do Azure em uma rede virtual, a rede virtual deve estar no mesmo grupo de recursos que seu espaço de trabalho do Azure Machine Learning.
>
> Ao usar as instâncias de contêiner do Azure dentro da rede virtual, o ACR (registro de contêiner do Azure) para seu espaço de trabalho também não pode estar na rede virtual.

Para usar a ACI em uma rede virtual no seu workspace, siga as seguintes etapas:

1. Para habilitar a delegação de sub-rede em sua rede virtual, use as informações do artigo [Adicionar ou remover uma delegação de sub-rede](../virtual-network/manage-subnet-delegation.md). É possível habilitar a delegação ao criar uma rede virtual ou adicioná-la a uma rede existente.

    > [!IMPORTANT]
    > Ao habilitar a delegação, use `Microsoft.ContainerInstance/containerGroups` como o valor de __Delegar sub-rede ao serviço__.

2. Implante o modelo usando [AciWebservice. deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none--vnet-name-none--subnet-name-none-&preserve-view=true), use os parâmetros `vnet_name` e `subnet_name`. Defina esses parâmetros para o nome da rede virtual e a sub-rede onde você habilitou a delegação.


## <a name="next-steps"></a>Próximas etapas

Este artigo é a parte três de uma série de redes virtuais de quatro partes. Consulte o restante dos artigos para saber como proteger uma rede virtual:

* [Parte 1: visão geral da rede virtual](how-to-network-security-overview.md)
* [Parte 2: proteger os recursos do espaço de trabalho](how-to-secure-workspace-vnet.md)
* [Parte 3: proteger o ambiente de treinamento](how-to-secure-training-vnet.md)
* [Parte 5: habilitar a funcionalidade do estúdio](how-to-enable-studio-virtual-network.md)