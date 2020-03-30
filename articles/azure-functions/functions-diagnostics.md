---
title: Visão geral do diagnóstico de funções do Azure
description: Saiba como solucionar problemas com seu aplicativo de função com diagnósticos de Funções do Azure.
author: yunjchoi
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
ms.openlocfilehash: 41acc5703f6b6e5cdeedf2afd0dc61b23bc579c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834045"
---
# <a name="azure-functions-diagnostics-overview"></a>Visão geral do diagnóstico de funções do Azure

Quando você está executando um aplicativo de função, você deseja estar preparado para quaisquer problemas que possam surgir, desde erros de 4xx até falhas de gatilho. O diagnóstico do Azure Functions é uma experiência inteligente e interativa para ajudá-lo a solucionar problemas do seu aplicativo de função sem configuração ou custo extra. Quando você se depara com seu aplicativo de função, o diagnóstico do Azure Functions aponta o que está errado para guiá-lo para as informações certas para solucionar e resolver o problema com mais facilidade e rapidez. Este artigo mostra o básico de como usar o diagnóstico de Funções do Azure para diagnosticar e resolver problemas comuns de aplicativos de função.

## <a name="start-azure-functions-diagnostics"></a>Iniciar diagnósticos de funções do Azure

Para acessar os diagnósticos de funções do Azure:

1. Navegue até o seu aplicativo de função no [portal Azure](https://portal.azure.com).
2. Selecione a guia **Recursos da** Plataforma.
3. Selecione **Diagnosticar e resolver problemas** em **Gerenciamento de Recursos**, que abre os diagnósticos de Funções do Azure.
4. Escolha uma categoria que melhor descreva a questão do seu aplicativo de função usando as palavras-chave no bloco da página inicial. Você também pode digitar uma palavra-chave que melhor descreva seu problema na barra de pesquisa. Por exemplo, você `execution` pode digitar para ver uma lista de relatórios de diagnóstico relacionados à execução do aplicativo de função e abri-los diretamente da página inicial.

![Home page](./media/functions-diagnostics/homepage.png)

## <a name="use-the-interactive-interface"></a>Use a interface Interativa

Depois de selecionar uma categoria de página inicial que melhor se alinhe com o problema do seu aplicativo de função, a interface interativa do Azure Functions, Genie, pode guiá-lo através do diagnóstico e resolução de problemas do seu aplicativo. Você pode usar os atalhos de azulejo fornecidos pelo Genie para visualizar o relatório de diagnóstico completo da categoria de problemas que você está interessado. Os atalhos de azulejo fornecem uma maneira direta de acessar suas métricas de diagnóstico.

![Gênio](./media/functions-diagnostics/genie.png)

Depois de selecionar um azulejo, você pode ver uma lista de tópicos relacionados ao problema descrito no azulejo. Esses tópicos fornecem trechos de informações notáveis do relatório completo. Você pode selecionar qualquer um desses tópicos para investigar mais os problemas. Além disso, você pode selecionar **Exibir relatório completo** para explorar todos os tópicos em uma única página.

![Visualização do relatório de diagnóstico](./media/functions-diagnostics/preview-of-diagnostic-report.png)

## <a name="view-a-diagnostic-report"></a>Exibir um relatório de diagnóstico

Depois de escolher um tópico, você pode visualizar um relatório de diagnóstico específico para o seu aplicativo de função. Os relatórios de diagnóstico usam ícones de status para indicar se há algum problema específico com o aplicativo. Você vê descrição detalhada do problema, ações recomendadas, métricas relacionadas e documentos úteis. Relatórios de diagnóstico personalizados são gerados a partir de uma série de verificações executadas em seu aplicativo de função. Os relatórios de diagnóstico podem ser uma ferramenta útil para identificar problemas em seu aplicativo de função e guiá-lo para resolver o problema.

## <a name="find-the-problem-code"></a>Encontre o código do problema

Para funções baseadas em script, você pode usar **execução de funções e erros** em **Função App Down ou Reporting Errors** para reduzir a linha de código causando exceções ou erros. Esse recurso pode ser uma ferramenta útil para chegar à causa raiz e corrigir problemas de uma linha específica de código. Esta opção não está disponível para funções C# e Java pré-compiladas.

![Relatório de diagnóstico sobre erros de execução da função](./media/functions-diagnostics/diagnostic-report-on-function-execution-errors.png)

![Exceção de função](./media/functions-diagnostics/function-exception.png)

## <a name="next-steps"></a>Próximas etapas

Você pode fazer perguntas ou fornecer feedback sobre os diagnósticos de funções do Azure no [UserVoice](https://feedback.azure.com/forums/355860-azure-functions). Por `[Diag]` favor, inclua no título de sua opinião.

> [!div class="nextstepaction"]
> [Monitore seus aplicativos de função](functions-monitoring.md)
