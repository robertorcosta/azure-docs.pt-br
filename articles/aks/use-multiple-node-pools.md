---
title: Use vários pools de nó no Azure Kubernetes Service (AKS)
description: Aprenda a criar e gerenciar vários pools de nó para um cluster no Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 87f066ed17e5274439082956803d269bdd5853f5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616508"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Criar e gerenciar vários pools de nó para um cluster no Azure Kubernetes Service (AKS)

No Azure Kubernetes Service (AKS), nós da mesma configuração são agrupados em *piscinas de nós*. Esses pools de nó contêm as VMs subjacentes que executam seus aplicativos. O número inicial de nódulos e seu tamanho (SKU) é definido quando você cria um cluster AKS, que cria um *pool de nó padrão*. Para suportar aplicativos com diferentes demandas de computação ou armazenamento, você pode criar pools adicionais de nó. Por exemplo, use esses pools de nós adicionais para fornecer GPUs para aplicações com uso intensivo de computação ou acesso a armazenamento SSD de alto desempenho.

> [!NOTE]
> Esse recurso permite maior controle sobre como criar e gerenciar vários pools de nó. Como resultado, são necessários comandos separados para criar/atualizar/excluir. Anteriormente, as `az aks create` `az aks update` operações de cluster através ou usavam a API managedCluster e eram a única opção para alterar seu plano de controle e um único pool de nós. Esse recurso expõe um conjunto de operações separado para pools de `az aks nodepool` agentes através da API agentPool e requer o uso do conjunto de comandos para executar operações em um pool de nós individuais.

Este artigo mostra como criar e gerenciar vários pools de nó em um cluster AKS.

## <a name="before-you-begin"></a>Antes de começar

Você precisa da versão 2.2.0 do Azure CLI ou posterior instalada e configurada posteriormente. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][install-azure-cli].

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que suportam vários pools de nó:

