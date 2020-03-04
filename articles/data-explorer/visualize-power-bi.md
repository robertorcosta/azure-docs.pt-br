---
title: 'Tutorial: Visualizar dados do Azure Data Explorer no Power BI'
description: Neste tutorial, você aprenderá como se conectar ao Azure Data Explorer com o Power BI e visualizar seus dados.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: ec1579792a6e247bf49946bb8609a626154fbd46
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037350"
---
# <a name="tutorial-visualize-data-from-azure-data-explorer-in-power-bi"></a>Tutorial: Visualizar dados do Azure Data Explorer no Power BI

O Azure Data Explorer é um serviço de exploração de dados rápido e altamente escalonável para dados de log e telemetria. Power BI é uma solução de análise de negócios que permite que você visualize os dados e compartilhar os resultados na sua organização. Neste tutorial, primeiro você aprenderá como renderizar elementos visuais no Azure Data Explorer. Em seguida, conecte-se ao Azure Data Explorer com o Power BI, crie um relatório com base em dados de exemplo e publique o relatório no serviço do Power BI.

Caso você não tenha uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/) antes de começar. Se você não estiver inscrito no Power BI Pro, [inscreva-se para uma avaliação gratuita](https://app.powerbi.com/signupredirect?pbi_source=web) antes de começar.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Renderizar elementos visuais no Azure Data Explorer
> * Conectar ao Azure Data Explorer no Power BI Desktop
> * Trabalhar com dados no Power BI Desktop
> * Criar um relatório com elementos visuais
> * Publicar e compartilhar o relatório

## <a name="prerequisites"></a>Prerequisites

Além de assinaturas do Azure e Power BI, você precisará do seguinte para concluir este tutorial:

* [Um cluster de teste e um banco de dados](create-cluster-database-portal.md)

* [Os dados de exemplo StormEvents](ingest-sample-data.md). [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selecione **DOWNLOAD GRATUITO**)

## <a name="render-visuals-in-azure-data-explorer"></a>Renderizar elementos visuais no Azure Data Explorer

Antes de passar para o Power BI, vamos ver como renderizar elementos visuais no Azure Data Explorer. Isso é ótimo para uma análise rápida.

1. Entre em [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. No painel esquerdo, selecione o banco de dados de teste que contém os dados de exemplo StormEvents.

1. Cole a consulta a seguir na janela à direita e selecione **executar**.

    ```Kusto
    StormEvents
    | summarize event_count=count() by State
    | where event_count > 1800
    | project State, event_count
    | sort by event_count
    | render columnchart
    ```

    Essa consulta conta os eventos de clima por estado. Ela então processa um gráfico de colunas para todos os estados que têm mais de 1800 eventos de clima.

    ![Gráfico de colunas de eventos](media/visualize-power-bi/events-column-chart.png)

1. Cole a consulta a seguir na janela à direita e selecione **executar**.

    ```Kusto
    StormEvents
    | where State == "WASHINGTON" and StartTime >= datetime(2007-07-01) and StartTime <= datetime(2007-07-31)
    | summarize StormCount = count() by EventType
    | render piechart
    ```

    Essa consulta conta eventos climáticos por tipo para o mês de julho no estado de Washington. Ela então processa um gráfico de pizza que mostra o percentual de cada tipo de evento.

    ![Gráfico de pizza de eventos](media/visualize-power-bi/events-pie-chart.png)

Agora é hora de examinar o Power BI, mas há muito mais que você pode fazer com elementos visuais no Azure Data Explorer.

## <a name="connect-to-azure-data-explorer"></a>Conectar o Azure Data Explorer

Agora você conecta ao Azure Data Explorer no Power BI Desktop.

1. No Power BI Desktop, na guia **Página Inicial**, selecione **Obter Dados** e depois **Mais**.

    ![Obter dados](media/visualize-power-bi/get-data-more.png)

1. Pesquise por *Azure Data Explorer*, depois selecione **Azure Data Explorer (Beta)** e **Conectar**.

    ![Pesquisar e obter dados](media/visualize-power-bi/search-get-data.png)

1. Em **Visualizar Conector**, selecione **Continuar**.

1. Na próxima tela, insira o nome do seu cluster de teste e o banco de dados. O cluster deve estar no formato de `https://<ClusterName>.<Region>.kusto.windows.net`. Insira *StormEvents* para o nome da tabela. Deixe todas as outras opções com os valores padrão e, em seguida, selecione **Okey**.

    ![Cluster, o banco de dados, opções de tabela](media/visualize-power-bi/cluster-database-table.png)

1. Na tela de visualização de dados, selecione **editar**.

    A tabela é aberta no Editor do Power Query, onde é possível editar linhas e colunas antes de importar os dados.

## <a name="work-with-data-in-power-bi-desktop"></a>Trabalhar com dados no Power BI Desktop

Agora que você tem uma conexão para o Data Explorer do Azure, você editar os dados no Editor do Power Query. Você remove linhas com valores nulos na coluna **BeginLat** e solta a coluna **StormSummary** JSON inteiramente. Essas são operações simples, mas você também pode executar transformações complexas durante a importação de dados.

1. Selecione a seta para a coluna **BeginLat**, desmarque a caixa de seleção **nulo** e, em seguida, selecione **Okey**.

    ![Filtrar coluna](media/visualize-power-bi/filter-column.png)

1. Clique com botão direito no cabeçalho da coluna **StormSummary** e, em seguida, selecione **Remover**.

    ![Remover coluna](media/visualize-power-bi/remove-column.png)

1. No painel **configurações de consulta**, altere o nome de *Consulta1* para *StormEvents*.

    ![Alterar nome da consulta](media/visualize-power-bi/query-name.png)

1. Sobre a guia **Página inicial** da faixa de opções, selecione **Fechar e aplicar**.

    ![Fechar e aplicar](media/visualize-power-bi/close-apply.png)

    Power Query aplica suas alterações e, em seguida, importa os dados de exemplo em um *modelo de dados*. As próximas etapas mostram como aprimorar esse modelo. Novamente, isso é apenas um exemplo simples para dar uma ideia do que é possível.

1. No lado esquerdo da janela principal, selecione a exibição de dados.

    ![Exibição de dados](media/visualize-power-bi/data-view.png)

1. Sobre a guia **Modelagem** da faixa de opções, selecione **Nova coluna**.

    ![Nova coluna](media/visualize-power-bi/new-column.png)

1. Insira a seguinte fórmula de expressões de Análise de Dados (DAX) na barra de fórmulas e, em seguida, pressione Enter.

    ```DAX
    DurationHours = DATEDIFF(StormEvents[StartTime], StormEvents[EndTime], hour)
    ```

    ![Barra de fórmulas](media/visualize-power-bi/formula-bar.png)

    Esta fórmula cria a coluna *DurationHours* que calcula quantas horas cada evento de clima durou. Use essa coluna em um visual na próxima seção.

1. Role para o lado direito da tabela para ver a coluna.

## <a name="create-a-report-with-visuals"></a>Criar um relatório com elementos visuais

Agora que os dados são importados e aprimoramos o modelo de dados, é hora de criar um relatório com elementos visuais. Você adiciona um gráfico de coluna com base na duração do evento e um mapa que mostra corte de danos.

1. No lado esquerdo da janela, selecione a exibição de relatório.

    ![Exibição de relatório](media/visualize-power-bi/report-view.png)

1. No painel **VISUALIZAÇÕES**, selecione o gráfico de coluna em cluster.

    ![Adicione gráfico de colunas](media/visualize-power-bi/add-column-chart.png)

    Um gráfico em branco é adicionado à tela.

    ![Gráfico em branco](media/visualize-power-bi/blank-chart.png)

1. Na lista **CAMPOS**, selecione **DurationHours** e **Estado**.

    ![Selecionar Campos](media/visualize-power-bi/select-fields.png)

    Agora você tem um gráfico que mostra o total de horas de eventos de clima por estado ao longo de um ano.

    ![Gráfico de coluna de duração](media/visualize-power-bi/duration-column-chart.png)

1. Clique em qualquer lugar na tela fora do gráfico de coluna.

1. No painel **VISUALIZAÇÕES**, selecione mapa.

    ![Adicionar Mapa](media/visualize-power-bi/add-map.png)

1. Na lista **CAMPOS**, selecione **CropDamage** e **Estado**. Redimensione o mapa, para que você possa ver claramente os estados dos EUA.

    ![Mapa de danos de corte](media/visualize-power-bi/crop-damage-map.png)

    O tamanho das bolhas representa o valor em dólar de danos de corte. Passe o mouse sobre as bolhas para ver os detalhes.

1. Mova e redimensione os elementos visuais para que você tenha um relatório que se parece com a imagem a seguir.

    ![Relatório concluído](media/visualize-power-bi/finished-report.png)

1. Salve o relatório com o nome *storm-events.pbix*.

## <a name="publish-and-share-the-report"></a>Publicar e compartilhar o relatório

Até este ponto, o trabalho feito no Power BI foi todo local, usando o Power BI Desktop. Agora você deve publicar o relatório para o serviço do Power BI, onde você pode compartilhá-lo com outras pessoas.

1. No Power BI Desktop, na guia **Início** da faixa de seleções, clique em **Publicar**.

    ![Botão Publicar](media/visualize-power-bi/publish-button.png)

1. Se você ainda não entrou no Power BI, percorra o processo de logon.

1. Selecione **Meu workspace**, em seguida, **Selecione**.

    ![Selecione o workspace](media/visualize-power-bi/select-workspace.png)

1. Quando a publicação for concluída, selecione **abra storm-events.pbix no Power BI**.

    ![Publicado com êxito](media/visualize-power-bi/publishing-succeeded.png)

    O relatório é aberto no serviço, com os mesmos elementos visuais e o layout definido no Power BI Desktop.

1. No canto superior direito da página, clique em **Compartilhar**.

    ![Compartilhar botão](media/visualize-power-bi/share-button.png)

1. Na tela **Compartilhar relatório**, adicione um colega da sua organização, adicione uma anotação e selecione **Compartilhamento**.

    ![Compartilhar Relatório](media/visualize-power-bi/share-report.png)

    Se seu colega tiver as permissões apropriadas, ele pode acessar o relatório que você compartilhou.

## <a name="clean-up-resources"></a>Limpar recursos

Se você não quiser manter o relatório que você criou, basta excluir o arquivo *storm-events.pbix*. Se você quiser remover o relatório publicado, siga estas etapas.

1. Sob **Meu workspace**, role para baixo até **RELATÓRIOS** e localize **storm-events**.

1. Selecione as reticências ( **...** ) ao lado **storm-events**, em seguida, selecione **REMOVER**.

    ![Remover relatório](media/visualize-power-bi/remove-report.png)

1. Confirme a remoção.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Gravar consultas](write-queries.md)
