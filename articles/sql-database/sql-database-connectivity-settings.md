---
title: Configurações de conectividade para banco de dados e data warehouse do Azure SQL
description: Este documento explica a escolha da versão TLS e a configuração Proxy vs. Redirecionar para o Azure SQL
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: d18fdee85bd0fbabe68fe9890c4a2dc74366041d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366080"
---
# <a name="azure-sql-connectivity-settings"></a>Configurações de conectividade SQL do Azure
> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo *não* se aplica à **instância gerenciada do banco de dados SQL do Azure**

Este artigo introduz configurações que controlam a conectividade ao Banco de Dados SQL do Azure no nível do servidor. Essas configurações se aplicam a **todos os** bancos de dados SQL e SQL Data Warehouse associados ao servidor.

> [!NOTE]
> Uma vez que essas configurações são aplicadas, elas entram em **vigor imediatamente** e podem resultar em perda de conexão para seus clientes se eles não atenderem aos requisitos de cada configuração.

As configurações de conectividade são acessíveis a partir da lâmina **firewalls e redes virtuais,** como mostrado na captura de tela abaixo:

 ![Captura de tela das configurações de conectividade][1]


## <a name="deny-public-network-access"></a>Negar acesso à rede pública
No portal Azure, quando a configuração **de acesso à rede pública Deny** é definida como **Sim**, somente conexões via pontos finais privados são permitidas. Quando esta configuração estiver definida como **Não,** os clientes podem se conectar usando o ponto final privado ou público.

Depois de **definir negar o acesso** à rede pública a **Yes**, as tentativas de login dos clientes que usam o ponto final público falharão com o seguinte erro:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar o acesso à rede pública via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell `Get` `Set` a seguir mostra como e a propriedade **Public Network Access** no nível lógico do servidor:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Alterar o acesso à rede pública via CLI
> [!IMPORTANT]
> Todos os scripts nesta seção exigem [a Cli do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de bash
O seguinte script da CLI mostra como alterar o Acesso à **Rede Pública** em uma concha de bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Política de Conexão
[A política de conexão](sql-database-connectivity-architecture.md#connection-policy) determina como os clientes se conectam ao Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Política de conexão de alterações via PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como alterar a política de conexão usando o PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id).Properties.connectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-connection-policy-via-azure-cli"></a>Política de conexão de alterações via Cli do Azure
> [!IMPORTANT]
> Todos os scripts nesta seção exigem [a Cli do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI em uma concha de bash
O seguinte script CLI mostra como alterar a política de conexão em uma concha de bash: 

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI a partir de um prompt de comando do Windows
O script CLI a seguir mostra como alterar a política de conexão a partir de um prompt de comando do Windows (com o Azure CLI instalado).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral de como a conectividade funciona no Banco de Dados SQL do Azure, consulte a [arquitetura de conectividade SQL do Azure](sql-database-connectivity-architecture.md)
- Para obter informações sobre como alterar a política de conexão de Banco de Dados SQL do Azure para um servidor do Banco de Dados SQL do Azure, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
