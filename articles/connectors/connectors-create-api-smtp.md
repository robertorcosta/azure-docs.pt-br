---
title: Conectar-se ao SMTP de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que enviam email por meio de sua conta de protocolo SMTP (SMTP) usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: fb501a158c839e6d4d71fc2af5ae50e48c248466
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789147"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Enviar um email de sua conta SMTP com Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector de protocolo SMTP (SMTP), você pode criar tarefas automatizadas e fluxos de trabalho que enviam email usando sua conta SMTP. Você também pode fazer com que outras ações usem a saída das ações de SMTP. Por exemplo, depois que o SMTP envia um email, você pode notificar sua equipe no Slack com o conector do Slack. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Suas credenciais de usuário e conta SMTP

  Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta SMTP.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta SMTP. Para usar uma ação de SMTP, inicie o aplicativo lógico com um gatilho, como um gatilho do Salesforce se você tiver uma conta do Salesforce.

  Por exemplo, você pode iniciar o aplicativo lógico com o gatilho **Quando um registro é criado** do Salesforce. 
  Esse gatilho é acionado sempre que um novo registro, como um lead, for criado no Salesforce. 
  Então, você pode dar sequência neste gatilho com a ação de SMTP **Enviar Email**. Dessa forma, quando o novo registro for criado, seu aplicativo lógico enviará um email a respeito do novo registro usando sua conta SMTP.

## <a name="connect-to-smtp"></a>Conectar-se ao SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Na última etapa em que você deseja adicionar uma ação de SMTP, escolha **Nova etapa**. 

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Na caixa de pesquisa, insira "smtp" como filtro. Na lista de ações, selecione a ação desejada.

1. Quando solicitado, forneça essas informações de conexão:

   | Propriedade | obrigatórios | Descrição |
   |----------|----------|-------------|
   | **Nome da Conexão** | SIM | Um nome para a conexão com seu servidor SMTP | 
   | **Endereço do Servidor SMTP** | SIM | O endereço do seu servidor SMTP | 
   | **Nome de usuário** | SIM | O nome de usuário da sua conta SMTP | 
   | **Senha** | SIM | A senha da sua conta SMTP | 
   | **Porta do Servidor SMTP** | Não | Uma porta específica no servidor SMTP que você deseja usar | 
   | **Habilitar SSL?** | Não | Ligar ou desligar a criptografia SSL. | 
   |||| 

1. Forneça os detalhes necessários para a ação selecionada. 

1. Salve seu aplicativo lógico ou continuar criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/smtpconnector/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)