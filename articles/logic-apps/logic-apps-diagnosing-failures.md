---
title: Solucionar problemas e diagnosticar falhas de fluxo de trabalho
description: Saiba como solucionar e diagnosticar problemas, erros e falhas em seus fluxos de trabalho em Aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905001"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Solucionar problemas e diagnosticar falhas de fluxo de trabalho nos Aplicativos Lógicos do Azure

Seu aplicativo lógico gera informações que podem ajudar você a diagnosticar e depurar problemas nele. É possível diagnosticar um aplicativo lógico revisando cada etapa no fluxo de trabalho por meio do Portal do Azure. Ou, você pode adicionar algumas etapas a um fluxo de trabalho para depuração de tempo de execução.

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>Verifique o histórico do gatilho

Cada aplicativo de lógica executado começa com uma tentativa de gatilho, então se o gatilho não disparar, siga estas etapas:

1. Verifique o status do gatilho [verificando o histórico do gatilho](../logic-apps/monitor-logic-apps.md#review-trigger-history). Para visualizar mais informações sobre a tentativa de disparo, selecione esse evento de gatilho, por exemplo:

   ![Exibir status e histórico do gatilho](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. Verifique as entradas do gatilho para confirmar se elas aparecem como você espera. Em **Inputs link**, selecione o link, que mostra o painel **Entradas.**

   As entradas de gatilho incluem os dados que o gatilho espera e requer para iniciar o fluxo de trabalho. A revisão dessas entradas pode ajudá-lo a determinar se as entradas do gatilho estão corretas e se a condição foi atendida para que o fluxo de trabalho possa continuar.

   Por exemplo, `feedUrl` a propriedade aqui tem um valor de alimentação RSS incorreto:

   ![Revisar entradas de gatilho para erros](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. Verifique as saídas dos gatilhos, se houver, para confirmar se eles aparecem como você espera. Em **'Saídas link',** selecione o link, que mostra o painel **Saídas.**

   As saídas de gatilho incluem os dados que o gatilho passa para a próxima etapa do seu fluxo de trabalho. A revisão dessas saídas pode ajudá-lo a determinar se os valores corretos ou esperados são repassados para a próxima etapa do seu fluxo de trabalho, por exemplo:

   ![Revisar saídas de gatilho para erros](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > Se você encontrar algum conteúdo que você não reconheça, saiba mais sobre [diferentes tipos de conteúdo](../logic-apps/logic-apps-content-type.md) no Azure Logic Apps.

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>Verificar o histórico de corridas

Cada vez que o gatilho é acionado para um item ou evento, o mecanismo Logic Apps cria e executa uma instância de fluxo de trabalho separada para cada item ou evento. Se uma execução falhar, siga estas etapas para rever o que aconteceu durante essa execução, incluindo o status de cada etapa do fluxo de trabalho mais as entradas e saídas para cada etapa.

1. Verifique o status de execução do fluxo de trabalho [verificando o histórico de execuçãos](../logic-apps/monitor-logic-apps.md#review-runs-history). Para exibir mais informações sobre uma execução com falha, incluindo todas as etapas em execução em seu status, selecione a execução com falha.

   ![Exibir histórico de execução e selecionar execução com falha](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. Depois que todos os passos da execução aparecerem, expanda o primeiro passo falho.

   ![Expandir a primeira etapa com falha](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. Verifique as entradas da etapa com falha para confirmar se elas aparecem como você espera.

1. Examine os detalhes de cada etapa em uma execução específica. No **Histórico de execuções**, selecione a execução que você deseja examinar.

   ![Examinar o histórico de execuções](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![Exibir os detalhes de uma execução de aplicativo lógico](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. Para examinar as entradas, saídas e as mensagens de erro para uma etapa específica, escolha essa etapa para que a forma se expanda e mostre os detalhes. Por exemplo: 

   ![Exibir detalhes da etapa](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>Realizar depuração de runtime

Para ajudar na depuração, você pode adicionar etapas de diagnóstico a um fluxo de trabalho de aplicativo lógico, juntamente com a revisão do gatilho e executa o histórico. Por exemplo, você pode adicionar etapas que usam o serviço [Webhook Tester](https://webhook.site/) para poder inspecionar solicitações HTTP e determinar seu tamanho, forma e formato exatos.

1. Acesse o site [do Webhook Tester](https://webhook.site/) e copie a URL exclusiva gerada.

1. Em seu aplicativo lógico, adicione uma ação HTTP POST mais o conteúdo corporal que você deseja testar, por exemplo, uma expressão ou outra saída de etapa.

1. Cole sua URL do Webhook Tester na ação HTTP POST.

1. Para rever como uma solicitação é formada quando gerada a partir do mecanismo Logic Apps, execute o aplicativo lógico e revisite o site do Webhook Tester para obter mais detalhes.

## <a name="next-steps"></a>Próximas etapas

* [Monitorar seu aplicativo lógico](../logic-apps/monitor-logic-apps.md)
