---
title: Regras de firewall de IP
description: Configure regras de firewall IP em nível de servidor para um banco de dados SQL ou firewall SQL Data Warehouse. Gerencie o acesso e configure as regras de firewall IP em nível de banco de dados para um banco de dados único ou pooled.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 03/18/2019
ms.openlocfilehash: af88fdf3378a6290c773c658ea6dd3469d7c92cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79531270"
---
# <a name="azure-sql-database-and-azure-sql-data-warehouse-ip-firewall-rules"></a>Regras de firewall IP do Azure SQL e do Azure SQL Data Warehouse

> [!NOTE]
> Este artigo se aplica aos servidores SQL do Azure e aos bancos de dados Azure SQL e Azure SQL Data Warehouse em um servidor SQL Do Azure. Para simplificar, *o Banco de Dados SQL* é usado para se referir tanto ao Banco de Dados SQL quanto ao SQL Data Warehouse.

> [!IMPORTANT]
> Este artigo faz *não* se aplica à *Instância Gerenciada do Banco de Dados SQL do Azure*. Para obter informações sobre a configuração da rede, consulte [Conecte seu aplicativo à instância gerenciada do banco de dados Azure SQL](sql-database-managed-instance-connect-app.md).

Quando você cria um novo servidor SQL do Azure chamado *mysqlserver*, por exemplo, o firewall SQL Database bloqueia todo o acesso ao ponto final público para o servidor (que é acessível *a mysqlserver.database.windows.net*).

> [!IMPORTANT]
> O SQL Data Warehouse só suporta regras de firewall IP em nível de servidor. Ele não suporta regras de firewall IP em nível de banco de dados.

## <a name="how-the-firewall-works"></a>Como funciona o firewall
As tentativas de conexão da internet e do Azure devem passar pelo firewall antes de chegarem ao seu servidor SQL ou banco de dados SQL, como mostra o diagrama a seguir.

   ![Diagrama de configuração do firewall][1]

### <a name="server-level-ip-firewall-rules"></a>Regras de firewall de IP no nível de servidor

  Essas regras permitem que os clientes acessem todo o Azure SQL Server, ou seja, todos os bancos dentro do mesmo servidor do Banco de Dados SQL. As regras estão armazenadas no banco de dados *principal.* Você pode ter um máximo de 128 regras de firewall IP de nível de servidor para um Servidor SQL Do Azure. Se você tiver os **recursos e serviços do Azure para acessar essa** configuração de servidor ativada, isso conta como uma regra de firewall única para o Azure SQL Server.
  
  Você pode configurar as regras de firewall IP em nível de servidor usando as instruções do portal Azure, PowerShell ou Transact-SQL.
  - Para usar o portal ou PowerShell, você deve ser o proprietário da assinatura ou um contribuinte de assinatura.
  - Para usar o Transact-SQL, você deve se conectar à instância do Banco de Dados SQL como o login principal no nível do servidor ou como administrador do Azure Active Directory. (Uma regra de firewall IP em nível de servidor deve ser criada primeiro por um usuário que tenha permissões de nível Azure.)

