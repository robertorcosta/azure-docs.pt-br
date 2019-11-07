---
title: Conectar-se com o sqlcmd
description: Use o utilitário de linha de comando sqlcmd para conectar-se ao SQL Data Warehouse do Azure e fazer uma consulta.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e4b432e0be0cdded5089965b9d272aa82e31bd36
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685743"
---
# <a name="connect-to-sql-data-warehouse-with-sqlcmd"></a>Conecte-se ao SQL Data Warehouse com sqlcmd
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use o utilitário de linha de comando [sqlcmd][sqlcmd] para se conectar e consultar um SQL data warehouse do Azure.  

## <a name="1-connect"></a>1. conectar
Para começar com o [sqlcmd][sqlcmd], abra o prompt de comando e digite **sqlcmd** seguido da cadeia de conexão do banco de dados SQL Data Warehouse. A cadeia de conexão precisará dos seguintes parâmetros:

* **Servidor (-S):** Servidor no formato `<`Nome do Servidor`>`.database.windows.net
* **Banco de dados (-d):** nome do banco de dados.
* **Habilitar Identificadores com Cotas (-I):** os identificadores com cotas devem ser habilitados para conectarem uma instância do SQL Data Warehouse.

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
> 
> 

## <a name="2-query"></a>2. consulta
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
Consulte a [documentação do sqlcmd][sqlcmd] para obter mais detalhes sobre as opções disponíveis no sqlcmd.

<!--Image references-->

<!--Article references-->

<!--MSDN references--> 
[sqlcmd]: https://msdn.microsoft.com/library/ms162773.aspx
[Azure portal]: https://portal.azure.com

<!--Other Web references-->
