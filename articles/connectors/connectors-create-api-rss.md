---
title: Conectar-se aos RSS feeds com os Aplicativos Lógicos do Azure
description: Automatize tarefas e fluxos de trabalho que monitoram e gerenciam RSS feeds usando os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 1e95b7bfce3d57dfe579a5622c3c13fac2b6af68
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: pt-BR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834808"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Gerencie RSS feeds usando os Aplicativos Lógicos do Azure

Com os Aplicativos Lógicos do Azure e o conector de RSS, é possível criar fluxos de trabalho e tarefas automatizadas para qualquer RSS feed, por exemplo:

* Monitorar quando itens de RSS feeds são publicados.
* Listar todos os itens de um RSS feed.

RSS (Rich Site Summary), também chamado de Really Simple Syndication, é um formato popular para sindicalização da Web e é usado para publicar conteúdos atualizados com frequência, como postagens de blog e manchetes de notícias. Muitos editores de conteúdo fornecem um RSS feed para que os usuários possam assinar um determinado conteúdo. 

É possível usar um gatilho RSS que obtém respostas de um RSS feed e disponibiliza a saída para outras ações. Você pode usar uma ação de RSS em seus aplicativos lógicos para executar uma tarefa com o RSS feed. Se ainda não estiver familiarizado com os aplicativos lógicos, veja [O que é o Aplicativo Lógico do Azure?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, [inscreva-se em uma conta gratuita do Azure](https://azure.microsoft.com/free/). 

* A URL de um RSS feed

* Conhecimento básico sobre [como criar aplicativos lógicos](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* O aplicativo lógico em que você deseja acessar um RSS feed. Para iniciar com um gatilho RSS, [crie um aplicativo lógico em branco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar uma ação de RSS, inicie seu aplicativo lógico com outro gatilho, por exemplo, o gatilho de **Recorrência**.

## <a name="connect-to-an-rss-feed"></a>Conectar-se com um RSS feed

1. Entre no [portal do Azure](https://portal.azure.com) e abra seu aplicativo lógico no Designer de Aplicativo Lógico, se ele ainda não estiver aberto.

1. Escolha um caminho: 

   * Para aplicativos lógicos em branco, na caixa de pesquisa, insira "rss" como o filtro. Na lista de gatilhos, selecione o gatilho desejado. 

     -ou-

   * Para aplicativos lógicos existentes, na etapa em que deseja adicionar uma ação, escolha **Nova etapa**. Na caixa de pesquisa, insira "rss" como o seu filtro. Na lista de ações, selecione a ação desejada.

1. Forneça os detalhes necessários para o gatilho ou a ação selecionada e continue criando o fluxo de trabalho do aplicativo lógico.

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos sobre gatilhos, ações e limites, que são explicados na descrição da OpenAPI do conector (anteriormente conhecido como Swagger), veja a [página de referência](/connectors/rss/) do conector.

## <a name="get-support"></a>Obtenha suporte

* Em caso de dúvidas, visite a [página de perguntas e respostas da Microsoft sobre os Aplicativos Lógicos do Azure](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Para enviar ou votar em ideias de recurso, visite o [site de comentários do usuário de Aplicativos Lógicos](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)