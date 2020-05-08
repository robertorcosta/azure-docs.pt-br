---
title: Restringir o acesso usando um ponto de extremidade de serviço
description: Restringir o acesso a um registro de contêiner do Azure usando um ponto de extremidade de serviço em uma rede virtual do Azure
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: da5ab67d6658d8760565353e2a690c53d862d0ed
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982574"
---
# <a name="restrict-access-to-a-container-registry-using-a-service-endpoint-in-an-azure-virtual-network"></a>Restringir o acesso a um registro de contêiner usando um ponto de extremidade de serviço em uma rede virtual do Azure

A [rede virtual do Azure](../virtual-network/virtual-networks-overview.md) fornece uma rede segura e privada para seus recursos do Azure e locais. Um [ponto de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) permite que você proteja o endereço IP público do registro de contêiner para apenas sua rede virtual. Esse ponto de extremidade fornece ao tráfego uma rota ideal para o recurso na rede de backbone do Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada solicitação.

Este artigo mostra como configurar um ponto de extremidade de serviço do registro de contêiner (versão prévia) em uma rede virtual. 

> [!IMPORTANT]
> O registro de contêiner do Azure agora dá suporte ao [link privado do Azure](container-registry-private-link.md), permitindo que pontos de extremidade privados de uma rede virtual sejam colocados em um registro. Os pontos de extremidade privados podem ser acessados de dentro da rede virtual, usando endereços IP privados. É recomendável usar pontos de extremidade privados em vez de pontos de extremidade de serviço na maioria dos cenários de rede.

A configuração de um ponto de extremidade de serviço do registro está disponível na camada de serviço do registro de contêiner **Premium** . Para obter informações sobre limites e camadas de serviço do registro, consulte [camadas do registro de contêiner do Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitações de visualização

* O desenvolvimento futuro de pontos de extremidade de serviço para o registro de contêiner do Azure não está planejado no momento. Em vez disso, recomendamos o uso de [pontos de extremidade privados](container-registry-private-link.md) .
* Você não pode usar o portal do Azure para configurar pontos de extremidade de serviço em um registro.
* Somente um cluster do [serviço kubernetes do Azure](../aks/intro-kubernetes.md) ou uma [máquina virtual](../virtual-machines/linux/overview.md) do Azure pode ser usado como um host para acessar um registro de contêiner usando um ponto de extremidade de serviço. *Não há suporte para outros serviços do Azure, incluindo instâncias de contêiner do Azure.*
* Cada registro dá suporte a um máximo de 100 regras de acesso de rede.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas de CLI do Azure neste artigo, CLI do Azure versão 2.0.58 ou posterior é necessária. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli].

* Se você ainda não tiver um registro de contêiner, crie um (SKU Premium necessário) e envie por push uma imagem `hello-world` de exemplo, como do Hub do Docker. Por exemplo, use o [portal do Azure][quickstart-portal] ou o [CLI do Azure][quickstart-cli] para criar um registro. 

* Se você quiser restringir o acesso ao registro usando um ponto de extremidade de serviço em uma assinatura do Azure diferente, registre o provedor de recursos para o registro de contêiner do Azure nessa assinatura. Por exemplo: 

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="configure-network-access-for-registry"></a>Configurar o acesso à rede para o registro

Nesta seção, configure o registro de contêiner para permitir o acesso de uma sub-rede em uma rede virtual do Azure. São fornecidas etapas equivalentes usando o CLI do Azure e portal do Azure.

### <a name="allow-access-from-a-virtual-network---cli"></a>Permitir o acesso de uma rede virtual-CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Adicionar um ponto de extremidade de serviço a uma sub-rede

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *myDockerVM*, o nome de rede virtual padrão será *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*. Verifique isso na portal do Azure ou usando o comando [AZ Network vnet List][az-network-vnet-list] :

```azurecli
az network vnet list \
  --resource-group myResourceGroup \
  --query "[].{Name: name, Subnet: subnets[0].name}"
```

Saída:

