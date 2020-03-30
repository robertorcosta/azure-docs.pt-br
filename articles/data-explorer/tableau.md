---
title: Use o conector ODBC do Azure Data Explorer para visualizar dados do Tableau
description: Neste artigo, você aprende a usar uma conexão ODBC (Open Database Connectivity, conectividade de banco de dados aberto) à conexão Azure Data Explorer para visualizar dados com o Tableau.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 903daf450800a7f060899d736c2b31920c1b51f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562438"
---
# <a name="visualize-data-from-azure-data-explorer-in-tableau"></a>Visualize dados do Azure Data Explorer no Tableau

 [Tableau](https://www.tableau.com/) é uma plataforma de análise visual para inteligência de negócios. Para se conectar ao Azure Data Explorer do Tableau e trazer dados de um cluster de amostra, use o driver SQL Server Open Database Connectivity (ODBC). 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte para concluir este artigo:

* [Conecte-se ao Azure Data Explorer com o ODBC](connect-odbc.md) usando o driver SQL Server ODBC, para conectar-se ao Azure Data Explorer do Tableau. 

* Tableau Desktop, versão completa ou [de teste.](https://www.tableau.com/products/desktop/download)

* Um cluster que inclui os dados de exemplo do StormEvents. Para obter mais informações, consulte [Criar um cluster e banco de dados do Azure Data Explorer](create-cluster-database-portal.md) e [ingerer dados de amostra no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="visualize-data-in-tableau"></a>Visualize dados no Tableau 

Depois de terminar de configurar o ODBC, você pode trazer dados de amostra para o Tableau.

1. No Tableau Desktop, no menu esquerdo, selecione **Outros bancos de dados (ODBC)**.

    ![Conecte-se ODBC](media/tableau/connect-odbc.png)

1. Em **Caso DSN,** selecione a fonte de dados criada para ODBC e selecione **"Entrar "Entrar".**

    ![Entrada da ODBC](media/tableau/odbc-sign-in.png)

1. Para **banco de dados,** selecione o banco de dados no cluster de amostras, como *TestDatabase*. Para **Esquema,** selecione *dbo*e para **Tabela,** selecione a tabela de amostras *StormEvents.*

    ![Selecione banco de dados e tabela](media/tableau/select-database-table.png)

1. Tableau agora mostra o esquema para os dados da amostra. Selecione **Atualizar agora** para levar os dados ao Tableau.

    ![Atualizar dados](media/tableau/update-data.png)

    Quando os dados são importados, o Tableau mostra linhas de dados semelhantes à imagem a seguir.

    ![Conjunto de resultados](media/tableau/result-set.png)

1. Agora você pode criar visualizações no Tableau com base nos dados que você trouxe do Azure Data Explorer. Para obter mais informações, consulte [Tableau Learning](https://www.tableau.com/learn).

## <a name="next-steps"></a>Próximas etapas

* [Escrever consultas para O Azure Data Explorer](write-queries.md)