---
title: Crie nódulos virtuais usando o Azure CLI
titleSuffix: Azure Kubernetes Service
description: Saiba como usar a CLI do Azure para criar um cluster do AKS (Serviços de Kubernetes do Azure) que usa nós virtuais para executar pods.
services: container-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: b6d44ceb9b447d670c4e51c951b547e90dfce38f
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984667"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes-using-the-azure-cli"></a>Criar e configurar um cluster do AKS (Serviços de Kubernetes do Azure) para usar os nós virtuais com a CLI do Azure

Para dimensionar rapidamente as cargas de trabalho do aplicativo em um cluster do AKS (Serviço de Kubernetes do Azure), é possível usar nós virtuais. Com nós virtuais, você tem provisionamento rápido de pods e somente paga por segundo pelo tempo de execução. Não é necessário aguardar o dimensionador automático de cluster do Kubernetes implantar nós de computação de VM para executar os pods adicionais. Nós virtuais são suportados apenas com pods e nós Linux.

Este artigo mostra como criar e configurar os recursos de rede virtual e o cluster do AKS para depois habilitar nós virtuais.

## <a name="before-you-begin"></a>Antes de começar

Os nós virtuais permitem a comunicação de rede entre pods executados no AZURE Container Instances (ACI) e no cluster AKS. Para fornecer essa comunicação, uma sub-rede de rede virtual é criada e permissões delegadas são atribuídas. Nós virtuais só funcionam com clusters do AKS criados usando rede *avançada*. Por padrão, os clusters do AKS são criados com rede *básica*. Este artigo mostra como criar uma rede virtual e sub-redes para então implantar um cluster do AKS que usa rede avançada.

Se você não tiver usado anteriormente ACI, registre o provedor de serviço com sua assinatura. Você pode verificar o status do registro de provedor ACI usando o comando [lista de provedor az][az-provider-list], conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider list --query "[?contains(namespace,'Microsoft.ContainerInstance')]" -o table
```

O provedor *Microsoft.ContainerInstance* deve relatar como *registrado*, conforme mostrado na saída de exemplo a seguir:

```output
Namespace                    RegistrationState    RegistrationPolicy
---------------------------  -------------------  --------------------
Microsoft.ContainerInstance  Registered           RegistrationRequired
```

Se o provedor é exibido como *NotRegistered*, registre o provedor usando o [registro de provedor az][az-provider-register] conforme mostrado no exemplo a seguir:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerInstance
```

## <a name="regional-availability"></a>Disponibilidade regional

As seguintes regiões são suportadas para implantações de nós virtuais:

* Austrália Oriental (austrálialeste)
* EUA Central (central)
* Leste dos EUA (eastus)
* Leste dos EUA 2 (eastus2)
* Japão Leste (japãoleste)
* Norte da Europa (northeurope)
* Sudeste da Ásia (sudeste da Ásia)
* Centro-Oeste dos EUA (centro-oeste)
* Europa Ocidental (westeurope)
* Oeste dos EUA (westus)
* Oeste dos EUA 2 (westus2)

## <a name="known-limitations"></a>Limitações conhecidas
A funcionalidade de Nodos Virtuais depende fortemente do conjunto de recursos da ACI. Os seguintes cenários ainda não são suportados com Nós Virtuais

