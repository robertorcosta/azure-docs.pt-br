---
title: Conecte-se ao Office 365 Outlook
description: Automatize tarefas e fluxos de trabalho que gerenciam e-mails, contatos e calendários no Office 365 Outlook usando aplicativos de lógica do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75732647"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Gerenciar emails, contatos e calendários no Outlook do Office 365 usando os Aplicativos Lógicos do Azure

Com [o Azure Logic Apps](../logic-apps/logic-apps-overview.md) e o [conector Office 365 Outlook,](/connectors/office365connector/)você pode criar tarefas e fluxos de trabalho automatizados que gerenciam sua conta do Office 365 através da criação de aplicativos lógicos. Por exemplo, você automatiza essas tarefas:

* Receba, envie e responda por e-mail. 
* Agende reuniões em sua agenda.
* Adicione e edite contatos. 

Você pode usar qualquer gatilho para iniciar seu fluxo de trabalho, por exemplo, quando um novo e-mail chega, quando um item de calendário é atualizado ou quando um evento acontece em um serviço de diferença, como o Salesforce. Você pode usar ações que respondam ao evento de gatilho, por exemplo, enviar um e-mail ou criar um novo evento de calendário. 

> [!NOTE]
> Para automatizar tarefas para uma @outlook.com ou @hotmail.com conta, use o [conector Outlook.com](../connectors/connectors-create-api-outlook.md).

## <a name="prerequisites"></a>Pré-requisitos

* Uma [conta do Office 365](https://www.office.com/)

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* O aplicativo lógico onde você deseja acessar sua conta do Office 365 Outlook. Para iniciar seu fluxo de trabalho com um gatilho do Office 365 Outlook, você precisa ter um [aplicativo de lógica em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para adicionar uma ação do Office 365 Outlook ao seu fluxo de trabalho, seu aplicativo lógico precisa já ter um gatilho.

## <a name="add-a-trigger"></a>Adicionar um gatilho

Um [gatilho](../logic-apps/logic-apps-overview.md#logic-app-concepts) é um evento que inicia o fluxo de trabalho em seu aplicativo lógico. Este aplicativo lógico de exemplo usa um gatilho de "pesquisa" que verifica qualquer evento de calendário atualizado em sua conta de e-mail, com base no intervalo e frequência especificados.

1. No [portal Azure,](https://portal.azure.com)abra seu aplicativo de lógica em branco no Logic App Designer.

1. Na caixa de pesquisa, insira `office 365 outlook` como o filtro. Este exemplo seleciona **Quando um próximo evento está começando em breve**.
   
   ![Selecione o gatilho para iniciar seu aplicativo lógico](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. Se você for solicitado a fazer login, forneça suas credenciais do Office 365 para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se sua conexão já existir, forneça as informações para as propriedades do gatilho.

   Este exemplo seleciona o calendário que o gatilho verifica, por exemplo:

   ![Configure as propriedades do gatilho](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. No gatilho, defina os valores **de frequência** e **intervalo.** Para adicionar outras propriedades de gatilho disponíveis, como **fuso horário,** selecione essas propriedades na lista **Adicionar novo parâmetro.**

   Por exemplo, se você quiser que o gatilho verifique o calendário a cada `15`15 minutos, defina a **freqüência** como **Minuto**e defina o **intervalo** como . 

   ![Defina a frequência e o intervalo para o gatilho](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. Selecione **Salvar** na barra de ferramentas do designer.

Agora adicione uma ação que corre após o gatilho disparar. Por exemplo, você pode adicionar a ação **de mensagem** Twilio Send, que envia um texto quando um evento de calendário começa em 15 minutos.

## <a name="add-an-action"></a>Adicionar uma ação

Uma [ação](../logic-apps/logic-apps-overview.md#logic-app-concepts) é uma operação que é executada pelo fluxo de trabalho em seu aplicativo lógico. Este aplicativo lógico de exemplo cria um novo contato no Office 365 Outlook. Você pode usar a saída de outro gatilho ou ação para criar o contato. Por exemplo, suponha que seu aplicativo lógico use o gatilho Dynamics 365, **quando um registro é criado**. Você pode adicionar o Office 365 Outlook Criar ação de **contato** e usar as saídas do gatilho SalesForce para criar o novo contato.

1. No [portal do Azure](https://portal.azure.com), abra o aplicativo lógico no Designer do aplicativo lógico.

1. Para adicionar uma ação como a última etapa do seu fluxo de trabalho, selecione **Nova etapa**. 

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre essas etapas. Selecione o**+** sinal de adição () que aparece e **selecione Adicionar uma ação**.

1. Na caixa de pesquisa, insira `office 365 outlook` como o filtro. Este exemplo seleciona **Criar contato**.

   ![Selecione a ação a ser executada em seu aplicativo lógico](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. Se você for solicitado a fazer login, forneça suas credenciais do Office 365 para que seu aplicativo lógico possa se conectar à sua conta. Caso contrário, se sua conexão já existir, forneça as informações para as propriedades da ação.

   Este exemplo seleciona a pasta de contatos onde a ação cria o novo contato, por exemplo:

   ![Configure as propriedades da ação](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   Para adicionar outras propriedades de ação disponíveis, selecione essas propriedades na lista **Adicionar novos parâmetros.**

1. Selecione **Salvar** na barra de ferramentas do designer.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Para obter detalhes técnicos sobre gatilhos, ações e limites descritos no arquivo Swagger do conector, consulte a [página de referência do conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)
