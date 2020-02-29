---
title: Autoridade de certificação permitida para habilitar HTTPS personalizado na CDN do Azure
description: Se você estiver usando seu próprio certificado para habilitar HTTPS em um domínio personalizado, deverá usar uma AC (autoridade de certificação) permitida para criá-lo.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 7b71611d43bc2d4de4c3e609462906c44fba0443
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919967"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autoridades de certificação permitidas para habilitar HTTPS personalizado no CDN do Azure

Você deve atender aos requisitos de certificado específicos ao [habilitar o recurso https usando seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) para um domínio personalizado da CDN (rede de distribuição de conteúdo) do Azure. O perfil **Azure CDN Standard da Microsoft** requer um certificado de uma das autoridades de certificação aprovadas na lista a seguir. Se um certificado de uma AC não aprovada ou se um certificado autoassinado for usado, a solicitação será rejeitada. Os perfis **da CDN standard do Azure da Verizon** e do **Azure CDN Premium da Verizon** aceitam qualquer certificado válido de qualquer AC válida.

> [!NOTE]
> A opção de usar seu próprio certificado para habilitar o recurso HTTPS de domínio personalizado *não* está disponível para os perfis **da CDN standard do Azure da Akamai** . 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

