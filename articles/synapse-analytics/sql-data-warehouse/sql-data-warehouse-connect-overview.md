---
title: Conectar-se ao SQL Data Warehouse do Azure
description: Conecte-se ao SQL Data Warehouse do Azure.
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
ms.openlocfilehash: 72825f588ff92383858020cdbcd92c7de3078ed5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350653"
---
# <a name="connect-to-azure-sql-data-warehouse"></a>Conectar-se ao SQL Data Warehouse do Azure
Conecte-se ao SQL Data Warehouse do Azure.

## <a name="find-your-server-name"></a>Localizar o nome do servidor
O nome do servidor no exemplo a seguir é samplesvr.database.windows.net. Para localizar o nome de servidor totalmente qualificado:

1. Vá para o [portal Azure.][Azure portal]
2. Clique em **SQL Data Warehouses**.
3. Clique no data warehouse que você deseja conectar.
4. Localize o nome completo do servidor.
   
    ![Nome completo do servidor](media/sql-data-warehouse-connect-overview/server-connect.PNG)

## <a name="supported-drivers-and-connection-strings"></a>Drivers suportados e cadeias de conexão
SQL Data Warehouse do Azure dá suporte ao [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP], e [JDBC][JDBC]. Para encontrar a versão e a documentação mais recentes, clique em um dos drivers anteriores. Para gerar automaticamente a cadeia de conexão para o driver que você está usando no Portal do Azure, clique em **Mostrar cadeias de conexão de banco de dados** no exemplo anterior. A seguir também há alguns exemplos da aparência de uma cadeia de conexão para cada driver.

> [!NOTE]
> Considere definir o tempo limite de conexão para 300 segundos a fim de permitir que a conexão perdure em curtos períodos de indisponibilidade.
> 
> 

### <a name="adonet-connection-string-example"></a>Exemplo de cadeia de conexão do ADO.NET
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### <a name="odbc-connection-string-example"></a>Exemplo de cadeia de conexão do ODBC
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### <a name="php-connection-string-example"></a>Exemplo de cadeia de conexão do PHP
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### <a name="jdbc-connection-string-example"></a>Exemplo de cadeia de conexão do JDBC
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="connection-settings"></a>Configurações de conexão
O SQL Data Warehouse padroniza algumas configurações durante a conexão e a criação do objeto. Essas configurações não podem ser substituídas e incluem:

| Configuração de banco de dados | Valor |
|:--- |:--- |
| [ANSI_NULLS][ANSI_NULLS] |ATIVADO |
| [QUOTED_IDENTIFIERS][QUOTED_IDENTIFIERS] |ATIVADO |
| [DATEFORMAT][DATEFORMAT] |mda |
| [DATEFIRST][DATEFIRST] |7 |

## <a name="next-steps"></a>Próximas etapas
Para se conectar e consultar com o Visual Studio, veja [Consultar com o Visual Studio][Query with Visual Studio]. Para saber mais sobre as opções de autenticação, consulte [Autenticação no Azure SQL Data Warehouse][Authentication to Azure SQL Data Warehouse].

<!--Articles-->
[Query with Visual Studio]:sql-data-warehouse-query-visual-studio.md
[Authentication to Azure SQL Data Warehouse]:sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Azure portal]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/server-connect.PNG


