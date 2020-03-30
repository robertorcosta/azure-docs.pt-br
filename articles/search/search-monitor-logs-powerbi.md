---
title: Visualize registros e métricas cognitivas do Azure com power bi
description: Visualize registros e métricas cognitivas do Azure com power bi
manager: eladz
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 618033e24236d26601fafaf7b0678ee83ec973e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650122"
---
# <a name="visualize-azure-cognitive-search-logs-and-metrics-with-power-bi"></a>Visualize registros e métricas cognitivas do Azure com power bi
[O Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-what-is-azure-search) permite armazenar registros de operação e métricas de serviço sobre seu serviço de pesquisa em uma conta do Azure Storage. Esta página fornece instruções de como você pode visualizar essas informações através de um aplicativo de modelo power bi. O aplicativo fornece insights detalhados sobre seu serviço de pesquisa, incluindo informações sobre métricas de Pesquisa, Indexação, Operações e Serviço.

Você pode encontrar o Power BI Template **App Azure Cognitive Search: Analyze Logs and Metrics** in the [Power BI Apps marketplace](https://appsource.microsoft.com/marketplace/apps).

## <a name="how-to-get-started-with-the-app"></a>Como começar com o aplicativo
1. Habilite o registro de diagnóstico para o seu serviço de pesquisa:
    1. Crie ou identifique uma [conta existente do Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) onde você pode arquivar os logs
    1. Navegue até o seu serviço de Busca Cognitiva do Azure no portal Azure
    1. Na seção Monitoramento na coluna esquerda, selecione **Configurações de diagnóstico**

        ![](media/search-monitor-logs-powerbi/diagnostic-settings.png)

    1. Selecione **+ Adicione a configuração de diagnóstico**
    1. Verifique **o arquivamento para uma conta de armazenamento,** forneça as informações da sua conta de armazenamento e verifique o **OperationLogs** e **allMetrics**

        ![](media/search-monitor-logs-powerbi/add-diagnostic-setting.png)
    1. Selecione **Salvar**

1. Depois que o registro for ativado, use seu serviço de pesquisa para começar a gerar logs e métricas. Leva até uma hora até que os contêineres apareçam no armazenamento Blob com estes registros. Você verá um contêiner **insights-logs-operationlogs** para registros de tráfego de pesquisa e um contêiner **insights-metrics-pt1m** para métricas.

1. Encontre o Aplicativo Azure Cognitive Search Power BI no [mercado de aplicativos Power BI](https://appsource.microsoft.com/marketplace/apps) e instale-o em um novo espaço de trabalho ou em um espaço de trabalho existente. O aplicativo se chama **Azure Cognitive Search: Analyze Logs and Metrics**.

1. Depois de instalar o aplicativo, selecione o aplicativo da sua lista de aplicativos no Power BI.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile.png)

1. Selecione **Conectar** para conectar seus dados

    ![](media/search-monitor-logs-powerbi/get-started-with-your-new-app.png)

1. Insira o nome da conta de armazenamento que contém seus registros e métricas. Por padrão, o aplicativo analisará os últimos 10 dias de dados, mas esse valor pode ser alterado com o parâmetro **Days.**

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account.png)

1. Selecione **Key** como o método de autenticação e forneça a chave da conta de armazenamento. Selecione **Private** como o nível de privacidade. Clique em Entrar para iniciar o processo de carregamento.

    ![](media/search-monitor-logs-powerbi/connect-to-storage-account-step-two.png)

1. Aguarde a atualização dos dados. Isso pode levar algum tempo, dependendo da quantidade de dados que você tem. Você pode ver se os dados ainda estão sendo atualizados com base no indicador abaixo.

    ![](media/search-monitor-logs-powerbi/workspace-view-refreshing.png)

1. Depois que a atualização de dados tiver sido concluída, selecione **O Relatório de Pesquisa Cognitiva do Azure** para visualizar o relatório.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-report.png)

1. Certifique-se de atualizar a página após a abertura do relatório para que ela seja aberta com seus dados.

    ![](media/search-monitor-logs-powerbi/powerbi-search.png)

## <a name="how-to-change-the-app-parameters"></a>Como alterar os parâmetros do aplicativo
Se você quiser visualizar dados de uma conta de armazenamento diferente ou alterar o número de dias de dados para consulta, siga as etapas abaixo para alterar os parâmetros **Dias** e **ArmazenamentoS.**

1. Navegue até seus aplicativos power bi, encontre seu aplicativo de pesquisa cognitiva Do Zure e selecione o botão **Editar aplicativo** para visualizar o espaço de trabalho.

    ![](media/search-monitor-logs-powerbi/azure-search-app-tile-edit.png)

1. Selecione **Configurações** nas opções Conjunto de dados.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-settings.png)

1. Enquanto estiver na guia Conjuntos de dados, altere os valores dos parâmetros e **selecione Aplicar**. Se houver um problema com a conexão, atualize as credenciais de origem de dados na mesma página.

1. Navegue de volta ao espaço de trabalho e selecione **Atualizar agora** nas opções Conjunto de dados.

    ![](media/search-monitor-logs-powerbi/workspace-view-select-refresh-now.png)

1. Abra o relatório para visualizar os dados atualizados. Você também pode precisar atualizar o relatório para visualizar os dados mais recentes.

## <a name="troubleshooting"></a>Solução de problemas
Se você descobrir que não pode ver seus dados, siga estas etapas de solução de problemas:

1. Abra o relatório e atualize a página para ter certeza de que você está visualizando os dados mais recentes. Há uma opção no relatório para atualizar os dados. Selecione isso para obter os dados mais recentes.

1. Certifique-se de que o nome da conta de armazenamento e a chave de acesso que você forneceu estão corretos. O nome da conta de armazenamento deve corresponder à conta configurada com os registros do serviço de pesquisa.

1. Confirme se sua conta de armazenamento contém os **contêineres insights-logs-operationlogs** e **insights-metrics-pt1m** e cada contêiner tem dados. Os registros e métricas estarão dentro de algumas camadas de pastas.

1. Verifique se o conjunto de dados ainda está sendo atualizado. O indicador de status de atualização é mostrado na etapa 8 acima. Se ainda estiver refrescado, aguarde até que a atualização esteja completa para abrir e atualizar o relatório.

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre a Pesquisa Cognitiva do Azure](https://docs.microsoft.com/azure/search/)

[O que é power bi?](https://docs.microsoft.com/power-bi/fundamentals/power-bi-overview)

[Conceitos básicos para designers no serviço do Power BI](https://docs.microsoft.com/power-bi/service-basic-concepts)