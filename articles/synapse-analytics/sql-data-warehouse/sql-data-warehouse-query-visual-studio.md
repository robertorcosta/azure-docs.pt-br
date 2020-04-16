---
title: Conecte-se com o VSTS
description: Consulta Azure Synapse Analytics com Visual Studio.
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/15/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 174ee07e389e598fed6ed8487e60303fbce81f77
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416042"
---
# <a name="connect-to-azure-synapse-analytics-with-visual-studio-and-ssdt"></a>Conecte-se ao Azure Synapse Analytics com visual studio e SSDT
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](../sql/get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Use o Visual Studio para consultar uma piscina SQL dentro do Azure Synapse em apenas alguns minutos. Este método usa a extensão SSDT (SSDT) do SQL Server Data Tools (SSDT) no Visual Studio 2019. 

## <a name="prerequisites"></a>Pré-requisitos
Para usar este tutorial, você precisará de:

* Um pool SQL existente. Para criar um, consulte [Criar um pool SQL](create-data-warehouse-portal.md).
* SSDT para Visual Studio. Se você tem o Visual Studio, provavelmente já tem SSDT para Visual Studio. Para obter instruções e opções de instalação, confira [Instalar o Visual Studio e o SSDT](sql-data-warehouse-install-visual-studio.md).
* O nome de servidor SQL totalmente qualificado. Para encontrar essas informações, consulte [Conecte-se ao pool SQL](../sql/connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Conecte-se ao seu pool SQL
1. Abra o Visual Studio 2019.
2. Abra o SQL Server Object Explorer selecionando **View** > **SQL Server Object Explorer**.
   
    ![Pesquisador de Objetos do SQL Server](./media/sql-data-warehouse-query-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server** .
   
    ![Adicionar SQL Server](./media/sql-data-warehouse-query-visual-studio/add-server.png)
4. Preencha os campos na janela Conectar ao Servidor.
   
    ![Conectar-se ao servidor](./media/sql-data-warehouse-query-visual-studio/connection-dialog.png)
   
   * **Nome do servidor**. Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Usuário** e **Senha**. Insira o nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
5. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.
   
    ![Explorar o AdventureWorksWeb](./media/sql-data-warehouse-query-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Execute uma consulta de amostra
Agora que uma conexão foi estabelecida com o banco de dados, escreveremos uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma janela de nova consulta é aberta.
   
    ![Nova Consulta](./media/sql-data-warehouse-query-visual-studio/new-query2.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Execute a consulta clicando na seta verde `CTRL` + `SHIFT` +ou use o seguinte atalho: `E`.
   
    ![Executar consulta](./media/sql-data-warehouse-query-visual-studio/run-query.png)
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](./media/sql-data-warehouse-query-visual-studio/query-results.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você pode conectar e consultar, tente [visualizar os dados com power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md).

Para configurar o ambiente para autenticação do Azure Active Directory, consulte [Authenticate to SQL pool](sql-data-warehouse-authentication.md).
