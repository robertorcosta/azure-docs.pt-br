---
title: Conectar ao Outlook.com
description: Automatize tarefas e fluxos de trabalho que gerenciam e-mails, calendários e contatos em Outlook.com usando aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75707179"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Gerencie e-mails, calendários e contatos em Outlook.com usando aplicativos azure logic

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [conector Outlook.com,](/connectors/outlook/)você @outlook.com @hotmail.com pode criar tarefas e fluxos de trabalho automatizados que gerenciam sua conta ou a criação de aplicativos lógicos. Por exemplo, você automatiza essas tarefas:

* Receba, envie e responda por e-mail.
* Agende reuniões em sua agenda.
* Adicione e edite contatos.

Você pode usar qualquer gatilho para iniciar seu fluxo de trabalho, por exemplo, quando um novo e-mail chega, quando um item de calendário é atualizado ou quando um evento acontece em um serviço de diferença. Você pode usar ações que respondam ao evento de gatilho, por exemplo, enviar um e-mail ou criar um novo evento de calendário.

> [!NOTE]
> Para automatizar tarefas para uma @fabrikam.onmicrosoft.comconta de trabalho da Microsoft, use o [conector Do Office 365 Outlook](../connectors/connectors-create-api-office365-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta Outlook.com](https://outlook.live.com/owa/)

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* O aplicativo lógico no qual você deseja acessar a conta do Outlook.com. Para iniciar seu fluxo de trabalho com um gatilho Outlook.com, você precisa ter um [aplicativo de lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para adicionar uma ação Outlook.com ao seu fluxo de trabalho, seu aplicativo lógico precisa já ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um gatilho

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho em seu aplicativo lógico. Este aplicativo lógico de exemplo usa um gatilho de "pesquisa" que verifica qualquer novo e-mail em sua conta de e-mail, com base no intervalo e frequência especificados.

1. No [portal Azure,](https://portal.azure.com)abra seu aplicativo de lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, insira "outlook.com" como filtro. Para este exemplo, **selecione Quando um novo e-mail chegar**.

1. Se você for solicitado a fazer login, forneça suas credenciais de Outlook.com para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se a sua conexão já existir, forneça as informações para as propriedades do gatilho:

1. No gatilho, defina os valores **de frequência** e **intervalo.**

   Por exemplo, se você quiser que o gatilho faça a pesquisa a cada 15 minutos, defina a **freqüência** como **Minuto**e defina o **intervalo** para **15**.

1. Na barra de ferramentas do designer, **selecione Salvar**, que salva seu aplicativo lógico.

Para responder ao gatilho, adicione outra ação. Por exemplo, você pode adicionar a ação **de mensagem** Twilio Send, que envia um texto quando um e-mail chega.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é executada pelo fluxo de trabalho em seu aplicativo lógico. Este aplicativo lógico de exemplo envia um e-mail de sua conta Outlook.com. Você pode usar a saída de outro gatilho para preencher a ação. Por exemplo, suponha que seu aplicativo lógico use o SalesForce **Quando um objeto é criado** gatilho. Você pode adicionar o Outlook.com Enviar uma ação **de e-mail** e usar as saídas do gatilho SalesForce no e-mail.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Para adicionar uma ação como a última etapa do seu fluxo de trabalho, selecione **Nova etapa**. 

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, digite "outlook.com" como seu filtro. Para este exemplo, selecione **Enviar um e-mail**. 

1. Se você for solicitado a fazer login, forneça suas credenciais de Outlook.com para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se sua conexão já existir, forneça as informações para as propriedades da ação.

1. Na barra de ferramentas do designer, **selecione Salvar**, que salva seu aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo de Swagger do conector, confira a [página de referência do conector](/connectors/outlook/). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
