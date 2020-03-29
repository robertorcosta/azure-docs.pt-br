---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 05/22/2019
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: ee80c04a6365c2cf337c4033a90df8d2993c299d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "76845988"
---
Os limites a seguir se aplicam aos tópicos do sistema Azure Event Grid e tópicos personalizados, *não* aos domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Microsoft Azure | 100 |
| Assinaturas de Evento por tópico | 500 |
| Publicar a taxa de um tópico personalizado (entrada) | 5.000 eventos por segundo, por tópico |
| Publicar solicitações | 250 por segundo |
| Tamanho do evento | 1 MB (carregado em vários eventos de 64 KB) |

Os seguintes limites se aplicam apenas aos domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de eventos | 100.000 |
| Assinaturas de eventos por tópico dentro de um domínio | 500 |
| Assinaturas de eventos de escopo de domínio | 50 |
| Taxa de publicação para um domínio de evento (ingress) | 5.000 eventos por segundo |
| Publicar solicitações | 250 por segundo |
| Domínios de eventos por assinatura do Azure | 100 |