---
title: Gerar uma cadeia de conexão com o PowerShell-banco de dados do Azure para PostgreSQL
description: Este artigo fornece um exemplo de Azure PowerShell para gerar uma cadeia de conexão para se conectar ao banco de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: a8e82c1863fde7a7580300606949e00a34c4e58c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87908129"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Como gerar uma cadeia de conexão do banco de dados do Azure para PostgreSQL com o PowerShell

Este artigo demonstra como gerar uma cadeia de conexão para um servidor de banco de dados do Azure para PostgreSQL. Você pode usar uma cadeia de conexão para se conectar a um banco de dados do Azure para PostgreSQL de vários aplicativos diferentes.

## <a name="requirements"></a>Requisitos

Este artigo usa os recursos criados no seguinte guia como um ponto de partida:

* [Início rápido: criar um servidor de banco de dados do Azure para PostgreSQL usando o PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Obtenha a cadeia de conexão

O `Get-AzPostgreSqlConnectionString` cmdlet é usado para gerar uma cadeia de conexão para conectar aplicativos ao banco de dados do Azure para PostgreSQL. O exemplo a seguir retorna a cadeia de conexão para um cliente PHP de **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Os valores válidos para o `Client` parâmetro incluem:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Personalizar os parâmetros de servidor do banco de dados do Azure para PostgreSQL usando o PowerShell](howto-configure-server-parameters-using-powershell.md)
