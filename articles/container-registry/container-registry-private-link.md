---
title: Configurar link privado
description: Configurar um ponto de extremidade privado em um registro de contêiner e habilitar um link privado em uma rede virtual local
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: b7dcf2d1eb1a77ea8b9660318ed2a7d4ec183b42
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128391"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configurar o link privado do Azure para um registro de contêiner do Azure 

Você pode configurar um [ponto de extremidade privado](../private-link/private-endpoint-overview.md) para o registro de contêiner do Azure para que os clientes em uma rede virtual do Azure acessem com segurança o registro por meio de um [link privado](../private-link/private-link-overview.md). O ponto de extremidade privado usa um endereço IP do espaço de endereço de rede virtual para o registro. O tráfego de rede entre os clientes na rede virtual e o registro atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Você pode [definir as configurações de DNS](../private-link/private-endpoint-overview.md#dns-configuration) para seu ponto de extremidade particular, para que as configurações sejam resolvidas para o endereço IP privado alocado do registro. Com a configuração de DNS, os clientes e serviços na rede podem continuar acessando o registro no nome de domínio totalmente qualificado do registro, como *myregistry.azurecr.Io*.

Esse recurso está disponível na camada de serviço do registro de contêiner **Premium** . Para obter informações sobre limites e camadas de serviço do registro, consulte [SKUs do registro de contêiner do Azure](container-registry-skus.md).

> [!IMPORTANT]
> Este recurso está atualmente em visualização e algumas [limitações](#preview-limitations) se aplicam. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="preview-limitations"></a>Limitações de visualização

* No momento, você não pode configurar um link privado com um ponto de extremidade privado em um [registro com replicação geográfica](container-registry-geo-replication.md). 

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

* Para usar as etapas de CLI do Azure neste artigo, é recomendável CLI do Azure versão 2.2.0 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli]. Ou execute em [Azure cloud Shell](../cloud-shell/quickstart.md).
* Se você ainda não tiver um registro de contêiner, crie um (camada Premium necessária) e envie por push uma imagem de exemplo, como `hello-world` do Hub do Docker. Por exemplo, use o [portal do Azure][quickstart-portal] ou o [CLI do Azure][quickstart-cli] para criar um registro. 

Os exemplos de CLI do Azure neste artigo usam as seguintes variáveis de ambiente. Substitua os valores apropriados para seu ambiente. Todos os exemplos são formatados para o shell bash:

```bash
registryName=<container-registry-name>
registryLocation=<container-registry-location> # Azure region such as westeurope where registry created
resourceGroup=<resource-group-name>
vmName=<virtual-machine-name>
```

## <a name="create-a-docker-enabled-virtual-machine"></a>Criar uma máquina virtual habilitada para Docker

Para fins de teste, use uma VM Ubuntu habilitada para Docker para acessar um registro de contêiner do Azure. Para usar Azure Active Directory autenticação para o registro, instale também o [CLI do Azure][azure-cli] na VM. Se você já tiver uma máquina virtual do Azure, ignore esta etapa de criação.

Você pode usar o mesmo grupo de recursos para sua máquina virtual e o registro de contêiner. Essa configuração simplifica a limpeza no final, mas não é necessária. Se você optar por criar um grupo de recursos separado para a máquina virtual e a rede virtual, execute [AZ Group Create][az-group-create]:

```azurecli
az group create --name $resourceGroup --location $registryLocation
```

Agora, implante uma máquina virtual do Ubuntu do Azure padrão com [AZ VM Create][az-vm-create]. O exemplo a seguir cria uma VM chamada *myDockerVM*.

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

Execute os seguintes comandos para instalar o Docker na VM do Ubuntu:

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

## <a name="set-up-private-link---cli"></a>Configurar o link privado-CLI

### <a name="get-network-and-subnet-names"></a>Obter nomes de rede e sub-rede

Se você ainda não os tiver, precisará dos nomes de uma rede virtual e de uma sub-rede para configurar um link privado. Neste exemplo, você usa a mesma sub-rede para a VM e o ponto de extremidade privado do registro. No entanto, em muitos cenários, você configuraria o ponto de extremidade em uma sub-rede separada. 

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *myDockerVM*, o nome de rede virtual padrão será *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*. Defina esses valores em variáveis de ambiente executando o comando [AZ Network vnet List][az-network-vnet-list] :

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

### <a name="disable-network-policies-in-subnet"></a>Desabilitar políticas de rede na sub-rede

[Desabilite as políticas de rede](../private-link/disable-private-endpoint-network-policy.md) , como grupos de segurança de rede na sub-rede para o ponto de extremidade privado. Atualize sua configuração de sub-rede com [AZ Network vnet subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $subnetName \
 --vnet-name $networkName \
 --resource-group $resourceGroup \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurar a zona DNS privada

Crie uma zona DNS privada para o domínio de registro de contêiner do priviate Azure. Em etapas posteriores, você cria registros DNS para seu domínio de registro dentro dessa zona DNS.

Para usar uma zona privada para substituir a resolução DNS padrão para o registro de contêiner do Azure, a zona deve ser nomeada **privatelink.azurecr.Io**. Execute o seguinte comando [AZ Network Private-DNS Zone Create][az-network-private-dns-zone-create] para criar a zona privada:

```azurecli
az network private-dns zone create \
  --resource-group $resourceGroup \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Criar um link de associação

Execute [AZ Network Private-DNS link vnet Create][az-network-private-dns-link-vnet-create] para associar sua zona privada à rede virtual. Este exemplo cria um link chamado *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $resourceGroup \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $networkName \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Criar um ponto de extremidade de registro privado

Nesta seção, crie o ponto de extremidade privado do registro na rede virtual. Primeiro, obtenha a ID de recurso do registro:

```azurecli
registryID=$(az acr show --name $registryName \
  --query 'id' --output tsv)
```

Execute o comando [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] para criar o ponto de extremidade particular do registro.

O exemplo a seguir cria o ponto de extremidade *myPrivateEndpoint* e a conexão de serviço *MyConnection*. Para especificar um recurso de registro de contêiner para o ponto de extremidade, passe `--group-ids registry`:

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

### <a name="get-private-ip-addresses"></a>Obter endereços IP privados

Execute [AZ Network Private-Endpoint show][az-network-private-endpoint-show] para consultar o ponto de extremidade para a ID de interface de rede:

```azurecli
networkInterfaceID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $resourceGroup \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

O associado à interface de rede são dois endereços IP privados para o registro de contêiner: um para o próprio registro e um para o ponto de extremidade de dados do registro. Execute os seguintes comandos [AZ Resource show][az-resource-show] para obter os endereços IP privados para o registro de contêiner e o ponto de extremidade de dados do registro:

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

### <a name="create-dns-records-in-the-private-zone"></a>Criar registros DNS na zona privada

Os comandos a seguir criam registros DNS na zona privada para o ponto de extremidade do registro e seu ponto de extremidade de dados. Por exemplo, se você tiver um registro chamado *myregistry* na região *westeurope* , os nomes dos pontos de extremidade serão `myregistry.azurecr.io` e `myregistry.westeurope.data.azurecr.io`. 

Primeiro, execute [AZ Network Private-DNS Record-defina um CREATE][az-network-private-dns-record-set-a-create] para criar um conjunto de registros vazio para o ponto de extremidade do registro e o ponto de extremidade de dados:

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

Execute o comando [AZ Network Private-DNS Record-set a Add-Record][az-network-private-dns-record-set-a-add-record] para criar os registros a para o ponto de extremidade do registro e o ponto de extremidade de dados:

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

O link privado agora está configurado e pronto para uso.

## <a name="set-up-private-link---portal"></a>Configurar o link privado-Portal

As etapas a seguir pressupõem que você já tenha uma rede virtual e uma sub-rede configuradas com uma VM para teste. Você também pode [criar uma nova rede virtual e sub-rede](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint"></a>Criar um ponto de extremidade privado

1. No portal, navegue até o registro de contêiner.
1. Em **configurações**, selecione **conexões de ponto de extremidade privado (versão prévia)** .
1. Selecione **+ ponto de extremidade privado**.
1. Na guia **noções básicas** , insira ou selecione as seguintes informações:

    | Configuração | {1&gt;Valor&lt;1} |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Assinatura | Selecione sua assinatura. |
    | Grupo de recursos | Insira o nome de um grupo existente ou crie um novo.|
    | **Detalhes da instância** |  |
    | Nome | Insira um nome exclusivo. |
    |Região|Selecione uma região.|
    |||
5. Selecione **Avançar: recurso**.
6. Insira ou selecione as seguintes informações:

    | Configuração | {1&gt;Valor&lt;1} |
    | ------- | ----- |
    |Método de conexão  | Selecione **conectar a um recurso do Azure em meu diretório**.|
    | Assinatura| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft. ContainerRegistry/registros**. |
    | Recurso |Selecione o nome do registro|
    |Subrecurso de destino |Selecionar **registro**|
    |||
7. Selecione **Avançar: configuração**.
8. Insira ou selecione as informações:

    | Configuração | {1&gt;Valor&lt;1} |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione a rede virtual na qual sua máquina virtual está implantada, como *myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *myDockerVMSubnet* , em que sua máquina virtual é implantada. |
    |**Integração do DNS privado**||
    |Integrar com a zona DNS privado |Selecione **Sim**. |
    |Zona DNS privado |Selecionar *(novo) privatelink.azurecr.Io* |
    |||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
2. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

Depois que o ponto de extremidade privado for criado, as configurações de DNS na zona privada aparecerão na página **visão geral** do ponto de extremidade.

![Configurações de DNS do ponto de extremidade](./media/container-registry-private-link/private-endpoint-overview.png)

Seu link privado agora está configurado e pronto para uso.

## <a name="validate-private-link-connection"></a>Validar conexão de link privado

Você deve validar se os recursos dentro da sub-rede do ponto de extremidade privado se conectam ao registro por meio de um endereço IP privado e têm a integração de zona DNS privada correta.

Para validar a conexão de link privado, use o SSH para a máquina virtual que você configurou na rede virtual.

Execute o comando `nslookup` para resolver o endereço IP do registro por meio do link privado:

```bash
nslookup $registryName.azurecr.io
```

Exemplo de saída mostra o endereço IP do registro no espaço de endereço da sub-rede:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Compare esse resultado com o endereço IP público na saída `nslookup` para o mesmo registro em um ponto de extremidade público:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operações de registro sobre o link privado

Verifique também se você pode executar operações de registro da máquina virtual na sub-rede. Faça uma conexão SSH com sua máquina virtual e execute [AZ ACR login][az-acr-login] para fazer logon no registro. Dependendo da configuração da VM, talvez seja necessário prefixar os comandos a seguir com `sudo`.

```bash
az acr login --name $registryName
```

Execute operações de registro, como `docker pull` para extrair uma imagem de exemplo do registro. Substitua `hello-world:v1` por uma imagem e uma marca apropriada para o registro, prefixado com o nome do servidor de logon do registro (todas as letras minúsculas):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

O Docker efetua pull da imagem com êxito para a VM.

## <a name="manage-private-endpoint-connections"></a>Gerenciar conexões de ponto de extremidade privado

Gerencie as conexões de ponto de extremidade privado do registro usando o portal do Azure ou usando comandos no grupo de comandos [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . As operações incluem aprovar, excluir, listar, rejeitar ou mostrar detalhes de conexões de ponto de extremidade privado do registro.

Por exemplo, para listar as conexões de ponto de extremidade privado de um registro, execute o comando [AZ ACR Private-Endpoint-Connection List][az-acr-private-endpoint-connection-list] . Por exemplo:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $registryName 
```

Quando você configura uma conexão de ponto de extremidade privado usando as etapas neste artigo, o registro aceita automaticamente conexões de clientes e serviços que têm permissões de RBAC no registro. Você pode configurar o ponto de extremidade para exigir aprovação manual de conexões. Para obter informações sobre como aprovar e rejeitar conexões de ponto de extremidade particulares, consulte [gerenciar uma conexão de ponto de extremidade privado](../private-link/manage-private-endpoint.md).

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, você pode, opcionalmente, excluir os recursos usando um único comando [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $resourceGroup
```

Para limpar seus recursos no portal, navegue até o grupo de recursos. Depois que o grupo de recursos for carregado, clique em **excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados nele.

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* Para saber mais sobre o link privado, consulte a documentação do [link privado do Azure](../private-link/private-link-overview.md) .
* Uma alternativa ao link privado é configurar as regras de acesso à rede para restringir o acesso ao registro. Para saber mais, confira [restringir o acesso a um registro de contêiner do Azure usando uma rede virtual do Azure ou regras de firewall](container-registry-vnet.md).

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
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns-zone/create#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns-link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns-record/set/a#az-network-private-dns-record-set-a-add-record
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
