---
title: Configure a autenticação do assinante Webhook - Azure Event Grid IoT Edge | Microsoft Docs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841723"
---
# <a name="configure-webhook-subscriber-authentication"></a>Configurar a autenticação do assinante do webhook

Este guia dá exemplos das possíveis configurações de assinantes do webhook para um módulo Event Grid. Por padrão, apenas pontos finais HTTPS são aceitos para assinantes do webhook. O módulo Event Grid será rejeitado se o assinante apresentar um certificado auto-assinado.

## <a name="allow-only-https-subscriber"></a>Permitir apenas assinante HTTPS

```json
 {
  "Env": [
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=false"
  ]
}
 ```

## <a name="allow-https-subscriber-with-self-signed-certificate"></a>Permitir assinante HTTPS com certificado auto-assinado

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
>Defina `outbound__webhook__allowUnknownCA` a `true` propriedade apenas em ambientes de teste, pois você normalmente pode usar certificados auto-assinados. Para cargas de trabalho de produção, recomendamos que sejam definidas como **falsas**.

## <a name="allow-https-subscriber-but-skip-certificate-validation"></a>Permitir assinante HTTPS, mas pular validação de certificado

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
>Defina `outbound__webhook__skipServerCertValidation` a `true` propriedade apenas em ambientes de teste, pois você pode não estar apresentando um certificado que precisa ser autenticado. Para cargas de trabalho de produção, recomendamos que sejam definidas como **falsas**

## <a name="allow-both-http-and-https-with-self-signed-certificates"></a>Permitir http e HTTPS com certificados auto-assinados

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
>Defina `outbound__webhook__httpsOnly` a `false` propriedade apenas em ambientes de teste, pois você pode querer trazer um assinante HTTP primeiro. Para cargas de trabalho de produção, recomendamos que sejam definidas como **verdadeiras**
