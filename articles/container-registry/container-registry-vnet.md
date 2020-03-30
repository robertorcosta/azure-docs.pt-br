---
title: Restringir o acesso com uma rede virtual
description: Permitir o acesso a um registro de contêiner do Azure apenas a partir de recursos em uma rede virtual do Azure ou de intervalos públicos de endereços IP.
ms.topic: article
ms.date: 07/01/2019
ms.openlocfilehash: a6b89b074c25ea0948597ede7e5681b100c7f429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454336"
---
# <a name="restrict-access-to-an-azure-container-registry-using-an-azure-virtual-network-or-firewall-rules"></a>Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall

[A Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) fornece rede privada e segura para seus recursos azure e no local. Ao limitar o acesso ao seu registro privado de contêineres do Azure a partir de uma rede virtual do Azure, você garante que apenas os recursos na rede virtual acessem o registro. Para cenários entre premissas, você também pode configurar regras de firewall para permitir o acesso ao registro apenas a partir de endereços IP específicos.

Este artigo mostra dois cenários para configurar regras de acesso de rede de entrada em um registro de contêiner: a partir de uma máquina virtual implantada em uma rede virtual ou a partir de um endereço IP público de uma VM.

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).
>

Se, em vez disso, você precisar configurar regras de acesso para recursos para chegar a um registro de contêiner atrás de um firewall, consulte [Configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md).


## <a name="preview-limitations"></a>Limitações de visualização

* Apenas um registro de contêiner **Premium** pode ser configurado com regras de acesso à rede. Para obter informações sobre os níveis de serviço de registro, consulte [As SKUs de registro de contêiner do Azure](container-registry-skus.md). 

* Apenas um cluster [azure Kubernetes Service](../aks/intro-kubernetes.md) ou [uma máquina virtual](../virtual-machines/linux/overview.md) Azure podem ser usados como um host para acessar um registro de contêineres em uma rede virtual. *Outros serviços do Azure, incluindo o Azure Container Instances, não são suportados no momento.*

* As operações [de tarefas do ACR](container-registry-tasks-overview.md) não são suportadas atualmente em um registro de contêiner acessado em uma rede virtual.

* Cada registro suporta um máximo de 100 regras de rede virtual.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas do Azure CLI neste artigo, é necessária a versão 2.0.58 ou posterior do Azure CLI. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli].

* Se você ainda não tiver um registro de contêiner, crie um (Premium `hello-world` SKU necessário) e empurre uma imagem de exemplo, como do Docker Hub. Por exemplo, use o [portal Azure][quickstart-portal] ou o [Azure CLI][quickstart-cli] para criar um registro. 

