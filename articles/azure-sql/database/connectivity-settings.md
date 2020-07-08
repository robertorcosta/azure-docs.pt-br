---
title: Configurações de conectividade para o banco de dados SQL do Azure e data warehouse
description: Este documento explica a opção de versão do protocolo TLS e o proxy versus a configuração de redirecionamento para o banco de dados SQL do Azure e a análise de Synapse do Azure
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 04c5d9c8eceb14ab68ca0d96f994bf6a64bbc431
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045358"
---
# <a name="azure-sql-connectivity-settings"></a>Configurações de conectividade do SQL do Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Este artigo apresenta as configurações que controlam a conectividade com o servidor para o banco de dados SQL do Azure e a análise de Synapse do Azure. Essas configurações se aplicam a **todos os** bancos de dados SQL e do Azure Synapse associados ao servidor.

> [!IMPORTANT]
> Este artigo *não* se aplica ao **Azure SQL instância gerenciada**

As configurações de conectividade podem ser acessadas na tela **firewalls e redes virtuais** , conforme mostrado na seguinte captura:

 ![Captura de tela das configurações de conectividade][1]

> [!NOTE]
> Depois que essas configurações são aplicadas, elas **entram em vigor imediatamente** e podem resultar em perda de conexão para seus clientes se eles não atenderem aos requisitos de cada configuração.

## <a name="deny-public-network-access"></a>Negar acesso à rede pública

Quando a configuração **negar acesso à rede pública** é definida como **Sim**, somente as conexões por meio de pontos de extremidade privados são permitidas. Quando essa configuração é definida como **não** (padrão), os clientes podem se conectar usando pontos de extremidade públicos (regras de firewall baseadas em IP, regras de firewall baseadas em VNET) ou pontos de extremidade privados (usando o link privado), conforme descrito na [visão geral de acesso à rede](network-access-controls-overview.md). 

 ![Captura de tela de conectividade com acesso negado à rede pública][2]

Qualquer tentativa de definir a configuração **negar acesso à rede pública** como **Sim** sem um ponto de extremidade privado existente no servidor lógico falhará com uma mensagem de erro semelhante a:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server. 
Please set up private endpoints and retry the operation. 
```

Quando a configuração **negar acesso à rede pública** é definida como **Sim**, somente as conexões por meio de pontos de extremidade privados são permitidas e todas as conexões por meio de pontos de extremidade públicos são negadas com uma mensagem de erro semelhante a:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Quando **negar configuração de acesso à rede pública** estiver definido como **Sim**, qualquer tentativa de adicionar ou atualizar regras de firewall será negada com uma mensagem de erro semelhante a:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Alterar o acesso à rede pública por meio do PowerShell

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como `Get` e `Set` a propriedade de **acesso à rede pública** no nível do servidor:

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

## <a name="minimal-tls-version"></a>Versão mínima do TLS 

A configuração de versão mínima do [protocolo TLS](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) permite que os clientes controlem a versão do TLS usada pelo banco de dados SQL do Azure.

No momento, damos suporte a TLS 1,0, 1,1 e 1,2. A definição de uma versão mínima do TLS garante que as versões mais recentes do TLS sejam suportadas. Por exemplo, escolhendo uma versão de TLS maior que 1,1. significa que somente as conexões com TLS 1,1 e 1,2 são aceitas e o TLS 1,0 é rejeitado. Após o teste para confirmar que seus aplicativos dão suporte a ele, é recomendável definir a versão mínima do TLS como 1,2, já que ela inclui correções para vulnerabilidades encontradas em versões anteriores e é a versão mais recente do TLS com suporte no banco de dados SQL do Azure.

> [!IMPORTANT]
> O padrão para a versão mínima do TLS é permitir todas as versões. No entanto, depois de aplicar uma versão do TLS, não é possível reverter para o padrão.

Para clientes com aplicativos que dependem de versões mais antigas do TLS, é recomendável definir a versão mínima do TLS de acordo com os requisitos de seus aplicativos. Para clientes que dependem de aplicativos para se conectarem usando uma conexão não criptografada, recomendamos não definir nenhuma versão mínima de TLS.

Para obter mais informações, consulte [Considerações sobre TLS para conectividade de banco de dados SQL](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

Depois de definir a versão mínima do TLS, as tentativas de logon de clientes que estão usando uma versão TLS inferior à versão mínima do TLS do servidor falharão com o seguinte erro:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-minimal-tls-version-via-powershell"></a>Definir a versão mínima do TLS por meio do PowerShell

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

O script do PowerShell a seguir mostra como `Get` e `Set` a propriedade de **versão mínima do TLS** no nível do servidor lógico:

```powershell
#Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# # Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-minimal-tls-version-via-azure-cli"></a>Definir a versão mínima do TLS via CLI do Azure

> [!IMPORTANT]
> Todos os scripts nesta seção exigem [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI do Azure em um shell bash

O script de CLI a seguir mostra como alterar a configuração de **versão mínima do TLS** em um shell bash:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```


## <a name="change-connection-policy"></a>Alterar política de conexão

A [política de conexão](connectivity-architecture.md#connection-policy) determina como os clientes se conectam ao banco de dados SQL do Azure.


## <a name="change-connection-policy-via-powershell"></a>Alterar a política de conexão por meio do PowerShell

> [!IMPORTANT]
> O módulo Azure Resource Manager do PowerShell ainda tem suporte do Banco de Dados SQL do Azure, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, confira [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos. O script a seguir requer o [módulo Azure PowerShell](/powershell/azure/install-az-ps).

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

- Para obter uma visão geral de como funciona a conectividade no banco de dados SQL do Azure, consulte [arquitetura de conectividade](connectivity-architecture.md)
- Para obter informações sobre como alterar a política de conexão para um servidor, consulte [Conn-Policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
