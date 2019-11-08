---
title: 'Início Rápido: Reconhecer a fala armazenada no armazenamento de blobs – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 195c4c94f7ab00a8740c9dd14aad0d0fd0daa9b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506096"
---
Neste início rápido, você usará a [API REST de Transcrição em Lote](../../../batch-transcription.md) para reconhecer a fala armazenada em um [blobs SAS](https://aka.ms/ignite2019/speech/placeholder). Após atender a alguns pré-requisitos, para reconhecer a fala usando uma API REST, só serão necessárias algumas etapas:
> [!div class="checklist"]
> * Envie a solicitação JSON para o Serviço de Fala para começar a transcrever falas.
> * Verifique o status da transcrição.
> * Baixe os resultados da transcrição quando terminar.