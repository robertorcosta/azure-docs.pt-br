---
title: 'SSMS: Conectar e pesquisar dados'
description: Saiba como se conectar a um banco de dados SQL no Azure usando o SSMS (SQL Server Management Studio). Em seguida, execute as instruções Transact-SQL (T-SQL) para consultar e editar dados.
keywords: conectar-se ao banco de dados sql, sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/10/2020
ms.openlocfilehash: 31bd47128a272e75d7021180b536fe6bf7420f55
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79299287"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure

Neste início rápido, você aprenderá a usar o SSMS (SQL Server Management Studio) para conectar-se a um Banco de Dados SQL do Azure e executar algumas consultas.

## <a name="prerequisites"></a>Pré-requisitos

A conclusão deste início rápido requer os seguintes itens:

- [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms/).
- O banco de dados de exemplo AdventureWorksLT. Se você precisar de uma cópia funcional do banco de dados AdventureWorksLT, crie uma concluindo o início rápido [Criar um banco de dados SQL do Azure](sql-database-single-database-get-started.md).
    - Os scripts neste artigo são escritos para usar o banco de dados AdventureWorksLT. Se você estiver usando uma instância gerenciada, precisará importar o banco de dados do Adventure Works para um banco de dados de instância ou modificar os scripts deste artigo para usar o banco de dados da Wide World Importers.

Se você simplesmente quiser executar algumas consultas ad hoc sem instalar o SSMS, confira [Início rápido: Usar o editor de consultas do portal do Azure para consultar um banco de dados SQL](sql-database-connect-query-portal.md).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao banco de dados. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon para concluir o início rápido.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **banco de dados SQL** ou **instâncias gerenciadas do SQL** que você deseja consultar.

3. Na página **Visão geral**, copie o nome do servidor totalmente qualificado. Ele está próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. O nome totalmente qualificado é semelhante a: *servername.database.windows.net*, exceto que ele tem o nome do servidor real.

## <a name="connect-to-your-database"></a>Conectar-se ao seu banco de dados

No SSMS, conecte-se ao servidor do Banco de Dados SQL do Azure.

> [!IMPORTANT]
> Um servidor do Banco de Dados SQL do Azure escuta na porta 1433. Para se conectar a um servidor do Banco de Dados SQL do Azure por trás de um firewall corporativo, essa porta do firewall precisa estar aberta.

1. Abra o SSMS.

2. A caixa de diálogo **Conectar-se ao Servidor** é exibida. Insira as seguintes informações:

   | Configuração      | Valor sugerido    | Descrição |
   | ------------ | ------------------ | ----------- |
   | **Tipo de servidor** | Mecanismo de banco de dados | Valor obrigatório. |
   | **Nome do servidor** | O nome do servidor totalmente qualificado | Algo como: **servername.database.windows.net**. |
   | **Autenticação** | Autenticação do SQL Server | Este tutorial usa a autenticação do SQL. |
   | **Logon** | ID de usuário da conta do administrador do servidor | A ID de usuário da conta do administrador do servidor usada para criar o servidor. |
   | **Senha** | Senha da conta do administrador do servidor | A senha da conta do administrador do servidor usada para criar o servidor. |
   ||||

   ![conectar-se ao servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Selecione **Opções** na caixa de diálogo **Conectar-se ao servidor**. No menu suspenso **Conectar-se ao banco de dados**, selecione **mySampleDatabase**. A conclusão do início rápido na seção [Pré-requisitos](#prerequisites) cria um banco de dados AdventureWorksLT chamado mySampleDatabase. Se sua cópia funcional do banco de dados AdventureWorks tiver um nome diferente de mySampleDatabase, selecione-a em vez disso.

   ![conectar o banco de dados no servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Selecione **Conectar**. A janela Pesquisador de Objetos se abre.

5. Para exibir objetos do banco de dados, expanda **Bancos de Dados** e, em seguida, expanda o nó do banco de dados.

   ![Objetos mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Consultar dados

Execute esse código Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) para consultar os 20 principais produtos por categoria.

1. No Pesquisador de Objetos, clique com o botão direito do mouse em **mySampleDatabase** e selecione **Nova Consulta**. Abre uma nova janela de consulta conectada ao banco de dados.

2. Na janela da consulta, cole a seguinte consulta SQL:

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. Na barra de ferramentas, selecione **Executar** para executar a consulta e recuperar dados das tabelas `Product` e `ProductCategory`.

    ![consulta para recuperar dados da tabela Product e ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

### <a name="insert-data"></a>Inserir dados

Execute esse código Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) para criar um produto na tabela `SalesLT.Product`.

1. Substitua a consulta anterior por esta.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Selecione **Executar** para inserir uma nova linha na tabela `Product`. O painel **Mensagens** é exibido **(1 linha afetada)** .

#### <a name="view-the-result"></a>Exibir o resultado

1. Substitua a consulta anterior por esta.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Selecione **Executar**. O seguinte resultado aparecerá.

   ![resultado da consulta de tabela Product](./media/sql-database-connect-query-ssms/result.png)

### <a name="update-data"></a>Atualizar dados

Execute este código Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) para modificar seu novo produto.

1. Substitua a consulta anterior por aquela que retorna o novo registro criado anteriormente:

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para atualizar a linha especificada na tabela `Product`. O painel **Mensagens** é exibido **(1 linha afetada)** .

### <a name="delete-data"></a>Excluir dados

Execute esse código Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) para remover o novo produto.

1. Substitua a consulta anterior por esta.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Selecione **Executar** para excluir a linha especificada na tabela `Product`. O painel **Mensagens** é exibido **(1 linha afetada)** .

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre o SSMS, confira [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Para se conectar e consultar usando o Portal do Azure, consulte [Conectar e consultar com o editor de consultas do SQL do Portal do Azure](sql-database-connect-query-portal.md).
- Para conectar e consultar usando o Visual Studio Code, veja [Conectar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Node.js, veja [Conectar e consultar com o Node.js](sql-database-connect-query-nodejs.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Python, veja [Conectar e consultar com o Python](sql-database-connect-query-python.md).
- Para conectar e consultar usando o Ruby, veja [Conectar e consultar com o Ruby](sql-database-connect-query-ruby.md).
