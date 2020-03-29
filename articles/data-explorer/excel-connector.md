---
title: Visualize dados usando o conector Azure Data Explorer para Microsoft Excel
description: Neste artigo, você aprende a usar o conector Excel para o Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 42f52581d8f2f80deb5d6250ed54ab64fc1ba4d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849047"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-excel"></a>Visualize dados usando o conector Azure Data Explorer para Excel

O Azure Data Explorer oferece duas opções para se conectar a dados no Excel: usar o conector nativo ou importar uma consulta do Azure Data Explorer. Este artigo mostra como usar o conector nativo no Excel e conectar-se ao cluster Azure Data Explorer para obter e visualizar dados.

O conector nativo do Azure Data Explorer Excel oferece a capacidade de exportar resultados de consulta para o Excel. Você também pode adicionar uma consulta KQL como uma fonte de dados do Excel para cálculos adicionais ou visualizações.

## <a name="define-kusto-query-as-an-excel-data-source-and-load-the-data-to-excel"></a>Defina a consulta kusto como uma fonte de dados do Excel e carregue os dados para o Excel

1. Abra **o Microsoft Excel**.
1. Na guia **Dados,** **selecione Obter dados** > **do Azure** > **do Azure Data Explorer**.

    ![Obter dados do Azure Data Explorer](media/excel-connector/get-data-from-adx.png)

1. Na janela **Azure Data Explorer (Kusto),** complete os seguintes campos e selecione **OK**.

    ![Janela do Azure Data Explorer (Kusto)](media/excel-connector/adx-connection-window.png)
    
    |Campo   |Descrição |
    |---------|---------|
    |**Cluster**   |   Nome do cluster (obrigatório)      |    
    |**Database**     |    Nome do banco de dados      |    
    |**Nome da tabela ou consulta do Azure Data Explorer**    |     Nome da tabela ou consulta do Azure Data Explorer    | 
    
    **Opções avançadas:**

     |Campo   |Descrição |
    |---------|---------|
    |**Número de registro de resultado de consulta de limite**     |     Limitar o número de registros carregados no excel  |    
    |**Limitar o tamanho dos dados do resultado da consulta (bytes)**    |    Limitar o tamanho dos dados      |   
    |**Desativar a truncação definida por resultados**    |         |      
    |**Instruções adicionais de conjunto (separadas por ponto e vírgula)**    |    Adicionar `set` instruções para aplicar à fonte de dados     |   

1.  No painel **Navegador,** navegue até a tabela correta. No painel de visualização da tabela, selecione **Transformar dados** para fazer alterações em seus dados ou selecione **Carregar** para carregá-los no Excel.

![Janela de visualização da tabela](media/excel-connector/navigate-table-preview-window.png)

   > [!TIP]
   > Se o nome **do banco de dados** e/ou a consulta do **Azure Data Explorer** já estiverem especificados, o painel de visualização da tabela correta será aberto automaticamente. 

## <a name="analyze-and-visualize-data-in-excel"></a>Analisar e visualizar dados no Excel

Uma vez que os dados são carregados para se destacar e estão disponíveis em sua folha de Excel, você pode analisar, resumir e visualizar os dados criando relacionamentos e visuais. 

1.  Na guia Design de **tabela,** **selecione Resumir com Tabela Dinâmica**. Na **janela Criar tabela dinâmica,** selecione a tabela relevante e selecione **OK**.

    ![Criar tabela pivô](media/excel-connector/create-pivot-table.png)

1. No painel **Campos de Tabela Dinâmica,** selecione as colunas de tabela relevantes para criar tabelas de resumo. No exemplo abaixo, **EventId** e **State** são selecionados.
    
    ![Selecione campos de tabela dinâmica](media/excel-connector/pivot-table-pick-fields.png)

1. Na guia **'Análise de tabela dinâmica',** selecione **Gráfico dinâmica** para criar visuais com base na tabela. 

    ![Gráfico pivô](media/excel-connector/pivot-table-analyze-pivotchart.png)

1. No exemplo abaixo, use **Event Id,** **StartTime**e **EventType** para visualizar informações adicionais sobre os eventos climáticos.

    ![Visualizar dados](media/excel-connector/visualize-excel-data.png)

1. Crie painéis completos para monitorar seus dados.

## <a name="next-steps"></a>Próximas etapas

[Visualize dados usando uma consulta do Azure Data Explorer Kusto importada para o Microsoft Excel](excel-blank-query.md)