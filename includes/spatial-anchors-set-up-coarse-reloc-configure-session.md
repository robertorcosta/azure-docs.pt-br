---
author: bucurb
ms.author: bobuc
ms.date: 09/18/2019
ms.service: azure-spatial-anchors
ms.topic: include
ms.openlocfilehash: 574003a150ef294aa6a2ebc035fe48cf877dec66
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76545187"
---
## <a name="configure-the-cloud-spatial-anchor-session"></a>Configurar a sessão de âncora espacial de nuvem

Cuidaremos da configuração da sessão de âncora espacial de nuvem em seguida. Na primeira linha, definimos o provedor de sensor na sessão. De agora em diante, qualquer âncora que criamos durante a sessão será associada a um conjunto de leituras de sensor. Em seguida, criamos uma instância de critérios de localização de dispositivo próximo e o inicializamos para corresponder aos requisitos do aplicativo. Por fim, instruímos a sessão a usar dados de sensor ao localizar âncoras criando um observador de nossos critérios de quase dispositivo.