---
title: Visualize dados usando o conector do Azure Data Explorer para Power BI
description: 'Neste artigo, você aprenderá a usar uma das três opções para visualizar dados no Power BI: o conector do Power BI para o Data Explorer do Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 361ea6ed76207e8e9721f64df61738b6cd9631dc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74024225"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Visualize dados usando o conector do Azure Data Explorer para Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Power BI é uma solução de análise de negócios que permite que você visualize os dados e compartilhar os resultados na sua organização. O Azure Data Explorer fornece três opções para se conectar a dados no Power BI: usar o conector interno, importar uma consulta do Azure Data Explorer ou usar uma consulta SQL. Este artigo mostra como usar o conector interno para obter dados e visualizá-los em um relatório Power BI. Usar o conector nativo do Azure Data Explorer para criar painéis de Power BI é simples. O conector de Power BI dá suporte aos [modos de conectividade de importação e consulta direta](https://docs.microsoft.com/power-bi/desktop-directquery-about). Você pode criar painéis usando o modo de **importação** ou **DirectQuery** dependendo do cenário, da escala e dos requisitos de desempenho. 

## <a name="prerequisites"></a>pré-requisitos

Você precisará do seguinte para concluir este artigo:

* Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.
* Uma conta de email organizacional que seja membro do Azure Active Directory, de modo que você possa se conectar ao [Cluster de ajuda do Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecione **DOWNLOAD GRATUITO**)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados do Azure Data Explorer

Primeiro, conecte-se ao cluster de ajuda do Azure Data Explorer, depois use um subconjunto dos dados da tabela de *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. No Power BI Desktop, na guia **Início**, selecione **Obter Dados** e depois **Mais**.

    ![Obter dados](media/power-bi-connector/get-data-more.png)

1. Pesquise *Data Explorer do Azure*, selecione **Azure data Explorer** , em seguida, **conectar**.

    ![Pesquisar e obter dados](media/power-bi-connector/search-get-data.png)

1. Na tela **Data Explorer do Azure (Kusto)** , preencha o formulário com as informações a seguir.

    ![Cluster, o banco de dados, opções de tabela](media/power-bi-connector/cluster-database-table.png)

    **Configuração** | **Valor** | **Descrição do campo**
    |---|---|---|
    | HDInsight | *https://help.kusto.windows.net* | A URL do cluster de ajuda. Para outros clusters, a URL está no formato *https://\<NomeCluster\>.\<Região\>.kusto.windows.net*. |
    | Banco de dados | Deixar em branco | Um banco de dados que está hospedado no cluster ao qual você está se conectando. Vamos selecioná-lo em uma etapa posterior. |
    | Nome da tabela | Deixar em branco | Uma das tabelas no banco de dados, ou uma consulta como <code>StormEvents \| take 1000</code>. Vamos selecioná-lo em uma etapa posterior. |
    | Opções Avançadas | Deixar em branco | Opções para suas consultas, como tamanho de conjunto de resultados. |
    | Modo de conectividade de dados | *DirectQuery* | Determina se o Power BI importa os dados ou se conecta diretamente à fonte de dados. Você pode usar uma das opções com esse conector. |
    | | | |
    
    > [!NOTE]
    > No modo de **importação** , os dados são movidos para Power bi. No modo **DirectQuery** , os dados são consultados diretamente do cluster de data Explorer do Azure.
    >
    > Use o modo de **importação** quando:
    > * O conjunto de dados é pequeno.
    > * Você não precisa de dados quase em tempo real. 
    > * Seus dados já estão agregados ou você executa a [agregação em Kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Use o modo **DirectQuery** quando:
    > * Seu conjunto de dados é muito grande. 
    > * Você precisa de dados quase em tempo real.   

1. Caso ainda não tenha uma conexão para o cluster de ajuda, entre. Entre com uma conta organizacional, depois selecione **Conectar**.

    ![Entrar](media/power-bi-connector/sign-in.png)

1. Na tela **Navegador**, expanda o banco de dados de **Exemplos**, selecione **StormEvents** e **Editar**.

    ![Selecionar tabela](media/power-bi-connector/select-table.png)

    A tabela é aberta no Editor do Power Query, onde é possível editar linhas e colunas antes de importar os dados.

1. No Editor do Power Query, selecione a seta ao lado da coluna **DamageCrops**, depois **Classificar em ordem decrescente**.

    ![Classificar DamageCrops em ordem decrescente](media/power-bi-connector/sort-descending.png)

1. Na guia **Página inicial**, selecione **Manter Linhas**, depois **Manter Primeiras Linhas**. Insira um valor de *1000* para trazer as primeiras 1000 linhas da tabela classificada.

    ![Manter linhas superiores](media/power-bi-connector/keep-top-rows.png)

1. Na guia **Página inicial**, selecione **Fechar e Aplicar**.

    ![Fechar e aplicar](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualizar dados em um relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais do relatório criado para este artigo, exclua o arquivo Power BI Desktop (. pbix).

## <a name="next-steps"></a>Próximas etapas

[Dicas para usar o conector de Data Explorer do Azure para Power BI para consultar dados](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