### <a name="database-level-ip-firewall-rules"></a>Regras de firewall de IP no nível de banco de dados

  Essas regras permitem que os clientes acessem certos bancos de dados (seguros) dentro do mesmo servidor do Banco de Dados SQL. Você cria as regras para cada banco de dados (incluindo o banco de dados *mestre)* e elas são armazenadas no banco de dados individual.
  
  Você só pode criar e gerenciar regras de firewall IP em nível de banco de dados para bancos de dados mestres e de usuário usando instruções Transact-SQL e somente depois de configurar o primeiro firewall em nível de servidor.
  
  Se você especificar um intervalo de endereçoIP na regra de firewall IP de nível de banco de dados que está fora do intervalo na regra de firewall IP em nível de servidor, apenas os clientes que têm endereços IP na faixa de nível de banco de dados podem acessar o banco de dados.
  
  Você pode ter no máximo 128 regras de firewall de IP no nível do banco de dados para um banco de dados. Para obter mais informações sobre a configuração de regras de firewall IP em nível de banco de dados, consulte o exemplo mais tarde neste artigo e consulte [sp_set_database_firewall_rule (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Recomendações de como definir regras de firewall

Recomendamos que você use regras de firewall IP em nível de banco de dados sempre que possível. Essa prática aumenta a segurança e torna seu banco de dados mais portátil. Use as regras de firewall IP em nível de servidor para administradores. Use também quando você tiver muitos bancos de dados que têm os mesmos requisitos de acesso, e você não deseja configurar cada banco de dados individualmente.

> [!NOTE]
> Para obter informações sobre bancos de dados portáteis no contexto de continuidade de negócios, confira [Requisitos de autenticação para a recuperação de desastres](sql-database-geo-replication-security-config.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>Regras de firewall de IP no nível de servidor versus no nível de banco de dados

*Os usuários de um banco de dados devem ser totalmente isolados de outro banco de dados?*

Se *sim,* use regras de firewall IP em nível de banco de dados para conceder acesso. Esse método evita o uso de regras de firewall IP em nível de servidor, que permitem acesso através do firewall a todos os bancos de dados. Isso reduziria a profundidade de suas defesas.

*Os usuários dos endereços IP precisam ter acesso a todos os bancos de dados?*

Se *sim,* use as regras de firewall IP em nível de servidor para reduzir o número de vezes que você tem que configurar regras de firewall IP.

*A pessoa ou equipe que configura as regras de firewall IP só tem acesso através do portal Azure, PowerShell ou da API REST?*

Nesse caso, você deve usar as regras de firewall IP em nível de servidor. As regras de firewall IP em nível de banco de dados só podem ser configuradas através do Transact-SQL.  

*A pessoa ou equipe que configura as regras de firewall IP está proibida de ter permissão de alto nível no nível do banco de dados?*

Nesse caso, use as regras de firewall IP em nível de servidor. Você precisa, pelo menos, da permissão *DO BANCO DE DADOS DE CONTROLE* no nível do banco de dados para configurar as regras de firewall IP em nível de banco de dados através do Transact-SQL.  

*Será que a pessoa ou equipe que configura ou audita as regras de firewall IP gerencia centralmente as regras de firewall IP para muitos (talvez centenas) de bancos de dados?*

Nesse cenário, as melhores práticas são determinadas por suas necessidades e ambiente. As regras de firewall de IP no nível de servidor podem ser mais fáceis de serem configuradas, mas o script pode configurar regras no nível de banco de dados. E mesmo que você use regras de firewall IP em nível de servidor, talvez seja necessário auditar as regras de firewall IP em nível de banco de dados para ver se os usuários com permissão *DE CONTROL* no banco de dados criam regras de firewall IP em nível de banco de dados.

*Posso usar uma mistura de regras de firewall IP em nível de servidor e de banco de dados?*

Sim. Alguns usuários, como administradores, podem precisar de regras de firewall IP em nível de servidor. Outros usuários, como usuários de um aplicativo de banco de dados, podem precisar de regras de firewall de IP no nível de banco de dados.

### <a name="connections-from-the-internet"></a>Conexões pela Internet

Quando um computador tenta se conectar ao servidor de banco de dados a partir da internet, o firewall primeiro verifica o endereço IP de origem da solicitação em relação às regras de firewall IP de nível de banco de dados para o banco de dados que a conexão solicita.

- Se o endereço estiver dentro de um intervalo especificado nas regras de firewall IP em nível de banco de dados, a conexão será concedida ao banco de dados SQL que contém a regra.
- Se o endereço não estiver dentro de um intervalo nas regras de firewall IP em nível de banco de dados, o firewall verificará as regras de firewall IP em nível de servidor. Se o endereço estiver dentro de um intervalo que esteja nas regras de firewall IP no nível do servidor, a conexão será concedida. Regras de firewall de IP de nível de servidor se aplicam a todos os bancos de dados SQL no SQL Server do Azure.  
- Se o endereço não estiver dentro de um intervalo que esteja em qualquer uma das regras de firewall IP em nível de banco de dados ou nível de servidor, a solicitação de conexão falhará.

> [!NOTE]
> Para acessar o Banco de Dados SQL do seu computador local, certifique-se de que o firewall em sua rede e computador local permita a comunicação de saída na porta TCP 1433.

### <a name="connections-from-inside-azure"></a>Conexões de dentro do Azure

Para permitir que aplicativos hospedados dentro do Azure se conectem ao seu servidor SQL, as conexões Do Zure devem ser ativadas. Quando um aplicativo do Azure tenta se conectar ao seu servidor de banco de dados, o firewall verifica se as conexões do Azure são permitidas. Uma configuração de firewall que tem endereços IP iniciais e finais iguais a *0.0.0.0 indica* que as conexões do Azure são permitidas. Isso pode ser ligado diretamente a partir da lâmina do Portal Do Azure, definindo as regras do Firewall, bem como alternando os **Serviços e recursos do Azure para acessar este servidor** para **ON** nas **configurações de Firewalls e redes virtuais.** Se a conexão não for permitida, a solicitação não chegará ao servidor SQL Database.

> [!IMPORTANT]
> Essa opção configura o firewall para permitir todas as conexões do Azure, incluindo conexões das assinaturas de outros clientes. Se você selecionar essa opção, certifique-se de que seu login e permissões de usuário limitem o acesso apenas a usuários autorizados.

## <a name="create-and-manage-ip-firewall-rules"></a>Crie e gerencie regras de firewall IP

Você cria a primeira configuração de firewall em nível de servidor usando o [portal Azure](https://portal.azure.com/) ou programáticamente usando [o Azure PowerShell](https://docs.microsoft.com/powershell/module/az.sql), [Azure CLI](https://docs.microsoft.com/cli/azure/sql/server/firewall-rule)ou uma [API](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate)Azure REST . Você cria e gerencia regras adicionais de firewall IP em nível de servidor usando esses métodos ou Transact-SQL.

> [!IMPORTANT]
> As regras de firewall IP em nível de banco de dados só podem ser criadas e gerenciadas usando o Transact-SQL.

Para melhorar o desempenho, as regras de firewall de IP de nível de servidor são temporariamente armazenadas em cache no nível do banco de dados. Para atualizar o cache, veja [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx).

> [!TIP]
> Você pode usar a [Auditoria de Banco de Dados SQL](sql-database-auditing.md) para auditar alterações de firewall no nível do servidor e no nível de banco de dados.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Use o portal Azure para gerenciar regras de firewall IP em nível de servidor

Para definir uma regra de firewall IP em nível de servidor no portal Azure, vá para a página de visão geral do banco de dados SQL do Azure ou do servidor SQL Database.

> [!TIP]
> Para ver um tutorial, consulte [Criar um banco de dados usando o Portal do Azure](sql-database-single-database-get-started.md).

#### <a name="from-the-database-overview-page"></a>A partir da página de visão geral do banco de dados

1. Para definir uma regra de firewall IP em nível de servidor a partir da página de visão geral do banco de dados, selecione **Definir firewall do servidor** na barra de ferramentas, como mostra a imagem a seguir. 

    ![Regra de firewall IP do servidor](./media/sql-database-get-started-portal/sql-database-server-set-firewall-rule.png)

    A página **Configurações do firewall** do servidor do Banco de Dados SQL é aberta.

2. Selecione **Adicionar IP cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando e, em seguida, **selecione Salvar**. Uma regra de firewall de IP no nível do servidor é criada para seu endereço IP atual.

    ![Definir regra de firewall IP em nível de servidor](./media/sql-database-get-started-portal/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>A partir da página de visão geral do servidor

A página de visão geral do servidor é aberta. Ele mostra o nome do servidor totalmente qualificado (como *mynewserver20170403.database.windows.net*) e fornece opções para configuração adicional.

1. Para definir uma regra de nível de servidor a partir desta página, selecione **Firewall** no menu **Configurações** no lado esquerdo.

2. Selecione **Adicionar IP cliente** na barra de ferramentas para adicionar o endereço IP do computador que você está usando e, em seguida, **selecione Salvar**. Uma regra de firewall de IP no nível do servidor é criada para seu endereço IP atual.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Use o Transact-SQL para gerenciar regras de firewall IP

| Visualização do catálogo ou procedimento armazenado | Nível | Descrição |
| --- | --- | --- |
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx) |Servidor |Exibe as regras de firewall de IP atuais no nível de servidor |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) |Servidor |Cria ou atualiza as regras de firewall de IP no nível de servidor |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx) |Servidor |Remove as regras de firewall de IP no nível de servidor |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx) |Banco de dados |Exibe as regras de firewall de IP atuais no nível de banco de dados |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) |Banco de dados |Cria ou atualiza as regras de firewall de IP no nível de banco de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) |Bancos de dados |Remove as regras de firewall de IP no nível de banco de dados |

