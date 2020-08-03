---
title: 'Tutorial: adicionar um banco de dados a um grupo de failover'
description: Adicione um banco de dados no banco de dados SQL do Azure a um grupo de failover de autotolerância usando o portal do Azure, o PowerShell ou o CLI do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 4caad36d21d3facb97dc358fdfee61e89c420213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496331"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>Tutorial: adicionar um banco de dados SQL do Azure a um grupo de failover
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Um [grupo de failover](auto-failover-group-overview.md) é uma camada de abstração declarativa que permite agrupar vários bancos de dados replicados geograficamente. Saiba como configurar um grupo de failover para um banco de dados SQL do Azure e failover de teste usando o portal do Azure, o PowerShell ou o CLI do Azure.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
>
> - Criar um banco de dados no Banco de Dados SQL do Azure
> - Crie um grupo de failover para o banco de dados entre dois servidores.
> - Failover de teste.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Para concluir este tutorial, verifique se você tem:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para concluir o tutorial, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Para concluir o tutorial, verifique se você tem os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- A versão mais recente do [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

---

## <a name="1---create-a-database"></a>1-criar um banco de dados

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-criar o grupo de failover

Nesta etapa, você criará um grupo de [failover](auto-failover-group-overview.md) entre um servidor existente e um novo servidor em outra região. Em seguida, adicione o banco de dados ao grupo de failover.

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Crie seu grupo de failover e adicione seu banco de dados a ele usando o portal do Azure.

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o banco de dados criado na seção 1, como `mySampleDatabase` .
1. Os grupos de failover podem ser configurados no nível do servidor. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

   ![Abrir servidor para banco de dados](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecione **grupos de failover** no painel **configurações** e, em seguida, selecione **Adicionar grupo** para criar um novo grupo de failover.

   ![Adicionar novo grupo de failover](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. Na página **grupo de failover** , insira ou selecione os seguintes valores e, em seguida, selecione **criar**:

   - **Nome do grupo de failover**: digite um nome de grupo de failover exclusivo, como `failovergrouptutorial` .
   - **Servidor secundário**: selecione a opção para *definir as configurações necessárias* e, em seguida, escolha **criar um novo servidor**. Como alternativa, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os valores a seguir, selecione **selecionar**.
      - **Nome do servidor**: digite um nome exclusivo para o servidor secundário, como `mysqlsecondary` .
      - **Logon de administrador do servidor**: tipo`azureuser`
      - **Senha**: digite uma senha complexa que atenda aos requisitos de senha.
      - **Local**: escolha um local na lista suspensa, como `East US` . Esse local não pode ser o mesmo local que o servidor primário.

     > [!NOTE]
     > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário.

     ![Criar um servidor secundário para o grupo de failover](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **Bancos de dados dentro do grupo**: depois que um servidor secundário é selecionado, essa opção fica desbloqueada. Selecione-o para **selecionar os bancos de dados a serem adicionados** e escolha o Database criado na seção 1. Adicionar o banco de dados ao grupo de failover iniciará automaticamente o processo de replicação geográfica.

   ![Adicionar Banco de dados SQL ao grupo de failover](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Crie seu grupo de failover e adicione seu banco de dados a ele usando o PowerShell.

   > [!NOTE]
   > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário.

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor no banco de dados SQL do Azure que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor no banco de dados SQL do Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um novo banco de dados único no banco de dados SQL do Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de failover no banco de dados SQL do Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados no banco de dados SQL do Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados a um grupo de failover no banco de dados SQL do Azure. |

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Crie seu grupo de failover e adicione seu banco de dados a ele usando o CLI do Azure.

   > [!NOTE]
   > As configurações de logon e firewall do servidor devem corresponder à do seu servidor primário.

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Esta parte do tutorial usa os seguintes cmdlets CLI do Azure:

| Comando | Observações |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor que hospeda bancos de dados e pools elásticos. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Cria um grupo de failover. |

---

## <a name="3---test-failover"></a>3-failover de teste

Nesta etapa, você falhará no grupo de failover para o servidor secundário e, em seguida, fará o failback usando o portal do Azure.

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Failover de teste usando o portal do Azure.

1. Selecione **SQL do Azure** no menu à esquerda da [portal do Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **todos os serviços**e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda.
1. Selecione o banco de dados criado na seção 2, como `mySampleDatbase` .
1. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

   ![Abrir servidor para banco de dados](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. Selecione **grupos de failover** no painel **configurações** e escolha o grupo de failover que você criou na seção 2.
  
   ![Selecione o grupo de failover no portal](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. Examine qual servidor é primário e qual servidor é secundário.
1. Selecione **failover** no painel de tarefas para fazer failover do grupo de failover que contém o banco de dados de exemplo.
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas.

   ![Fazer failover do grupo de failover que contém o banco de dados](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. Examine qual servidor agora é primário e qual servidor é secundário. Se o failover tiver sido bem-sucedido, os dois servidores deverão ter funções trocadas.
1. Selecione **failover** novamente para reprovar os servidores de volta para suas funções originais.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Failover de teste usando o PowerShell.

Verifique a função da réplica secundária:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Fazer failover para o servidor secundário:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

Reverta o grupo de failover de volta para o servidor primário:

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do Banco de Dados SQL do Azure. |

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Failover de teste usando o CLI do Azure.

Verifique qual servidor é o secundário:

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Fazer failover para o servidor secundário:

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

Reverta o grupo de failover de volta para o servidor primário:

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

Esta parte do tutorial usa os seguintes cmdlets CLI do Azure:

| Comando | Observações |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lista grupos de failover em um servidor. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Define o primário do grupo de failover fazendo failover de todos os bancos de dados do servidor primário atual. |

---

## <a name="clean-up-resources"></a>Limpar os recursos

Limpe os recursos excluindo o grupo de recursos.

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Exclua o grupo de recursos usando o portal do Azure.

1. Procure o grupo de recursos no [portal do Azure](https://portal.azure.com).
1. Selecione **excluir grupo de recursos** para excluir todos os recursos no grupo, bem como o próprio grupo de recursos.
1. Digite o nome do grupo de recursos, `myResourceGroup` , na caixa de texto e, em seguida, selecione **excluir** para excluir o grupo de recursos.  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Exclua o grupo de recursos usando o PowerShell.

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos |

# <a name="the-azure-cli"></a>[A CLI do Azure](#tab/azure-cli)

Exclua o grupo de recursos usando o CLI do Azure.

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Esta parte do tutorial usa os seguintes cmdlets CLI do Azure:

| Comando | Observações |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

---

> [!IMPORTANT]
> Se você quiser manter o grupo de recursos, mas excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes que ele seja removido do grupo de failover pode causar um comportamento imprevisível.

## <a name="full-scripts"></a>Scripts completos

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor que hospeda bancos de dados individuais e pools elásticos no Azure SQL Database. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor no banco de dados SQL do Azure. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um novo banco de dados no banco de dados SQL do Azure. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo grupo de failover no banco de dados SQL do Azure. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados no banco de dados SQL do Azure. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais bancos de dados a um grupo de failover no banco de dados SQL do Azure. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover no banco de dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover no banco de dados SQL do Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos no banco de dados SQL do Azure.|

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Define uma assinatura como a assinatura ativa atual. |
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor que hospeda bancos de dados individuais e pools elásticos no Azure SQL Database. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de IP no nível de servidor no banco de dados SQL do Azure. |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Cria um banco de dados no banco de dados SQL do Azure. |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Cria um grupo de failover no banco de dados SQL do Azure. |
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lista os grupos de failover em um servidor no banco de dados SQL do Azure. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Define o primário do grupo de failover fazendo failover de todos os bancos de dados do servidor primário atual. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

# <a name="the-portal"></a>[O portal](#tab/azure-portal)

Não há nenhum script disponível para o portal do Azure.

---

Você pode encontrar outros scripts do banco de dados SQL do Azure aqui: [Azure PowerShell](powershell-script-content-guide.md) e [CLI do Azure](az-cli-script-samples-content-guide.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um banco de dados no banco de dados SQL do Azure a um grupo de failover e testou o failover. Você aprendeu a:

> [!div class="checklist"]
>
> - Criar um banco de dados no Banco de Dados SQL do Azure
> - Crie um grupo de failover para o banco de dados entre dois servidores.
> - Failover de teste.

Avance para o próximo tutorial sobre como adicionar seu pool elástico a um grupo de failover.

> [!div class="nextstepaction"]
> [Tutorial: adicionar um pool elástico do banco de dados SQL do Azure a um grupo de failover](failover-group-add-elastic-pool-tutorial.md)
