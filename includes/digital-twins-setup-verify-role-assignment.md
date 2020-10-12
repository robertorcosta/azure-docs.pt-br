---
author: baanders
description: incluir arquivo para verificar a atribuição de função na configuração de gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: fdb3bd034d93e623037be9fa0721a805924af5c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88864679"
---
Uma maneira de verificar se você configurou com êxito a atribuição de função é exibir as atribuições de função para a instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com). Vá para a instância do gêmeos digital do Azure na portal do Azure (você pode procurá-lo na página de [instâncias do gêmeos digital do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) ou pesquisar seu nome na barra de pesquisa do Portal).

Em seguida, exiba todas as suas funções atribuídas em *controle de acesso (iam) > atribuições de função*. O usuário deve aparecer na lista com uma função do proprietário do *gêmeos digital do Azure (versão prévia)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Exibição das atribuições de função para uma instância do gêmeos digital do Azure no portal do Azure":::