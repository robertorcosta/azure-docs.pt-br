---
title: incluir arquivo
description: incluir arquivo
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: include
ms.date: 10/18/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: e084256d9c2043d4382ca180ef3178175b301367
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "91745414"
---
Os limites a seguir aplicam-se aos **tópicos** da Grade de Eventos do Azure (tópicos sistema, personalizado e parceiro). 

| Recurso | Limite |
| --- | --- |
| Tópicos personalizados por assinatura do Microsoft Azure | 100 |
| Assinaturas de Evento por tópico | 500 |
| Publicar a taxa de um tópico personalizado ou parceiro (entrada) | 5\.000 eventos/s ou 1 MB/s (o que for atingido primeiro)<br/>Não se aplica aos tópicos de sistema. |
| Tamanho do evento | 1 MB  |
| Conexões do ponto de extremidade privado por tópico  | 64 | 
| Regras de firewall de IP por tópico | 16 | 

Os limites a seguir aplicam-se aos **domínios** da Grade de Eventos do Azure. 

| Recurso | Limite |
| --- | --- |
| Tópicos por domínio de eventos | 100.000 |
| Assinaturas de eventos por tópico em um domínio | 500 |
| Assinaturas de eventos no escopo do domínio | 50 |
| Publicar taxa para um domínio de eventos (entrada) | 5\.000 eventos/s ou 1 MB/s (o que for atingido primeiro) |
| Domínios de Eventos por Assinatura do Azure | 100 |
| Conexões do ponto de extremidade privado por domínio | 64 | 
| Regras de firewall de IP por domínio | 16 | 


