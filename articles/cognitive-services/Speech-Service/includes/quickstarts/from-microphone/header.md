---
title: 'Início Rápido: Reconhecer fala de um microfone – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/07/2019
ms.author: erhopf
ms.openlocfilehash: f3f28cdd8c3c77a5cde2ead86c49b41d54bab5d7
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960776"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de dados de áudio capturados em um microfone. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas quatro etapas para reconhecer a fala:

> [!div class="checklist"]
> * Crie um objeto `SpeechConfig` a partir da sua chave de assinatura e região.
> * Crie um objeto `SpeechRecognizer` usando o objeto `SpeechConfig` acima.
> * Com o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `SpeechRecognitionResult` retornado.
