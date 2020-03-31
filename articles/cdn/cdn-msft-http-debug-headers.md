---
title: Depurar cabeçalhos HTTP para CDN azure da Microsoft | Microsoft Docs
description: Os cabeçalhos de solicitação de cache de depuração fornecem informações adicionais sobre a política de cache aplicada ao ativo solicitado. Esses cabeçalhos são específicos do Azure CDN da Microsoft.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: magattus
ms.openlocfilehash: 297c65c1cd89163b8663819f844dc6c2a83fd1bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68814081"
---
# <a name="debug-http-header-for-azure-cdn-from-microsoft"></a>Depurar cabeçalho HTTP para CDN azure da Microsoft
O cabeçalho `X-Cache`de resposta de depuração, fornece detalhes sobre qual camada da pilha de CDN o conteúdo foi servido. Este cabeçalho é específico do Azure CDN da Microsoft.

### <a name="response-header-format"></a>Formato do cabeçalho de resposta

Cabeçalho | Descrição
-------|------------
X-Cache: TCP_HIT | Este cabeçalho é devolvido quando o conteúdo é servido a partir do cache de borda CDN. 
X-Cache: TCP_REMOTE_HIT | Este cabeçalho é retornado quando o conteúdo é servido a partir do cache regional CDN (camada de escudo Origin)
X-Cache: TCP_MISS | Este cabeçalho é devolvido quando há uma falha de cache, e o conteúdo é servido a partir da Origem. 


