---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 5c3277394350036b8863185e83b7cfcd4975be1c
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095102"
---
Ele permite que você registre e cancele o registro de um hub de notificação por meio do serviço de back-end que você criou. 

Um alerta é exibido quando uma ação é especificada e o aplicativo está em primeiro plano. Caso contrário, as notificações aparecem no centro de notificações.

> [!NOTE]
> Normalmente, você executaria as ações de registro (e cancelamento de registro) durante o ponto adequado no ciclo de vida do aplicativo (ou como parte da experiência de primeira execução, talvez) sem entradas explícitas de registro/cancelamento de registro do usuário. No entanto, este exemplo exigirá uma entrada de usuário explícita para permitir que essa funcionalidade seja explorada e testada com mais facilidade.
