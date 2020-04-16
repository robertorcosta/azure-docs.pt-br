---
title: Conecte-se e consulte o Synapse SQL com o Visual Studio e o SSDT
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428557"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Conecte-se ao Synapse SQL com Visual Studio e SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [Sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Use o Visual Studio para consultar o pool SQL usando o Azure Synapse Analytics. Este método usa a extensão SSDT (SSDT) do SQL Server Data Tools (SSDT) no Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Ferramentas suportadas para SQL sob demanda (visualização)

O visual studio não é suportado atualmente para SQL sob demanda (visualização). No entanto, o Azure Data Studi (preview)o é uma ferramenta totalmente suportada. O SQL Server Management Studio é parcialmente suportado a partir da versão 18.4 e tem recursos limitados, como conexão e consulta.

## <a name="prerequisites"></a>Pré-requisitos
Para usar este tutorial, você precisa ter os seguintes componentes:

* Um pool SQL existente. Se você não tiver um, consulte [Criar um pool SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) para completar este pré-requisito.
* SSDT para Visual Studio. Se você tem o Visual Studio, provavelmente já tem esse componente. Para obter instruções e opções de instalação, confira [Instalar o Visual Studio e o SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* O nome de servidor SQL totalmente qualificado. Para encontrá-lo, consulte [Conectar-se ao pool SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. Conecte-se ao pool SQL
1. Abra o Visual Studio 2019.
2. Abra o SQL Server Object Explorer. Para fazer isso, **selecione Exibir** > **SQL Server Object Explorer**.
   
    ![Pesquisador de Objetos do SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Clique no ícone **Adicionar SQL Server** .
   
    ![Adicionar SQL Server](./media/get-started-visual-studio/add-server.png)
4. Preencha os campos na janela Conectar ao Servidor.
   
    ![Conectar-se ao servidor](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome do servidor**: Digite o nome do **servidor** previamente identificado.
   * **Autenticação**: Selecione **autenticação do servidor SQL** ou **autenticação integrada do diretório ativo:**
   * **Nome de usuário** e **senha**: Digite seu nome de usuário e senha se a autenticação do servidor SQL foi selecionada acima.
   * Clique em **Conectar**.
5. Para explorar, expanda seu servidor do SQL Azure. Você pode exibir os bancos de dados associados ao servidor. Expanda o AdventureWorksDW para ver as tabelas no banco de dados de exemplo.
   
    ![Explorar o AdventureWorksWeb](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Execute uma consulta de amostra
Agora que uma conexão foi estabelecida ao seu banco de dados, você escreverá uma consulta.

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
Agora que você pode conectar e consultar, tente [visualizar os dados com power bi](get-started-power-bi-professional.md).
Para configurar o ambiente para autenticação do Azure Active Directory, consulte [Authenticate to SQL pool](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 