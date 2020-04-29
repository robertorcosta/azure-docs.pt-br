---
title: Conectar e consultar o SQL Synapse com o Visual Studio e o SSDT
description: Use o Visual Studio para consultar o pool SQL usando o Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428557"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Conectar-se ao Synapse SQL com o Visual Studio e o SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Use o Visual Studio para consultar o pool SQL usando o Azure Synapse Analytics. Esse método usa a extensão SQL Server Data Tools (SSDT) no Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ferramentas compatíveis com o SQL sob demanda (versão prévia)

No momento, o Visual Studio não tem suporte para SQL sob demanda (versão prévia). No entanto, o Azure data Studi (versão prévia) o é uma ferramenta totalmente suportada. O SQL Server Management Studio tem suporte parcial da versão 18,4 e tem recursos limitados, como conexão e consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para usar este tutorial, você precisa ter os seguintes componentes:

* Um pool SQL existente. Se você não tiver um, consulte [criar um pool SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para concluir este pré-requisito.
* SSDT para Visual Studio. Se você tiver o Visual Studio, provavelmente já tem esse componente. Para obter instruções e opções de instalação, confira [Instalar o Visual Studio e o SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* O nome de servidor SQL totalmente qualificado. Para encontrar isso, consulte [conectar-se ao pool do SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. conectar-se ao pool do SQL
1. Abra o Visual Studio 2019.
2. Abra o Pesquisador de Objetos do SQL Server. Para fazer isso, selecione **Exibir** > **pesquisador de objetos do SQL Server**.
   
    ![Pesquisador de Objetos do SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server** .
   
    ![Adicionar SQL Server](./media/get-started-visual-studio/add-server.png)
4. Preencha os campos na janela Conectar ao Servidor.
   
    ![Conectar-se ao servidor](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome do servidor**: Insira o **nome do servidor** identificado anteriormente.
   * **Autenticação**: selecione **autenticação de SQL Server** ou **Active Directory autenticação integrada**:
   * **Nome de Usuário** e **Senha**: Insira seu nome de usuário e senha se a Autenticação do SQL Server foi selecionada acima.
   * Clique em **Conectar**.
5. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.
   
    ![Explorar o AdventureWorksWeb](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. executar uma consulta de exemplo
Agora que uma conexão foi estabelecida com seu banco de dados, você escreverá uma consulta.

1. Clique com o botão direito do mouse em seu banco de dados no Gerenciador de Objetos do SQL Server.
2. Selecione **Nova Consulta**. Uma janela de nova consulta é aberta.
   
    ![Nova Consulta](./media/get-started-visual-studio/new-query2.png)
3. Copie a seguinte consulta T-SQL na janela de consulta:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Executa a consulta. Para fazer isso, clique na seta verde ou use este atalho: `CTRL`+`SHIFT`+`E`.
   
    ![Executar consulta](./media/get-started-visual-studio/run-query.png)
5. Examine os resultados da consulta. Neste exemplo, a tabela FactInternetSales tem 60398 linhas.
   
    ![Resultados da consulta](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Próximas etapas
Agora que você pode se conectar e consultar, tente [visualizar os dados com o Power BI](get-started-power-bi-professional.md).
Para configurar seu ambiente para Azure Active Directory autenticação, confira [autenticar no pool do SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 