---
title: Autoridade de certificação permitida para habilitar HTTPS personalizado na porta frontal do Azure
description: Se você estiver usando seu próprio certificado para habilitar o HTTPS em um domínio de 0Custom de porta frontal do Azure, você deverá usar uma autoridade de certificação (CA) permitida para criá-lo.
services: frontdoor
documentationcenter: ''
author: duongau
ms.assetid: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2018
ms.author: duau
ms.openlocfilehash: 973df2505eefc2a46aa105b874f32b61fe6e8b36
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91269787"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-front-door"></a>Autoridades de certificação permitidas para habilitar o HTTPS personalizado na porta frontal do Azure

Para um domínio personalizado do Azure front door, ao [habilitar o recurso https usando seu próprio certificado](front-door-custom-domain-https.md?tabs=option-2-enable-https-with-your-own-certificate), você deve usar uma AC (autoridade de certificação) permitida para criar seu certificado TLS/SSL. Caso contrário, se você usar uma AC não permitida ou um certificado autoassinado, sua solicitação será rejeitada.

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]
