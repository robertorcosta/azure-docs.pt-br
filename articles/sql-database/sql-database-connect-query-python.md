---
title: Usar o Python para consultar um banco de dados
description: Este tópico mostra como usar o Python para criar um programa que se conecta a um Banco de Dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: e82f8feae0096202e48a58296dd2e9d21bb61885
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768566"
---
# <a name="quickstart-use-python-to-query-an-azure-sql-database"></a>Início Rápido: Usar Python para consultar um banco de dados SQL do Azure

Neste início rápido, você usará o Python para se conectar a um Banco de Dados SQL do Azure e usar instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um [Banco de Dados SQL do Azure](sql-database-single-database-get-started.md)
- [Python](https://python.org/downloads) 3 e software relacionado

  # <a name="macostabmacos"></a>[macOS](#tab/macos)

  Para instalar o Homebrew e o Python, o driver ODBC e o SQLCMD e o driver do Python para SQL Server, use as etapas **1.2**, **1.3** e **2.1** em [criar aplicativos Python usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Para obter mais informações, confira [Microsoft ODBC Driver no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntutabubuntu"></a>[Ubuntu](#tab/ubuntu)

  Para instalar o Python e outros pacotes necessários, use `sudo apt-get install python python-pip gcc g++ build-essential`.

  Para instalar o driver ODBC, o SQLCMD e o driver do Python para SQL Server, confira [configurar um ambiente para desenvolvimento em Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Para obter mais informações, confira [Microsoft ODBC Driver no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windowstabwindows"></a>[Windows](#tab/windows)

  Para instalar o Python, o driver ODBC, o SQLCMD e o driver do Python para SQL Server, confira [configurar um ambiente para desenvolvimento em Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Para obter mais informações, confira [Microsoft ODBC Driver](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar o banco de dados do **Adventure Works**.

> [!NOTE]
> Você também pode optar por usar uma instância gerenciada do Azure SQL.
>
> Para criar e configurar, use o [portal do Azure](sql-database-managed-instance-get-started.md), o [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) ou a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) e, em seguida, configure a conectividade [local](sql-database-managed-instance-configure-p2s.md) ou de [VM](sql-database-managed-instance-configure-vm.md).
>
> Para carregar dados, confira [restaurar com o BACPAC](sql-database-import.md) com o arquivo [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou [restaurar o banco de dados do Wide World Importers](sql-database-managed-instance-get-started-restore.md).

Para explorar ainda mais o Python e o Banco de Dados SQL do Azure, confira [Bibliotecas do Banco de Dados SQL do Azure para Python](/python/api/overview/azure/sql), o [repositório pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e um [exemplo de pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu Banco de Dados SQL 

1. Em um editor de texto, crie um novo arquivo chamado *sqltest.py*.  
   
1. Adicione o código seguinte: Substitua seus próprios valores para \<server>, \<database>, \<username> e \<password>.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'
   driver= '{ODBC Driver 17 for SQL Server}'
   cnxn = pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password)
   cursor = cnxn.cursor()
   cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
   row = cursor.fetchone()
   while row:
       print (str(row[0]) + " " + str(row[1]))
       row = cursor.fetchone()
   ```
   

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o comando a seguir:

   ```cmd
   python sqltest.py
   ```

1. Verifique se as primeiras 20 linhas de Categoria/Produto são retornadas e feche a janela Comando.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Drivers Python da Microsoft para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Central de desenvolvedores do Python](https://azure.microsoft.com/develop/python/?v=17.23h)

