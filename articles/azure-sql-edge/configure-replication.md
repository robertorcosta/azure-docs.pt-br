---
title: Configurar replicação para o SQL do Azure no Edge (versão prévia)
description: Saiba mais sobre como configurar replicação para o SQL do Azure no Edge (versão prévia)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: e2b37e0f3ccf5fcebe4723c05d644f2cbb7c1d56
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593985"
---
# <a name="configure-replication-to-azure-sql-edge-preview"></a>Configurar replicação para o SQL do Azure no Edge (versão prévia) 

A instância do SQL do Azure no Edge pode ser configurada como assinante de push de uma replicação transacional unidirecional ou replicação de instantâneo. A instância do SQL do Azure no Edge não pode agir como o publicador ou o distribuidor de uma configuração de replicação transacional. Replicação de mesclagem, replicação P2P e Publicação do Oracle não são compatíveis com o SQL do Azure no Edge.

## <a name="supported-configurations"></a>**Configurações compatíveis**:
  
- A instância do SQL do Azure no Edge deve ser um assinante de push de um editor.
- O editor e o distribuidor podem ser
   - Uma instância do SQL Server em execução local ou uma instância do SQL Server em execução em uma máquina virtual do Azure. Para obter mais informações, veja [SQL Server na visão geral de Máquinas Virtuais do Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/). As instâncias do SQL Server devem estar usando uma versão superior ao SQL Server 2016.
   - Uma instância da Instância Gerenciada do Banco de Dados SQL do Azure. Uma Instância Gerenciada pode hospedar bancos de dados de editores, distribuidores e assinantes. Para obter mais informações, veja [Replicação com a Instância Gerenciada do Banco de Dados SQL](https://docs.microsoft.com/azure/sql-database/replication-with-sql-database-managed-instance/).

- O banco de dados de distribuição e os agentes de replicação não podem ser colocados em uma instância do SQL do Azure no Edge.  

> [!NOTE]
> A tentativa de configurar a replicação usando uma versão incompatível pode resultar no erro número MSSQL_REPL20084 (o processo não pôde se conectar ao assinante) e MSSQL_REPL40532 (não é possível abrir o servidor \<name> solicitado pelo logon. Houve falha no logon.).  

Para usar todos os recursos do SQL do Azure no Edge, você deve estar usando as versões mais recentes do [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) e do [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).  

## <a name="remarks"></a>Comentários

- A replicação pode ser configurada usando o [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) ou executando instruções Transact-SQL no editor, usando o SQL Server Management Studio ou o [Azure Database Studio](https://docs.microsoft.com/sql/azure-data-studio/download-azure-data-studio).
- A replicação só pode usar logons de autenticação do SQL Server para se conectar a uma instância do SQL do Azure no Edge.
- Tabelas replicadas devem ter uma chave primária.
- Uma única publicação no SQL Server pode dar suporte a assinantes do SQL do Azure no Edge e do SQL Server (local e SQL Server em uma máquina virtual do Azure).  
- Gerenciamento de replicação, monitoramento e solução de problemas devem ser executados no SQL Server local.  
- Há suporte apenas para assinaturas push no SQL do Azure no Edge.  
- Há suporte apenas para `@subscriber_type = 0` no **sp_addsubscription** do SQL do Azure no Edge.  
- O SQL do Azure no Edge não dá suporte para replicação bidirecional, imediata, atualizável ou ponto a ponto.
- O SQL do Azure no Edge só dá suporte para um subconjunto de recursos disponíveis no SQL Server ou na Instância Gerenciada do Banco de Dados SQL do Azure, pois essa tentativa de replicar um banco de dados (ou objetos do banco de dados) que contêm um ou mais recursos sem suporte resultará em uma falha. Por exemplo, a tentativa de replicar um banco de dados que contém objetos com tipos de dado espaciais resultará em um erro. Para obter mais informações sobre os recursos compatíveis do SQL do Azure no Edge, confira [Recursos compatíveis do SQL do Azure no Edge](features.md).

## <a name="scenarios"></a>Cenários  

### <a name="initializing-reference-data-on-an-edge-instance"></a>Inicializando dados de referência em uma instância de borda

Um cenário comum em que a replicação pode ser útil é quando há a necessidade de inicializar a instância de borda com dados de referência que mudam com o tempo. Por exemplo, atualizar modelos de ML na instância de borda depois que eles foram treinados em uma instância local do SQL Server.

1. Crie uma publicação de replicação transacional em um banco de dados do SQL Server local.  
2. No SQL Server local, use o **Assistente para Nova Assinatura** ou instruções Transact-SQL para criar um push para assinatura para o SQL do Azure no Edge.  
3. O banco de dados replicado no SQL do Azure no Edge pode ser inicializado usando um instantâneo gerado pelo agente de instantâneo e distribuído e entregue pelo agente de distribuição ou usando um backup do banco de dados do editor. Mais uma vez, se o backup do banco de dados contiver objetos/recursos incompatíveis com o SQL do Azure no Edge, a operação de restauração falhará.

## <a name="limitations"></a>Limitações

As opções a seguir não são compatíveis com assinaturas do SQL do Azure no Edge:

- Copiar associação de grupos de arquivos  
- Copiar esquemas de particionamento de tabela  
- Copiar esquemas de particionamento de índice  
- Copiar estatísticas definidas pelo usuário  
- Copiar associações padrão  
- Copiar associações de regra  
- Copiar índices de texto completo  
- Copiar XML XSD  
- Copiar índices XML  
- Permissões de cópia  
- Copiar índices espaciais  
- Copiar índices filtrados  
- Copiar atributo de compactação de dados  
- Copiar atributo de coluna esparsa  
- Copie os tipos de dados filestream, hierarchyid ou espaciais.
- Converter hierarchyid em tipos de dados MAX  
- Converter espacial em tipos de dados MAX  
- Copiar propriedades estendidas  
- Permissões de cópia  

## <a name="examples"></a>Exemplos

Crie uma publicação e uma assinatura push. Para obter mais informações, consulte:
  
- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Crie uma assinatura push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/) usando o nome/IP do servidor do SQL do Azure no Edge como assinante (por exemplo, **myEdgeinstance,1433**) e o nome de um banco de dados na instância do SQL do Azure no Edge como banco de dados de destino (por exemplo, **AdventureWorks**).  

## <a name="see-also"></a>Confira também  

- [Criar uma publicação](https://docs.microsoft.com/sql/relational-databases/replication/publish/create-a-publication)
- [Criar uma Assinatura Push](https://docs.microsoft.com/sql/relational-databases/replication/create-a-push-subscription/)
- [Tipos de Replicação](https://docs.microsoft.com/sql/relational-databases/replication/types-of-replication)
- [Monitoramento (Replicação)](https://docs.microsoft.com/sql/relational-databases/replication/monitor/monitoring-replication)
- [Inicializar uma Assinatura](https://docs.microsoft.com/sql/relational-databases/replication/initialize-a-subscription)  


