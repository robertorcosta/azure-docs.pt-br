---
title: Configure a autenticação do cliente de chamadas recebidas - Azure Event Grid IoT Edge | Microsoft Docs
description: Configure protocolos de API expostos pela Event Grid no IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841784"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Configurar a autenticação do cliente de chamadas recebidas

Este guia dá exemplos das possíveis configurações de autenticação do cliente para o módulo Event Grid. O módulo Event Grid suporta dois tipos de autenticação do cliente:

* Baseado em chave SAS (SAS) baseado em chave de assinatura de acesso compartilhado
* Baseado em certificados

Consulte o guia [de segurança e autenticação](security-authentication.md) para todas as configurações possíveis.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Habilite a autenticação do cliente baseada em certificados, sem certificados auto-assinados

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

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Habilite a autenticação do cliente baseada em certificados, permita certificados auto-assinados

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
>Defina a **propriedade inbound__clientAuth__clientCert__allowUnknownCA** **verdadeira** apenas em ambientes de teste, pois você normalmente pode usar certificados auto-assinados. Para cargas de trabalho de produção, recomendamos que você defina esta propriedade como **falsa** e certificados de uma autoridade de certificado (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Habilitar a autenticação do cliente baseada em certificados e sas-key

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
>A autenticação de cliente baseada em chaves SAS permite que um módulo de borda não IoT faça operações de gerenciamento e tempo de execução, assumindo, é claro, que as portas API estejam acessíveis fora da rede IoT Edge.
