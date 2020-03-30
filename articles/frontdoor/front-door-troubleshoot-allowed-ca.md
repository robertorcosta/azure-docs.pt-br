---
title: CA permitida para habilitar HTTPS personalizado na porta da frente do Azure
description: Se você estiver usando seu próprio certificado para habilitar HTTPS em um domínio personalizado, deverá usar uma AC (autoridade de certificação) permitida para criá-lo.
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: sharadag
ms.openlocfilehash: cac6bc9895f2b8778f2b27cc6b1dff4d4b898ae7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471516"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificados permitidas para habilitar HTTPS personalizado na Porta da Frente do Azure

Para um domínio personalizado do Azure Front Door, quando você [habilita o recurso HTTPS usando seu próprio certificado,](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate)você deve usar uma autoridade de certificado (CA) permitida para criar seu certificado SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
