---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 05/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7e7a0424e4454639211c6494aab0700e75269361
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83721731"
---
Os limites a seguir aplicam-se aos tópicos do sistema Grade de Eventos do Azure e aos tópicos personalizados, *não* aos domínios de eventos.

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Microsoft Azure | 100 |
| Assinaturas de Evento por tópico | 500 |
| Publicar a taxa de um tópico personalizado (entrada) | 5\.000 eventos por segundo, por tópico |
| Tamanho do evento | 1 MB. No entanto, as operações são cobradas em incrementos de 64 KB. Assim, os eventos com mais de 64 KB incorrerão em encargos de operações como se fossem vários eventos. Por exemplo, um evento com 130 KB incorreria em operações como se fosse três eventos separados.  |
| Tópicos por domínio de eventos | 100.000 |
| Assinaturas de eventos por tópico em um domínio | 500 |
| Assinaturas de eventos no escopo do domínio | 50 |
| Publicar taxa para um domínio de eventos (entrada) | 5\.000 eventos por segundo |
| Domínios de Eventos por Assinatura do Azure | 100 |
| Conexões do ponto de extremidade privado por tópico ou domínio | 64 | 
| Regras de firewall de IP por tópico ou domínio | 16 | 