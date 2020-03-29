---
title: Exibir e criar consultas para aplicativos lógicos nos logs do Azure Monitor
description: Exibir e criar consultas nos registros do Monitor do Azure para aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908066"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Exibir e criar consultas para monitoramento e rastreamento nos registros do Monitor do Azure para aplicativos de lógica do Azure

Você pode visualizar as consultas subjacentes que produzem os resultados dos logs do [Monitor do Azure](../log-analytics/log-analytics-overview.md) e criar consultas que filtram os resultados com base em seus critérios específicos. Por exemplo, você pode encontrar mensagens baseado em um número de controle de intercâmbio específico. As consultas usam o [idioma de consulta Kusto,](https://aka.ms/LogAnalyticsLanguageReference)que você pode editar se quiser exibir diferentes resultados. Para obter mais informações, consulte [as consultas de log do Azure Monitor](../azure-monitor/log-query/query-language.md).

## <a name="prerequisites"></a>Pré-requisitos

* Um espaço de trabalho do Log Analytics. Se você não tiver um espaço de trabalho do Log Analytics, saiba[como criar um espaço de trabalho do Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Um aplicativo lógico que é configurado com o registro do Azure Monitor e envia essas informações para um espaço de trabalho do Log Analytics. Saiba [como configurar os logs do Azure Monitor para o seu aplicativo lógico](../logic-apps/monitor-logic-apps.md).

* Se você estiver usando uma conta de integração, certifique-se de configurar a conta com o registro do Azure Monitor para enviar essas informações para um espaço de trabalho do Log Analytics. Saiba como configurar o [registro do Azure Monitor para sua conta de integração](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Ver consultas por trás dos resultados

Para visualizar ou editar a consulta que produz os resultados no resumo do seu espaço de trabalho, siga estas etapas:

1. Em qualquer página de resultados, na parte inferior, selecione **Ver Tudo**.

   ![Veja todos os resultados](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   A página Logs é aberta e mostra a consulta por trás da página de resultados anteriores.

   ![Página de logs - exibição de consulta](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Na página **Logs,** você pode selecionar essas opções:

   * Para exibir os resultados da consulta como uma tabela, o editor de consulta, selecione **Tabela**.

   * Para alterar a consulta, atualize a seqüência de consultas e selecione **Executar** para exibir os resultados na tabela.

## <a name="create-your-own-query"></a>Crie sua própria consulta

Para encontrar ou filtrar resultados com base em propriedades ou valores específicos, você pode criar sua própria consulta começando a partir de uma consulta vazia ou usando uma consulta existente. Para obter mais informações, consulte [Iniciar com consultas de log no Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

1. No [portal Azure,](https://portal.azure.com)encontre e selecione seu espaço de trabalho log analytics.

1. No menu do espaço de trabalho, em **Geral,** **selecione Logs**.

1. Comece a partir de uma consulta vazia ou de quaisquer consultas existentes disponíveis.

   * Para verificar se existem consultas existentes, na barra de ferramentas de consulta, selecione **'Histórico de consultas de** > **amostras',** que mostra consultas de versões anteriores ou selecione **Explorador de consulta**, que mostra consultas pré-construídas.

     Por exemplo, a solução Logic Apps B2B fornece essas consultas pré-construídas:

     ![Comece com consultas pré-construídas da solução "Logic Apps B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Para começar a partir de uma consulta vazia, no editor de consulta, comece a digitar o [idioma de consulta kusto](../azure-monitor/log-query/query-language.md) para sua consulta.

     ![Comece com consulta vazia](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Próximas etapas

* [Esquemas de rastreamento AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Esquemas de rastreamento X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Esquemas de acompanhamento personalizado](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)