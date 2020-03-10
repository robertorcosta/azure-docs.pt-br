---
title: 'Início Rápido: Configurar isolamento da carga de trabalho – T-SQL'
description: Use o T-SQL para configurar o isolamento da carga de trabalho.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bd3ad98116b18a77a77e8f6f327689d0ebb7dd21
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200508"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Início Rápido: Configurar isolamento da carga de trabalho com o T-SQL

Neste guia de início rápido, você criará rapidamente um classificador e um grupo de cargas de trabalho para reservar recursos para o carregamento de dados. O grupo de cargas de trabalho alocará 20% dos recursos do sistema para carregamento de dados.  O classificador de cargas de trabalho atribuirá solicitações ao grupo de cargas de trabalho do carregamento de dados.  Com 20% de reserva para carregamento de dados, esses recursos garantem a conformidade com os SLAs.

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

> [!NOTE]
> A criação de uma instância de Análise de SQL no Azure Synapse Analytics pode resultar em um novo serviço faturável.  Para obter mais informações, confira [Preços do Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Pré-requisitos
 
Este início rápido pressupõe que você já tem uma instância de Análise de SQL no Azure Synapse e que você tem permissões CONTROL DATABASE. Se precisar, use [Criar e conectar – portal](create-data-warehouse-portal.md) para criar um data warehouse chamado **mySampleDataWarehouse**.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>Criar logon para carregamento de dados

Crie um logon de autenticação do SQL Server no banco de dados `master` usando [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) para "ELTLogin".

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Criar usuário

[Create user](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "ELTLogin", no mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Criar um grupo de cargas de trabalho
Crie um [grupo de cargas de trabalho](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) com 20% de reserva para carregamento de dados.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Criar um classificador de carga de trabalho

Crie um [classificador de cargas de trabalho](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) a fim de mapear o ELTLogin para o grupo de cargas de trabalho do carregamento de dados.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Exibir grupos de cargas de trabalho e classificadores e valores de runtime existentes

```sql
--Workload groups
SELECT * FROM 
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM 
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM 
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Limpar os recursos

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Você está sendo cobrado por unidades de data warehouse e pelos dados armazenados em seu data warehouse. Esses recursos de computação e armazenamento são cobrados separadamente.

- Se desejar manter os dados no armazenamento, será possível pausar a computação quando você não estiver usando o pool de SQL. Ao pausar a computação, você será cobrado apenas pelo armazenamento de dados. Quando você estiver pronto para trabalhar com os dados, retome a computação.
- Se desejar remover encargos futuros, será possível excluir o data warehouse.

Siga estas etapas para limpar os recursos.

1. Entre no [portal do Azure](https://portal.azure.com) e selecione seu data warehouse.

    ![Limpar os recursos](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Para pausar a computação, selecione o botão **Pausar**. Quando o data warehouse for pausado, você verá um botão **Iniciar**.  Para retomar a computação, selecione **Iniciar**.

3. Para remover o data warehouse para não ser cobrado pela computação ou pelo armazenamento, selecione **Excluir**.

4. Para remover o SQL Server criado, selecione **mynewserver-20180430.database.windows.net** na imagem anterior e, em seguida, selecione **Excluir**.  Tenha cuidado com essa exclusão, uma vez que a exclusão do servidor também exclui todos os bancos de dados atribuídos ao servidor.

5. Para remover o grupo de recursos, selecione **myResourceGroup** e, em seguida, **Excluir grupo de recursos**.

## <a name="next-steps"></a>Próximas etapas

- Você acabou de criar um grupo de cargas de trabalho. Execute algumas consultas como ELTLogin para ver o desempenho delas. Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir as consultas e o grupo de cargas de trabalho atribuído.
- Para saber mais sobre o gerenciamento de cargas de trabalho de Análise de SQL, confira [Gerenciamento de Cargas de Trabalho](sql-data-warehouse-workload-management.md) e [Isolamento de Cargas de Trabalho](sql-data-warehouse-workload-isolation.md).
