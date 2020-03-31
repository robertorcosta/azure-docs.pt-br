---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67171635"
---
## <a name="update-properties"></a>Atualizar as propriedades

Para atualizar as propriedades em uma âncora, use o método `UpdateAnchorProperties()`. Se dois ou mais dispositivos tentarem atualizar as propriedades para a mesma âncora ao mesmo tempo, usaremos um modelo de simultaneidade otimista. Ou seja, a primeira gravação ganhará.  Todas as outras gravações receberão um erro de "Simultaneidade": uma atualização das propriedades será necessária antes que seja possível tentar novamente.
