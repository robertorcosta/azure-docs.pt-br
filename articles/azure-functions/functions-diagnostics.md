---
title: Visão geral do diagnóstico de Azure Functions
description: Saiba como você pode solucionar problemas com seu aplicativo de funções com o Azure Functions Diagnostics.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: fb329273d442c023233fee2e7e01aabe5f5bff8c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122351"
---
# <a name="azure-functions-diagnostics-overview"></a>Visão geral do diagnóstico de Azure Functions

Quando você estiver executando um aplicativo de funções, você deseja estar preparado para quaisquer problemas que possam surgir, de erros de 4xx para disparar falhas. O diagnóstico de Azure Functions é uma experiência inteligente e interativa para ajudá-lo a solucionar problemas do seu aplicativo de funções sem nenhuma configuração ou custo adicional. Quando você encontrar problemas com seu aplicativo de funções, Azure Functions diagnósticos indicará o que está errado. Ele orienta você às informações certas para solucionar problemas e resolver o problema com mais facilidade e rapidez. Este artigo mostra as noções básicas de como usar o diagnóstico de Azure Functions para diagnosticar e resolver mais rapidamente problemas comuns do aplicativo de funções.

## <a name="start-azure-functions-diagnostics"></a>Iniciar o diagnóstico de Azure Functions

Para iniciar o diagnóstico de Azure Functions:

1. Navegue até seu aplicativo de funções no [portal do Azure](https://portal.azure.com).
1. Selecione **diagnosticar e resolver problemas** para abrir o diagnóstico de Azure functions.
1. Escolha uma categoria que melhor descreva o problema do seu aplicativo de funções usando as palavras-chave no bloco Home Page. Você também pode digitar uma palavra-chave que melhor descreva seu problema na barra de pesquisa. Por exemplo, você pode digitar `execution` para ver uma lista de relatórios de diagnóstico relacionados à sua execução de aplicativo de funções e abri-los diretamente da Home Page.

   :::image type="content" source="./media/functions-diagnostics/functions-app-search-azure-functions-diagnostics.png" alt-text="Procure Azure Functions Diagnostics." border="true":::

## <a name="use-the-interactive-interface"></a>Usar a interface interativa

Depois de selecionar uma categoria de Home Page que melhor se alinha com o problema do seu aplicativo de funções, a interface interativa do Azure Functions Diagnostics, chamada gênio, pode orientá-lo no diagnóstico e na resolução do problema de seu aplicativo. Você pode usar os atalhos de bloco fornecidos pelo gênio para exibir o relatório de diagnóstico completo da categoria do problema em que você está interessado. Os atalhos de bloco fornecem uma maneira direta de acessar suas métricas de diagnóstico.

:::image type="content" source="./media/functions-diagnostics/genie.png" alt-text="Gênio é a interface do Azure Functions Diagnostics." border="false":::

Depois de selecionar um bloco, você poderá ver uma lista de tópicos relacionados ao problema descrito no bloco. Esses tópicos fornecem trechos de informações notáveis do relatório completo. Selecione qualquer um desses tópicos para investigar mais os problemas. Além disso, você pode selecionar **Exibir relatório completo** para explorar todos os tópicos em uma única página.

:::image type="content" source="./media/functions-diagnostics/preview-of-diagnostic-report.png" alt-text="Visualização do relatório de diagnóstico" border="false":::

## <a name="view-a-diagnostic-report"></a>Exibir um relatório de diagnóstico

Depois de escolher um tópico, você pode exibir um relatório de diagnóstico específico para seu aplicativo de funções. Os relatórios de diagnóstico usam ícones de status para indicar se há problemas específicos com seu aplicativo. Você verá uma descrição detalhada do problema, as ações recomendadas, as métricas relacionadas e os documentos úteis. Relatórios de diagnóstico personalizados são gerados de uma série de verificações executadas em seu aplicativo de funções. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar problemas em seu aplicativo de funções e orientá-lo para resolver o problema.

## <a name="find-the-problem-code"></a>Localizar o código do problema

Para funções baseadas em script, você pode usar a **execução de função e erros** em **aplicativo de funções ou relatar erros** para restringir a linha de código causando exceções ou erros. Você pode usar essa ferramenta para obter a causa raiz e corrigir problemas de uma linha de código específica. Essa opção não está disponível para funções pré-compiladas C# e Java.

:::image type="content" source="./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png" alt-text="Relatório de diagnóstico sobre erros de execução de função" border="false":::

:::image type="content" source="./media/functions-diagnostics/function-exception.png" alt-text="Exibição dos detalhes da exceção." border="false":::

## <a name="next-steps"></a>Próximas etapas

Você pode fazer perguntas ou fornecer comentários sobre o diagnóstico de Azure Functions no [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Inclua `[Diag]` no título de seus comentários.

> [!div class="nextstepaction"]
> [Monitorar seus aplicativos de funções](functions-monitoring.md)
