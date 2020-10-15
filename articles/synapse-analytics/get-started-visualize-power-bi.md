---
title: 'Tutorial: Introdução ao Azure Synapse Analytics: visualizar dados do workspace com o Power BI'
description: Neste tutorial, você aprenderá a criar um workspace do Power BI, vincular seu workspace do Azure Synapse e criar um conjunto de dados do Power BI que utiliza os dados no workspace do Azure Synapse.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: 40a2cd5898ede7fc30db9a8475c5ab7cc68095ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87337629"
---
# <a name="visualize-data-with-power-bi"></a>Visualizar os dados com o Power BI

Neste tutorial, você aprenderá a criar um workspace do Power BI, vincular seu workspace do Azure Synapse e criar um conjunto de dados do Power BI que utiliza os dados no workspace do Azure Synapse. 

## <a name="overview"></a>Visão geral

Com base nos dados de táxi de Nova York, criamos conjuntos de dados agregados em duas tabelas:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Você pode vincular um workspace do Power BI ao seu workspace do Azure Synapse. Essa funcionalidade permite que você obtenha dados com facilidade em seu workspace do Power BI. Você pode editar os relatórios de Power BI diretamente no seu workspace do Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Criar um workspace do Power BI

1. Entre no [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Crie um workspace do Power BI chamado **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Vincular seu workspace do Azure Synapse ao novo workspace do Power BI

1. No Synapse Studio, vá para **Gerenciar** > **Serviços Vinculados**.
1. Selecione **Novo** > **Conectar ao Power BI** e defina estes campos:

    |Configuração | Valor sugerido | 
    |---|---|
    |**Nome**|**NYCTaxiWorkspace1**|
    |**Nome do workspace**|**NYCTaxiWorkspace1**|

1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Criar um conjunto de dados do Power BI que use dados em seu workspace do Azure Synapse

1. No Synapse Studio, acesse **Desenvolver** > **Power BI**.
1. Navegue até **NYCTaxiWorkspace1** > **Conjuntos de dados do Power BI** e selecione **Novo conjunto de dados do Power BI**.
1. Passe o mouse sobre o banco de dados **SQLDB1** e selecione **Baixar arquivo .pbids**.
1. Abra o arquivo **.pbids** baixado. O Power BI Desktop abre e se conecta automaticamente ao **SQLDB1** no workspace do Azure Synapse.
1. Se você vir uma caixa de diálogo chamada **Banco de dados do SQL Server**:
    1. Selecione **Conta Microsoft**.
    1. Selecione **Entrar** e entre na conta.
    1. Selecione **Conectar**.
1. Depois que a caixa de diálogo **Navegador** for aberta, verifique a tabela **PassengerCountStats** e selecione **Carregar**.
1. Depois que a caixa de diálogo **Configurações de conexão** for exibida, selecione **DirectQuery** > **OK**.
1. Selecione o botão **Relatório** à esquerda.
1. Adicione um **Gráfico de linha** ao relatório.
    1. Arraste a coluna **PassengerCount** para **Visualizações** > **Eixo**.
    1. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Visualizações** > **Valores**.
1. Na guia **Página Inicial**, selecione **Publicar**.
1. Selecione **Salvar** para salvar as alterações.
1. Escolha o nome do arquivo **PassengerAnalysis.pbix** e, em seguida, selecione **Salvar**.
1. Em **Selecionar um destino**, escolha **NYCTaxiWorkspace1** e clique em **Selecionar**.
1. Aguarde a conclusão da publicação.

### <a name="configure-authentication-for-your-dataset"></a>Configurar autenticação para o conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e escolha **Entrar**.
1. No lado esquerdo, em **Workspaces**, selecione o workspace **NYCTaxiWorkspace1**.
1. Dentro desse workspace, localize um conjunto de dados chamado de **Análise de Passageiros** e um relatório chamado **Análise de Passageiros**.
1. Passe o mouse sobre o conjunto de dados **PassengerAnalysis**, selecione o botão de reticências (...) e, em seguida, selecione **Configurações**.
1. Em **Credenciais da fonte de dados**, defina o **Método de autenticação** como **OAuth2** e selecione **Entrar**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar um relatório no Synapse Studio

1. Volte para o Synapse Studio e selecione **Fechar e atualizar**.
1. Acesse o hub **Desenvolver**.
1. Passe o mouse sobre **Power BI** e selecione para atualizar o nó **Relatórios do Power BI**.
1. Agora, em **Power BI**, você deve ver:
    * Em **NYCTaxiWorkspace1** > **Conjunto de dados do Power BI**, um novo conjunto de dados chamado **PassengerAnalysis**.
    * Em **NYCTaxiWorkspace1** > **Relatórios do Power BI**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis**. O relatório é aberto e você pode editá-lo diretamente no Synapse Studio.

## <a name="monitor-activities"></a>Monitorar atividades

1. No Synapse Studio, navegue até o hub **monitorar**.
1. Nesse local, você poderá ver um histórico de todas as atividades que estão ocorrendo no workspace e quais estão ativas no momento.
1. Explore as **Execuções de pipeline**, **Aplicativos do Apache Spark** e **Solicitações de SQL** e você poderá ver o que já fez no workspace.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar](get-started-monitor.md)
                                 