* Usando o diretor de serviço para puxar imagens ACR. [Solução é](https://github.com/virtual-kubelet/virtual-kubelet/blob/master/providers/azure/README.md#Private-registry) usar [segredos kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Limitações de rede virtual,](../container-instances/container-instances-vnet.md) incluindo peering VNet, políticas de rede Kubernetes e tráfego de saída para a internet com grupos de segurança de rede.
* Recipientes init
* [Aliases host](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argumentos](../container-instances/container-instances-exec.md#restrictions) para executivo na ACI
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) não implantarão pods no nó virtual
* Nós virtuais suportam o agendamento de pods Linux. Você pode instalar manualmente o provedor [Virtual Kubelet ACI](https://github.com/virtual-kubelet/azure-aci) de código aberto para agendar contêineres do Windows Server para ACI. 

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta.

Para abrir o Cloud Shell, selecione **Experimentar** no canto superior direito de um bloco de código. Você também pode iniciar o Cloud Shell [https://shell.azure.com/bash](https://shell.azure.com/bash)em uma guia de navegador separada indo para . Selecione **Copiar** para copiar os blocos de código, cole o código no Cloud Shell e depois pressione Enter para executá-lo.

Se preferir instalar e usar a CLI localmente, este artigo requer a CLI do Azure versão 2.0.49 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos do Azure é um grupo lógico no qual os recursos do Azure são implantados e gerenciados. Crie um grupo de recursos com o comando [az group create][az-group-create]. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no *local do westus.*

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual usando o comando [az network vnet create][az-network-vnet-create]. O exemplo a seguir cria uma rede virtual nomeada *myVnet* com um prefixo de endereço de *10.0.0.0/8* e uma sub-rede nomeada *myAKSSubnet*. O prefixo de endereço dessa sub-rede é por padrão *10.240.0.0/16*:

```azurecli-interactive
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16
```

Agora, crie uma sub-rede adicional para os nós virtuais usando o comando [az network vnet subnet create][az-network-vnet-subnet-create]. O exemplo a seguir cria uma sub-rede nomeada *myVirtualNodeSubnet* com o prefixo de endereço de *10.241.0.0/16*.

```azurecli-interactive
az network vnet subnet create \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name myVirtualNodeSubnet \
    --address-prefixes 10.241.0.0/16
```

## <a name="create-a-service-principal"></a>Criar uma entidade de serviço

Para permitir a interação de um cluster AKS com outros recursos do Azure, usamos uma entidade de serviço do Azure Active Directory. Essa entidade de serviço pode ser criada automaticamente pelo portal ou pela CLI do Azure, ou você pode criar previamente um e atribuir permissões adicionais.

Crie uma entidade de serviço usando o comando [az ad sp create-for-rbac][az-ad-sp-create-for-rbac]. O parâmetro `--skip-assignment` limita a atribuição de outras permissões.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A saída deverá ser semelhante ao seguinte exemplo:

```output
{
  "appId": "bef76eb3-d743-4a97-9534-03e9388811fc",
  "displayName": "azure-cli-2018-11-21-18-42-00",
  "name": "http://azure-cli-2018-11-21-18-42-00",
  "password": "1d257915-8714-4ce7-a7fb-0e5a5411df7f",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anote o *appId* e a *senha*. Esses valores serão usados nas próximas etapas.

## <a name="assign-permissions-to-the-virtual-network"></a>Atribuir permissões para a rede virtual

Para permitir que o cluster use e gerencie a rede virtual, é necessário conceder à entidade de serviço do AKS os direitos corretos para usar os recursos de rede.

Primeiro, obtenha a ID do recurso de rede virtual usando [az network vnet show][az-network-vnet-show]:

```azurecli-interactive
az network vnet show --resource-group myResourceGroup --name myVnet --query id -o tsv
```

Para conceder o acesso correto para que o cluster do AKS use a rede virtual, crie uma atribuição de função usando o comando [az role assignment create][az-role-assignment-create]. Substitua `<appId`> e `<vnetId>` pelos valores coletados nas duas etapas anteriores.

```azurecli-interactive
az role assignment create --assignee <appId> --scope <vnetId> --role Contributor
```

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Implante um cluster do AKS dentro da sub-rede do AKS criada na etapa anterior. Obtenha a ID dessa sub-rede usando [az network vnet subnet show][az-network-vnet-subnet-show]:

```azurecli-interactive
az network vnet subnet show --resource-group myResourceGroup --vnet-name myVnet --name myAKSSubnet --query id -o tsv
```

Use o comando [az aks create][az-aks-create] para criar um cluster do AKS. O exemplo a seguir cria um cluster chamado *myAKSCluster* com um nó. Substitua pelo `<subnetId>` ID obtido na etapa `<appId>` anterior `<password>` e, em seguida, pelos valores coletados na seção anterior.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 1 \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id <subnetId> \
    --service-principal <appId> \
    --client-secret <password>
```

Após alguns minutos, o comando é concluído e retorna informações formatadas em JSON sobre o cluster.

## <a name="enable-virtual-nodes-addon"></a>Habilite o addon de nodos virtuais

Para habilitar nós virtuais, agora use o comando [az aks enable-addons][az-aks-enable-addons]. O exemplo a seguir usa a sub-rede nomeada *myVirtualNodeSubnet* criada na etapa anterior:

```azurecli-interactive
az aks enable-addons \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --addons virtual-node \
    --subnet-name myVirtualNodeSubnet
```

## <a name="connect-to-the-cluster"></a>Conectar-se ao cluster

Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Esta etapa baixa credenciais e configura a CLI de Kubernetes para usá-las.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```console
kubectl get nodes
```

O resultado do exemplo a seguir mostra o único nó de VM criado e o nó virtual para Linux, *virtual-node-aci-linux*:

```output
NAME                          STATUS    ROLES     AGE       VERSION
virtual-node-aci-linux        Ready     agent     28m       v1.11.2
aks-agentpool-14693408-0      Ready     agent     32m       v1.11.2
```

## <a name="deploy-a-sample-app"></a>Implantar um aplicativo de exemplo

Crie um arquivo chamado `virtual-node.yaml` e copie no YAML a seguir. Para agendar o contêiner no nó, define-se um [nodeSelector][node-selector] e um [toleration][toleration].

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aci-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aci-helloworld
  template:
    metadata:
      labels:
        app: aci-helloworld
    spec:
      containers:
      - name: aci-helloworld
        image: microsoft/aci-helloworld
        ports:
        - containerPort: 80
      nodeSelector:
        kubernetes.io/role: agent
        beta.kubernetes.io/os: linux
        type: virtual-kubelet
      tolerations:
      - key: virtual-kubelet.io/provider
        operator: Exists
      - key: azure.com/aci
        effect: NoSchedule
```

Execute o aplicativo com o comando [kubectl apply][kubectl-apply].

```console
kubectl apply -f virtual-node.yaml
```

Use o comando [kubectl get pods][kubectl-get] com o argumento `-o wide` para gerar uma lista dos pods e do nó agendado. Observe que o pod `aci-helloworld` foi agendado no nó `virtual-node-aci-linux`.

```console
kubectl get pods -o wide
```

```output
NAME                            READY     STATUS    RESTARTS   AGE       IP           NODE
aci-helloworld-9b55975f-bnmfl   1/1       Running   0          4m        10.241.0.4   virtual-node-aci-linux
```

O pod é atribuído a um endereço IP interno da sub-rede da rede virtual do Azure delegada para uso com nós virtuais.

> [!NOTE]
> Se você usar imagens armazenadas no Registro de Contêiner do Azure, [configure e use um segredo do Kubernetes][acr-aks-secrets]. Uma limitação atual dos nódulos virtuais é que você não pode usar a autenticação principal do serviço Azure AD integrado. Se você não usar um segredo, pods agendados em nós virtuais falham ao iniciar e relatam o erro `HTTP response status code 400 error code "InaccessibleImage"`.

## <a name="test-the-virtual-node-pod"></a>Testar o pod de nó virtual

Para testar o pod em execução no nó virtual, navegue até o aplicativo de demonstração com um cliente Web. Como o pod é atribuído a um endereço IP interno, é possível testar rapidamente essa conectividade por outro pod no cluster do AKS. Crie um pod de teste e uma sessão de terminal a ele:

```console
kubectl run --generator=run-pod/v1 -it --rm testvk --image=debian
```

Instale `curl` no pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Agora acesse o endereço `curl`do seu *http://10.241.0.4*pod usando, como . Forneça seu próprio endereço IP interno exibido no comando `kubectl get pods` anterior:

```console
curl -L http://10.241.0.4
```

O aplicativo de demonstração é exibido, conforme mostrado na saída de exemplo condensada a seguir:

```output
<html>
<head>
  <title>Welcome to Azure Container Instances!</title>
</head>
[...]
```

Feche a sessão de terminal do pod de teste com `exit`. Quando a sessão for encerrada, o pod será excluído.

## <a name="remove-virtual-nodes"></a>Remover nós virtuais

Se você não quiser mais usar nós virtuais, é possível desabilitá-los usando o comando [az aks disable-addons][az aks disable-addons]. 

Se necessário, [https://shell.azure.com](https://shell.azure.com) vá para abrir o Azure Cloud Shell em seu navegador.

Primeiro, exclua o `aci-helloworld` pod em execução no nó virtual:

```console
kubectl delete -f virtual-node.yaml
```

O comando exemplo a seguir desativa os nós virtuais do Linux:

```azurecli-interactive
az aks disable-addons --resource-group myResourceGroup --name myAKSCluster --addons virtual-node
```

Agora, remova os recursos de rede virtual e o grupo de recursos:

```azurecli-interactive
# Change the name of your resource group, cluster and network resources as needed
RES_GROUP=myResourceGroup
AKS_CLUSTER=myAKScluster
AKS_VNET=myVnet
AKS_SUBNET=myVirtualNodeSubnet

# Get AKS node resource group
NODE_RES_GROUP=$(az aks show --resource-group $RES_GROUP --name $AKS_CLUSTER --query nodeResourceGroup --output tsv)

# Get network profile ID
NETWORK_PROFILE_ID=$(az network profile list --resource-group $NODE_RES_GROUP --query [0].id --output tsv)

# Delete the network profile
az network profile delete --id $NETWORK_PROFILE_ID -y

# Delete the subnet delegation to Azure Container Instances
az network vnet subnet update --resource-group $RES_GROUP --vnet-name $AKS_VNET --name $AKS_SUBNET --remove delegations 0
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, agendamos um pod no nó virtual e atribuímos a um endereço IP privado interno. Ao invés disso, você pode criar uma implantação de serviço e direcionar o tráfego para o pod por meio de um balanceador de carga ou controlador de entrada. Para obter mais informações, confira [Create a basic ingress controller in AKS][aks-basic-ingress] (Criar um controlador de entrada básico no AKS).

Nós virtuais geralmente são um componente de uma solução de dimensionamento no AKS. Para saber mais sobre essas soluções de dimensionamento, consulte os artigos a seguir:

- [Usar o dimensionamento automático de pod horizontal do Kubernetes][aks-hpa]
- [Usar o dimensionador automático de cluster do Kubernetes][aks-cluster-autoscaler]
- [Confira a amostra de autoescala para dedos virtuais][virtual-node-autoscale]
- [Leia mais sobre a biblioteca de código aberto Virtual Kubelet][virtual-kubelet-repo]

<!-- LINKS - external -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[node-selector]:https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[toleration]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[aks-github]: https://github.com/azure/aks/issues
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
[acr-aks-secrets]: https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-network-vnet-create]: /cli/azure/network/vnet#az-network-vnet-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-network-vnet-show]: /cli/azure/network/vnet#az-network-vnet-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-show
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-enable-addons]: /cli/azure/aks#az-aks-enable-addons
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az aks disable-addons]: /cli/azure/aks#az-aks-disable-addons
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: autoscaler.md
[aks-basic-ingress]: ingress-basic.md
[az-provider-list]: /cli/azure/provider#az-provider-list
[az-provider-register]: /cli/azure/provider#az-provider-register