* Se você quiser restringir o acesso ao registro usando uma rede virtual em uma assinatura diferente do Azure, você precisa registrar o provedor de recursos para o Registro de Contêineres do Azure nessa assinatura. Por exemplo: 

  ```azurecli
  az account set --subscription <Name or ID of subscription of virtual network>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

## <a name="about-network-rules-for-a-container-registry"></a>Sobre as regras da rede para um registro de contêineres

Um registro de contêiner do Azure por padrão aceita conexões pela internet de hosts em qualquer rede. Com uma rede virtual, você pode permitir que apenas recursos do Azure, como um cluster AKS ou Azure VM, acessem o registro com segurança, sem cruzar um limite de rede. Você também pode configurar regras de firewall de rede para permitir apenas faixas específicas de endereços IP da Internet. 

Para limitar o acesso a um registro, primeiro altere a ação padrão do registro para que ele negue todas as conexões de rede. Em seguida, adicione regras de acesso à rede. Os clientes que têm acesso através das regras da rede devem continuar a [autenticar ao registro de contêineres](https://docs.microsoft.com/azure/container-registry/container-registry-authentication) e estar autorizados a acessar os dados.

### <a name="service-endpoint-for-subnets"></a>Ponto final de serviço para sub-redes

Para permitir o acesso de uma sub-rede em uma rede virtual, você precisa adicionar um [ponto final](../virtual-network/virtual-network-service-endpoints-overview.md) de serviço para o serviço de Registro de Contêineres do Azure. 

Serviços multilocatários, como o Registro de Contêineres do Azure, usam um único conjunto de endereços IP para todos os clientes. Um ponto final de serviço atribui um ponto final para acessar um registro. Este ponto final dá ao tráfego uma rota ideal para o recurso sobre a rede backbone do Azure. As identidades de rede virtual e a sub-rede também são transmitidas com cada solicitação.

### <a name="firewall-rules"></a>Regras de firewall

Para as regras da rede IP, forneça faixas de endereços de internet permitidas usando notação CIDR como *16.17.18.0/24* ou endereços IP individuais como *16.17.18.19*. As regras da rede IP só são permitidas para endereços IP *públicos* na Internet. Intervalos de endereços IP reservados para redes privadas (conforme definido em RFC 1918) não são permitidos nas regras de IP.

## <a name="create-a-docker-enabled-virtual-machine"></a>Crie uma máquina virtual habilitada para Docker

Para este artigo, use uma VM Ubuntu habilitada para Docker para acessar um registro de contêiner do Azure. Para usar a autenticação do Azure Active Directory no registro, instale também o [Azure CLI][azure-cli] na VM. Se você já tem uma máquina virtual Azure, pule essa etapa de criação.

Você pode usar o mesmo grupo de recursos para sua máquina virtual e seu registro de contêineres. Esta configuração simplifica a limpeza no final, mas não é necessária. Se você optar por criar um grupo de recursos separado para a máquina virtual e a rede virtual, execute [a criação do grupo AZ][az-group-create]. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *westcentralus:*

```azurecli
az group create --name myResourceGroup --location westus
```

Agora implante uma máquina virtual Ubuntu Azure padrão com [az vm create][az-vm-create]. O exemplo a seguir cria uma VM chamada *myDockerVM*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

A criação da VM demora alguns minutos. Quando o comando for concluído, observe o `publicIpAddress` exibido pela CLI do Azure. Use este endereço para fazer conexões SSH com a VM e, opcionalmente, para a configuração posterior das regras de firewall.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois que a VM estiver em execução, estabeleça uma conexão SSH à VM. Substitua *publicIpAddress* pelo endereço IP público da VM.

```bash
ssh azureuser@publicIpAddress
```

Execute o seguinte comando para instalar o Docker no VM Ubuntu:

```bash
sudo apt install docker.io -y
```

Após a instalação, execute o seguinte comando para verificar se o Docker está funcionando corretamente na VM:

```bash
sudo docker run -it hello-world
```

Saída:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga as etapas em [Instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure em sua máquina virtual do Ubuntu. Para este artigo, certifique-se de instalar a versão 2.0.58 ou posterior.

Saia da conexão SSH.

## <a name="allow-access-from-a-virtual-network"></a>Permitir acesso a partir de uma rede virtual

Nesta seção, configure seu registro de contêiner para permitir o acesso de uma sub-rede em uma rede virtual Do Zure. Etapas equivalentes usando o portal Azure CLI e Azure são fornecidas.

### <a name="allow-access-from-a-virtual-network---cli"></a>Permitir acesso a partir de uma rede virtual - CLI

#### <a name="add-a-service-endpoint-to-a-subnet"></a>Adicione um ponto final de serviço a uma sub-rede

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *como DockerVM,* o nome padrão da rede virtual é *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*. Verifique isso no portal Dozure ou usando o comando [az network vnet list:][az-network-vnet-list]

```azurecli
az network vnet list --resource-group myResourceGroup --query "[].{Name: name, Subnet: subnets[0].name}"
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

Use o comando [az network vnet subnet update][az-network-vnet-subnet-update] para adicionar um ponto final de serviço **Microsoft.ContainerRegistry** à sua sub-rede. Substitua os nomes de sua rede virtual e sub-rede no seguinte comando:

```azurecli
az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints Microsoft.ContainerRegistry
```

Use o comando [az network vnet show show][az-network-vnet-subnet-show] para recuperar o ID de recurso da sub-rede. Você precisa disso em uma etapa posterior para configurar uma regra de acesso à rede.

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

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso padrão da rede ao registro

