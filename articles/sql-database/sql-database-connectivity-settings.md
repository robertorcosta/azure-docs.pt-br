---
title: Configurações de conectividade para o banco de dados SQL do Azure e data warehouse
description: Este documento explica a opção de versão do TLS e o proxy versus a configuração de redirecionamento para SQL do Azure
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: cd239106bfd3ac785cffbf1365f298da565179ec
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/14/2020
ms.locfileid: "79366080"
---
# <a name="azure-sql-connectivity-settings"></a>Configurações de conectividade do SQL do Azure
> [!NOTE]
> Os recursos estão disponíveis apenas no **oeste dos EUA 2, leste dos EUA, centro-sul dos EUA** com outras regiões em breve para seguir

> [!NOTE]
> Este artigo se aplica ao SQL Server do Azure e aos bancos de dados SQL Database e SQL Data Warehouse criados no servidor SQL do Azure. Para simplificar, o banco de dados SQL é usado quando se refere ao Banco de Dados SQL e ao SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo não *se aplica* a **instância gerenciada do banco de dados SQL do Azure**

Este artigo apresenta as configurações que controlam a conectividade com o banco de dados SQL do Azure no nível do servidor. Essas configurações se aplicam a **todos os** bancos de dados SQL e SQL data warehouse associados ao servidor.

> [!NOTE]
> Depois que essas configurações são aplicadas, elas **entram em vigor imediatamente** e podem resultar em perda de conexão para seus clientes se eles não atenderem aos requisitos de cada configuração.

As configurações de conectividade podem ser acessadas na folha **firewalls e redes virtuais** , conforme mostrado na captura de tela abaixo:

 ![Captura de tela das configurações de conectividade][1]


## <a name="deny-public-network-access"></a>Negar acesso à rede pública
No portal do Azure, quando a configuração **negar acesso à rede pública** é definida como **Sim**, somente as conexões por meio de pontos de extremidade privados são permitidas. Quando essa configuração é definida como **não**, os clientes podem se conectar usando o ponto de extremidade público ou privado.

Depois que a configuração **negar acesso à rede pública** para **Sim**, as tentativas de logon de clientes que usam o ponto de extremidade público falharão com o seguinte erro:

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. The public network interface on this server is not accessible. To connect to this server, use the Private Endpoint from inside your virtual network.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar o acesso à rede pública por meio do PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como `Get` e `Set` a propriedade de **acesso à rede pública** no nível do servidor lógico:

```powershell
#Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Enabled" 
```

## <a name="change-public-network-access-via-cli"></a>Alterar o acesso à rede pública via CLI
> [!IMPORTANT]
> Todos os scripts nesta seção exigem [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash
O script da CLI a seguir mostra como alterar o **acesso à rede pública** em um shell bash:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"

```


## <a name="connection-policy"></a>Política de Conexão
A [política de conexão](sql-database-connectivity-architecture.md#connection-policy) determina como os clientes se conectam ao Azure SQL Server. 

## <a name="change-connection-policy-via-powershell"></a>Alterar a política de conexão por meio do PowerShell
[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do banco de dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo AZ. Sql. Para esses cmdlets, consulte [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo AZ e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

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

## <a name="change-connection-policy-via-azure-cli"></a>Alterar a política de conexão via CLI do Azure
> [!IMPORTANT]
> Todos os scripts nesta seção exigem [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash
O script da CLI a seguir mostra como alterar a política de conexão em um shell bash: 

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

### <a name="azure-cli-from-a-windows-command-prompt"></a>CLI do Azure de um prompt de comando do Windows
O script da CLI a seguir mostra como alterar a política de conexão de um prompt de comando do Windows (com CLI do Azure instalado).

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Próximas etapas
- Para obter uma visão geral de como funciona a conectividade no banco de dados SQL do Azure, consulte [arquitetura de conectividade do SQL do Azure](sql-database-connectivity-architecture.md)
- Para obter informações sobre como alterar a política de conexão de Banco de Dados SQL do Azure para um servidor do Banco de Dados SQL do Azure, consulte [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: ./media/sql-database-get-started-portal/manage-connectivity-settings.png
