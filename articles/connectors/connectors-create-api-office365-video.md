---
title: Conecte-se ao vídeo do Office 365
description: Automatize tarefas e fluxos de trabalho que gerenciam vídeos no Office 365 Video usando aplicativos azure logic
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75665778"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Gerenciar vídeos no Vídeo Office365 usando aplicativos azure logic

Conecte-se ao Vídeo do Office 365 para obter informações sobre um vídeo do Office 365, obter uma lista de vídeos e muito mais. Com o Vídeo do Office 365, é possível:

* Criar seu fluxo de negócios baseado nos dados obtidos do Vídeo do Office 365. 

* Usar ações que verificam o status do portal do vídeo, obter uma lista de todos os vídeos em um canal e muito mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. 

Por exemplo, é possível usar o conector da Pesquisa do Bing para pesquisar vídeos do Office 365 e usar o conector do Vídeo do Office 365 para obter informações sobre determinado vídeo. Se o vídeo atender às suas necessidades, você poderá postá-lo no Facebook.

É possível começar criando um aplicativo lógico agora. Consulte [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Conecte-se ao vídeo do Office365

Ao adicionar esse conector aos seus aplicativos lógicos, é necessário entrar em sua conta de Vídeo do Office 365 e permitir que os aplicativos lógicos se conectem à sua conta.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Depois de criar a conexão, insira as propriedades do vídeo do Office 365, como o nome de locatário ou a ID do canal. 

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja os gatilhos e ações definidos no swagger e também os limites nos [detalhes do conector](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)