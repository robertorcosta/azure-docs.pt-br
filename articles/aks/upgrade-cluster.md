---
title: Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como atualizar um cluster do AKS (serviço kubernetes do Azure) para obter os recursos e as atualizações de segurança mais recentes.
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: da46c44dc9cc16dfa44aacb15b35b652c0c912a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87050624"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)

Como parte do ciclo de vida de um cluster do AKS, muitas vezes você precisará atualizar para a versão mais recente do Kubernetes. É importante aplicar as versões mais recentes de segurança do Kubernetes ou atualizar para obter os recursos mais recentes. Este artigo mostra como atualizar os componentes mestres ou um único pool de nós padrão em um cluster AKS.

Para clusters AKS que usam vários pools de nó ou nós do Windows Server, consulte [atualizar um pool de nós em AKs][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.65 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

> [!WARNING]
> Uma atualização do cluster AKS dispara um Cordon e dreno de seus nós. Se você tiver uma cota de computação baixa disponível, a atualização poderá falhar. Consulte [aumentar cotas](../azure-portal/supportability/resource-manager-core-quotas-request.md) para obter mais informações.

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificação de atualizações disponíveis do cluster do AKS

Para verificar quais versões do Kubernetes estão disponíveis para seu cluster, use o comando [az aks get-upgrades][az-aks-get-upgrades]. O exemplo abaixo verifica atualizações disponíveis para o cluster nomeado *myAKSCluster* em um grupo de recursos nomeado *myResourceGroup*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando você atualiza um cluster AKS com suporte, as versões secundárias do kubernetes não podem ser ignoradas. Por exemplo, as atualizações entre *1.12. x*  ->  *1.13. x* ou *1.13. x*  ->  *1.14.* x são permitidas, no entanto *1.12. x*  ->  *1.14. x* não é.
>
> Para atualizar, de *1.12. x*  ->  *1.14.* x, primeiro atualize de *1.12. x*  ->  *1.13. x*e, em seguida, atualize de *1.13. x*  ->  *1.14. x*.
>
> Ignorar várias versões só pode ser feito ao atualizar de uma versão sem suporte de volta para uma versão com suporte. Por exemplo, atualizar de um *1,10. x* sem suporte > um *1.15. x* com suporte pode ser concluído.

A saída de exemplo a seguir mostra que o cluster pode ser atualizado para as versões *1.13.9* e *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Se nenhuma atualização estiver disponível, você receberá:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>Personalizar a atualização do surto do nó (versão prévia)

> [!Important]
> As sobretensões de nó exigem a cota de assinatura para a contagem máxima de surtos solicitada para cada operação de atualização. Por exemplo, um cluster que tem cinco pools de nós, cada um com uma contagem de quatro nós, tem um total de 20 nós. Se cada pool de nós tiver um valor máximo de surto de 50%, a cota de computação e IP adicional de 10 nós (2 nós * 5 pools) será necessária para concluir a atualização.
>
> Se estiver usando o Azure CNI, valide se há IPs disponíveis na sub-rede também para [atender aos requisitos de IP do Azure CNI](configure-azure-cni.md).

Por padrão, o AKS configura atualizações para surtos com um nó adicional. Um valor padrão de um para a configuração de surto máximo permite que o AKS Minimize a interrupção da carga de trabalho criando um nó adicional antes de Cordon/dreno de aplicativos existentes para substituir um nó com versão mais antiga. O valor máximo de surto pode ser personalizado por pool de nós para permitir uma compensação entre a velocidade de atualização e a interrupção da atualização. Ao aumentar o valor máximo de surtos de tensão, o processo de atualização é concluído mais rapidamente, mas a definição de um valor grande para o surto máximo pode causar interrupções durante o processo de atualização. 

Por exemplo, um valor máximo de surto de 100% fornece o processo de atualização mais rápido possível (duplicando a contagem de nós), mas também faz com que todos os nós no pool de nós sejam drenados simultaneamente. Talvez você queira usar um valor mais alto, como este para os ambientes de teste. Para pools de nós de produção, recomendamos uma configuração de max_surge de 33%.

AKS aceita ambos os valores inteiros e um valor percentual para pico máximo. Um inteiro, como "5", indica cinco nós adicionais para fazer um surto. Um valor de "50%" indica um valor de surto da metade da contagem de nós atual no pool. Os valores de porcentagem máxima de surtos podem ser um mínimo de 1% e um máximo de 100%. Um valor percentual é arredondado para a contagem de nós mais próxima. Se o valor máximo de surto for menor que a contagem de nós atual no momento da atualização, a contagem de nós atual será usada para o valor máximo de surtos de tensão.

Durante uma atualização, o valor máximo de surto pode ser um mínimo de 1 e um valor máximo igual ao número de nós no pool de nós. Você pode definir valores maiores, mas o número máximo de nós usados para picos máximos não será maior do que o número de nós no pool no momento da atualização.

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>Configurar o recurso de visualização para personalizar a atualização do surto do nó

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

Levará vários minutos para o registro. Use o comando abaixo para verificar se o recurso está registrado:

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

Durante a visualização, você precisa da extensão da CLI de *AKs* para usar o pico máximo. Use o comando [AZ Extension Add][az-extension-add] e, em seguida, verifique se há atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> A configuração de surto máximo em um pool de nós é permanente.  Atualizações de kubernetes subsequentes ou atualizações de versão de nó usarão essa configuração. Você pode alterar o valor máximo de surtos para seus pools de nós a qualquer momento. Para pools de nós de produção, recomendamos uma configuração de pico máximo de 33%.

Use os comandos a seguir para definir valores de pico máximo para pools de nós novos ou existentes.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster AKS

Com uma lista de versões disponíveis para o cluster do AKS, use o comando [az aks upgrade][az-aks-upgrade] para atualizar. Durante o processo de atualização, o AKS adiciona um novo nó ao cluster que executa a versão especificada do kubernetes, depois cuidadosamente [Cordon e esvazia][kubernetes-drain] um dos nós antigos para minimizar a interrupção na execução de aplicativos. Quando o novo nó é confirmado como executando pods de aplicativo, o nó antigo é excluído. Esse processo se repete até que todos os nós no cluster tenham sido atualizados.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

O cluster demora alguns minutos para ser atualizado, dependendo de quantos nós que você tem.

> [!NOTE]
> Há um tempo total permitido para a conclusão de uma atualização de cluster. Esse tempo é calculado por meio do produto do `10 minutes * total number of nodes in the cluster` . Por exemplo, em um cluster de 20 nós, as operações de atualização devem ter êxito em 200 minutos ou AKS falhará na operação para evitar um estado de cluster irrecuperável. Para recuperar em caso de falha de atualização, repita a operação de atualização depois que o tempo limite tiver sido atingido.

Para confirmar se a atualização teve êxito, use o comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora executa *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como atualizar um cluster do AKS existente. Para saber mais sobre como implantar e gerenciar clusters do AKS, confira os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
