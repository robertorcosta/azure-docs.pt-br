---
title: Usar o conector ODBC do Azure Data Explorer para visualizar dados do tableau
description: Neste artigo, você aprenderá a usar uma conexão ODBC (conectividade aberta de banco de dados) para o Azure Data Explorer conexão para visualizar dados com o tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562438"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualizar dados do Azure Data Explorer no tableau

 [Tableau](https://www.tableau.com/) é uma plataforma de análise visual para Business Intelligence. Para se conectar ao Azure Data Explorer do tableau e inserir dados de um cluster de exemplo, use o driver ODBC (Open Database Connectivity) do SQL Server. 

## <a name="prerequisites"></a>Prerequisites

Você precisará do seguinte para concluir este artigo:

* [Conecte-se ao azure data Explorer com ODBC](connect-odbc.md) usando o driver odbc do SQL Server, para se conectar ao Azure data Explorer do tableau. 

* Tableau desktop, completo ou versão de [avaliação](https://www.tableau.com/products/desktop/download) .

* Um cluster que inclui os dados de exemplo do StormEvents. Para obter mais informações, consulte [criar um cluster de data Explorer do Azure e um banco](create-cluster-database-portal.md) de dados e ingestão de informações de [exemplo no Azure data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualizar dados no tableau 

Depois de concluir a configuração do ODBC, você pode inserir dados de exemplo no tableau.

1. No tableau desktop, no menu à esquerda, selecione **outros bancos de dados (ODBC)** .

    ![Conecte-se ODBC](media/tableau/connect-odbc.png)

1. Para **DSN**, selecione a fonte de dados que você criou para ODBC e, em seguida, selecione **entrar**.

    ![Entrada ODBC](media/tableau/odbc-sign-in.png)

1. Para **banco de dados**, selecione o banco de dados no cluster de exemplo, como *TestDatabase*. Para **esquema**, selecione *dbo*e, para **tabela**, selecione a tabela de exemplo *StormEvents* .

    ![Selecionar Banco de dados e tabela](media/tableau/select-database-table.png)

1. Tableau agora mostra o esquema para os dados de exemplo. Selecione **Atualizar agora** para colocar os dados em tableau.

    ![Atualizar dados](media/tableau/update-data.png)

    Quando os dados são importados, o tableau mostra linhas de dados semelhantes à imagem a seguir.

    ![Conjunto de resultados](media/tableau/result-set.png)

1. Agora você pode criar visualizações no tableau com base nos dados que você colocou do Azure Data Explorer. Para obter mais informações, consulte [tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Próximas etapas

* [Gravar consultas para Azure Data Explorer](write-queries.md)