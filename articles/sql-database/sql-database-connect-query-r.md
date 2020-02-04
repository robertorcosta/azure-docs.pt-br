---
title: Usar o R com os Serviços do Machine Learning para consultar um banco de dados (versão prévia)
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Este artigo mostra como usar um script R com os Serviços do Machine Learning do Banco de Dados SQL do Azure para conectar-se a um Banco de dados SQL do Azure e consultá-lo usando instruções Transact-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: python
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph, carlrab
manager: cgronlun
ms.date: 05/29/2019
ms.openlocfilehash: 7103afc29e4021d950d9a3634b190f4439ecfe8d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76768504"
---
# <a name="quickstart-use-r-with-machine-learning-services-to-query-an-azure-sql-database-preview"></a>Início Rápido: Usar o R com os Serviços do Machine Learning para consultar um Banco de Dados SQL do Azure (versão prévia)

Neste início rápido, você usará o R com os Serviços do Machine Learning para se conectar a um Banco de Dados SQL do Azure e usar as instruções T-SQL para consultar dados.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Prerequisites

- Uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Um [Banco de Dados SQL do Azure](sql-database-single-database-get-started.md)
- [Serviços do Machine Learning](sql-database-machine-learning-services-overview.md) com R habilitado. [Inscrever-se na versão prévia](sql-database-machine-learning-services-overview.md#signup).
- SSMS ([SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms))

> [!IMPORTANT]
> Os scripts deste artigo são escritos para usar o banco de dados do **Adventure Works**.

> [!NOTE]
> Durante a versão prévia pública, a Microsoft integrará você e habilitará o aprendizado de máquina para seu banco de dados novo ou existente; no entanto, não há mais suporte para a opção de implantação da instância gerenciada no momento.

Os Serviços do Machine Learning com R são um recurso do Banco de Dados SQL do Azure, usado para executar scripts do R no banco de dados. Para obter mais informações, confira o [Projeto R](https://www.r-project.org/).

## <a name="get-sql-server-connection-information"></a>Obter informações de conexão do SQL Server

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor ou do host, do nome do banco de dados e das informações de logon nos próximos procedimentos.

1. Entre no [portal do Azure](https://portal.azure.com/).

2. Navegue até a página **bancos de dados SQL** ou **instâncias gerenciadas do SQL**.

3. Na página **Visão geral**, examine o nome do servidor totalmente qualificado próximo ao **Nome do servidor** para um banco de dados individual ou o nome do servidor totalmente qualificado próximo ao **Host** para instância gerenciada. Para copiar o nome do servidor ou o nome do host, passe o mouse sobre ele e selecione o ícone **Copiar**.

## <a name="create-code-to-query-your-sql-database"></a>Criar código para consultar seu Banco de Dados SQL

1. Abra o **SQL Server Management Studio** e conecte-se ao seu banco de dados SQL.

   Se você precisar de ajuda para se conectar, veja o [Início Rápido: Usar o SQL Server Management Studio para conectar e consultar um Banco de Dados SQL do Azure](sql-database-connect-query-ssms.md).

1. Passar o script R completo para o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

   O script é passado por meio do argumento `@script`. Tudo dentro do argumento `@script` precisa ser código R válido.
   
   >[!IMPORTANT]
   >O código neste exemplo usa os dados de exemplo AdventureWorksLT, que você pode escolher como fonte durante a criação de seu banco de dados. Se o banco de dados tiver dados diferentes, use tabelas de seu próprio banco de dados na consulta SELECT. 

    ```sql
    EXECUTE sp_execute_external_script
    @language = N'R'
    , @script = N'OutputDataSet <- InputDataSet;'
    , @input_data_1 = N'SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid'
    ```

   > [!NOTE]
   > Se você receber erros, talvez a versão prévia pública dos Serviços de Machine Learning (com R) não esteja habilitada para seu banco de dados SQL. Veja os [Pré-requisitos](#prerequisites) acima.

## <a name="run-the-code"></a>Executar o código

1. Execute o procedimento armazenado [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql).

1. Verifique se as primeiras 20 linhas de Categoria/Produto são retornadas na janela **Mensagens**.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Serviços do Machine Learning do Banco de Dados SQL do Azure (com o R)](sql-database-machine-learning-services-overview.md)
- [Criar e executar scripts R simples em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-quickstart-r-create-script.md)
- [Escrever funções do R avançadas em Serviços do Machine Learning do Banco de Dados SQL do Azure (versão prévia)](sql-database-machine-learning-services-functions.md)
