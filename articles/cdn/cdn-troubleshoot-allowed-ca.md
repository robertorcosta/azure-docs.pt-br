---
title: CA permitida para habilitar HTTPS personalizado no CDN do Azure
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919967"
---
# <a name="allowed-certificate-authorities-for-enabling-custom-https-on-azure-cdn"></a>Autoridades de certificação permitidas para habilitar HTTPS personalizado no CDN do Azure

Você deve atender aos requisitos específicos de certificado quando [habilitar o recurso HTTPS usando seu próprio certificado](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) para um domínio personalizado cdn (Azure Content Delivery Network, rede de entrega de conteúdo do Azure). O **Azure CDN Standard do** perfil microsoft requer um certificado de uma das autoridades de certificado (CA) aprovadas na lista a seguir. Se um certificado de um CA não aprovado ou se um certificado auto-assinado for usado, a solicitação será rejeitada. **Azure CDN Standard da Verizon** e **Azure CDN Premium dos** perfis Verizon aceitam qualquer certificado válido de qualquer CA válido.

> [!NOTE]
> A opção de usar seu próprio certificado para habilitar o recurso HTTPS de domínio personalizado *não* está disponível para **o Azure CDN Standard a partir de perfis Akamai.** 
>

[!INCLUDE [cdn-front-door-allowed-ca](../../includes/cdn-front-door-allowed-ca.md)]

