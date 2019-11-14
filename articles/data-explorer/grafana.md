---
title: Visualizar dados do Azure Data Explorer usando o Grafana
description: Neste artigo, você aprende a configurar o Azure Data Explorer como uma fonte de dados para Grafana e, em seguida, Visualizar dados de um cluster de exemplo.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037987"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualizar dados do Azure Data Explorer no Grafana

O Grafana é uma plataforma de análise que permite consultar e visualizar dados e, em seguida, criar e compartilhar painéis com base nas visualizações. O Grafana fornece um *plug-in* do Azure Data Explore que permite conectar e visualizar dados do Azure Data Explorer. Neste artigo, você aprende a configurar o Azure Data Explorer como uma fonte de dados para Grafana e, em seguida, Visualizar dados de um cluster de exemplo.

Use o vídeo a seguir para saber como usar o plug-in do Data Explorer do Azure do Grafana, configurar o Data Explorer do Azure como uma fonte de dados para Grafana e, em seguida, Visualizar dados. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

Como alternativa, você pode [Configurar a fonte de dados](#configure-the-data-source) e [Visualizar os dados](#visualize-data) conforme detalhado no artigo abaixo.

## <a name="prerequisites"></a>pré-requisitos

Você precisará do seguinte para concluir este artigo:

* [Grafana versão 5.3.0 ou posterior](https://docs.grafana.org/installation/) para o sistema operacional

* [Plug-in do Azure Data Explorer](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) para Grafana

* Um cluster que inclui os dados de exemplo do StormEvents. Para obter mais informações, consulte [Início rápido: criar um cluster e um banco de dados do Azure Data Explorer](create-cluster-database-portal.md) e [Ingerir dados de exemplo no Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Especificar propriedades e testar a conexão

Com a entidade de serviço atribuída à função *visualizadores*, agora você especifica as propriedades na instância do Grafana e testa a conexão com Azure Data Explorer.

1. No Grafana, no menu esquerdo, selecione o ícone de engrenagem e, em seguida, **Fonte de Dados**.

    ![Fontes de dados](media/grafana/data-sources.png)

1. Clique em **Adicionar fonte de dados**.

1. Na página **Fonte de Dados/Novo**, insira um nome para a fonte de dados e selecione o tipo **Fonte de Dados do Azure Data Explorer**.

    ![Nome e tipo de conexão](media/grafana/connection-name-type.png)

1. Insira o nome do cluster no formato https://{ClusterName}.{Region}.kusto.windows.net. Insira os outros valores no portal do Azure ou na CLI. Consulte na tabela abaixo a imagem a seguir para um mapeamento.

    ![Propriedades da conexão](media/grafana/connection-properties.png)

    | UI do Grafana | Portal do Azure | CLI do Azure |
    | --- | --- | --- |
    | ID da assinatura | ID DA ASSINATURA | SubscriptionId |
    | ID do locatário | ID do Diretório | locatário |
    | ID do cliente | ID do aplicativo | appId |
    | Segredo do cliente | Senha | password |
    | | | |

1. Selecione **Salvar e Testar**.

    Se o teste for obtido com êxito, siga para a próxima seção. Se você tiver problemas, verifique os valores especificados em Grafana e examine as etapas anteriores.

## <a name="visualize-data"></a>Visualizar dados

Agora que você terminou de configurar o Azure Data Explorer como uma fonte de dados para o Grafana, é hora de visualizar os dados. Mostraremos um exemplo básico aqui, mas você pode fazer muito mais. É recomendável consultar [Gravar consultas para Azure Data Explorer](write-queries.md) para exemplos de outras consultas a serem executadas no conjunto de dados de exemplo.

1. No Grafana, no menu esquerdo, selecione o ícone de adição e, em seguida, **Painel**.

    ![Criar painel](media/grafana/create-dashboard.png)

1. Na guia **Adicionar**, selecione **Grafo**.

    ![Adicionar grafo](media/grafana/add-graph.png)

1. No painel de garfo, selecione **Título do Painel** e, em seguida, **Editar**.

    ![Editar painel](media/grafana/edit-panel.png)

1. Na parte inferior do painel, selecione **Fonte de Dados** e, em seguida, selecione a fonte de dados que você configurou.

    ![Selecione uma fonte de dados](media/grafana/select-data-source.png)

1. No painel de consulta, copie na consulta a seguir e selecione **Executar**. A consulta agrupa a contagem de eventos por dia para o conjunto de dados de exemplo.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Executar consulta](media/grafana/run-query.png)

1. O grafo não mostra nenhum resultado porque tem como escopo, por padrão, os dados das últimas seis horas. No menu superior, selecione **Últimas 6 horas**.

    ![Últimas seis horas](media/grafana/last-six-hours.png)

1. Especifique um intervalo personalizado que abrange 2007, o ano incluído em nosso conjunto de dados de exemplo do StormEvents. Escolha **Aplicar**.

    ![Intervalo de datas personalizado](media/grafana/custom-date-range.png)

    Agora, o grafo mostra os dados de 2007, classificados por dia.

    ![Grafo concluído](media/grafana/finished-graph.png)

1. No menu superior, selecione o ícone salvar: ![Ícone Salvar](media/grafana/save-icon.png).

## <a name="create-alerts"></a>Criar alertas

1. No painel inicial, selecione **alertas** > **canais de notificação** para criar um novo canal de notificação

    ![criar canal de notificação](media/grafana/create-notification-channel.png)

1. Crie um novo **canal de notificação**e, em seguida, **salve**.

    ![Criar novo canal de notificação](media/grafana/new-notification-channel-adx.png)

1. No **painel**, selecione **Editar** na lista suspensa.

    ![Selecione Editar no painel](media/grafana/edit-panel-4-alert.png)

1. Selecione o ícone de sino de alerta para abrir o painel **alerta** . Selecione **criar alerta**. Preencha as propriedades a seguir no painel **alerta** .

    ![Propriedades do alerta](media/grafana/alert-properties.png)

1. Selecione o ícone **salvar painel** para salvar as alterações.

## <a name="next-steps"></a>Próximas etapas

* [Gravar consultas para Azure Data Explorer](write-queries.md)
