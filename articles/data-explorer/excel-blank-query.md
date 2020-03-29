---
title: Visualize dados usando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel
description: Neste artigo, você aprende como importar uma consulta do Azure Data Explorer Kusto para o Microsoft Excel.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 4999000e2084922b43b8085034f545d4b5c644a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849081"
---
# <a name="visualize-data-using-an-azure-data-explorer-kusto-query-imported-into-microsoft-excel"></a>Visualize dados usando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel

O Azure Data Explorer oferece duas opções para se conectar a dados no Excel: usar o conector nativo ou importar uma consulta do Azure Data Explorer. Este artigo mostra como importar uma consulta do Azure Data Explorer para o Excel para visualizar dados. Adicione a consulta Kusto como uma fonte de dados do Excel para fazer cálculos ou visualizações adicionais nos dados.

## <a name="prerequisites"></a>Pré-requisitos

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de e-mail organizacional que é um membro do diretório Azure Active, para que você possa se conectar ao cluster de ajuda do [Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/Samples) 
<br>ou</br>
* Crie [um cluster de teste e banco de dados](create-cluster-database-portal.md) e faça login no aplicativo [Azure Data Explorer Web UI](https://dataexplorer.azure.com/).

## <a name="define-kusto-query-as-an-excel-data-source"></a>Defina a consulta kusto como uma fonte de dados do Excel

1. Na [Azure Data Explorer Web UI](https://dataexplorer.azure.com/clusters/help/databases/Samples), execute a consulta e verifique os resultados.

1. Selecione a guia **Compartilhar** e selecione **Consulta ao POWER BI**.

    ![Consulta da Web UI ao Power BI](media/excel-blank-query/web-ui-query-to-powerbi.png)

1. Uma janela é exibida com a seguinte notificação:

    ![consulta de exportação para área de transferência](media/excel-blank-query/query-exported-to-clipboard.png)

1. Abra **o Microsoft Excel**.

1. Na guia **Dados,** **selecione Obter dados** > **de outras fontes** > **consulta em branco**.

    ![Obter dados e selecionar consulta em branco](media/excel-blank-query/get-data-blank-query.png)

1. A janela **Power Query Editor** é aberta. Na janela, selecione **Editor Avançado**.

    ![Janela do editor de consulta de energia](media/excel-blank-query/power-query-editor.png)

1. Na janela **Editor Avançado,** cole a consulta que você exportou para a área de transferência e selecione **Feito**.

    ![Consulta avançada do editor](media/excel-blank-query/advanced-editor-query.png)    

1. Para autenticar, selecione **Editar credenciais**.

    ![Editar credenciais](media/excel-blank-query/edit-credentials.png)

1. Selecione **Conta Organizacional** e **Login**. Complete o processo de login e selecione **Conectar**.

    ![Login completo](media/excel-blank-query/complete-sign-in.png)

    Repita as etapas anteriores para adicionar mais consultas. Você pode renomear as consultas para nomes mais significativos.

1. Selecione o botão **'Carregar &',** para obter seus dados no Excel.

    ![Selecione fechar e carregar](media/excel-blank-query/close-and-load.png)

1. Agora seus dados estão no Excel. Selecione o botão **Atualizar** para atualizar a consulta.

    ![Ver dados no excel](media/excel-blank-query/data-in-excel.png)

## <a name="next-steps"></a>Próximas etapas

[Visualize dados usando o conector Azure Data Explorer para Excel](excel-connector.md)