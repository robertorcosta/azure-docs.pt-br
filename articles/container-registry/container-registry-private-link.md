---
title: Configurar link privado
description: Configure um ponto final privado em um registro de contêiner e habilite um link privado em uma rede virtual local
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: de8228d84497e71f24dba3dd4e6162cb6735a8c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498920"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configure o Azure Private Link para um registro de contêiner Azure 

Você pode configurar um [ponto final privado](../private-link/private-endpoint-overview.md) para o seu registro de contêiner do Azure para que os clientes em uma rede virtual do Azure acessem o registro com segurança por meio de um link [privado](../private-link/private-link-overview.md). O ponto final privado usa um endereço IP do espaço de endereço de rede virtual para o seu registro. O tráfego de rede entre os clientes na rede virtual e o registro atravessa a rede virtual e um link privado na rede backbone da Microsoft, eliminando a exposição da internet pública.

Você pode [configurar as configurações de DNS](../private-link/private-endpoint-overview.md#dns-configuration) para o seu ponto final privado, de modo que as configurações se resolvam para o endereço IP privado alocado do registro. Com a configuração de DNS, os clientes e serviços da rede podem continuar acessando o registro no nome de domínio totalmente qualificado do registro, como *myregistry.azurecr.io*.

Esse recurso está disponível no nível de serviço de registro de contêineres **Premium.** Para obter informações sobre os níveis e limites do serviço de registro, consulte [As SKUs de registro de contêineres do Azure](container-registry-skus.md).

> [!IMPORTANT]
> Este recurso está atualmente em visualização, e algumas [limitações](#preview-limitations) se aplicam. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="preview-limitations"></a>Limitações de visualização

* Atualmente, você não pode configurar um link privado com um ponto final privado em um [registro geo-replicado](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas do Azure CLI neste artigo, recomenda-se a versão 2.2.0 ou posterior do Azure CLI. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli]. Ou executado no [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se você ainda não tiver um registro de contêiner, crie um (nível `hello-world` Premium necessário) e empurre uma imagem de exemplo, como do Docker Hub. Por exemplo, use o [portal Azure][quickstart-portal] ou o [Azure CLI][quickstart-cli] para criar um registro.
* Se você quiser configurar o acesso ao registro usando um link privado em uma assinatura diferente do Azure, você precisa registrar o provedor de recursos para o Registro de Contêineres do Azure nessa assinatura. Por exemplo: 

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Os exemplos de CLI do Azure neste artigo usam as seguintes variáveis de ambiente. Valores substitutos apropriados para o seu ambiente. Todos os exemplos são formatados para a concha Bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Crie uma máquina virtual habilitada para Docker

Para fins de teste, use uma VM Ubuntu habilitada para Docker para acessar um registro de contêiner do Azure. Para usar a autenticação do Azure Active Directory no registro, instale também o [Azure CLI][azure-cli] na VM. Se você já tem uma máquina virtual Azure, pule essa etapa de criação.

Você pode usar o mesmo grupo de recursos para sua máquina virtual e seu registro de contêineres. Esta configuração simplifica a limpeza no final, mas não é necessária. Se você optar por criar um grupo de recursos separado para a máquina virtual e a rede virtual, execute [o az group create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Agora implante uma máquina virtual Ubuntu Azure padrão com [az vm create][az-vm-create]. O exemplo a seguir cria uma VM chamada *myDockerVM*.

```azurecli
az vm create \
  --resource-group $resourceGroup \
  --name $vmName \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A criação da VM demora alguns minutos. Quando o comando for concluído, observe o `publicIpAddress` exibido pela CLI do Azure. Use esse endereço para estabelecer conexões SSH à VM.

### <a name="install-docker-on-the-vm"></a>Instalar o Docker na VM

Depois que a VM estiver em execução, estabeleça uma conexão SSH à VM. Substitua *publicIpAddress* pelo endereço IP público da VM.

```bash
ssh azureuser@publicIpAddress
```

Execute os seguintes comandos para instalar o Docker no VM Ubuntu:

```bash
sudo apt-get update
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

Siga as etapas em [Instalar a CLI do Azure com apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) para instalar a CLI do Azure em sua máquina virtual do Ubuntu. Por exemplo: 

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

Saia da conexão SSH.

## <a name="set-up-private-link---cli"></a>Configurar link privado - CLI

### <a name="get-network-and-subnet-names"></a>Obtenha nomes de rede e sub-rede

Se você ainda não os tiver, precisará dos nomes de uma rede virtual e de uma sub-rede para configurar um link privado. Neste exemplo, você usa a mesma sub-rede para a VM e o ponto final privado do registro. No entanto, em muitos cenários você configuraria o ponto final em uma sub-rede separada. 

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *como DockerVM,* o nome padrão da rede virtual é *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*. Defina esses valores em variáveis de ambiente executando o comando [az network vnet list:][az-network-vnet-list]

```azurecli
networkName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Name: name}' --output tsv)

subnetName=$(az network vnet list \
  --resource-group $resourceGroup \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo networkName=$networkName
echo subnetName=$subnetName
```

### <a name="disable-network-policies-in-subnet"></a>Desativar políticas de rede na sub-rede

Desativar políticas de [rede,](../private-link/disable-private-endpoint-network-policy.md) como grupos de segurança de rede na sub-rede para o ponto final privado. Atualize sua configuração de sub-rede [com a atualização da sub-rede de rede az][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configure a zona dns privada

Crie uma zona de DNS privada para o domínio de registro de contêiner Priviate Azure. Em etapas posteriores, você cria registros De DNS para seu domínio de registro dentro desta região de DNS.

Para usar uma região privada para substituir a resolução DNS padrão do registro de contêiner do Azure, a região deve ser nomeada **privatelink.azurecr.io**. Execute a seguinte [região de rede az private-dns criar][az-network-private-dns-zone-create] comando para criar a região privada:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Crie um link de associação

Execute [a rede az private-dns link vnet criar][az-network-private-dns-link-vnet-create] para associar sua zona privada com a rede virtual. Este exemplo cria um link chamado *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Criar um ponto final de registro privado

Nesta seção, crie o ponto final privado do registro na rede virtual. Primeiro, obtenha o ID de recurso do seu registro:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Execute o comando [az network private-endpoint create][az-network-private-endpoint-create] para criar o ponto final privado do registro.

O exemplo a seguir cria o ponto final *myPrivateEndpoint* e o service connection *myConnection*. Para especificar um recurso de registro `--group-ids registry`de contêiner para o ponto final, passe:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $resourceGroup \
    --vnet-name $networkName \
    --subnet $subnetName \
    --private-connection-resource-id $registryID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Obtenha endereços IP privados

Executar [az network private-endpoint show][az-network-private-endpoint-show] para consultar o ponto final para o ID de interface de rede:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Associados à interface de rede estão dois endereços IP privados para o registro de contêineres: um para o próprio registro e outro para o ponto final de dados do registro. Execute os seguintes comandos [az resource show][az-resource-show] para obter os endereços IP privados para o registro de contêiner e o ponto final de dados do registro:

```azurecli
privateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

dataEndpointPrivateIP=$(az resource show \
  --ids $networkInterfaceID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

### <a name="create-dns-records-in-the-private-zone"></a>Criar registros DeDNS na zona privada

Os seguintes comandos criam registros DNS na região privada para o ponto final do registro e seu ponto final de dados. Por exemplo, se você tiver um registro chamado *myregistry* na `myregistry.azurecr.io` região `myregistry.westeurope.data.azurecr.io`da *Europa Ocidental,* os nomes do ponto final são e . 

Primeira execução [az rede private-dns conjunto de registro de criação][az-network-private-dns-record-set-a-create] para criar um conjunto de registros vazio para o ponto final do registro e ponto final de dados:

```azurecli
az network private-dns record-set a create \
  --name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup
```

Execute a [rede az private-dns gravar um][az-network-private-dns-record-set-a-add-record] comando de registro para criar os registros A para o ponto final do registro e ponto final de dados:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $registryName \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $privateIP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${registryName}.${registryLocation}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $resourceGroup \
  --ipv4-address $dataEndpointPrivateIP
```

O link privado está agora configurado e pronto para uso.

## <a name="set-up-private-link---portal"></a>Configurar link privado - portal

As etapas a seguir supõem que você já tem uma rede virtual e uma sub-rede configuradas com uma VM para testes. Você também pode [criar uma nova rede virtual e sub-rede](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

1. No portal, navegue até o seu registro de contêineres.
1. Em **Configurações,** selecione **Conexões de ponto final privado (Visualização)**.
1. Selecione **+ Ponto final privado**.
1. Na guia Noções Básicas, digite ou selecione as **seguintes** informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Digite o nome de um grupo existente ou crie um novo.|
    | **Detalhes de instância** |  |
    | Nome | Insira um nome exclusivo. |
    |Região|Selecione uma região.|
    |||
5. Selecione **A seguir: Recurso**.
6. Digite ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione **Conectar a um recurso do Azure no meu diretório**.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft.ContainerRegistry/registros**. |
    | Recurso |Selecione o nome do seu registro|
    |Subrecursos de destino |Selecionar **registro**|
    |||
7. Selecione **A seguir: Configuração**.
8. Digite ou selecione as informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione a rede virtual onde sua máquina virtual está implantada, como *myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *o myDockerVMSubnet,* onde sua máquina virtual está implantada. |
    |**Integração Privada de DNS**||
    |Integrar com a zona DNS privado |Selecione **Sim**. |
    |Zona DNS privado |Selecione *(Novo) privatelink.azurecr.io* |
    |||

1. Selecione **Revisão + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
2. Quando você vir a **mensagem aprovada pela Validação,** selecione **Criar**.

Depois que o ponto final privado é criado, as configurações de DNS na região privada aparecem na página **Visão Geral** do ponto final.

![Configurações de DNS de ponto final](./media/container-registry-private-link/private-endpoint-overview.png)

Seu link privado está agora configurado e pronto para uso.

## <a name="validate-private-link-connection"></a>Validar a conexão de link privado

Você deve validar que os recursos dentro da sub-rede do ponto final privado se conectam ao seu registro em um endereço IP privado e têm a integração de região de DNS privada correta.

Para validar a conexão de link privado, SSH para a máquina virtual configurada na rede virtual.

Execute `nslookup` o comando para resolver o endereço IP do seu registro no link privado:

```bash
nslookup $registryName.azurecr.io
```

A saída de exemplo mostra o endereço IP do registro no espaço de endereço da sub-rede:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Compare este resultado com o `nslookup` endereço IP público na saída para o mesmo registro em um ponto final público:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operações de registro por link privado

Verifique também se você pode realizar operações de registro a partir da máquina virtual na sub-rede. Faça uma conexão SSH com sua máquina virtual e execute [o login az acr][az-acr-login] para fazer login no seu registro. Dependendo da configuração da VM, talvez seja `sudo`necessário prefixar os seguintes comandos com .

```bash
az acr login --name $registryName
```

Realizar operações `docker pull` de registro, como retirar uma imagem de amostra do registro. Substitua por `hello-world:v1` uma imagem e tag apropriada satisfaz seu registro, prefixada com o nome do servidor de login do registro (todas as minúsculas):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker puxa com sucesso a imagem para a VM.

## <a name="manage-private-endpoint-connections"></a>Gerenciar conexões de ponto final privado

Gerenciar as conexões de ponto final privado de um registro usando o portal Azure ou usando comandos no grupo de comandos [az acr private-endpoint-connection.][az-acr-private-endpoint-connection] As operações incluem aprovar, excluir, listar, rejeitar ou mostrar detalhes das conexões de ponto final privado de um registro.

Por exemplo, para listar as conexões de ponto final privado de um registro, execute o comando [az acr private-endpoint-connection list.][az-acr-private-endpoint-connection-list] Por exemplo: 

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Quando você configura uma conexão de ponto final privado usando as etapas deste artigo, o registro aceita automaticamente conexões de clientes e serviços que possuem permissões RBAC no registro. Você pode configurar o ponto final para exigir aprovação manual das conexões. Para obter informações sobre como aprovar e rejeitar conexões de ponto final privados, consulte [Gerenciar uma conexão de ponto final privado](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Limpar recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, você pode excluir opcionalmente os recursos usando um único comando [de exclusão de grupo az:](/cli/azure/group)

```azurecli
az group delete --name $resourceGroup
```

Para limpar seus recursos no portal, navegue até o seu grupo de recursos. Uma vez que o grupo de recursos esteja carregado, clique em **Excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados lá.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Private Link, consulte a documentação do [Azure Private Link.](../private-link/private-link-overview.md)
* Uma alternativa ao link privado é configurar regras de acesso à rede para restringir o acesso ao registro. Para saber mais, consulte [Restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall](container-registry-vnet.md).

<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms
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
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
