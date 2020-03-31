---
ms.openlocfilehash: c5ca85c0dfe8d601821a78c02b2230c0909c8003
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67171633"
---
Não é possível atualizar o local de uma âncora após ela ter sido criada no serviço: você precisa criar uma âncora e excluir a antiga para acompanhar uma nova posição.

Se você não precisar localizar uma âncora para atualizar as propriedades dela, poderá usar o método `GetAnchorPropertiesAsync()`, que retorna um objeto `CloudSpatialAnchor` com propriedades.
