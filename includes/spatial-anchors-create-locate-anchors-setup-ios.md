---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006453"
---
## <a name="setting-up-the-library"></a>Configurar a biblioteca

Invoque `Start()` para habilitar sua sessão a processar os dados do ambiente.

Para manipular eventos gerados pela sua sessão, defina a propriedade `delegate` de sua sessão para um objeto, como o modo de exibição. Esse objeto deve implementar o protocolo `SSCCloudSpatialAnchorSessionDelegate`.
