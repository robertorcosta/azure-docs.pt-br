---
title: Conectar-se ao Trello de aplicativos lógicos do Azure
description: Automatizar tarefas e fluxos de trabalho que monitoram e gerenciam listas, quadros e cartões em seus projetos do Trello usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789130"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitorar e gerenciar o Trello com os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector do Trello, você pode criar tarefas automatizadas e fluxos de trabalho que monitoram e gerenciam listas, cartões, quadros e membros da equipe do Trello e assim por diante, por exemplo:

* Monitore quando novos cartões são adicionados a listas e quadros. 
* Crie, obtenha e gerencie listas, cartões e quadros.
* Adicione comentários e membros aos cartões.
* Liste quadros, rótulos de quadro, cartões em quadros, comentários de cartão, membros de cartão, membros da equipe e equipes em que você é um membro. 
* Obtenha as equipes.

Você pode usar gatilhos para obter respostas da sua conta do Trello e disponibilizar a saída para outras ações. Você pode usar ações que executam tarefas com sua conta do Trello. Você também pode fazer com que outras ações usem a saída das ações do Trello. Por exemplo, quando um novo cartão é adicionado ao painel ou à lista, você pode enviar mensagens com o conector do Slack. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso você não tenha uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* Suas credenciais de usuário e conta do Trello

  Suas credenciais autorizam o aplicativo lógico a criar uma conexão e acessar sua conta do Trello.

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico no qual você deseja acessar a conta do Trello. Para iniciar com um gatilho do Trello, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação do Trello, inicie seu aplicativo lógico com um gatilho, por exemplo, o gatilho **Recorrência**.

## <a name="connect-to-trello"></a>Conectar-se ao Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Para aplicativos lógicos em branco, na caixa de pesquisa, insira "trello" como o filtro. Na lista de gatilhos, selecione o gatilho desejado. 

   -ou-

   Para aplicativos lógicos existentes, na última etapa em que deseja adicionar uma ação, escolha **Nova etapa**. 
   Na caixa de pesquisa, insira "trello" como o filtro. 
   Na lista de ações, selecione a ação desejada.

   Para adicionar uma ação entre as etapas, mova o ponteiro sobre a seta entre as etapas. 
   Escolha o sinal de adição ( **+** ) que aparece e, em seguida, selecione **Adicionar uma ação**.

1. Se você for solicitado a entrar no Trello, autorize o acesso ao aplicativo lógico e entre.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/trello/) do conector.

## <a name="get-support"></a>Obter suporte

* Em caso de dúvidas, visite o [Fórum dos Aplicativos Lógicos do Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)