* Consulte [Cotas, restrições ao tamanho da máquina virtual e disponibilidade de região no Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Você não pode excluir o pool de nó do sistema, por padrão o primeiro pool de nó.
* O cluster AKS deve usar o balanceador de carga SKU padrão para usar vários pools de nó, o recurso não é suportado com balanceadores básicos de carga SKU.
* O cluster AKS deve usar conjuntos de escala de máquinas virtuais para os nós.
* O nome de uma piscina de nó só pode conter caracteres alfanuméricas minúsculos e deve começar com uma letra minúscula. Para os pools de nós Linux o comprimento deve ser entre 1 e 12 caracteres, para os pools de nó do Windows o comprimento deve ser entre 1 e 6 caracteres.
* Todos os pools de nós devem residir na mesma rede virtual.
* Ao criar vários pools de nós no tempo de criação de cluster, todas as versões kubernetes usadas por pools de nós devem corresponder à versão definida para o plano de controle. Isso pode ser atualizado após o cluster ter sido provisionado usando operações por pool de nó.

## <a name="create-an-aks-cluster"></a>Criar um cluster AKS

Para começar, crie um cluster AKS com um único pool de nó. O exemplo a seguir usa o comando [az group create][az-group-create] para criar um grupo de recursos chamado *myResourceGroup* na região *de Eastus.* Um cluster AKS chamado *myAKSCluster* é criado usando o comando [az aks create.][az-aks-create] Uma *versão --kubernetes* do *1.15.7* é usada para mostrar como atualizar um pool de nós em uma etapa seguinte. Você pode especificar qualquer [versão do Kubernetes suportada][supported-versions].

> [!NOTE]
> O *SKU* do balanceador de carga básico não é **suportado** ao usar vários pools de nó. Por padrão, os clusters AKS são criados com o *Balanceador* de carga Padrão SKU do portal Azure CLI e Azure.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

São necessários alguns minutos para criar o cluster.

> [!NOTE]
> Para garantir que o cluster funcione de forma confiável, você deve executar pelo menos 2 (dois) nós no pool de nós padrão, já que os serviços essenciais do sistema estão sendo executados neste pool de nós.

Quando o cluster estiver pronto, use o comando [az aks get-credentials][az-aks-get-credentials] para obter as credenciais de cluster para uso com: `kubectl`

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Adicione uma piscina de nó

O cluster criado na etapa anterior tem um único pool de nó. Vamos adicionar um segundo pool de nó usando o comando [az aks nodepool add.][az-aks-nodepool-add] O exemplo a seguir cria um pool de nóchamado *mynodepool* que executa *3* nós:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> O nome de uma piscina de nó deve começar com uma letra minúscula e só pode conter caracteres alfanuméricos. Para os pools de nós Linux o comprimento deve ser entre 1 e 12 caracteres, para os pools de nó do Windows o comprimento deve ser entre 1 e 6 caracteres.

Para ver o status de seus pools de nó, use o comando [az aks node pool list][az-aks-nodepool-list] e especifique o nome do grupo de recursos e do cluster:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

O exemplo a seguir mostra que *o mynodepool* foi criado com sucesso com três nós no pool de nó. Quando o cluster AKS foi criado na etapa anterior, um *nodepool padrão1* foi criado com uma contagem de nodos de *2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Se nenhum *VmSize* for especificado quando você adicionar um pool de nós, o tamanho padrão será *Standard_DS2_v3* para pools de nós do Windows e *Standard_DS2_v2* para pools de nós Linux. Se nenhum *OrchestratorVersion* for especificado, ele será padrão para a mesma versão do plano de controle.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Adicione um pool de nó com uma sub-rede exclusiva (visualização)

Uma carga de trabalho pode exigir a divisão dos nós de um cluster em piscinas separadas para isolamento lógico. Esse isolamento pode ser suportado com sub-redes separadas dedicadas a cada pool de nó no cluster. Isso pode atender a requisitos como ter espaço de endereço de rede virtual não contíguo para dividir em pools de nó.

#### <a name="limitations"></a>Limitações

* Todas as sub-redes atribuídas a nodepools devem pertencer à mesma rede virtual.
* Os pods do sistema devem ter acesso a todos os nós do cluster para fornecer funcionalidades críticas, como a resolução DNS via coreDNS.
* A atribuição de uma sub-rede única por pool de nó é limitada ao Azure CNI durante a pré-visualização.
* O uso de políticas de rede com uma sub-rede única por pool de nó não é suportado durante a visualização.

Para criar um pool de nó com uma sub-rede dedicada, passe o ID de recurso da sub-rede como um parâmetro adicional ao criar um pool de nó.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Atualize uma piscina de nó

> [!NOTE]
> As operações de upgrade e escala em um pool de clusterou ou nó não podem ocorrer simultaneamente, se uma tentativa de erro for retornada. Em vez disso, cada tipo de operação deve ser concluído no recurso de destino antes da próxima solicitação sobre esse mesmo recurso. Leia mais sobre isso em nosso [guia de solução de problemas](https://aka.ms/aks-pending-upgrade).

Quando seu cluster AKS foi inicialmente criado `--kubernetes-version` na primeira etapa, um de *1.15.7* foi especificado. Isso definiu a versão Kubernetes tanto para o plano de controle quanto para o pool de nós padrão. Os comandos nesta seção explicam como atualizar um único pool de nóespecífico.

A relação entre a atualização da versão Kubernetes do plano de controle e a piscina de nós são explicadas na [seção abaixo](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> A versão de imagem do sistema operacional do pool de nó está vinculada à versão Kubernetes do cluster. Você só receberá atualizações de imagem do SISTEMA OPERACIONAL, após uma atualização de cluster.

Como existem duas piscinas de nós neste exemplo, devemos usar [o upgrade aks nodepool][az-aks-nodepool-upgrade] para atualizar um pool de nós. Vamos atualizar o *mynodepool* para Kubernetes *1.15.7*. Use o comando [aks nodepool upgrade][az-aks-nodepool-upgrade] para atualizar o pool de nó, como mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

Liste o status de suas piscinas de nó novamente usando o comando [az aks node pool pool.][az-aks-nodepool-list] O exemplo a seguir mostra que *o mynodepool* está no estado *de atualização* para *1.15.7*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para atualizar os nós para a versão especificada.

Como uma prática recomendada, você deve atualizar todos os pools de nós em um cluster AKS para a mesma versão kubernetes. O comportamento `az aks upgrade` padrão é atualizar todos os pools de nós junto com o plano de controle para alcançar esse alinhamento. A capacidade de atualizar pools de nós individuais permite que você execute uma atualização de rolagem e agende pods entre pools de nós para manter o tempo de atividade do aplicativo dentro das restrições acima mencionadas.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Atualize um plano de controle de cluster com várias piscinas de nó

> [!NOTE]
> Kubernetes usa o esquema padrão de versão [semântica.](https://semver.org/) O número da versão é expresso como *x.y.z*, onde *x* é a versão principal, *y* é a versão menor, e *z* é a versão patch. Por exemplo, na versão *1.12.6*, 1 é a versão principal, 12 é a versão menor, e 6 é a versão de patch. A versão Kubernetes do plano de controle e a piscina inicial de nós são definidas durante a criação do cluster. Todas as piscinas adicionais de nós têm sua versão Kubernetes definida quando são adicionadas ao cluster. As versões kubernetes podem diferir entre as piscinas de nó, bem como entre uma piscina de nós e o plano de controle.

Um cluster AKS tem dois objetos de recurso de cluster com versões Kubernetes associadas.

1. Uma versão kubernetes do plano de controle de cluster.
2. Uma piscina de nó com uma versão Kubernetes.

Um plano de controle mapeia para uma ou muitas piscinas de nós. O comportamento de uma operação de upgrade depende do comando Azure CLI.

Atualizar um plano de controle `az aks upgrade`AKS requer o uso . Este comando atualiza a versão do plano de controle e todos os pools de nós no cluster.

A emissão `az aks upgrade` do `--control-plane-only` comando com a bandeira atualiza apenas o plano de controle de cluster. Nenhuma das piscinas de nó associadas no cluster são alteradas.

A atualização de pools `az aks nodepool upgrade`de nós individuais requer o uso . Este comando atualiza apenas o pool de nó de destino com a versão kubernetes especificada

### <a name="validation-rules-for-upgrades"></a>Regras de validação para upgrades

As atualizações válidas do Kubernetes para o plano de controle de um cluster e os pools de nós são validados pelos seguintes conjuntos de regras.

* Regras para versões válidas para atualizar pools de nó:
   * A versão do pool de nó deve ter a mesma versão *principal* do plano de controle.
   * A versão *menor* do pool de nó deve estar dentro de duas versões *menores* da versão do plano de controle.
   * A versão do pool de nó `major.minor.patch` não pode ser maior do que a versão de controle.

* Regras para o envio de uma operação de atualização:
   * Você não pode rebaixar o plano de controle ou uma versão kubernetes do pool de nó.
   * Se uma versão kubernetes do pool de nó não for especificada, o comportamento depende do cliente que está sendo usado. A declaração nos modelos do Gerenciador de recursos volta à versão existente definida para o pool de nós se usada, se nenhuma for definida, a versão do plano de controle é usada para voltar atrás.
   * Você pode atualizar ou dimensionar um plano de controle ou um pool de nó em um determinado momento, você não pode enviar várias operações em um único plano de controle ou recurso de pool de nó simultaneamente.

## <a name="scale-a-node-pool-manually"></a>Dimensione uma piscina de nó manualmente

À medida que a carga de trabalho do aplicativo exige mudança, talvez seja necessário dimensionar o número de nós em um pool de nós. O número de nódulos pode ser ampliado para cima ou para baixo.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Para dimensionar o número de nódulos em uma piscina de nó, use o comando [az aks node pool scale.][az-aks-nodepool-scale] O exemplo a seguir dimensiona o número de nós em *mynodepool* para *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Liste o status de suas piscinas de nó novamente usando o comando [az aks node pool pool.][az-aks-nodepool-list] O exemplo a seguir mostra que *o mynodepool* está no estado *Scaling* com uma nova contagem de *5* nomes:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para a operação de escala ser concluída.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Dimensione um pool de nó específico automaticamente, habilitando o cluster autoscaler

O AKS oferece um recurso separado para dimensionar automaticamente pools de nó com um recurso chamado [cluster autoscaler](cluster-autoscaler.md). Este recurso pode ser habilitado por pool de nó com contagens mínimas e máximas únicas por pool de nó. Saiba como [usar o cluster autoscaler por pool de nó](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Exclua um pool de nó

Se você não precisar mais de um pool, você pode excluí-lo e remover os nós VM subjacentes. Para excluir um pool de nó, use o comando [az aks node pool delete][az-aks-nodepool-delete] e especifique o nome do pool de nó. O exemplo a seguir exclui o *mynoodepool* criado nas etapas anteriores:

> [!CAUTION]
> Não há opções de recuperação para perda de dados que possam ocorrer quando você exclui um pool de nós. Se os pods não puderem ser agendados em outros pools de nós, esses aplicativos não estarão disponíveis. Certifique-se de não excluir um pool de nós quando os aplicativos em uso não tiverem backups de dados ou a capacidade de executar em outros pools de nós em seu cluster.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

O exemplo a seguir, saída do comando [az aks node pool pool][az-aks-nodepool-list] mostra que *o mynodepool* está no estado *de exclusão:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para excluir os nódulos e a piscina de nó.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Especifique um tamanho de VM para uma piscina de nó

Nos exemplos anteriores para criar um pool de nó, um tamanho vm padrão foi usado para os nós criados no cluster. Um cenário mais comum é que você crie pools de nós com diferentes tamanhos e capacidades de VM. Por exemplo, você pode criar um pool de nós que contenha nós com grandes quantidades de CPU ou memória, ou um pool de nodos que fornece suporte a GPU. Na próxima etapa, você [usa manchas e tolerâncias](#schedule-pods-using-taints-and-tolerations) para dizer ao agendador kubernetes como limitar o acesso a pods que podem ser executados nesses nós.

No exemplo a seguir, crie um pool de nó baseado em GPU que use o tamanho *Standard_NC6* VM. Estes VMs são alimentados pela placa NVIDIA Tesla K80. Para obter informações sobre os tamanhos de VM disponíveis, consulte [Tamanhos para máquinas virtuais Linux no Azure][vm-sizes].

Crie um pool de nó usando o [comando az aks node pool add add][az-aks-nodepool-add] novamente. Desta vez, especifique o nome *gpunodepool*e use o `--node-vm-size` parâmetro para especificar o tamanho *Standard_NC6:*

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

O exemplo a seguir, saída do comando [az aks node pool pool][az-aks-nodepool-list] mostra que *o gpunodepool* está *criando* árdeos com o *VmSize*especificado:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Leva alguns minutos para que o *gpunodepool* seja criado com sucesso.

## <a name="schedule-pods-using-taints-and-tolerations"></a>Agende pods usando manchas e tolerâncias

Agora você tem dois pools de nó em seu cluster - o pool de nó padrão criado inicialmente e o pool de nó baseado em GPU. Use o comando [kubectl get nodes][kubectl-get] para visualizar os nódulos em seu cluster. A saída do exemplo a seguir mostra os nós:

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

O Agendador Kubernetes pode usar taints e tolerations para restringir quais cargas de trabalho podem ser executados em nós.

* Um **taint** é aplicado a um nó que indica que apenas os pods específicos podem ser agendados neles.
* Um **toleration**, em seguida, é aplicado a um pod que lhes permite *tolerar* um taint de nó.

Para obter mais informações sobre como usar recursos programados do Kubernetes avançados, consulte [As melhores práticas para recursos avançados do agendador em AKS][taints-tolerations]

Neste exemplo, aplique uma mancha ao nó baseado em GPU usando o comando --node-taints. Especifique o nome do nó baseado em `kubectl get nodes` GPU na saída do comando anterior. A mancha é aplicada como um par *de chaves=valor* e, em seguida, uma opção de agendamento. O exemplo a seguir usa o *sku=gpu* pair e define pods de outra forma tem a capacidade *NoSchedule:*

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

O exemplo básico a seguir O manifesto YAML usa uma tolerância para permitir que o agendador Kubernetes execute um pod NGINX no nó baseado em GPU. Para obter um exemplo mais apropriado, mas demorado, para executar um trabalho tensorflow contra o conjunto de dados MNIST, consulte [Usar GPUs para cargas de trabalho intensivas em computação em AKS][gpu-cluster].

Crie um arquivo chamado `gpu-toleration.yaml` e copie no exemplo YAML a seguir:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Agende o `kubectl apply -f gpu-toleration.yaml` pod usando o comando:

```console
kubectl apply -f gpu-toleration.yaml
```

Leva alguns segundos para agendar a cápsula e puxar a imagem NGINX. Use o comando [kubectl describe pod][kubectl-describe] para visualizar o status do pod. A seguinte saída de exemplo condensado mostra que a tolerância *sku=gpu:NoSchedule* é aplicada. Na seção de eventos, o agendador atribuiu o pod ao nó *aks-gpunodepool-28993262-vms000000 gpu:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

Somente os pods que têm essa tolerância aplicada podem ser agendados em nódulos em *gpunodepool*. Qualquer outra cápsula seria agendada na piscina de nó *nodepool1.* Se você criar pools adicionais de nó, você pode usar manchas adicionais e tolerâncias para limitar quais pods podem ser agendados nesses recursos de nó.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Especifique uma mancha, rótulo ou tag para um pool de nó

Ao criar uma piscina de nó, você pode adicionar manchas, rótulos ou tags para essa piscina de nó. Quando você adiciona uma mancha, etiqueta ou marca, todos os nós dentro dessa piscina de nós também recebem essa mancha, rótulo ou tag.

Para criar uma piscina de nó com uma mancha, use [az aks nodepool add][az-aks-nodepool-add]. Especifique o nome `--node-taints` *taintnp* e use o parâmetro para especificar *sku=gpu:NoSchedule* para a mancha.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

O exemplo a seguir de saída do comando [az aks nodepool list][az-aks-nodepool-list] mostra que *taintnp* está *criando* nodes com os *nodeTaints*especificados:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

As informações de mancha são visíveis em Kubernetes para lidar com regras de agendamento para nódulos.

Você também pode adicionar rótulos a uma piscina de nó durante a criação da piscina de nó. As etiquetas definidas na piscina de nós são adicionadas a cada nó na piscina de nós. Esses [rótulos são visíveis em Kubernetes][kubernetes-labels] para lidar com regras de agendamento para nós.

Para criar uma piscina de nó com um rótulo, use [az aks nodepool add][az-aks-nodepool-add]. Especifique o nome `--labels` *labelnp* e use o parâmetro para especificar *dept=IT* e *costcenter=9999* para rótulos.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> O rótulo só pode ser definido para piscinas de nó durante a criação da piscina de nó. Os rótulos também devem ser um par de chave/valor e ter uma [sintaxe válida][kubernetes-label-syntax].

O exemplo a seguir, saída do comando [az aks nodepool list][az-aks-nodepool-list] mostra que *labelnp* está *criando* nós com os *nodes*especificados:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

Você pode aplicar uma tag Azure a piscinas de nó no seu cluster AKS. As tags aplicadas a um pool de nós são aplicadas em cada nó dentro do pool de nós e são persistidas através de upgrades. As tags também são aplicadas a novos nós adicionados a um pool de nós durante operações de saída de escala. Adicionar uma tag pode ajudar em tarefas como rastreamento de políticas ou estimativa de custos.

Crie uma piscina de nó usando o [az aks nodepool add][az-aks-nodepool-add]. Especifique o nome *tagnodepool* e use o `--tag` parâmetro para especificar *dept=IT* e *costcenter=9999* para tags.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Você também pode `--tags` usar o parâmetro ao usar o comando [az aks nodepool update,][az-aks-nodepool-update] bem como durante a criação de clusters. Durante a criação `--tags` do cluster, o parâmetro aplica a tag ao pool de nó inicial criado com o cluster. Todos os nomes de marcas devem aderir às limitações em [Usar tags para organizar seus recursos do Azure][tag-limitation]. Atualizar um pool de `--tags` nó com o parâmetro atualiza quaisquer valores de tag existentes e anexa quaisquer novas tags. Por exemplo, se o seu pool de nó tivesse *dept=IT* e *costcenter=9999* para tags e você o atualizasse com *team=dev* e *costcenter=111* para tags, você teria *dept=IT*, *costcenter=111*e *team=dev* para tags.

O exemplo a seguir de saída do comando [az aks nodepool list][az-aks-nodepool-list] mostra que *tagnodepool* está *criando* árdeos com a *tag*especificada:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Gerenciar pools de nó usando um modelo de gerenciador de recursos

Quando você usa um modelo do Azure Resource Manager para criar e gerenciar recursos, você normalmente pode atualizar as configurações do seu modelo e reimplantar para atualizar o recurso. Com pools de nó em AKS, o perfil inicial do pool de nó não pode ser atualizado uma vez que o cluster AKS tenha sido criado. Esse comportamento significa que você não pode atualizar um modelo de Gerenciador de recursos existente, fazer uma alteração nos pools de nó e reimplantar. Em vez disso, você deve criar um modelo de gerenciador de recursos separado que atualize apenas os pools de nó para um cluster AKS existente.

Crie um modelo `aks-agentpools.json` como e cole o seguinte exemplo manifesto. Este modelo de exemplo configura as seguintes configurações:

* Atualiza o pool de nós *Linux* chamado *myagentpool* para executar três nós.
* Define os nódulos na piscina de nó para executar kubernetes versão *1.15.7*.
* Define o tamanho do nó como *Standard_DS2_v2*.

Edite esses valores conforme necessário para atualizar, adicionar ou excluir pools de nó conforme necessário:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Implante este modelo usando o comando [az group deployment create,][az-group-deployment-create] como mostrado no exemplo a seguir. Você é solicitado para o nome e localização do cluster AKS existente:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Você pode adicionar uma tag ao seu pool de nó adicionando a propriedade *de tag* no modelo, conforme mostrado no exemplo a seguir.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Pode levar alguns minutos para atualizar seu cluster AKS, dependendo das configurações e operações do pool de nó que você define no modelo do Gerenciador de recursos.

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>Atribuir um IP público por nó para um pool de nó (visualização)

> [!WARNING]
> Durante a pré-visualização da atribuição de um IP público por nó, ele não pode ser usado com o *Standard Load Balancer SKU em AKS* devido a possíveis regras de balanceador de carga conflitantes com o provisionamento de VM. Como resultado dessa limitação, os pools de agentes do Windows não são suportados com esse recurso de visualização. Durante a visualização, você deve usar o *Basic Load Balancer SKU* se precisar atribuir um IP público por nó.

Os nódulos AKS não exigem seus próprios endereços IP públicos para comunicação. No entanto, os cenários podem exigir que os nódulos em um pool de nós recebam seus próprios endereços IP públicos dedicados. Um cenário comum é para cargas de trabalho de jogos, onde um console precisa fazer uma conexão direta com uma máquina virtual em nuvem para minimizar os saltos. Este cenário pode ser alcançado no AKS, registrando-se para um recurso de pré-visualização, Node Public IP (preview).

Registre-se no recurso IP público do nó emitindo o seguinte comando Azure CLI.

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

Após o registro bem-sucedido, implante um modelo [above](#manage-node-pools-using-a-resource-manager-template) do Azure Resource `enableNodePublicIP` Manager seguindo as mesmas instruções acima e adicione a propriedade booleana ao agentPoolProfiles. Defina o `true` valor como por `false` padrão, ele é definido como se não fosse especificado. 

Esta propriedade é uma propriedade somente de tempo de criação e requer uma versão aPI mínima de 2019-06-01. Isso pode ser aplicado tanto aos pools de nó Linux quanto Windows.

## <a name="clean-up-resources"></a>Limpar os recursos

Neste artigo, você criou um cluster AKS que inclui nomes baseados em GPU. Para reduzir o custo desnecessário, você pode querer excluir o *gpunodepool*ou todo o cluster AKS.

Para excluir o pool de nó baseado em GPU, use o comando [aks nodepool excluir][az-aks-nodepool-delete] como mostrado no exemplo a seguir:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Para excluir o próprio cluster, use o comando [az group delete][az-group-delete] para excluir o grupo de recursos AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como criar e gerenciar vários grupos de nó em um cluster AKS. Para obter mais informações sobre como controlar pods em pools de nós, consulte [As melhores práticas para recursos avançados do agendador no AKS][operator-best-practices-advanced-scheduler].

Para criar e usar os pools de nó de contêiner do Windows Server, consulte [Criar um contêiner do Windows Server em AKS][aks-windows].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md