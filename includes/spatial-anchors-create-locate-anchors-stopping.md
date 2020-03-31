---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "67171643"
---
## <a name="pause-reset-or-stop-the-session"></a>Pausar, redefinir ou interromper a sessão

Para interromper a sessão temporariamente, você pode invocar `Stop()`. Isso interromperá eventuais inspetores e o processamento de ambiente, mesmo se você invocar ProcessFrame(). Em seguida, você pode invocar `Start()` para retomar o processamento. Ao retomar, os dados de ambiente já capturados na sessão são mantidos.
