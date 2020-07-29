---
title: Autoridade de certificação permitida para habilitar HTTPS personalizado na porta frontal do Azure
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
ms.openlocfilehash: 611f5730afed4c3a84b81d6acfd33b633c532bbc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80874663"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificação permitidas para habilitar o HTTPS personalizado na porta frontal do Azure

Para um domínio personalizado do Azure front door, ao [habilitar o recurso https usando seu próprio certificado](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), você deve usar uma AC (autoridade de certificação) permitida para criar seu certificado TLS/SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
