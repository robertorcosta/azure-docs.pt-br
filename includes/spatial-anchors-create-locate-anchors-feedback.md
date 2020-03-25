---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694271"
---
## <a name="provide-feedback-to-the-user"></a>Fornecer comentários ao usuário

Você pode escrever um código para processar o evento atualizado da sessão. Esse evento é acionado toda vez que a sessão aprimora a compreensão do ambiente em que você está. Essa ação permite que você:

- Use a classe `UserFeedback` para fornecer comentários ao usuário à medida que o dispositivo for movido e a sessão atualizar a compreensão do ambiente. Para fazer isso,
- Determine em que ponto há dados espaciais controlados suficientes para criar âncoras espaciais. Determine isso com `ReadyForCreateProgress` ou `RecommendedForCreateProgress`. Depois que `ReadyForCreateProgress` estiver acima de 1, teremos dados suficientes para salvar uma âncora espacial de nuvem, embora recomendemos aguardar até que `RecommendedForCreateProgress` esteja acima de 1 para fazer isso.
