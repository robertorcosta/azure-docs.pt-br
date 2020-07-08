---
title: Configurar a autenticação de assinante de webhook-grade de eventos do Azure IoT Edge | Microsoft Docs
description: Configurar a autenticação do assinante do webhook
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 101dcae5870322878cec48098f2efae32cc68c14
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "76841723"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurar a autenticação do assinante do webhook

Este guia fornece exemplos de configurações possíveis de assinante de webhook para um módulo de grade de eventos. Por padrão, somente pontos de extremidade HTTPS são aceitos para assinantes do webhook. O módulo de grade de eventos será rejeitado se o assinante apresentar um certificado autoassinado.

## <a name="allow-only-https-subscriber"></a>Permitir somente assinante HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Permitir assinante HTTPS com certificado autoassinado

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Defina a propriedade `outbound__webhook__allowUnknownCA` como `true` somente em ambientes de teste, pois você normalmente pode usar certificados autoassinados. Para cargas de trabalho de produção, recomendamos que elas sejam definidas como **false**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Permitir assinante HTTPS, mas ignorar validação de certificado

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=true",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

>[!NOTE]
>Defina a propriedade `outbound__webhook__skipServerCertValidation` como `true` somente em ambientes de teste, pois talvez você não esteja apresentando um certificado que precise ser autenticado. Para cargas de trabalho de produção, recomendamos que elas sejam definidas como **false**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Permitir HTTP e HTTPS com certificados autoassinados

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=false",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Defina a propriedade `outbound__webhook__httpsOnly` como `false` somente em ambientes de teste como você pode querer abrir um assinante http primeiro. Para cargas de trabalho de produção, recomendamos que elas sejam definidas como **true**
