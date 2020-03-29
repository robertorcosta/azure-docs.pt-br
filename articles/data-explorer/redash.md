---
title: Visualize o Azure Data Explorer com o Redash
description: Neste artigo, você aprende a visualizar dados no Azure Data Explorer com o conector nativo Redash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773949"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualize dados do Azure Data Explorer no Redash

[O Redash](https://redash.io/) conecta e consulta suas fontes de dados, constrói dashboards para visualizar dados e compartilhá-los com seus pares. Neste artigo, você aprende como configurar o Azure Data Explorer como fonte de dados para o Redash e, em seguida, visualizar dados.

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar cluster e banco de dados](create-cluster-database-portal.md).
1. Ingerir dados conforme explicado na [ingestão de dados de amostra no Azure Data Explorer](ingest-sample-data.md). Para obter mais opções de ingestão, consulte [visão geral da ingestão](ingest-data-overview.md).

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Criar o conector azure data explorer no Redash 

1. Faça login no [Redash](https://www.redash.io/). Selecione **Começar para** criar uma conta.
1. Em **Vamos começar,** selecione **Conectar uma fonte de dados.**

    ![Conectar uma fonte de dados](media/redash/connect-data-source.png)

1. Em Criar uma nova janela **Fonte de dados,** selecione **O Azure Data Explorer (Kusto)** e selecione **Criar**. 

    ![Selecione a fonte de dados do Azure Data Explorer](media/redash/select-adx-data-source.png)

1. Na janela **Azure Data Explorer (Kusto),** preencha o formulário a seguir e selecione **Criar**.

    ![Janela de configurações do Azure Data Explorer (Kusto)](media/redash/adx-settings-window.png)

1. Na janela **Configurações,** **selecione Salvar** e **Testar conexão** para testar a conexão de origem de dados **do Azure Data Explorer (Kusto).**

## <a name="create-queries-in-redash"></a>Criar consultas no Redash

1. No canto superior esquerdo do Redash, selecione **Criar** > **consulta**. Clique em **Nova consulta** e renomeie a consulta.

    ![Criar consulta](media/redash/create-query.png)

1. Digite sua consulta no painel de edição superior e selecione **Salvar** e **Executar**. Selecione **Publicar** para publicar consulta para uso futuro.

    ![Salvar e executar consulta](media/redash/save-and-execute-query.png)

    No painel esquerdo, você pode ver o nome da conexão de origem de dados **(conector Github** em nosso fluxo) no menu suspenso e as tabelas no banco de dados selecionado. 

1. Veja os resultados da consulta no painel central inferior. Crie uma visualização para acompanhar a consulta selecionando o botão **Nova Visualização.**

    ![Nova visualização](media/redash/new-visualization.png)

1. Na tela de visualização, selecione o **Tipo de Visualização** e os campos relevantes como **Coluna X** e Coluna **Y**. **Salve** a visualização.

    ![Configure e salve a visualização](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Criar uma consulta usando um parâmetro

1. **Criar** > **consulta para** criar uma nova consulta. Adicione um parâmetro a{}ele usando colchetes { }. Selecione **{}{ }** para abrir a janela **Adicionar parâmetro.** Você também pode selecionar o *ícone de configurações* para modificar os atributos de um parâmetro existente e abrir a **<parameter_name>** janela. 

    ![parâmetro de inserção](media/redash/insert-parameter.png)

1. Diga seu parâmetro. Selecione **Tipo**: **Lista de suspenso baseada em consulta** no menu suspenso. Selecione **OK**

    ![lista de seleção baseada em consulta](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > A consulta usa vários valores, portanto, você `| where Type in ((split('{{Type}}', ',')))`deve incluir a seguinte sintaxe . Para obter mais informações, consulte [o operador](/azure/kusto/query/inoperator). Isso resulta em [várias opções de parâmetro de consulta no aplicativo redash](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Crie um painel no Redash

1. Para criar seu painel de instrumentos, **crie** > **o dashboard**. Alternativamente, selecione o painel de instrumentos existente, **dashboards** > selecionar um painel na lista.

    ![Criar painel](media/redash/create-dashboard.png)

1. Na **janela Novo painel,** nomeie seu painel e selecione **Salvar**. Em<Dashboard_name janela **>,** selecione **Adicionar Widget** para criar um novo widget. 

1. Em Adicionar janela **Widget,** selecione nome da consulta, **Escolha Visualização**e **Parâmetros**. Selecione **Adicionar ao painel**

   ![Escolha visualizações e adicione ao painel](media/redash/add-widget-window.png)

1. Selecione **Edição concluída** para concluir a criação do painel.

1.  No modo de edição do painel, selecione **Usar filtros de nível de painel** para usar o parâmetro **Tipo** previamente definido.

    ![Criação completa do painel](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Próximas etapas

* [Escrever consultas para O Azure Data Explorer](write-queries.md)


