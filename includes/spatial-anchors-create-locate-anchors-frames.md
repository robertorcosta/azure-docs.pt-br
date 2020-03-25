---
ms.openlocfilehash: 505670e719e86086dbf7721b4298ec913220f928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76694517"
---
## <a name="provide-frames-to-the-session"></a>Fornecer quadros para a sessão

A sessão de âncora espacial funciona pelo mapeamento do espaço em torno do usuário. Fazer isso ajuda a determinar o local em que se encontram as âncoras. As plataformas móveis (iOS e Android) exigem uma chamada nativa ao feed da câmera para obter quadros da biblioteca RA da plataforma. Por outro lado, o HoloLens examina constantemente o ambiente e, portanto, não há necessidade de uma chamada específica como em dispositivos móveis.