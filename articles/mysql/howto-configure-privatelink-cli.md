---
title: Link privado para o método de instalação da CLI do banco de dados do Azure para MySQL (versão prévia)
description: Saiba como configurar o link privado para o banco de dados do Azure para MySQL do CLI do Azure
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: 59c38423f771685dc79a8be12a383cfdec6a0266
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77031519"
---
# <a name="create-and-manage-private-link-for-azure-database-for-mysql-preview-using-cli"></a>Criar e gerenciar um link privado para o banco de dados do Azure para MySQL (versão prévia) usando a CLI

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. Neste artigo, você aprenderá a usar o CLI do Azure para criar uma VM em uma rede virtual do Azure e um servidor de banco de dados do Azure para MySQL com um ponto de extremidade privado do Azure.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure em que o banco de dados do Azure para MySQL dá suporte a tipos de preço Uso Geral e com otimização de memória.

## <a name="prerequisites"></a>{1&gt;{2&gt;Pré-requisitos&lt;2}&lt;1}

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este guia de início rápido exigirá a versão 2.0.28 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar qualquer recurso, você deve criar um grupo de recursos para hospedar a Rede Virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *MyResource* Group no local *westeurope* :

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma Rede Virtual com [az network vnet create](/cli/azure/network/vnet). O exemplo cria uma Rede Virtual padrão nomeada *myVirtualNetwork* com uma sub-rede nomeada *mySubnet*:

```azurecli-interactive
az network vnet create \
 --name myVirtualNetwork \
 --resource-group myResourceGroup \
 --subnet-name mySubnet
```

## <a name="disable-subnet-private-endpoint-policies"></a>Desabilitar políticas de ponto de extremidade privado de sub-rede 
O Azure implanta recursos em uma sub-rede dentro de uma rede virtual. Portanto, você precisa criar ou atualizar a sub-rede para desativar as políticas de rede de ponto de extremidade privado. Atualize uma configuração de sub-rede denominada *mySubnet* com [az network vnet subnet update](https://docs.microsoft.com/cli/azure/network/vnet/subnet?view=azure-cli-latest#az-network-vnet-subnet-update):

```azurecli-interactive
az network vnet subnet update \
 --name mySubnet \
 --resource-group myResourceGroup \
 --vnet-name myVirtualNetwork \
 --disable-private-endpoint-network-policies true
```
## <a name="create-the-vm"></a>Criar a máquina virtual 
Crie uma VM com az vm create. Quando solicitado, forneça uma senha a ser usada como credencial de entrada para a VM. Este exemplo cria uma VM chamada *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
Anote o Endereço IP Público da VM. Você usará esse endereço para conectar-se à VM pela Internet na próxima etapa.

## <a name="create-an-azure-database-for-mysql-server"></a>Criar um Banco de Dados do Azure para o servidor MySQL 
Crie um banco de dados do Azure para MySQL com o comando AZ MySQL Server CREATE. Lembre-se de que o nome do seu servidor MySQL deve ser exclusivo no Azure, portanto, substitua o valor do espaço reservado entre colchetes com seu próprio valor exclusivo: 

```azurecli-interactive
# Create a logical server in the resource group 
az mysql server create \
--name mydemoserver \
--resource-group myResourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Observe que a ID do servidor MySQL é semelhante à ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforMySQL/servers/servername.``` você usará a ID do servidor MySQL na próxima etapa. 

## <a name="create-the-private-endpoint"></a>Criar um Ponto de Extremidade Privado 
Crie um ponto de extremidade privado para o servidor MySQL em sua rede virtual: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<MySQL Server ID>" \  
    --group-ids mysqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurar a Zona DNS Privada 
Crie uma zona de DNS privado para o domínio do servidor MySQL e crie um link de associação com a rede virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.mysql.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.mysql.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for MySQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.mysql.database.azure.com --resource-group myResourceGroup -a <Private IP Address>
```

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte forma:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar Arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Talvez seja necessário selecionar **Mais escolhas** > **Usar uma conta diferente** para especificar as credenciais inseridas durante a criação da VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  

## <a name="access-the-mysql-server-privately-from-the-vm"></a>Acessar o servidor MySQL de forma privada da VM

1. Na Área de Trabalho Remota de  *myVM*, abra o PowerShell.

2. Digite  `nslookup mydemomysqlserver.privatelink.mysql.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemomysqlserver.privatelink.mysql.database.azure.com
    Address:  10.1.3.4

3. Test the private link connection for the MySQL server using any available client. In the example below I have used [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/wb-installing-windows.html) to do the operation.


4. In **New connection**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | Connection Name| Select the connection name of your choice.|
    | Hostname | Select *mydemoserver.privatelink.mysql.database.azure.com* |
    | Username | Enter username as *username@servername* which is provided during the MySQL server creation. |
    | Password | Enter a password provided during the MySQL server creation. |
    ||

5. Select Connect.

6. Browse databases from left menu.

7. (Optionally) Create or query information from the MySQL database.

8. Close the remote desktop connection to myVm.

## Clean up resources 
When no longer needed, you can use az group delete to remove the resource group and all the resources it has: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}
- Saiba mais sobre [o que é o ponto de extremidade privado do Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
