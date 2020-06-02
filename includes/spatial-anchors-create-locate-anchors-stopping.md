---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006469"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausar, redefinir ou interromper a sessão

Para interromper a sessão temporariamente, você pode invocar `Stop()`. Isso interromperá inspetores e o processamento de ambiente, mesmo se você invocar `ProcessFrame()`. Em seguida, você pode invocar `Start()` para retomar o processamento. Ao retomar, os dados de ambiente já capturados na sessão são mantidos.
