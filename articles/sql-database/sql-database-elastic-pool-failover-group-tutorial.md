---
title: 'Tutorial: Adicione um pool elástico a um grupo de failover'
description: Adicione um pool elástico de banco de dados Azure SQL a um grupo de failover usando o portal Azure, PowerShell ou Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: c57f9eed2147504dd7b3313d58468fb76ab40caa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268971"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Tutorial: Adicione um pool elástico de banco de dados Azure SQL a um grupo de failover

Configure um grupo de failover para um pool elástico de banco de dados Azure SQL e teste failover usando o portal Azure.  Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> - Crie um banco de dados único do Azure SQL.
> - Adicione o banco de dados único em um pool elástico. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para dois pools elásticos entre dois servidores SQL lógicos.
> - Failover de teste.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, verifique se você tem: 

- Uma assinatura do Azure. [Crie uma conta gratuita](https://azure.microsoft.com/free/) se você ainda não tiver uma.


## <a name="1---create-a-single-database"></a>1 - Criar um único banco de dados 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - Adicionar banco de dados único ao pool elástico
Nesta etapa, você criará um pool elástico e adicionará seu banco de dados único a ele. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie sua piscina elástica usando o portal Azure. 


1. Selecione **SQL do Azure** no menu à esquerda do portal do Azure. Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione **+ Adicionar** para abrir a página **Selecionar opção de implantação do SQL**. Você pode exibir informações adicionais sobre os diferentes bancos de dados selecionando Mostrar detalhes no bloco Bancos de dados.
1. Selecione **o pool Elástico** do tipo **recurso** que está parado no bloco **SQL Databases.** Selecione **Criar** para criar seu pool elástico. 

    ![Selecione piscina elástica](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. Configure seu pool elástico com os seguintes valores:
   - **Nome**: Forneça um nome exclusivo para `myElasticPool`sua piscina elástica, como . 
   - **Assinatura**: Selecione sua assinatura na estada.
   - **ResourceGroup**: `myResourceGroup` Selecione a partir da queda, o grupo de recursos que você criou na seção 1. 
   - **Servidor**: Selecione o servidor que você criou na seção 1 a partir da queda.  

       ![Crie um novo servidor para pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Cálculo + armazenamento**: Selecione **Configurar o pool elástico** para configurar seu cálculo, armazenamento e adicionar seu banco de dados único ao seu pool elástico. Na guia Configurações do **pool,** deixe o padrão de Gen5, com 2 vCores e 32gb. 

1. Na página **Configurar,** selecione a guia **Bancos de dados** e opte por **Adicionar banco de dados**. Escolha o banco de dados criado na seção 1 e **selecione Aplicar** para adicioná-lo ao seu pool elástico. Selecione **Aplicar** novamente para aplicar as configurações do pool elástico e fechar a **página Configurar.** 

    ![Adicionar SQL DB ao pool elástico](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Selecione **'Revisar + criar para** rever suas configurações de pool elástico' e, em seguida, selecione **Criar** para criar seu pool elástico. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Crie seus pools elásticos e servidor secundário usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um banco de dados SQL do Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico. | 

---

## <a name="3---create-the-failover-group"></a>3 - Criar o grupo failover 
Nesta etapa, você criará um [grupo de failover](sql-database-auto-failover-group.md) entre um servidor Azure SQL existente e um novo servidor SQL Azure em outra região. Em seguida, adicione a piscina elástica ao grupo failover. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Crie seu grupo failover usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o pool elástico criado na `myElasticPool`seção anterior, tais como . 
1. No **painel Visão geral,** selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.
  
    ![Abra o servidor para piscina elástica](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos Failover** no painel **Configurações** e selecione **Adicionar grupo** para criar um novo grupo de failover. 

    ![Adicionar novo grupo de failover](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. Na página **Grupo failover,** digite ou selecione os seguintes valores e selecione **Criar**:
    - **Nome do grupo Failover**: Digite um `failovergrouptutorial`nome de grupo de failover único, como . 
    - **Servidor secundário :** Selecione a opção para *configurar as configurações necessárias* e, em seguida, escolha **Criar um novo servidor**. Alternativamente, você pode escolher um servidor já existente como o servidor secundário. Depois de inserir os seguintes valores para o seu novo servidor secundário, **selecione Selecionar**. 
        - **Nome do servidor**: Digite um nome único `mysqlsecondary`para o servidor secundário, como . 
        - **Login de admin do servidor**: Tipo`azureuser`
        - **Senha**: Digite uma senha complexa que atenda aos requisitos de senha.
        - **Localização**: Escolha um local a partir `East US`da queda, tais como . Este local não pode ser o mesmo local que seu servidor principal.

       > [!NOTE]
       > As configurações de login e firewall do servidor devem coincidir com a do servidor principal. 
    
       ![Criar um servidor secundário para o grupo failover](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. Selecione **Bancos de Dados dentro do grupo** e selecione o pool elástico criado na seção 2. Um aviso deve aparecer, solicitando que você crie um pool elástico no servidor secundário. Selecione o aviso e selecione **OK** para criar o pool elástico no servidor secundário. 
        
    ![Adicionar piscina elástica ao grupo failover](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Selecione **Selecionar** para aplicar suas configurações de pool elástico no grupo failover e, em seguida, selecione **Criar** para criar seu grupo de failover. A adição da piscina elástica ao grupo failover iniciará automaticamente o processo de geo-replicação.


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Crie seu grupo de failover usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor do Banco de Dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um banco de dados SQL do Azure.| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais Bancos de Dados SQL do Azure a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |

---


## <a name="4---test-failover"></a>4 - Failover de teste 
Nesta etapa, você falhará seu grupo de failover no servidor secundário e, em seguida, falhará usando o portal Azure. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Teste failover do seu grupo failover usando o portal Azure. 

1. Selecione **Azure SQL** no menu à esquerda do [portal Azure](https://portal.azure.com). Se o **SQL do Azure** não estiver na lista, selecione **Todos os serviços** e, em seguida, digite SQL do Azure na caixa de pesquisa. (Opcional) Selecione a estrela ao lado de **SQL do Azure** para marcá-lo como favorito e adicioná-lo como um item no menu de navegação à esquerda. 
1. Selecione o pool elástico criado na `myElasticPool`seção anterior, tais como . 
1. Selecione o nome do servidor em **nome do servidor** para abrir as configurações do servidor.

    ![Abra o servidor para piscina elástica](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. Selecione **grupos Failover** no painel **Configurações** e escolha o grupo de failover criado na seção 2. 
  
   ![Selecione o grupo failover do portal](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. Revise qual servidor é primário e qual servidor é secundário. 
1. Selecione **Failover** do painel de tarefas para falhar sobre o grupo de failover que contém seu pool elástico. 
1. Selecione **Sim** no aviso que notifica que as sessões de TDS serão desconectadas. 

   ![Falha no seu grupo de failover contendo seu banco de dados SQL](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. Revise qual servidor é primário, qual servidor é secundário. Se failover foi bem sucedido, os dois servidores devem ter trocado de funções. 
1. Selecione **Failover** novamente para falhar o grupo failover de volta às configurações originais. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Teste failover do seu grupo failover usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

Falhe seu grupo de failover no servidor secundário e, em seguida, falhe usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

Esta parte do tutorial usa os seguintes cmdlets do PowerShell:

| Comando | Observações |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do Banco de Dados SQL do Azure. |


---

## <a name="clean-up-resources"></a>Limpar recursos 

Limpe os recursos excluindo o grupo de recursos. 


# <a name="portal"></a>[Portal](#tab/azure-portal)


1. Procure o grupo de recursos no [portal do Azure](https://portal.azure.com).
1. Selecione **Excluir grupo de recursos** para excluir todos os recursos do grupo, bem como o próprio grupo de recursos. 
1. Digite o nome do `myResourceGroup`grupo de recursos, na caixa de texto e selecione **Excluir** para excluir o grupo de recursos. 


# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Limpe seus recursos usando o PowerShell. 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

Esta parte do tutorial usa o seguinte cmdlet powershell:

| Comando | Observações |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos | 

---

> [!IMPORTANT]
> Se você quiser manter o grupo de recursos, mas excluir o banco de dados secundário, remova-o do grupo de failover antes de excluí-lo. A exclusão de um banco de dados secundário antes de ser removido do grupo failover pode causar comportamento imprevisível. 

## <a name="full-script"></a>Script completo

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Este script usa os comandos a seguir. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor do Banco de Dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Cria uma regra de firewall para um servidor lógico. | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Cria um novo banco de dados individual do Banco de Dados SQL do Azure. | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool de banco de dados elástico para um banco de dados SQL do Azure.| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Define propriedades para um banco de dados ou move um banco de dados existente para um pool, elástico. | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um grupo de failover. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Obtém um ou mais bancos de dados SQL. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Adiciona um ou mais Bancos de Dados SQL do Azure a um grupo de failover. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém ou lista os grupos de failover do Banco de Dados SQL do Azure. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Executa um failover de um grupo de failover do Banco de Dados SQL do Azure. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Remove um grupo de recursos | 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Não há scripts disponíveis para o portal Azure.

---

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você adicionou um pool elástico azure SQL Database a um grupo de failover e testou failover. Você aprendeu a:

> [!div class="checklist"]
> - Crie um banco de dados único do Azure SQL.
> - Adicione o banco de dados único em um pool elástico. 
> - Crie um [grupo de failover](sql-database-auto-failover-group.md) para dois pools elásticos entre dois servidores SQL lógicos.
> - Failover de teste.

Avance para o próximo tutorial sobre como migrar usando DMS.

> [!div class="nextstepaction"]
> [Tutorial: Migre o SQL Server para um banco de dados agrupado usando DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
