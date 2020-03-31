---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 02/21/2019
ms.author: rgarcia
ms.openlocfilehash: 63725d55e2b2935ec6a899789249259b096865c3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/26/2020
ms.locfileid: "67171617"
---
### <a name="access-tokens"></a>Tokens de acesso

Tokens de acesso são um método mais robusto para autenticar com Âncoras Espaciais do Azure. Isso é válido especialmente à medida que você prepara seu aplicativo para uma implantação de produção. O resumo dessa abordagem é configurar um serviço de back-end com o qual seu aplicativo cliente possa se autenticar com segurança. O serviço de back-end interage com o AAD em runtime e com o serviço de token de Âncoras Espaciais do Azure para solicitar um token de acesso. Esse token é então entregue ao aplicativo cliente e usado no SDK para autenticar com as Âncoras Espaciais do Azure.
