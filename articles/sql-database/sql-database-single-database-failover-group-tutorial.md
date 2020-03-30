---
title: 'Tutorial: Adicione um único banco de dados a um grupo de failover'
description: Adicione um banco de dados único do Azure SQL database a um grupo de failover usando o portal Azure, PowerShell ou Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: c5ce6a1c2f231d372a2a8113eb9043a236090388
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061705"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>Tutorial: Adicione um banco de dados único do Azure SQL a um grupo de failover

Um [grupo failover](sql-database-auto-failover-group.md) é uma camada de abstração declarativa que permite agrupar bancos de dados geo-replicados mulitple. Aprenda a configurar um grupo de failover para um banco de dados único do Banco de Dados SQL do Azure e teste failover usando o portal Azure, PowerShell ou Azure CLI.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> - Crie um banco de dados único do Azure SQL.
> - Crie um grupo de failover para um único banco de dados entre dois servidores SQL lógicos.
> - Failover de teste.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para concluir este tutorial, verifique se você tem: 

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Para completar o tutorial, certifique-se de ter os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Para completar o tutorial, certifique-se de ter os seguintes itens:

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.
- A versão mais recente do [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

---

## <a name="1---create-a-single-database"></a>1 - Criar um único banco de dados 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - Criar o grupo failover 
Nesta etapa, você criará um [grupo de failover](sql-database-auto-failover-group.md) entre um servidor Azure SQL existente e um novo servidor SQL Azure em outra região. Em seguida, adicione o banco de dados ao grupo de failover. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Crie seu grupo de failover e adicione seu banco de dados único a ele usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o banco de dados único `mySampleDatabase`criado na seção 1, tais como . 
1. Os grupos failover podem ser configurados no nível do servidor. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

   ![Servidor aberto para db único](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos Failover** no painel **Configurações** e selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-single-database-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. Na página **Grupo failover,** digite ou selecione os seguintes valores e selecione **Criar**:
    - **Nome do grupo Failover**: Digite um `failovergrouptutorial`nome de grupo de failover único, como . 
    - **Servidor secundário :** Selecione a opção para *configurar as configurações necessárias* e, em seguida, escolha **Criar um novo servidor**. Alternativamente, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os seguintes valores, **selecione Selecionar**. 
        - **Nome do servidor**: Digite um nome único `mysqlsecondary`para o servidor secundário, como . 
        - **Login de admin do servidor**: Tipo`azureuser`
        - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
        - **Localização**: Escolha um local a partir `East US`da queda, tais como . Este local não pode ser o mesmo local que seu servidor principal.

    > [!NOTE]
    > As configurações de login e firewall do servidor devem coincidir com a do servidor principal. 
    
      ![Criar um servidor secundário para o grupo failover](media/sql-database-single-database-failover-group-tutorial/create-secondary-failover-server.png)

   - **Bancos de dados dentro do grupo**: Uma vez que um servidor secundário é selecionado, essa opção fica desbloqueada. Selecione-o para **Selecionar bancos de dados para adicionar** e, em seguida, escolha o banco de dados criado na seção 1. A adição do banco de dados ao grupo de failover iniciará automaticamente o processo de geo-replicação. 
        
    ![Adicionar SQL DB ao grupo failover](media/sql-database-single-database-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Crie seu grupo de failover e adicione seu banco de dados único a ele usando o PowerShell. 

   > [!NOTE]
   > As configurações de login e firewall do servidor devem coincidir com a do servidor principal. 

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
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
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
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor do Banco de Dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um novo banco de dados individual do Banco de Dados SQL do Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais Bancos de Dados SQL do Azure a um grupo de failover. |

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Crie seu grupo de failover e adicione seu banco de dados único a ele usando a CLI AZ. 

   > [!NOTE]
   > As configurações de login e firewall do servidor devem coincidir com a do servidor principal. 

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary logical server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password
   
   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

Esta parte do tutorial usa os seguintes cmdlets Az CLI:

| Comando | Observações |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor do Banco de Dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Cria um grupo de failover. | 

---

## <a name="3---test-failover"></a>3 - Failover de teste 
Nesta etapa, você falhará seu grupo de failover no servidor secundário e, em seguida, falhará usando o portal Azure. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Failover de teste usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o banco de dados único `mySampleDatbase`criado na seção 2, tais como . 
1. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

   ![Servidor aberto para db único](media/sql-database-single-database-failover-group-tutorial/open-sql-db-server.png)

1. Selecione **grupos Failover** no painel **Configurações** e escolha o grupo de failover criado na seção 2. 
  
   ![Selecione o grupo failover do portal](media/sql-database-single-database-failover-group-tutorial/select-failover-group.png)

1. Revise qual servidor é primário e qual servidor é secundário. 
1. Selecione **Failover** do painel de tarefas para failover seu grupo de failover contendo seu banco de dados único de amostra. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Falha no seu grupo de failover contendo seu banco de dados SQL](media/sql-database-single-database-failover-group-tutorial/failover-sql-db.png)

1. Revise qual servidor agora é primário e qual servidor é secundário. Se falhar mais do que foi bem sucedido, os dois servidores devem ter trocado de funções. 
1. Selecione **Failover** novamente para falhar os servidores de volta às suas funções originais. 

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Teste failover usando PowerShell. 


Verifique o papel da réplica secundária: 

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

Falha no servidor secundário: 

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



# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Failover de teste usando o Cli AZ. 

Verifique qual servidor é o secundário:

   
   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

Falha no servidor secundário: 

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

Esta parte do tutorial usa os seguintes cmdlets Az CLI:

| Comando | Observações |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lista grupos de failover em um servidor. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Define o primário do grupo de failover fazendo failover de todos os bancos de dados do servidor primário atual. | 

---

## <a name="clean-up-resources"></a>Limpar recursos 
Limpe os recursos excluindo o grupo de recursos. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Exclua o grupo de recursos usando o portal Azure. 

1. Procure o grupo de recursos no [portal do Azure](https://portal.azure.com).
1. Selecione **Excluir grupo de recursos** para excluir todos os recursos do grupo, bem como o próprio grupo de recursos. 
1. Digite o nome do `myResourceGroup`grupo de recursos, na caixa de texto e selecione **Excluir** para excluir o grupo de recursos.  

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Exclua o grupo de recursos usando a Cli AZ. 


   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

Esta parte do tutorial usa os seguintes cmdlets Az CLI:

| Comando | Observações |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

---


> [!IMPORTANT]
> Se você quiser manter o grupo de recursos, mas excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes de ser removido do grupo failover pode causar comportamento imprevisível. 


## <a name="full-scripts"></a>Scripts completos

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor do Banco de Dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um novo banco de dados individual do Banco de Dados SQL do Azure. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais Bancos de Dados SQL do Azure a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do Banco de Dados SQL do Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos | 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add single database to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | Define uma assinatura como a assinatura ativa atual. | 
| [az group create](/cli/azure/group#az-group-create) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Cria um servidor do Banco de Dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | Cria as regras de firewall de um servidor. | 
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | Cria um banco de dados. | 
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | Cria um grupo de failover. | 
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | Lista grupos de failover em um servidor. |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | Define o primário do grupo de failover fazendo failover de todos os bancos de dados do servidor primário atual. | 
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

# <a name="portal"></a>[Portal](#tab/azure-portal)
Não há scripts disponíveis para o portal Azure. 
 
---

Você pode encontrar outros scripts de banco de dados Azure SQL aqui: [Azure PowerShell](sql-database-powershell-samples.md) e [Azure CLI](sql-database-cli-samples.md). 

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um banco de dados único do Azure SQL Database a um grupo de failover e testou failover. Você aprendeu a: 

> [!div class="checklist"]
> - Crie um banco de dados único do Azure SQL. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para um único banco de dados entre dois servidores SQL lógicos.
> - Failover de teste.

Avance para o próximo tutorial sobre como adicionar seu pool elástico a um grupo de failover. 

> [!div class="nextstepaction"]
> [Tutorial: Adicione um pool elástico de banco de dados Azure SQL a um grupo de failover](sql-database-elastic-pool-failover-group-tutorial.md)
