---
title: Configuração - Azure Event Grid IoT Edge | Microsoft Docs
description: Configuração em Event Grid em IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 841b5092775353bbe3340dbbd55610026f998a15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76846477"
---
# <a name="event-grid-configuration"></a>Configuração da grade de eventos

Event Grid fornece muitas configurações que podem ser modificadas por ambiente. A seção a seguir é uma referência a todas as opções disponíveis e seus padrões.

## <a name="tls-configuration"></a>Configuração TLS

Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação](security-authentication.md). Exemplos de seu uso podem ser encontrados [neste artigo](configure-api-protocol.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound__serverAuth__tlsPolicy`| Política TLS do módulo Grade de Eventos. O valor padrão é apenas HTTPS.
|`inbound__serverAuth__serverCert__source`| Fonte do certificado de servidor usado pelo Event Grid Module para sua configuração TLS. O valor padrão é IoT Edge.

## <a name="incoming-client-authentication"></a>Autenticação de cliente de entrada

Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação](security-authentication.md). Exemplos podem ser encontrados [neste artigo](configure-client-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`inbound__clientAuth__clientCert__enabled`| Para ativar/desativar a autenticação do cliente baseada em certificados. O valor padrão é true.
|`inbound__clientAuth__clientCert__source`| Fonte para validação de certificados de cliente. O valor padrão é IoT Edge.
|`inbound__clientAuth__clientCert__allowUnknownCA`| Política para permitir um certificado de cliente auto-assinado. O valor padrão é true.
|`inbound__clientAuth__sasKeys__enabled`| Para ativar/desativar a autenticação do cliente baseada em chave SAS. O valor padrão está desligado.
|`inbound__clientAuth__sasKeys__key1`| Um dos valores para validar as solicitações recebidas.
|`inbound__clientAuth__sasKeys__key2`| Segundo valor opcional para validar solicitações recebidas.

## <a name="outgoing-client-authentication"></a>Autenticação de cliente de saída
Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação](security-authentication.md). Exemplos podem ser encontrados [neste artigo](configure-identity-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound__clientAuth__clientCert__enabled`| Para ativar/desativar a anexação de um certificado de identidade para solicitações de saída. O valor padrão é true.
|`outbound__clientAuth__clientCert__source`| Fonte para recuperar o certificado de saída do módulo Event Grid. O valor padrão é IoT Edge.

## <a name="webhook-event-handlers"></a>Manipuladores de eventos Webhook

Para saber mais sobre a autenticação do cliente em geral, consulte [Segurança e Autenticação](security-authentication.md). Exemplos podem ser encontrados [neste artigo](configure-webhook-subscriber-auth.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
|`outbound__webhook__httpsOnly`| Política para controlar se apenas assinantes HTTPS serão permitidos. O valor padrão é verdadeiro (apenas HTTPS).
|`outbound__webhook__skipServerCertValidation`| Sinalizar para controlar se valida o certificado do assinante. O valor padrão é true.
|`outbound__webhook__allowUnknownCA`| Política para controlar se um certificado auto-assinado pode ser apresentado por um assinante. O valor padrão é true. 

## <a name="delivery-and-retry"></a>Entregar e tentar novamente

Para saber mais sobre esse recurso em geral, consulte [Entrega e Retry](delivery-retry.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `broker__defaultMaxDeliveryAttempts` | Número máximo de tentativas para entregar um evento. O valor padrão é 30.
| `broker__defaultEventTimeToLiveInSeconds` | Tempo de vida (TTL) em segundos após o qual um evento será descartado se não for entregue. O valor padrão é **de 7200** segundos

## <a name="output-batching"></a>Envio em lote de saída

Para saber mais sobre esse recurso em geral, consulte [O lote de Entrega e Saída](delivery-output-batching.md).

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `api__deliveryPolicyLimits__maxBatchSizeInBytes` | Valor máximo permitido `ApproxBatchSizeInBytes` para o botão. O valor padrão é `1_058_576`.
| `api__deliveryPolicyLimits__maxEventsPerBatch` | Valor máximo permitido `MaxEventsPerBatch` para o botão. O valor padrão é `50`.
| `broker__defaultMaxBatchSizeInBytes` | Tamanho máximo da `MaxEventsPerBatch` solicitação de entrega quando apenas é especificado. O valor padrão é `1_058_576`.
| `broker__defaultMaxEventsPerBatch` | Número máximo de eventos a serem `MaxBatchSizeInBytes` adicionados a um lote quando apenas é especificado. O valor padrão é `10`.

## <a name="metrics"></a>Métricas

Para saber mais sobre o uso de métricas com event grid no IoT Edge, consulte [tópicos e assinaturas do monitor](monitor-topics-subscriptions.md)

| Nome da propriedade | Descrição |
| ---------------- | ------------ |
| `metrics__reporterType` | Tipo de repórter para apontar métricas. Padrão `none` é e desativa métricas. Configuração `prometheus` para habilitar métricas no formato de exposição Prometheus.