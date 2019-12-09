---
title: 'Início Rápido: Reconhecer a fala armazenada no Armazenamento de Blobs – Serviço de Fala'
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
ms.openlocfilehash: ef61139056ad194d89e2a67cb4ac1b1414a3380b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74828818"
---
Neste início rápido, você usará a [API REST de Transcrição em Lote](../../../batch-transcription.md) para reconhecer a fala armazenada em um [blobs SAS](https://aka.ms/ignite2019/speech/placeholder). Após atender a alguns pré-requisitos, para reconhecer a fala usando uma API REST, só serão necessárias algumas etapas:
> [!div class="checklist"]
> * Envie a solicitação JSON para o serviço de Fala para começar a transcrever falas.
> * Verifique o status da transcrição.
> * Baixe os resultados da transcrição quando terminar.