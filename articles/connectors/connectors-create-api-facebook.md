---
title: Conectar-se ao Facebook
description: Gerenciar a linha do tempo e a página com as APIs REST do Facebook e os Aplicativos Lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 1aa936f902dc17c9a401959c19824f6c581547b1
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789827"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Gerenciar sua linha do tempo e página do Facebook usando aplicativos lógicos do Azure

Conecte-se ao Facebook e publique em uma linha do tempo, recebe um feed de página e muito mais. Com o Facebook, você pode:

* Criar seu fluxo de negócios com base nos dados que você obtém do Facebook. 
* Usar um gatilho quando uma nova publicação for recebida.
* Usar ações que publicam em sua linha do tempo, obtêm uma feed de página e mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando há uma nova publicação em sua linha do tempo, você pode publicá-la e enviá-la ao seu feed do Twitter. 

É possível começar criando um aplicativo lógico agora. Consulte [Criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Criar uma conexão com o Facebook

Quando você adiciona esse conector aos seus aplicativos lógicos, precisa autorizar que os aplicativos lógicos se conectem ao Facebook.

1. Entre na sua conta do Facebook.

2. Selecione **Autorizar**e permita que seus aplicativos lógicos se conectem e usem o Facebook. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Referência de conector

Para obter detalhes técnicos, como gatilhos, ações e limites, conforme descrito pelo arquivo OpenAPI (anteriormente Swagger) do conector, consulte a [página de referência do conector](/connectors/facebook/).

## <a name="next-steps"></a>Próximos passos

* Saiba mais sobre outros [conectores de Aplicativos Lógicos](../connectors/apis-list.md)