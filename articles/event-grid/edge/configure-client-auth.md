---
title: Configurar a autenticação de cliente de chamadas de entrada-IoT Edge de grade de eventos do Azure | Microsoft Docs
description: Configure os protocolos de API expostos pela grade de eventos em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841784"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurar a autenticação de cliente de chamadas de entrada

Este guia fornece exemplos das possíveis configurações de autenticação de cliente para o módulo de grade de eventos. O módulo de grade de eventos dá suporte a dois tipos de autenticação de cliente:

* Baseada em chave de SAS (assinatura de acesso compartilhado)
* Baseado em certificados

Consulte o guia de [segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Habilitar a autenticação de cliente baseada em certificado, não há certificados autoassinados

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Habilitar a autenticação de cliente baseada em certificado, permitir certificados autoassinados

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Defina a propriedade **inbound__clientAuth__clientCert__allowUnknownCA** como **true** somente em ambientes de teste como você normalmente pode usar certificados autoassinados. Para cargas de trabalho de produção, recomendamos que você defina essa propriedade como **false** e certificados de uma autoridade de certificação (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Habilitar a autenticação de cliente baseada em certificado e na chave SAS

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>A autenticação de cliente baseada em chave SAS permite que um módulo sem IoT Edge faça gerenciamento e operações de tempo de execução supondo que as portas de API estejam acessíveis fora da rede IoT Edge.
