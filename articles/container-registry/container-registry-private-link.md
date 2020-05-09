---
title: Configurar link privado
description: Configurar um ponto de extremidade privado em um registro de contêiner e habilitar o acesso em um link privado em uma rede virtual local
ms.topic: article
ms.date: 05/07/2020
ms.openlocfilehash: 46ec816d85a528fd3208026ef76dff8470154767
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2020
ms.locfileid: "82982407"
---
# <a name="configure-azure-private-link-for-an-azure-container-registry"></a>Configurar o link privado do Azure para um registro de contêiner do Azure 

Limite o acesso a um registro atribuindo endereços IP privados da rede virtual aos pontos de extremidade do registro usando o [link privado do Azure](../private-link/private-link-overview.md). O tráfego de rede entre os clientes na rede virtual e o registro atravessa a rede virtual e um link privado na rede de backbone da Microsoft, eliminando a exposição da Internet pública.

Você pode [definir as configurações de DNS](../private-link/private-endpoint-overview.md#dns-configuration) para seu ponto de extremidade particular, para que as configurações sejam resolvidas para o endereço IP privado alocado do registro. Com a configuração de DNS, os clientes e serviços na rede podem continuar acessando o registro no nome de domínio totalmente qualificado do registro, como *myregistry.azurecr.Io*.

Esse recurso está disponível na camada de serviço do registro de contêiner **Premium** . Para obter informações sobre limites e camadas de serviço do registro, consulte [camadas do registro de contêiner do Azure](container-registry-skus.md).

## <a name="things-to-know"></a>Observações importantes

* Atualmente, a verificação de imagem usando a central de segurança do Azure não está disponível em um registro configurado com um ponto de extremidade privado.

## <a name="prerequisites"></a>Pré-requisitos

* Para usar as etapas de CLI do Azure neste artigo, é recomendável CLI do Azure versão 2.2.0 ou posterior. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli]. Ou execute em [Azure cloud Shell](../cloud-shell/quickstart.md).
* Se você ainda não tiver um registro de contêiner, crie um (camada Premium necessária) e [importe](container-registry-import-images.md) uma imagem `hello-world` de exemplo, como do Hub do Docker. Por exemplo, use o [portal do Azure][quickstart-portal] ou o [CLI do Azure][quickstart-cli] para criar um registro.
* Para configurar o acesso ao registro usando um link privado em uma assinatura do Azure diferente, você precisa registrar o provedor de recursos para o registro de contêiner do Azure nessa assinatura. Por exemplo: 

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

Os exemplos de CLI do Azure neste artigo usam as seguintes variáveis de ambiente. Substitua os valores apropriados para seu ambiente. Todos os exemplos são formatados para o shell bash:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Configurar o link privado-CLI

### <a name="get-network-and-subnet-names"></a>Obter nomes de rede e sub-rede

Se você ainda não os tiver, precisará dos nomes de uma rede virtual e de uma sub-rede para configurar um link privado. Neste exemplo, você usa a mesma sub-rede para a VM e o ponto de extremidade privado do registro. No entanto, em muitos cenários, você configuraria o ponto de extremidade em uma sub-rede separada. 

Quando você cria uma VM, o Azure por padrão cria uma rede virtual no mesmo grupo de recursos. O nome da rede virtual é baseado no nome da máquina virtual. Por exemplo, se você nomear sua máquina virtual *myDockerVM*, o nome de rede virtual padrão será *myDockerVMVNET*, com uma sub-rede chamada *myDockerVMSubnet*. Defina esses valores em variáveis de ambiente executando o comando [AZ Network vnet List][az-network-vnet-list] :

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Desabilitar políticas de rede na sub-rede

