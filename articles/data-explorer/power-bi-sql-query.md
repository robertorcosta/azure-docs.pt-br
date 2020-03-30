---
title: Visualize dados do Azure Data Explorer com uma consulta Power BI SQL
description: 'Neste artigo, você aprende a usar uma das três opções para visualizar dados no Power BI: uma consulta SQL contra um cluster Do Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: d402d4c1ee77d0f97d2a5c3bdf43d0cc62aac096
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560466"
---
# <a name="visualize-data-from-azure-data-explorer-using-a-sql-query-in-power-bi"></a>Visualizar dados no Azure Data Explorer usando uma consulta SQL no Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Power BI é uma solução de análise de negócios que permite que você visualize os dados e compartilhar os resultados na sua organização.

O Azure Data Explorer fornece três opções para se conectar a dados no Power BI: usar o conector interno, importar uma consulta do Azure Data Explorer ou usar uma consulta SQL. Este artigo mostra como usar uma consulta SQL para obter dados e visualizá-los em um relatório do Power BI.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa do seguinte para concluir este artigo:

* Uma conta de email organizacional que seja membro do Azure Active Directory, de modo que você possa se conectar ao [Cluster de ajuda do Azure Data Explorer](https://dataexplorer.azure.com/clusters/help/databases/samples).

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecione **DOWNLOAD GRATUITO**)

## <a name="get-data-from-azure-data-explorer"></a>Obter dados do Azure Data Explorer

Primeiro, conecte-se ao cluster de ajuda do Azure Data Explorer, depois use um subconjunto dos dados da tabela de *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

Geralmente, você usa a linguagem de consulta nativa com o Azure Data Explorer, mas ele também dá suporte a consultas SQL, o que você vai usar aqui. O Azure Data Explorer traduz a consulta SQL em uma consulta nativa para você.

1. No Power BI Desktop, na guia **Início**, selecione **Obter Dados** e depois **Mais**.

    ![Obter dados](media/power-bi-sql-query/get-data-more.png)

1. Pesquise *Banco de dados SQL*, selecione **Banco de Dados SQL do Azure**, depois **Conectar**.

    ![Pesquisar e obter dados](media/power-bi-sql-query/search-get-data.png)

1. Na tela **Banco de dados do SQL Server**, preencha o formulário com as informações a seguir.

    ![Banco de dados, tabela, opções de consulta](media/power-bi-sql-query/database-table-query.png)

    **Configuração** | **Valor** | **Descrição do campo**
    |---|---|---|
    | Servidor | *help.kusto.windows.net* | A URL para o cluster de ajuda (sem *https://*). Para outros clusters, a URL está no formulário * \<ClusterName\>.\< Região\>(kusto.windows.net.* |
    | Banco de dados | *Amostras* | O banco de dados de exemplo está hospedado no cluster ao qual você está se conectando. |
    | Modo de conectividade de dados | *Importar* | Determina se o Power BI importa os dados ou se conecta diretamente à fonte de dados. Você pode usar uma das opções com esse conector. |
    | Tempo limite do comando | Deixar em branco | Por quanto tempo a consulta é executada antes de gerar um erro de tempo limite. |
    | Instrução SQL | Copie a consulta abaixo desta tabela | A instrução SQL que o Azure Data Explorer se traduz em uma consulta nativa. |
    | Outras opções | Mantenha como valores padrão | As opções não se aplicam aos clusters do Azure Data Explorer. |
    | | | |

    ```SQL
    SELECT TOP 1000 *
    FROM StormEvents
    ORDER BY DamageCrops DESC
    ```

1. Caso ainda não tenha uma conexão para o cluster de ajuda, entre. Entre com uma conta Microsoft, depois selecione **Conectar**.

    ![Entrar](media/power-bi-sql-query/sign-in.png)

1. Na tela **help.kusto.windows.net: Exemplos**, selecione **Carregar**.

    ![Carregar dados](media/power-bi-sql-query/load-data.png)

    A tabela é aberta na janela principal do Power BI, no modo de exibição de relatório, onde é possível criar relatórios com base nos dados de exemplo.

## <a name="visualize-data-in-a-report"></a>Visualizar dados em um relatório

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Limpar recursos

Se você não precisar mais do relatório que criou para este artigo, exclua o arquivo Power BI Desktop (.pbix).

## <a name="next-steps"></a>Próximas etapas

[Visualize dados usando o conector do Azure Data Explorer para Power BI](power-bi-connector.md)