```console
[
  {
    "Name": "myDockerVMVNET",
    "Subnet": "myDockerVMSubnet"
  }
]
```

Use o comando [AZ Network vnet subnet Update][az-network-vnet-subnet-update] para adicionar um ponto de extremidade de serviço **Microsoft. ContainerRegistry** à sua sub-rede. Substitua os nomes de sua rede virtual e sub-rede no seguinte comando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Use o comando [AZ Network vnet subnet show][az-network-vnet-subnet-show] para recuperar a ID de recurso da sub-rede. Você precisará disso em uma etapa posterior para configurar uma regra de acesso à rede.

```azurecli
az network vnet subnet show \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --query "id"
  --output tsv
``` 

Saída:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso de rede padrão ao registro

Por padrão, um registro de contêiner do Azure permite conexões de hosts em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação padrão para negar acesso. Substitua o nome do registro no seguinte comando [AZ ACR Update][az-acr-update] :

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [AZ ACR Network-Rule Add][az-acr-network-rule-add] para adicionar uma regra de rede ao registro que permite o acesso da sub-rede da VM. Substitua o nome do registro de contêiner e a ID de recurso da sub-rede no seguinte comando: 

 ```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --subnet <subnet-resource-id>
```

## <a name="verify-access-to-the-registry"></a>Verificar o acesso ao registro

Depois de aguardar alguns minutos para que a configuração seja atualizada, verifique se a VM pode acessar o registro de contêiner. Faça uma conexão SSH com sua VM e execute o comando [AZ ACR login][az-acr-login] para fazer logon no registro. 

```bash
az acr login --name mycontainerregistry
```

Você pode executar operações de registro, como `docker pull` executar, para extrair uma imagem de exemplo do registro. Substitua uma imagem e um valor de marca apropriado para o registro, prefixado com o nome do servidor de logon do registro (todas as letras minúsculas):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

O Docker efetua pull da imagem com êxito para a VM.

Este exemplo demonstra que você pode acessar o registro de contêiner privado por meio da regra de acesso de rede. No entanto, o registro não pode ser acessado de um host de logon que não tenha uma regra de acesso à rede configurada. Se você tentar fazer logon de outro host usando o `az acr login` comando ou `docker login` comando, a saída será semelhante à seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso padrão do registro

Para restaurar o registro para permitir o acesso por padrão, remova as regras de rede que estão configuradas. Em seguida, defina a ação padrão para permitir o acesso. São fornecidas etapas equivalentes usando o CLI do Azure e portal do Azure.

### <a name="restore-default-registry-access---cli"></a>Restaurar acesso padrão do registro-CLI

#### <a name="remove-network-rules"></a>Remover regras de rede

Para ver uma lista de regras de rede configuradas para o registro, execute o seguinte comando [AZ ACR Network-Rule List][az-acr-network-rule-list] :

```azurecli
az acr network-rule list --name mycontainerregistry 
```

Para cada regra configurada, execute o comando [AZ ACR Network-Rule remove][az-acr-network-rule-remove] para removê-la. Por exemplo: 

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet
```

#### <a name="allow-access"></a>Permitir o acesso

Substitua o nome do registro no seguinte comando [AZ ACR Update][az-acr-update] :
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, você pode, opcionalmente, excluir os recursos usando um único comando [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name myResourceGroup
```

Para limpar seus recursos no portal, navegue até o grupo de recursos MyResource Group. Depois que o grupo de recursos for carregado, clique em **excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados nele.

## <a name="next-steps"></a>Próximas etapas

* Para restringir o acesso a um registro usando um ponto de extremidade privado em uma rede virtual, consulte [Configurar o link privado do Azure para um registro de contêiner do Azure](container-registry-private-link.md).
* Se você precisar configurar as regras de acesso do registro por trás de um firewall do cliente, consulte [configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md).


<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png


<!-- LINKS - External -->
[aci-helloworld]: https://hub.docker.com/r/microsoft/aci-helloworld/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
