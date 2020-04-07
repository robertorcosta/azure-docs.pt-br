---
title: Conectar-se ao SSMS
description: Use o SQL Server Management Studio (SSMS) para conectar e consultar o Azure Synapse Analytics.
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
ms.openlocfilehash: 0d65ea9b684439cab384bbaa09fcdef1302b6bcf
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743852"
---
# <a name="connect-to-azure-synapse-analytics-with-sql-server-management-studio-ssms"></a>Conecte-se ao Azure Synapse Analytics com o SSMS (SSMS)

> [!div class="op_single_selector"]
>
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Aprendizado de máquina do Azure](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [Sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md)
> * [SSMS](sql-data-warehouse-query-ssms.md)

Use o SQL Server Management Studio (SSMS) para conectar e consultar um data warehouse dentro do Azure Synapse.

## <a name="prerequisites"></a>Pré-requisitos

Para usar este tutorial, você precisará de:

* Um pool SQL existente. Para criar um, consulte [Criar um pool SQL](create-data-warehouse-portal.md).
* SSMS (SQL Server Management Studio) instalado. [Baixe SSMS](/sql/ssms/download-sql-server-management-studio-ssms?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) gratuitamente se você ainda não tiver.
* O nome de servidor SQL totalmente qualificado. Para encontrar essas informações, consulte [Conecte-se ao pool SQL](sql-data-warehouse-connect-overview.md).

## <a name="1-connect-to-your-sql-pool"></a>1. Conecte-se ao seu pool SQL

1. Abra o SSMS.
2. Abra o Object Explorer selecionando **File** > **Connect Object Explorer**.

    ![Pesquisador de Objetos do SQL Server](./media/sql-data-warehouse-query-ssms/connect-object-explorer.png)
3. Preencha os campos na janela Conectar ao Servidor.

   ![Conectar-se ao servidor](./media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)

   * **Nome do servidor**. Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**. Selecione **Autenticação do SQL Server** ou **Autenticação Integrada do Active Directory**.
   * **Nome de Usuário** e **Senha**. Insira o nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
4. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.

   ![Explorar o AdventureWorksWeb](./media/sql-data-warehouse-query-ssms/explore-tables.png)

## <a name="2-run-a-sample-query"></a>2. Execute uma consulta de amostra

Agora que uma conexão foi estabelecida com o banco de dados, escreveremos uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma janela de nova consulta é aberta.

   ![Nova Consulta](./media/sql-data-warehouse-query-ssms/new-query.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:

   ```sql
   SELECT COUNT(*) FROM dbo.FactInternetSales;
   ```

4. Execute a consulta clicando `Execute` ou use `F5`o seguinte atalho: .

   ![Executar consulta](./media/sql-data-warehouse-query-ssms/execute-query.png)
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.

   ![Resultados da consulta](./media/sql-data-warehouse-query-ssms/results.png)

## <a name="next-steps"></a>Próximas etapas

Agora que você pode conectar e consultar, tente [visualizar os dados com power bi](sql-data-warehouse-get-started-visualize-with-power-bi.md). Para configurar o ambiente para autenticação do Azure Active Directory, consulte [Authenticate to SQL pool](sql-data-warehouse-authentication.md).