[Desabilite as políticas de rede](../private-link/disable-private-endpoint-network-policy.md) , como grupos de segurança de rede na sub-rede para o ponto de extremidade privado. Atualize sua configuração de sub-rede com [AZ Network vnet subnet Update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Configurar a zona DNS privada

Crie uma zona DNS privada para o domínio de registro de contêiner do Azure privado. Em etapas posteriores, você cria registros DNS para seu domínio de registro nesta zona DNS.

Para usar uma zona privada para substituir a resolução DNS padrão para o registro de contêiner do Azure, a zona deve ser nomeada **privatelink.azurecr.Io**. Execute o seguinte comando [AZ Network Private-DNS Zone Create][az-network-private-dns-zone-create] para criar a zona privada:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Criar um link de associação

Execute [AZ Network Private-DNS link vnet Create][az-network-private-dns-link-vnet-create] para associar sua zona privada à rede virtual. Este exemplo cria um link chamado *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Criar um ponto de extremidade de registro privado

Nesta seção, crie o ponto de extremidade privado do registro na rede virtual. Primeiro, obtenha a ID de recurso do registro:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Execute o comando [AZ Network Private-Endpoint Create][az-network-private-endpoint-create] para criar o ponto de extremidade particular do registro.

O exemplo a seguir cria o ponto de extremidade *myPrivateEndpoint* e a conexão de serviço *MyConnection*. Para especificar um recurso de registro de contêiner para o ponto `--group-ids registry`de extremidade, passe:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-private-ip-addresses"></a>Obter endereços IP privados

Execute [AZ Network Private-Endpoint show][az-network-private-endpoint-show] para consultar o ponto de extremidade para a ID de interface de rede:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

O associado ao adaptador de rede neste exemplo são dois endereços IP privados para o registro de contêiner: um para o próprio registro e um para o ponto de extremidade de dados do registro. Os comandos [AZ Resource show][az-resource-show] a seguir obtêm os endereços IP privados para o registro de contêiner e o ponto de extremidade de dados do registro:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Se o registro for [replicado geograficamente](container-registry-geo-replication.md), consulte o ponto de extremidade de dados adicional para cada réplica de registro.

### <a name="create-dns-records-in-the-private-zone"></a>Criar registros DNS na zona privada

Os comandos a seguir criam registros DNS na zona privada para o ponto de extremidade do registro e seu ponto de extremidade de dados. Por exemplo, se você tiver um registro chamado *myregistry* na região *westeurope* , os nomes dos pontos de `myregistry.azurecr.io` extremidade `myregistry.westeurope.data.azurecr.io`serão e. 

> [!NOTE]
> Se o registro for [replicado geograficamente](container-registry-geo-replication.md), crie registros DNS adicionais para o IP do ponto de extremidade de dados de cada réplica.

Primeiro, execute [AZ Network Private-DNS Record-defina um CREATE][az-network-private-dns-record-set-a-create] para criar um conjunto de registros vazio para o ponto de extremidade do registro e o ponto de extremidade de dados:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Execute o comando [AZ Network Private-DNS Record-set a Add-Record][az-network-private-dns-record-set-a-add-record] para criar os registros a para o ponto de extremidade do registro e o ponto de extremidade de dados:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

O link privado agora está configurado e pronto para uso.

## <a name="set-up-private-link---portal"></a>Configurar o link privado-Portal

Configure um link privado ao criar um registro ou adicione um link privado a um registro existente. As etapas a seguir pressupõem que você já tenha uma rede virtual e uma sub-rede configuradas com uma VM para teste. Você também pode [criar uma nova rede virtual e sub-rede](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Criar um ponto de extremidade privado-novo registro

1. Ao criar um registro no portal, na guia **noções básicas** , em **SKU**, selecione **Premium**.
1. Selecione a guia **rede** .
1. Em **conectividade de rede**, selecione **ponto de extremidade** > privado **+ Adicionar**.
1. Insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Insira o nome de um grupo existente ou crie um novo.|
    | Nome | Insira um nome exclusivo. |
    | Sub-recurso |Selecionar **registro**|
    | **Rede** | |
    | Rede virtual| Selecione a rede virtual na qual sua máquina virtual está implantada, como *myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *myDockerVMSubnet* , em que sua máquina virtual é implantada. |
    |**Integração do DNS privado**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecionar *(novo) privatelink.azurecr.Io* |
    |||
1. Defina as configurações restantes do registro e, em seguida, selecione **revisar + criar**.

  ![Criar registro com ponto de extremidade privado](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Criar um ponto de extremidade privado-registro existente

1. No portal, navegue até o registro de contêiner.
1. Em **configurações**, selecione **rede**.
1. Na guia **pontos** de extremidade particulares, selecione **+ ponto de extremidades privado**.
1. Na guia **noções básicas** , insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    | **Detalhes do projeto** | |
    | Subscription | Selecione sua assinatura. |
    | Resource group | Insira o nome de um grupo existente ou crie um novo.|
    | **Detalhes da instância** |  |
    | Nome | Insira um nome. |
    |Região|Selecione uma região.|
    |||
5. Selecione **Avançar: Recurso**.
6. Insira ou selecione as seguintes informações:

    | Configuração | Valor |
    | ------- | ----- |
    |Método de conexão  | Selecione **conectar a um recurso do Azure em meu diretório**.|
    | Subscription| Selecione sua assinatura. |
    | Tipo de recurso | Selecione **Microsoft. ContainerRegistry/registros**. |
    | Recurso |Selecione o nome do registro|
    |Subrecurso de destino |Selecionar **registro**|
    |||
7. Selecione **Avançar: configuração**.
8. Insira ou selecione as informações:

    | Configuração | Valor |
    | ------- | ----- |
    |**Rede**| |
    | Rede virtual| Selecione a rede virtual na qual sua máquina virtual está implantada, como *myDockerVMVNET*. |
    | Sub-rede | Selecione uma sub-rede, como *myDockerVMSubnet* , em que sua máquina virtual é implantada. |
    |**Integração do DNS privado**||
    |Integrar com a zona DNS privado |Selecione **Sim** na barra superior. |
    |Zona DNS privado |Selecionar *(novo) privatelink.azurecr.Io* |
    |||

1. Selecione **Examinar + criar**. Você é levado até a página **Examinar + criar**, na qual o Azure valida sua configuração. 
2. Quando vir a mensagem **Validação aprovada**, selecione **Criar**.

Depois que o ponto de extremidade privado for criado, as configurações de DNS na zona privada aparecerão na página pontos de extremidade **particulares** no Portal:

1. No portal, navegue até o registro de contêiner e selecione **configurações > rede**.
1. Na guia **pontos** de extremidade particulares, selecione o ponto de extremidades privado que você criou.
1. Na página **visão geral** , examine as configurações de link e as configurações de DNS personalizadas.

  ![Configurações de DNS do ponto de extremidade](./media/container-registry-private-link/private-endpoint-overview.png)

Seu link privado agora está configurado e pronto para uso.

## <a name="disable-public-access"></a>Desabilitar acesso público

Para muitos cenários, desabilite o acesso ao registro de redes públicas. Essa configuração impede que clientes fora da rede virtual atinjam os pontos de extremidade do registro. Para desabilitar o acesso público usando o portal:

1. No portal, navegue até o registro de contêiner e selecione **configurações > rede**.
1. Na guia **acesso público** , em **permitir acesso público**, selecione **desabilitado**. Em seguida, selecione **Salvar**.

## <a name="validate-private-link-connection"></a>Validar conexão de link privado

Você deve validar se os recursos dentro da sub-rede do ponto de extremidade privado se conectam ao registro por meio de um endereço IP privado e têm a integração de zona DNS privada correta.

Para validar a conexão de link privado, use o SSH para a máquina virtual que você configurou na rede virtual.

Execute o `nslookup` comando para resolver o endereço IP do registro por meio do link privado:

```bash
nslookup $REGISTRY_NAME.azurecr.io
```

Exemplo de saída mostra o endereço IP do registro no espaço de endereço da sub-rede:

```console
[...]
myregistry.azurecr.io       canonical name = myregistry.privatelink.azurecr.io.
Name:   myregistry.privatelink.azurecr.io
Address: 10.0.0.6
```

Compare esse resultado com o endereço IP público na `nslookup` saída para o mesmo registro em um ponto de extremidade público:

```console
[...]
Non-authoritative answer:
Name:   myregistry.westeurope.cloudapp.azure.com
Address: 40.78.103.41
```

### <a name="registry-operations-over-private-link"></a>Operações de registro sobre o link privado

Verifique também se você pode executar operações de registro da máquina virtual na sub-rede. Faça uma conexão SSH com sua máquina virtual e execute [AZ ACR login][az-acr-login] para fazer logon no registro. Dependendo da configuração da VM, talvez seja necessário prefixar os comandos a seguir `sudo`com.

```bash
az acr login --name $REGISTRY_NAME
```

Execute operações de registro, `docker pull` como para efetuar pull de uma imagem de exemplo do registro. Substitua `hello-world:v1` por uma imagem e uma marca apropriada para o registro, prefixado com o nome do servidor de logon do registro (todas as letras minúsculas):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

O Docker efetua pull da imagem com êxito para a VM.

## <a name="manage-private-endpoint-connections"></a>Gerenciar conexões de ponto de extremidade privado

Gerencie as conexões de ponto de extremidade privado do registro usando o portal do Azure ou usando comandos no grupo de comandos [AZ ACR Private-Endpoint-Connection][az-acr-private-endpoint-connection] . As operações incluem aprovar, excluir, listar, rejeitar ou mostrar detalhes de conexões de ponto de extremidade privado do registro.

Por exemplo, para listar as conexões de ponto de extremidade privado de um registro, execute o comando [AZ ACR Private-Endpoint-Connection List][az-acr-private-endpoint-connection-list] . Por exemplo: 

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Quando você configura uma conexão de ponto de extremidade privado usando as etapas neste artigo, o registro aceita automaticamente conexões de clientes e serviços que têm permissões de RBAC no registro. Você pode configurar o ponto de extremidade para exigir aprovação manual de conexões. Para obter informações sobre como aprovar e rejeitar conexões de ponto de extremidade particulares, consulte [gerenciar uma conexão de ponto de extremidade privado](../private-link/manage-private-endpoint.md).

## <a name="add-zone-records-for-replicas"></a>Adicionar registros de zona para réplicas

Conforme mostrado neste artigo, quando você adiciona uma conexão de ponto de extremidade privada a um registro, os registros `privatelink.azurecr.io` DNS na zona são criados para o registro e seus pontos de extremidade de dados nas regiões em que o registro é [replicado](container-registry-geo-replication.md). 

Se posteriormente você adicionar uma nova réplica, precisará adicionar manualmente um novo registro de zona para o ponto de extremidade de dados nessa região. Por exemplo, se você criar uma réplica de *myregistry* no local *northeurope* , adicione um registro de zona para `myregistry.northeurope.data.azurecr.io`. Para obter as etapas, consulte [criar registros DNS na zona privada](#create-dns-records-in-the-private-zone) neste artigo.

## <a name="clean-up-resources"></a>Limpar os recursos

Se você criou todos os recursos do Azure no mesmo grupo de recursos e não precisa mais deles, você pode, opcionalmente, excluir os recursos usando um único comando [AZ Group Delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

Para limpar seus recursos no portal, navegue até o grupo de recursos. Depois que o grupo de recursos for carregado, clique em **excluir grupo de recursos** para remover o grupo de recursos e os recursos armazenados nele.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o link privado, consulte a documentação do [link privado do Azure](../private-link/private-link-overview.md) .
* Se você precisar configurar as regras de acesso do registro por trás de um firewall do cliente, consulte [configurar regras para acessar um registro de contêiner do Azure atrás de um firewall](container-registry-firewall-access-rules.md).

<!-- LINKS - external -->
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
[az-acr-update]: /cli/azure/acr#az-acr-update
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
