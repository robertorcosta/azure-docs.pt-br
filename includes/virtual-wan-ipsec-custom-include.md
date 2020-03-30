---
title: incluir arquivo
description: incluir arquivo
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168375"
---
Ao trabalhar com políticas iPsec personalizadas, tenha em mente os seguintes requisitos:

* **IKE** - Para ike, você pode selecionar qualquer parâmetro da Criptografia IKE, além de qualquer parâmetro do IKE Integrity, além de qualquer parâmetro do DH Group.
* **IPsec** - Para IPsec, você pode selecionar qualquer parâmetro da IPsec Encryption, além de qualquer parâmetro da IPsec Integrity, mais PFS. Se algum dos parâmetros para IPsec Encryption ou IPsec Integrity for GCM, então os parâmetros para ambas as configurações devem ser GCM.

>[!NOTE]
> Com as políticas de IPsec personalizadas, não há conceito de respondente e iniciador (ao contrário das políticas de IPsec padrão). Ambos os lados (no local e no gateway Azure VPN) usarão as mesmas configurações para a Fase 1 e a Fase 2 do IKE. Os protocolos IKEv1 e IKEv2 são suportados. Não há suporte para o Azure apenas como um respondente.
>

**Configurações e parâmetros disponíveis**

| Configuração | Parâmetros |
|--- |--- |
| Criptografia IKE | AES256, AES192, AES128 |
| Integridade ike | SHA384, SHA256, SHA1 |
| Grupo DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Criptografia IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Integridade do IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Grupo PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
