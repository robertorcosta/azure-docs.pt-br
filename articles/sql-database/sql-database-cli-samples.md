---
title: Exemplos de script da CLI do Azure
description: Exemplos de script da CLI do Azure para criar e gerenciar servidores do Banco de Dados SQL do Azure, pools elásticos, bancos de dados e firewalls.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 459a5ea69e11a8614c572f68fce039b6cabbb1ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "80061711"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Amostras da CLI do Azure para o Banco de Dados SQL do Azure

Banco de dados SQL do Azure podem ser configurado usando a <a href="/cli/azure">CLI do Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este tópico exigirá que você esteja executando a CLI do Azure versão 2.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, confira [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

# <a name="single-database--elastic-pools"></a>[Banco de dados individual e pools elásticos](#tab/single-database)

| | |
|---|---|
|**Criar um banco de dados individual e um pool elástico**||
| [Criar um Banco de Dados individual e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-cli.md) | Cria um Banco de Dados SQL do Azure e configura uma regra de firewall no nível do servidor. |
| [Criar pools Elásticos e mover bancos de dados em pools](scripts/sql-database-move-database-between-pools-cli.md) | Cria pools elásticos do SQL, move Bancos de Dados SQL do Azure em pools e altera os tamanhos da computação. |
|**Dimensionar um banco de dados individual e um pool elástico**||
| [Dimensionar um banco de dados individual](scripts/sql-database-monitor-and-scale-database-cli.md) | Dimensiona um Banco de Dados SQL do Azure para um tamanho da computação diferente depois de consultar as informações de tamanho do banco de dados. |
| [Dimensionar um pool elástico](scripts/sql-database-scale-pool-cli.md) | Dimensiona um pool elástico do SQL para um tamanho da computação diferente. |
|**Configurar a replicação geográfica e o failover**||
| [Adicionar um banco de dados individual ao grupo de failover](scripts/sql-database-add-single-db-to-failover-group-cli.md)| Cria um banco de dados e um grupo de failover, adiciona o banco de dados ao grupo de failover e testa o failover no servidor secundário. |
| [Configurar um grupo de failover para um pool elástico](scripts/sql-database-add-elastic-pool-to-failover-group-cli.md) | Cria um banco de dados, adiciona-o a um pool elástico, adiciona o pool elástico ao grupo de failover e testa o failover para o servidor secundário. |
| [Configurar e fazer failover de um banco de dados individual usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-cli.md)| Configura a replicação geográfica ativa para um Banco de dados SQL do Azure e faz o failover desse banco de dados para a réplica secundária. |
| [Configurar e fazer failover de um banco de dados em pool usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-cli.md)| Configura a replicação geográfica ativa para um Banco de dados SQL do Azure em um pool elástico SQL e faz o failover desse banco de dados para a réplica secundária. |
| **Auditoria e detecção de ameaças** |
| [Configurar auditoria e detecção de ameaças](scripts/sql-database-auditing-and-threat-detection-cli.md)| Configura políticas de detecção de ameaças e auditoria para um Banco de Dados SQL do Azure. |
| **Fazer backup de um banco de dados, restaurá-lo, copiá-lo e importá-lo**||
| [Fazer backup de um banco de dados](scripts/sql-database-backup-database-cli.md)| Faz backup de um Banco de Dados SQL do Azure em um backup do Armazenamento do Azure. |
| [Restaurar um banco de dados](scripts/sql-database-restore-database-cli.md)| Restaura um Banco de Dados SQL do Azure com base em um backup com redundância geográfica e restaura um Banco de Dados SQL do Azure excluído para o backup mais recente. |
| [Copiar um banco de dados para o novo servidor](scripts/sql-database-copy-database-to-new-server-cli.md) | Cria uma cópia do Banco de Dados SQL do Azure existente em um novo Azure SQL Server. |
| [Importar um banco de dados de um arquivo bacpac](scripts/sql-database-import-from-bacpac-cli.md)| Importa um banco de dados para um SQL Server do Azure de um arquivo *.bacpac*. |
|||

Saiba mais sobre a [API da CLI do Azure de banco de dados individual](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

# <a name="managed-instance"></a>[Instância Gerenciada](#tab/managed-instance)

A tabela a seguir inclui links para exemplos de scripts da CLI do Azure para o Banco de Dados SQL do Azure – Instância Gerenciada.

| | |
|---|---|
| **Criar uma Instância Gerenciada**||
| [Criar uma instância gerenciada](scripts/sql-database-create-configure-managed-instance-cli.md)| Cria uma instância gerenciada. |
| **Configurar TDE (Transparent Data Encryption)**||
| [Gerenciar Transparent Data Encryption em uma Instância Gerenciada usando o Azure Key Vault](scripts/transparent-data-encryption-byok-sql-managed-instance-cli.md)| Configura o TDE (Transparent Data Encryption) na Instância Gerenciada do SQL do Azure usando o Azure Key Vault com vários cenários principais. |
|**Configurar um grupo de failover**||
| [Configurar um grupo de failover para uma instância gerenciada](scripts/sql-database-add-managed-instance-to-failover-group-cli.md) | Cria duas instâncias gerenciadas, adiciona-as a um grupo de failover e, em seguida, testa o failover da instância gerenciada primária para a instância gerenciada secundária. |
|||

Para obter exemplos adicionais de Instância Gerenciada, confira os scripts [criar](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/), [atualizar](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/), [mover um banco de dados](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/), [trabalhar com](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).

Saiba mais sobre a [API da CLI do Azure da Instância Gerenciada](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances).

---