Por padrão, um registro de contêiner do Azure permite conexões de hosts em qualquer rede. Para limitar o acesso a uma rede selecionada, altere a ação padrão para negar o acesso. Substitua o nome do seu registro no seguinte comando [de atualização az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [az acr network-add para][az-acr-network-rule-add] adicionar uma regra de rede ao seu registro que permite o acesso da sub-rede da VM. Substitua o nome do registro de contêiner e o ID de recurso da sub-rede no seguinte comando: 

 ```azurecli
az acr network-rule add --name mycontainerregistry --subnet <subnet-resource-id>
```

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

### <a name="allow-access-from-a-virtual-network---portal"></a>Permitir acesso a partir de uma rede virtual - portal

#### <a name="add-service-endpoint-to-subnet"></a>Adicionar ponto final de serviço à sub-rede

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *como DockerVM,* o nome padrão da rede virtual é *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*.

Para adicionar um ponto final de serviço para o Registro de Contêineres do Azure a uma sub-rede:

1. Na caixa de pesquisa na parte superior do [portal Azure, digite][azure-portal] *redes virtuais*. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
1. Na lista de redes virtuais, selecione a rede virtual onde sua máquina virtual está implantada, como *o myDockerVMVNET*.
1. Em **Configurações,** selecione **Sub-redes**.
1. Selecione a sub-rede onde sua máquina virtual está implantada, como *myDockerVMSubnet*.
1. Em **pontos finais do Serviço,** selecione **Microsoft.ContainerRegistry**.
1. Selecione **Salvar**.

![Adicionar ponto final de serviço à sub-rede][acr-subnet-service-endpoint] 

#### <a name="configure-network-access-for-registry"></a>Configurar o acesso à rede para registro

Por padrão, um registro de contêiner do Azure permite conexões de hosts em qualquer rede. Para limitar o acesso à rede virtual:

1. No portal, navegue até o seu registro de contêineres.
1. Em **Configurações,** selecione **Firewall e redes virtuais**.
1. Para negar o acesso por padrão, opte por permitir o acesso a partir de **redes selecionadas**. 
1. Selecione **Adicionar rede virtual existente**e selecione a rede virtual e a sub-rede configurada com um ponto final de serviço. Selecione **Adicionar**.
1. Selecione **Salvar**.

![Configurar rede virtual para registro de contêineres][acr-vnet-portal]

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

## <a name="allow-access-from-an-ip-address"></a>Permitir acesso a partir de um endereço IP

Nesta seção, configure seu registro de contêiner para permitir o acesso a partir de um endereço OU intervalo IP específico. Etapas equivalentes usando o portal Azure CLI e Azure são fornecidas.

### <a name="allow-access-from-an-ip-address---cli"></a>Permitir acesso a partir de um endereço IP - CLI

#### <a name="change-default-network-access-to-registry"></a>Alterar o acesso padrão da rede ao registro

Se você ainda não fez isso, atualize a configuração do registro para negar o acesso por padrão. Substitua o nome do seu registro no seguinte comando [de atualização az acr:][az-acr-update]

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

#### <a name="remove-network-rule-from-registry"></a>Remover a regra da rede do registro

Se você adicionou anteriormente uma regra de rede para permitir o acesso da sub-rede da VM, remova o ponto final de serviço da sub-rede e a regra da rede. Substitua o nome do registro de contêiner e o ID de recurso da sub-rede que você recuperou em uma etapa anterior no comando [az acr network-rule remove:][az-acr-network-rule-remove] 

```azurecli
# Remove service endpoint

az network vnet subnet update \
  --name myDockerVMSubnet \
  --vnet-name myDockerVMVNET \
  --resource-group myResourceGroup \
  --service-endpoints ""

# Remove network rule

az acr network-rule remove --name mycontainerregistry --subnet <subnet-resource-id>
```

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

Use o comando [az acr network-add para][az-acr-network-rule-add] adicionar uma regra de rede ao seu registro que permite o acesso a partir do endereço IP da VM. Substitua o nome do registro de contêiner e o endereço IP público da VM no seguinte comando.

```azurecli
az acr network-rule add --name mycontainerregistry --ip-address <public-IP-address>
```

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

### <a name="allow-access-from-an-ip-address---portal"></a>Permitir acesso a partir de um endereço IP - portal

#### <a name="remove-existing-network-rule-from-registry"></a>Remover a regra de rede existente do registro

Se você adicionou anteriormente uma regra de rede para permitir o acesso da sub-rede da VM, remova a regra existente. Pule esta seção se quiser acessar o registro de uma VM diferente.

* Atualize as configurações da sub-rede para remover o ponto final de serviço da sub-rede para o Registro de Contêineres do Azure. 

  1. No [portal Azure,][azure-portal]navegue até a rede virtual onde sua máquina virtual está implantada.
  1. Em **Configurações,** selecione **Sub-redes**.
  1. Selecione a sub-rede onde sua máquina virtual está implantada.
  1. Em **pontos finais do Serviço,** remova a caixa de seleção para **Microsoft.ContainerRegistry**. 
  1. Selecione **Salvar**.

* Remova a regra de rede que permite que a sub-rede acesse o registro.

  1. No portal, navegue até o seu registro de contêineres.
  1. Em **Configurações,** selecione **Firewall e redes virtuais**.
  1. Em **redes virtuais,** selecione o nome da rede virtual e selecione **Remover**.
  1. Selecione **Salvar**.

#### <a name="add-network-rule-to-registry"></a>Adicionar regra de rede ao registro

1. No portal, navegue até o seu registro de contêineres.
1. Em **Configurações,** selecione **Firewall e redes virtuais**.
1. Se você ainda não fez isso, opte por permitir o acesso a partir de **redes selecionadas**. 
1. Em **redes virtuais,** certifique-se de que nenhuma rede seja selecionada.
1. Em **Firewall,** digite o endereço IP público de uma VM. Ou digite um intervalo de endereços na notação CIDR que contenha o endereço IP da VM.
1. Selecione **Salvar**.

![Configurar a regra de firewall para registro de contêineres][acr-vnet-firewall-portal]

Continue a [verificar o acesso ao registro](#verify-access-to-the-registry).

## <a name="verify-access-to-the-registry"></a>Verifique o acesso ao registro

Depois de esperar alguns minutos para que a configuração seja atualizada, verifique se a VM pode acessar o registro do contêiner. Faça uma conexão SSH com sua VM e execute o comando [az acr login][az-acr-login] para fazer login no seu registro. 

```bash
az acr login --name mycontainerregistry
```

Você pode executar operações `docker pull` de registro, como executar para puxar uma imagem de amostra do registro. Substitua um valor de imagem e tag apropriado para o seu registro, prefixado com o nome do servidor de login de registro (em todas as minúsculas):

```bash
docker pull mycontainerregistry.azurecr.io/hello-world:v1
``` 

Docker puxa com sucesso a imagem para a VM.

Este exemplo demonstra que você pode acessar o registro de contêineres privados através da regra de acesso à rede. No entanto, o registro não pode ser acessado a partir de um host de login diferente que não tenha uma regra de acesso à rede configurada. Se você tentar fazer login `az acr login` de `docker login` outro host usando o comando ou comando, a saída será semelhante à seguinte:

```Console
Error response from daemon: login attempt to https://xxxxxxx.azurecr.io/v2/ failed with status: 403 Forbidden
```

## <a name="restore-default-registry-access"></a>Restaurar o acesso ao registro padrão

Para restaurar o registro para permitir o acesso por padrão, remova todas as regras de rede configuradas. Em seguida, defina a ação padrão para permitir o acesso. Etapas equivalentes usando o portal Azure CLI e Azure são fornecidas.

### <a name="restore-default-registry-access---cli"></a>Restaurar o acesso ao registro padrão - CLI

#### <a name="remove-network-rules"></a>Remover regras de rede

Para ver uma lista de regras de rede configuradas para o seu registro, execute o seguinte comando [az acr network-rule list:][az-acr-network-rule-list]

```azurecli
az acr network-rule list--name mycontainerregistry 
```

Para cada regra configurada, execute o comando [az acr network-rule remove][az-acr-network-rule-remove] para removê-la. Por exemplo: 

```azurecli
# Remove a rule that allows access for a subnet. Substitute the subnet resource ID.

az acr network-rule remove \
  --name mycontainerregistry \
  --subnet /subscriptions/ \
  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myDockerVMVNET/subnets/myDockerVMSubnet

# Remove a rule that allows access for an IP address or CIDR range such as 23.45.1.0/24.

az acr network-rule remove \
  --name mycontainerregistry \
  --ip-address 23.45.1.0/24
```

#### <a name="allow-access"></a>Permitir o acesso

Substitua o nome do seu registro no seguinte comando [de atualização az acr:][az-acr-update]
```azurecli
az acr update --name myContainerRegistry --default-action Allow
```

### <a name="restore-default-registry-access---portal"></a>Restaurar acesso ao registro padrão - portal


1. No portal, navegue até o seu registro de contêineres e selecione **Firewall e redes virtuais.**
1. Em **redes virtuais,** selecione cada rede virtual e **selecione Remover**.
1. Em **Firewall,** selecione cada intervalo de endereços e selecione o ícone Excluir.
1. Em **Permitir acesso a partir de**, selecione Todas as **redes**. 
1. Selecione **Salvar**.

## <a name="clean-up-resources"></a>Limpar recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, você pode excluir opcionalmente os recursos usando um único comando [de exclusão de grupo az:](/cli/azure/group)

```azurecli
az group delete --name myResourceGroup
```

Para limpar seus recursos no portal, navegue até o grupo de recursos myResourceGroup. Uma vez que o grupo de recursos esteja carregado, clique em **Excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados lá.

## <a name="next-steps"></a>Próximas etapas

Vários recursos de rede virtual e funcionalidades foram discutidos neste artigo, embora brevemente. A documentação da Rede Virtual do Azure aborda estes tópicos extensivamente:

* [Rede virtual](https://docs.microsoft.com/azure/virtual-network/manage-virtual-network)
* [Sub-rede](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)
* [Pontos finais de serviço](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

<!-- IMAGES -->

[acr-subnet-service-endpoint]: ./media/container-registry-vnet/acr-subnet-service-endpoint.png
[acr-vnet-portal]: ./media/container-registry-vnet/acr-vnet-portal.png
[acr-vnet-firewall-portal]: ./media/container-registry-vnet/acr-vnet-firewall-portal.png

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
