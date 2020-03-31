---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67171624"
---
Assim como os tokens de acesso, se um token do Azure AD não estiver definido, você deverá manipular o evento TokenRequired ou implementar o método tokenRequired no protocolo delegado.

Você pode manipular o evento de maneira síncrona definindo a propriedade nos argumentos do evento.