O exemplo a seguir analisa as regras existentes, permite uma série de endereços IP no *servidor Contoso*e exclui uma regra de firewall IP:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Em seguida, adicione uma regra de firewall de IP no nível do servidor.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Para excluir uma regra de firewall IP em nível de servidor, execute o *procedimento sp_delete_firewall_rule* armazenado. O exemplo a seguir exclui a regra *ContosoFirewallRule*:

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Use o PowerShell para gerenciar regras de firewall IP em nível de servidor 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo PowerShell Azure Resource Manager ainda é suportado pelo Banco de Dados SQL do Azure, mas todo o desenvolvimento agora é para o módulo Az.Sql. Para obter esses cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos nos módulos Az e AzureRm são substancialmente idênticos.

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Servidor |Retorna as regras de firewall atuais no nível de servidor |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Servidor |Cria as regras de firewall no nível de servidor |
| [Regra de firewall set-AzSqlServer](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Servidor |Atualiza as propriedades de uma regra de firewall existente no nível de servidor |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Servidor |Remove as regras de firewall no nível de servidor |

O exemplo a seguir usa o PowerShell para definir uma regra de firewall IP em nível de servidor:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```
> [!TIP]
> Por $servername especificar o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, especifique **mysqldbserver** em vez de **mysqldbserver.database.windows.net**

> [!TIP]
> Para obter exemplos de PowerShell no contexto de um quickstart, consulte [Criar DB - PowerShell](sql-database-powershell-samples.md) e [Criar um único banco de dados e configurar uma regra de firewall IP em nível de servidor do SQL Database usando o PowerShell](scripts/sql-database-create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Use o CLI para gerenciar regras de firewall IP em nível de servidor

| Cmdlet | Nível | Descrição |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Servidor|Cria uma regra de firewall de IP do servidor|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Servidor|Lista as regras de firewall de IP em um servidor|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Servidor|Mostra o detalhe de uma regra de firewall IP|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Servidor|Atualiza uma regra de firewall IP|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Servidor|Exclui uma regra de firewall IP|

O exemplo a seguir usa CLI para definir uma regra de firewall IP em nível de servidor:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```
> [!TIP]
> Por $servername especificar o nome do servidor e não o nome DNS totalmente qualificado, por exemplo, especifique **mysqldbserver** em vez de **mysqldbserver.database.windows.net**

> [!TIP]
> Para um exemplo de CLI no contexto de um quickstart, consulte [Criar DB - Azure CLI](sql-database-cli-samples.md) e Criar um único banco de dados e configurar uma regra de firewall IP do Banco de Dados [SQL usando o Azure CLI](scripts/sql-database-create-and-configure-database-cli.md).

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Use uma API REST para gerenciar regras de firewall IP em nível de servidor

| API | Nível | Descrição |
| --- | --- | --- |
| [Regras de firewall de lista](https://docs.microsoft.com/rest/api/sql/firewallrules/listbyserver) |Servidor |Exibe as regras de firewall de IP atuais no nível de servidor |
| [Criar ou atualizar regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/createorupdate) |Servidor |Cria ou atualiza as regras de firewall de IP no nível de servidor |
| [Excluir regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/delete) |Servidor |Remove as regras de firewall de IP no nível de servidor |
| [Obter regras de firewall](https://docs.microsoft.com/rest/api/sql/firewallrules/get) | Servidor | Obtém regras de firewall de IP no nível de servidor |

## <a name="troubleshoot-the-database-firewall"></a>Solucionar problemas no firewall do banco de dados

Considere os seguintes pontos quando o acesso ao serviço SQL Database não se comportar como você espera.

- **Configuração de firewall local:**

  Antes que seu computador possa acessar o Banco de Dados SQL, talvez seja necessário criar uma exceção de firewall no computador para a porta TCP 1433. Para fazer conexões dentro do limite de nuvem do Azure, você pode ter que abrir portas adicionais. Para obter mais informações, consulte a seção "SQL Database: Outside vs inside" de Portas além de [1433 para ADO.NET 4.5 e SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Tradução de endereço de rede:**

  Devido à tradução de endereço de rede (NAT), o endereço IP usado pelo computador para se conectar ao Banco de Dados SQL pode ser diferente do endereço IP nas configurações de configuração IP do seu computador. Para visualizar o endereço IP que seu computador está usando para se conectar ao Azure:
    1. Entre no portal.
    1. Vá para a guia **Configurar** no servidor que hospeda seu banco de dados.
    1. O **endereço IP do cliente atual** é exibido na seção **Endereços IP permitidos.** Selecione **Adicionar** para **endereços IP permitidos** para permitir que este computador acesse o servidor.

- **As alterações na lista de desporto ainda não entraram em vigor:**

  Pode haver até um atraso de cinco minutos para que as alterações na configuração de firewall do Banco de Dados SQL entrem em vigor.

- **O login não é autorizado ou uma senha incorreta foi usada:**

  Se um login não tiver permissões no servidor do Banco de Dados SQL ou a senha estiver incorreta, a conexão com o servidor será negada. Criar uma configuração de firewall só dá aos clientes a *oportunidade* de tentar se conectar ao seu servidor. O cliente ainda deve fornecer as credenciais de segurança necessárias. Para obter mais informações sobre a preparação de logins, consulte [Controlando e concedendo acesso ao banco de dados SQL e ao SQL Data Warehouse](sql-database-manage-logins.md).

- **Endereço IP dinâmico:**

  Se você tem uma conexão com a Internet que usa endereçamento IP dinâmico e tem problemas para passar pelo firewall, tente uma das seguintes soluções:
  
  - Peça ao seu provedor de serviços de Internet a faixa de endereçoIP atribuída aos computadores clientes que acessam o servidor sql database. Adicione esse intervalo de endereçoIP como uma regra de firewall IP.
  - Obtenha endereçamento IP estático em vez disso para seus computadores clientes. Adicione os endereços IP como regras de firewall IP.

## <a name="next-steps"></a>Próximas etapas

- Confirme se o ambiente de rede corporativa permite a comunicação de entrada das faixas de endereçoIP computacional (incluindo as faixas SQL) que são usadas pelos data centers do Azure. Você pode ter que adicionar esses endereços IP à lista de de permitir. Consulte as [faixas ip do data center do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).  
- Para obter um início rápido sobre a criação de uma regra de firewall IP em nível de servidor, consulte [Criar um banco de dados SQL do Azure](sql-database-single-database-get-started.md).
- Para obter ajuda para se conectar a um banco de dados SQL do Azure a partir de aplicativos de código aberto ou de terceiros, consulte [amostras de código quickstart do Cliente para o Banco de Dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
- Para obter informações sobre portas adicionais que você pode precisar para abrir, consulte a seção "Banco de dados SQL: Fora vs dentro" de Portas além de [1433 para ADO.NET 4,5 e SQL Database](sql-database-develop-direct-route-ports-adonet-v12.md)
- Para obter uma visão geral da segurança do banco de dados SQL do Azure, consulte [Protegendo seu banco de dados](sql-database-security-overview.md).

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
