---
title: Conecte-se com sqlcmd
description: Use o utilitário de linha de comando sqlcmd para conectar e consultar um pool Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 02157ca0d32d2347e50cc84a5c52e9c47b0f33b5
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745190"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>Conecte-se à piscina Synapse SQL com sqlcmd

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Aprendizado de máquina do Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Use o utilitário de linha de comando [sqlcmd][sqlcmd] para conectar e consultar um pool SQL.  

## <a name="1-connect"></a>1. Conecte

Para começar com [sqlcmd][sqlcmd], abra o prompt de comando e digite **sqlcmd** seguido pela seqüência de conexões para o banco de dados do pool SQL. A cadeia de conexão precisará dos seguintes parâmetros:

* **Servidor (-S):** Servidor no formato `<`Nome do Servidor`>`.database.windows.net
* **Banco de dados (-d):** Nome do banco de dados.
* **Habilitar identificadores citados (-I):** Os identificadores citados devem ser habilitados para se conectar a uma instância de pool SQL.

Para usar a Autenticação do SQL Server, você precisa adicionar os parâmetros do nome de usuário/senha:

* **Usuário (-U):** usuário do servidor no formato `<`Usuário`>`
* **Senha (-P):** senha associada ao usuário.

Por exemplo, a cadeia de conexão pode parecer com o seguinte:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Para usar a autenticação Integrada do Azure Active Directory, você precisa adicionar os parâmetros do Azure Active Directory:

* **Autenticação do Azure Active Directory (-G):** usar o Azure Active Directory para a autenticação

Por exemplo, a cadeia de conexão pode parecer com o seguinte:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Você precisa [habilitar a Autenticação do Azure Active Directory](sql-data-warehouse-authentication.md) para autenticar usando o Active Directory.

## <a name="2-query"></a>2. Consulta

Após a conexão, você pode executar quaisquer instruções Transact-SQL compatíveis com a instância.  Neste exemplo, as consultas são enviadas no modo interativo.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

Os próximos exemplos mostram como você pode executar as consultas no modo de lote usando a opção -Q ou direcionando o SQL para sqlcmd.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais detalhes sobre as opções disponíveis em sqlcmd, consulte [a documentação sqlcmd](/sql/tools/sqlcmd-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).
