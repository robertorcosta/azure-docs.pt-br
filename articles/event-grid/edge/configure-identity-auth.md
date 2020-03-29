---
title: Configurar identidade - Azure Event Grid IoT Edge | Microsoft Docs
description: Configure a identidade do módulo Event Grid
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841758"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Configurar identidade para o módulo Event Grid

Este artigo mostra como configurar a identidade para Grid on Edge. Por padrão, o módulo Event Grid apresenta seu certificado de identidade configurado pelo daemon de segurança IoT. Event Grid on Edge apresenta seu certificado de identidade com suas chamadas de saída quando entrega eventos. Um assinante pode então validar seu módulo Event Grid que enviou o evento antes de aceitar.

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="always-present-identity-certificate"></a>Sempre apresentar certificado de identidade
Aqui está uma configuração de exemplo para sempre apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Não apresente certificado de identidade
Aqui está uma configuração de exemplo para não apresentar um certificado de identidade em chamadas de saída. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```
