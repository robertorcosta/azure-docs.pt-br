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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845988"
---
Os limites a seguir se aplicam aos tópicos do sistema de grade de eventos do Azure e aos tópicos personalizados, *não* aos domínios de evento.

| Grupos | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Microsoft Azure | 100 |
| Assinaturas de Evento por tópico | 500 |
| Publicar a taxa de um tópico personalizado (entrada) | 5\.000 eventos por segundo, por tópico |
| Solicitações de publicação | 250 por segundo |
| Tamanho do evento | 1 MB (cobrado como vários eventos de 64 KB) |

Os limites a seguir se aplicam somente a domínios de evento.

| Grupos | Limite |
| --- | --- |
| Tópicos por domínio de evento | 100 mil |
| Assinaturas de evento por tópico em um domínio | 500 |
| Assinaturas de evento de escopo de domínio | 50 |
| Taxa de publicação para um domínio de evento (entrada) | 5\.000 eventos por segundo |
| Solicitações de publicação | 250 por segundo |
| Domínios de evento por assinatura do Azure | 100 |