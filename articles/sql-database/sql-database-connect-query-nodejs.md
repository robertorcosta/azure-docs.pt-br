---
title: Usar o Node.js para consultar um banco de dados
description: Como usar o Node.js para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-lo usando instruções T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: nodejs
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: c0da38a41bf613237ea3b164d70e4729a7284ca7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "76768598"
---
# <a name="quickstart-use-nodejs-to-query-an-azure-sql-database"></a>Início Rápido: Use o Node.js para consultar um banco de dados SQL do Azure

Neste início rápido, você usará o Node.js para se conectar a um Banco de Dados SQL do Azure e usar instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um [Banco de Dados SQL do Azure](sql-database-single-database-get-started.md)
- Software relacionado ao [Node.js](https://nodejs.org)

  # <a name="macos"></a>[macOS](#tab/macos)

  Instale o Homebrew e o Node.js e, em seguida, instale o driver ODBC e o SQLCMD usando as etapas **1.2** e **1.3** em [Criar aplicativos Node.js usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/node/mac/).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Instale o Node.js e, em seguida, instale o driver ODBC e o SQLCMD usando as etapas **1.2** e **1.3** em [Criar aplicativos Node.js usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu/).

  # <a name="windows"></a>[Windows](#tab/windows)

  Instale o Chocolatey e o Node.js e, em seguida, instale o driver ODBC e o SQLCMD usando as etapas **1.2** e **1.3** em [Criar aplicativos Node.js usando o SQL Server no Windows](https://www.microsoft.com/sql-server/developer-get-started/node/windows/).

  ---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar o banco de dados do **Adventure Works**.

> [!NOTE]
> Você também pode optar por usar uma instância gerenciada do Azure SQL.
>
> Para criar e configurar, use o [portal do Azure](sql-database-managed-instance-get-started.md), o [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) ou a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) e, em seguida, configure a conectividade [local](sql-database-managed-instance-configure-p2s.md) ou de [VM](sql-database-managed-instance-configure-vm.md).
>
> Para carregar dados, confira [restaurar com o BACPAC](sql-database-import.md) com o arquivo [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou [restaurar o banco de dados do Wide World Importers](sql-database-managed-instance-get-started-restore.md).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**. 

## <a name="create-the-project"></a>Criar o projeto

Abra um prompt de comando e crie uma pasta chamada *sqltest*. Abra a pasta que você criou e execute o seguinte comando:

  ```bash
  npm init -y
  npm install tedious
  ```

## <a name="add-code-to-query-database"></a>Adicionar código ao banco de dados de consulta

1. Em seu editor de texto favorito, crie um novo arquivo, *sqltest.js*.

1. Substitua o conteúdo pelo código a seguir. Depois adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

    ```js
    const { Connection, Request } = require("tedious");

    // Create connection to database
    const config = {
      authentication: {
        options: {
          userName: "username", // update me
          password: "password" // update me
        },
        type: "default"
      },
      server: "your_server.database.windows.net", // update me
      options: {
        database: "your_database", //update me
        encrypt: true
      }
    };

    const connection = new Connection(config);

    // Attempt to connect and execute queries if connection goes through
    connection.on("connect", err => {
      if (err) {
        console.error(err.message);
      } else {
        queryDatabase();
      }
    });

    function queryDatabase() {
      console.log("Reading rows from the Table...");

      // Read all rows from table
      const request = new Request(
        `SELECT TOP 20 pc.Name as CategoryName,
                       p.name as ProductName
         FROM [SalesLT].[ProductCategory] pc
         JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid`,
        (err, rowCount) => {
          if (err) {
            console.error(err.message);
          } else {
            console.log(`${rowCount} row(s) returned`);
          }
        }
      );

      request.on("row", columns => {
        columns.forEach(column => {
          console.log("%s\t%s", column.metadata.colName, column.value);
        });
      });
      
      connection.execSql(request);
    }
    ```

> [!NOTE]
> O exemplo de código usa o banco de dados de modelo **AdventureWorksLT** para o SQL Azure.

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o programa.

    ```bash
    node sqltest.js
    ```

1. Verifique se as 20 linhas superiores são retornadas, depois feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Driver do Microsoft Node.js para SQL Server](/sql/connect/node-js/node-js-driver-for-sql-server)

- Conectar e consultar no Windows/Linux/macOS com [.NET core](sql-database-connect-query-dotnet-core.md), [Visual Studio Code](sql-database-connect-query-vscode.md) ou [SSMS](sql-database-connect-query-ssms.md) (somente Windows)

- [Introdução ao .NET Core no Windows/Linux/macOS usando a linha de comando](/dotnet/core/tutorials/using-with-xplat-cli)

- Projetar seu primeiro banco de dados SQL do Azure usando [.NET](sql-database-design-first-database-csharp.md) ou [SSMS](sql-database-design-first-database.md)
