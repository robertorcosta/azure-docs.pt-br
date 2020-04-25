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
ms.openlocfilehash: 5992726893b722b0aa46c976a0167793f5ee6bb4
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82132177"
---
Os limites a seguir se aplicam aos tópicos do sistema de grade de eventos do Azure e aos tópicos personalizados, *não* aos domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Microsoft Azure | 100 |
| Assinaturas de Evento por tópico | 500 |
| Publicar a taxa de um tópico personalizado (entrada) | 5.000 eventos por segundo, por tópico |
| Solicitações de publicação | 250 por segundo |
| Tamanho do evento | 1 MB. No entanto, as operações são cobradas em incrementos de 64 KB. Assim, os eventos com mais de 64 KB incorrerão em encargos de operações como se fossem vários eventos. Por exemplo, um evento que é 130 KB incorreria em operações como se fosse três eventos separados.  |

Os limites a seguir se aplicam somente a domínios de evento.

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de evento | 100.000 |
| Assinaturas de evento por tópico em um domínio | 500 |
| Assinaturas de evento de escopo de domínio | 50 |
| Taxa de publicação para um domínio de evento (entrada) | 5.000 eventos por segundo |
| Solicitações de publicação | 250 por segundo |
| Domínios de evento por assinatura do Azure | 100 |