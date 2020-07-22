---
title: Usar o Python para consultar um banco de dados
description: Este tópico mostra como usar o Python para criar um programa que se conecta a um banco de dados no Banco de Dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
titleSuffix: Azure SQL Database & SQL Managed Instance
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-python-october2019, sqldbrb=2, tracking-python
ms.devlang: python
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/29/2020
ms.openlocfilehash: 34d98905c9f068944ca9c149afa8fafa28a4cb68
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515063"
---
# <a name="quickstart-use-python-to-query-a-database-in-azure-sql-database-or-azure-sql-managed-instance"></a>Início Rápido: Usar o Python para consultar um banco de dados no Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Neste guia de início rápido, você usará o Python para se conectar ao Banco de Dados SQL do Azure ou na Instância Gerenciada de SQL do Azure e usará instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa de:

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

  | Ação | Banco de Dados SQL | Instância Gerenciada do SQL | SQL Server na VM do Azure |
  |:--- |:--- |:---|:---|
  | Criar| [Portal](single-database-create-quickstart.md) | [Portal](../managed-instance/instance-create-quickstart.md) | [Portal](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  || [CLI](scripts/create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/create-and-configure-database-powershell.md) | [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) | [PowerShell](../virtual-machines/windows/sql-vm-create-powershell-quickstart.md)
  | Configurar | [Regra de firewall de IP no nível do servidor](firewall-create-server-level-portal-quickstart.md)| [Conectividade de uma VM](../managed-instance/connect-vm-instance-configure.md)|
  |||[Conectividade do local](../managed-instance/point-to-site-p2s-configure.md) | [Conectar-se a uma Instância do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)
  |Carregar dados|Adventure Works carregado por guia de início rápido|[Restaurar o Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) | [Restaurar o Wide World Importers](../managed-instance/restore-sample-database-quickstart.md) |
  |||Restaurar ou importar o Adventure Works de um arquivo [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)| Restaurar ou importar o Adventure Works de um arquivo [BACPAC](database-import.md) do [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|
  |||

- [Python](https://python.org/downloads) 3 e software relacionado

  # <a name="macos"></a>[macOS](#tab/macos)

  Para instalar o Homebrew e o Python, o driver ODBC e o SQLCMD e o driver do Python para SQL Server, use as etapas **1.2**, **1.3** e **2.1** em [criar aplicativos Python usando o SQL Server no macOS](https://www.microsoft.com/sql-server/developer-get-started/python/mac/).

  Para obter mais informações, confira [Driver ODBC da Microsoft no macOS](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="ubuntu"></a>[Ubuntu](#tab/ubuntu)

  Para instalar o Python e outros pacotes necessários, use `sudo apt-get install python python-pip gcc g++ build-essential`.

  Para instalar o driver ODBC, o SQLCMD e o driver do Python para SQL Server, confira [configurar um ambiente para desenvolvimento em Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#linux).

  Para obter mais informações, confira [Driver ODBC da Microsoft no Linux](/sql/connect/odbc/linux-mac/installing-the-microsoft-odbc-driver-for-sql-server).

  # <a name="windows"></a>[Windows](#tab/windows)

  Para instalar o Python, o driver ODBC, o SQLCMD e o driver do Python para SQL Server, confira [configurar um ambiente para desenvolvimento em Python pyodbc](/sql/connect/python/pyodbc/step-1-configure-development-environment-for-pyodbc-python-development#windows).

  Para obter mais informações, confira [Driver ODBC da Microsoft](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server).

---

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar o banco de dados do **Adventure Works**.

> [!NOTE]
> Você pode optar por usar uma instância gerenciada de SQL do Azure.
>
> Para a criação e a configuração, use o [portal do Azure](../managed-instance/instance-create-quickstart.md), o [PowerShell](../managed-instance/scripts/create-configure-managed-instance-powershell.md) ou a [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) e configure a conectividade [local](../managed-instance/point-to-site-p2s-configure.md) ou da [VM](../managed-instance/connect-vm-instance-configure.md).
>
> Para carregar dados, confira [restaurar com o BACPAC](database-import.md) com o arquivo [Adventure Works](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works) ou [restaurar o banco de dados do Wide World Importers](../managed-instance/restore-sample-database-quickstart.md).

Para explorar ainda mais o Python e o banco de dados no Banco de Dados SQL do Azure, confira [Bibliotecas do Banco de Dados SQL do Azure para Python](/python/api/overview/azure/sql), o [repositório pyodbc](https://github.com/mkleehammer/pyodbc/wiki/) e um [exemplo de pyodbc](https://github.com/mkleehammer/pyodbc/wiki/Getting-started).

## <a name="get-server-connection-information"></a>Obter informações de conexão do servidor

Obtenha as informações de conexão necessárias para se conectar ao banco de dados no Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Acesse a página **Bancos de Dados SQL** ou **Instâncias Gerenciadas de SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado ao lado de **Nome do servidor** para obter um banco de dados no Banco de Dados SQL do Azure ou o nome do servidor totalmente qualificado (ou endereço IP) ao lado de **Host** para obter uma instância gerenciada de SQL do Azure ou um SQL Server na VM do Azure. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

> [!NOTE]
> Para obter informações de conexão do SQL Server na VM do Azure, confira [Conectar-se a uma instância do SQL Server](../virtual-machines/windows/sql-vm-create-portal-quickstart.md#connect-to-sql-server).

## <a name="create-code-to-query-your-database"></a>Criar um código para consultar o banco de dados 

1. Em um editor de texto, crie um novo arquivo chamado *sqltest.py*.  
   
1. Adicione o código seguinte: Substitua os seus valores de \<server>, \<database>, \<username> e \<password>.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 
   
   ```python
   import pyodbc
   server = '<server>.database.windows.net'
   database = '<database>'
   username = '<username>'
   password = '<password>'   
   driver= '{ODBC Driver 17 for SQL Server}'
   
   with pyodbc.connect('DRIVER='+driver+';SERVER='+server+';PORT=1433;DATABASE='+database+';UID='+username+';PWD='+ password) as conn:
       with conn.cursor() as cursor:
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

1. Verifique se as primeiras 20 linhas de Categoria/Produto são retornadas e, em seguida, feche a janela Comando.

## <a name="next-steps"></a>Próximas etapas

- [Criar seu primeiro banco de dados no Banco de Dados SQL do Azure](design-first-database-tutorial.md)
- [Drivers Python da Microsoft para SQL Server](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/)
- [Central de desenvolvedores do Python](https://azure.microsoft.com/develop/python/?v=17.23h)

