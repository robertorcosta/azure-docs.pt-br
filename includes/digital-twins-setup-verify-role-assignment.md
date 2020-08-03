---
author: baanders
description: incluir arquivo para verificar a atribuição de função na configuração de gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e651b02bf72ced58b6cba637a68ace3258514176
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87405544"
---
Uma maneira de verificar se você configurou com êxito a atribuição de função é exibir as atribuições de função para a instância do gêmeos digital do Azure no [portal do Azure](https://portal.azure.com). Acesse a página do portal das [instâncias do Azure digital gêmeos](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances) (você pode usar esse link ou pesquise-a na barra de pesquisa do Portal) e selecione o nome da instância que você deseja verificar. 

Em seguida, exiba todas as suas funções atribuídas em *controle de acesso (iam) > atribuições de função*. O usuário deve aparecer na lista com uma função do proprietário do *gêmeos digital do Azure (versão prévia)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Exibição das atribuições de função para uma instância do gêmeos digital do Azure no portal do Azure":::