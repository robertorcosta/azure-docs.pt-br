---
title: Private Link - Azure CLI - Banco de dados Azure para PostgreSQL - Servidor único
description: Saiba como configurar link privado para banco de dados Azure para postgreSQL- Único servidor do Azure CLI
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/09/2020
ms.openlocfilehash: d4288b901a0e6e132e32f8391d108e79861fc331
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371028"
---
# <a name="create-and-manage-private-link-for-azure-database-for-postgresql---single-server-using-cli"></a>Criar e gerenciar o Private Link for Azure Database para PostgreSQL - Servidor único usando CLI

Um ponto de extremidade privado é o bloco de construção fundamental para o link privado no Azure. Ele permite que os recursos do Azure, como VMs (máquinas virtuais), se comuniquem de forma privada com recursos de link privado. Neste artigo, você aprenderá a usar o Azure CLI para criar uma VM em uma Rede Virtual Azure e um Banco de Dados Azure para servidor PostgreSQL Single com um ponto final privado do Azure.

> [!NOTE]
> Esse recurso está disponível em todas as regiões do Azure onde o Banco de Dados Azure para PostgreSQL - Servidor único suporta níveis de preços otimizados para propósito geral e memória otimizada.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este guia de instruções, você precisa:

- Um [banco de dados e servidor do Banco de Dados do Azure para PostgreSQL](quickstart-create-server-database-azure-cli.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI do Azure localmente, este guia de início rápido exigirá a versão 2.0.28 ou posterior da CLI do Azure. Execute `az --version` para localizar a versão instalada. Para informações sobre como instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Antes de criar qualquer recurso, você deve criar um grupo de recursos para hospedar a Rede Virtual. Crie um grupo de recursos com [az group create](/cli/azure/group). Este exemplo cria um grupo de recursos chamado *myResourceGroup* na localização da *Europa Ocidental:*

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie uma Rede Virtual com a rede [az vnet criar](/cli/azure/network/vnet). O exemplo cria uma Rede Virtual padrão nomeada *myVirtualNetwork* com uma sub-rede nomeada *mySubnet*:

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
## <a name="create-the-vm"></a>Criar a VM 
Crie uma VM com az vm create. Quando solicitado, forneça uma senha a ser usada como credencial de entrada para a VM. Este exemplo cria uma VM chamada *myVm*: 
```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image Win2019Datacenter
```
 Anote o Endereço IP Público da VM. Você usará esse endereço para conectar-se à VM pela Internet na próxima etapa.

## <a name="create-an-azure-database-for-postgresql---single-server"></a>Criar um banco de dados Azure para PostgreSQL - Servidor único 
Crie um banco de dados Azure para PostgreSQL com o comando az postgres server create. Lembre-se de que o nome do seu PostgreSQL Server deve ser único em todo o Azure, então substitua o valor do espaço reservado entre parênteses pelo seu próprio valor único: 

```azurecli-interactive
# Create a logical server in the resource group 
az postgres server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westeurope \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2
```

Observe que o ID do servidor ```/subscriptions/subscriptionId/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/servername.``` PostgreSQL é semelhante ao Você usará o PostgreSQL Server ID na próxima etapa. 

## <a name="create-the-private-endpoint"></a>Criar um Ponto de Extremidade Privado 
Crie um ponto final privado para o servidor PostgreSQL em sua Rede Virtual: 
```azurecli-interactive
az network private-endpoint create \  
    --name myPrivateEndpoint \  
    --resource-group myResourceGroup \  
    --vnet-name myVirtualNetwork  \  
    --subnet mySubnet \  
    --private-connection-resource-id "<PostgreSQL Server ID>" \  
    --group-ids postgresqlServer \  
    --connection-name myConnection  
 ```

## <a name="configure-the-private-dns-zone"></a>Configurar a Zona DNS Privada 
Crie uma zona de DNS privada para o domínio do servidor PostgreSQL e crie um link de associação com a Rede Virtual. 
```azurecli-interactive
az network private-dns zone create --resource-group myResourceGroup \ 
   --name  "privatelink.postgres.database.azure.com" 
az network private-dns link vnet create --resource-group myResourceGroup \ 
   --zone-name  "privatelink.postgres.database.azure.com"\ 
   --name MyDNSLink \ 
   --virtual-network myVirtualNetwork \ 
   --registration-enabled false 

#Query for the network interface ID  
networkInterfaceId=$(az network private-endpoint show --name myPrivateEndpoint --resource-group myResourceGroup --query 'networkInterfaces[0].id' -o tsv)
 
 
az resource show --ids $networkInterfaceId --api-version 2019-04-01 -o json 
# Copy the content for privateIPAddress and FQDN matching the Azure database for PostgreSQL name 
 
 
#Create DNS records 
az network private-dns record-set a create --name myserver --zone-name privatelink.postgres.database.azure.com --resource-group myResourceGroup  
az network private-dns record-set a add-record --record-set-name myserver --zone-name privatelink.postgres.database.windows.net --resource-group myResourceGroup -a <Private IP Address>
```

> [!NOTE] 
> O FQDN na configuração de DNS do cliente não resolve o IP privado configurado. Você terá que configurar uma região DNS para o FQDN configurado como mostrado [aqui](../dns/dns-operations-recordsets-portal.md).

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Conecte-se à VM *myVm* da Internet da seguinte forma:

1. Na barra de pesquisa do portal, insira *myVm*.

1. Selecione o botão **Conectar**. Depois de selecionar o botão **Conectar**, **Conectar-se à máquina virtual** abre.

1. Selecione **Baixar arquivo RDP**. O Azure cria um arquivo *.rdp* (protocolo RDP) e ele é baixado no computador.

1. Abra o arquivo *downloaded.rdp*.

    1. Se solicitado, selecione **Conectar**.

    1. Insira o nome de usuário e a senha que você especificou ao criar a VM.

        > [!NOTE]
        > Você pode precisar selecionar **Mais opções** > **Use uma conta diferente,** para especificar as credenciais inseridas quando criou a VM.

1. Selecione **OK**.

1. Você pode receber um aviso do certificado durante o processo de logon. Se você receber um aviso de certificado, selecione **Sim** ou **Continuar**.

1. Depois que a área de trabalho da VM for exibida, minimize-a para voltar para sua área de trabalho local.  

## <a name="access-the-postgresql-server-privately-from-the-vm"></a>Acesse o servidor PostgreSQL privadamente a partir da VM

1. Na Área de Trabalho Remota de  *myVM*, abra o PowerShell.

2. Digite  `nslookup mydemopostgresserver.privatelink.postgres.database.azure.com`. 

    Você receberá uma mensagem semelhante a esta:
    ```azurepowershell
    Server:  UnKnown
    Address:  168.63.129.16
    Non-authoritative answer:
    Name:    mydemopostgresserver.privatelink.postgres.database.azure.com
    Address:  10.1.3.4
    ```

3. Teste a conexão de link privado para o servidor PostgreSQL usando qualquer cliente disponível. No exemplo abaixo eu usei [o estúdio Azure Data](https://docs.microsoft.com/sql/azure-data-studio/download?view=sql-server-ver15) para fazer a operação.

4. Em **Nova conexão,** digite ou selecione essas informações:

    | Configuração | Valor |
    | ------- | ----- |
    | Tipo de servidor| Selecione **PostgreSQL**.|
    | Nome do servidor| Selecione *mydemopostgresserver.privatelink.postgres.database.azure.com* |
    | Nome de usuário | Digite o username@servername nome de usuário como é fornecido durante a criação do servidor PostgreSQL. |
    |Senha |Digite uma senha fornecida durante a criação do servidor PostgreSQL. |
    |SSL|Selecione **Obrigatório**.|
    ||

5. Selecione Conectar.

6. Procurar bancos de dados no menu à esquerda.

7. (Opcionalmente) Criar ou consultar informações do servidor postgreSQL.

8. Feche a conexão remota da área de trabalho para myVm.

## <a name="clean-up-resources"></a>Limpar recursos 
Quando não for mais necessário, você poderá usar az group delete para remover o grupo de recursos e todos os recursos que ele contém: 

```azurecli-interactive
az group delete --name myResourceGroup --yes 
```

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [o que é o ponto final privado do Azure](https://docs.microsoft.com/azure/private-link/private-endpoint-overview)
