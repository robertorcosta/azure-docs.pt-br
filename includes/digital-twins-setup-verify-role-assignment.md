---
author: baanders
description: incluir arquivo para verificar a atribuição de função na configuração de gêmeos digital do Azure
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: e5e4bced12ffe9dc674b25a9a6513218cf8989e4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87095709"
---
Uma maneira de verificar se você configurou com êxito a atribuição de função é exibir as atribuições de função para a instância do gêmeos digital do Azure no portal do Azure. Na página do portal das [instâncias do gêmeos digital do Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.DigitalTwins%2FdigitalTwinsInstances), selecione o nome da instância que você deseja verificar. Em seguida, exiba todas as suas funções atribuídas em *controle de acesso (iam) > atribuições de função*. O usuário deve aparecer na lista com uma função do proprietário do *gêmeos digital do Azure (versão prévia)*. 

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/verify-role-assignment.png" alt-text="Exibição das atribuições de função para uma instância do gêmeos digital do Azure no portal do Azure":::