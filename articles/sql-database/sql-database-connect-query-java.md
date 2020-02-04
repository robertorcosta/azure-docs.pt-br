---
title: Usar o Java para consultar um banco de dados
description: Mostra como usar o Java para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-los usando instruções do T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: java
ms.topic: quickstart
author: ajlam
ms.author: andrela
ms.reviewer: v-masebo
ms.date: 03/25/2019
ms.custom: seo-java-july2019. seo-java-august2019
ms.openlocfilehash: 034f92ca3b7552373ae69148d09d58d3a5dd166a
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768656"
---
# <a name="quickstart-use-java-to-query-an-azure-sql-database"></a>Início Rápido: Como usar o Java para consultar um banco de dados SQL do Azure

Neste início rápido, você usará o Java para se conectar a um Banco de Dados SQL do Azure e usar instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um [Banco de Dados SQL do Azure](sql-database-single-database-get-started.md)
- Software relacionado a [Java](/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server)

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Instale o Homebrew, o Java e, em seguida, o Maven usando as etapas **1.2** e **1.3** em [Criar aplicativos Java usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Instale o Java, o Java Development Kit e, em seguida, o Maven usando as etapas **1.2**, **1.3** e **1.4** em [Criar aplicativos Java usando o SQL Server no Ubuntu](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Instale o Java e, em seguida, o Maven usando as etapas **1.2** e **1.3** em [Criar aplicativos Java usando o SQL Server no Windows](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).

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

2. Selecione **bancos de dados SQL** ou abra a página **Instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**. 

## <a name="create-the-project"></a>Criar o projeto

1. A partir do prompt de comando, crie um novo projeto Maven chamado *sqltest*.

    ```bash
    mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0" --batch-mode
    ```

1. Altere o diretório para *sqltest* e abra *pom.xml* com seu editor de texto preferido. Adicione o **Microsoft JDBC Driver para SQL Server** às suas dependências do projeto usando o código a seguir.

    ```xml
    <dependency>
        <groupId>com.microsoft.sqlserver</groupId>
        <artifactId>mssql-jdbc</artifactId>
        <version>7.0.0.jre8</version>
    </dependency>
    ```

1. Também no *pom.xml*, adicione as seguintes propriedades ao seu projeto. Se não tiver uma seção de propriedades, você pode adicioná-lo depois das dependências.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Salve e feche o *pom.xml*.

## <a name="add-code-to-query-database"></a>Adicionar código ao banco de dados de consulta

1. Você já deve ter um arquivo chamado *App.java* no seu projeto Maven localizado em:

   *..\sqltest\src\main\java\com\sqldbsamples\App.java*

1. Abra o arquivo e substitua o conteúdo pelo código a seguir. Depois adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

    ```java
    package com.sqldbsamples;

    import java.sql.Connection;
    import java.sql.Statement;
    import java.sql.PreparedStatement;
    import java.sql.ResultSet;
    import java.sql.DriverManager;

    public class App {

        public static void main(String[] args) {

            // Connect to database
            String hostName = "your_server.database.windows.net"; // update me
            String dbName = "your_database"; // update me
            String user = "your_username"; // update me
            String password = "your_password"; // update me
            String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;"
                + "hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
            Connection connection = null;

            try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName "
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";

                try (Statement statement = connection.createStatement();
                ResultSet resultSet = statement.executeQuery(selectSql)) {

                    // Print results from select statement
                    System.out.println("Top 20 categories:");
                    while (resultSet.next())
                    {
                        System.out.println(resultSet.getString(1) + " "
                            + resultSet.getString(2));
                    }
                    connection.close();
                }
            }
            catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    ```

   > [!NOTE]
   > O exemplo de código usa o banco de dados de modelo **AdventureWorksLT** para o SQL Azure.

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o aplicativo.

    ```bash
    mvn package -DskipTests
    mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
    ```

1. Verifique se as 20 linhas superiores são retornadas, depois feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)  

- [Microsoft JDBC Driver para SQL Server](https://github.com/microsoft/mssql-jdbc)  

- [Relatar problemas/fazer perguntas](https://github.com/microsoft/mssql-jdbc/issues)  
