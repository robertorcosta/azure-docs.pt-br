---
title: 'Início Rápido: Traduzir uma fala em fala - Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 77b060a5caf9865a1296cd6644cf0c51e3253a0e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504840"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para traduzir de maneira interativa uma fala de um idioma para uma fala em outro idioma. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 6 etapas para traduzir fala em fala:
> [!div class="checklist"]
> * Crie um objeto ````SpeechTranslationConfig```` a partir de sua chave de assinatura e região.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar os idiomas de origem e de destino.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar o nome da voz de fala.
> * Crie um objeto ````TranslationRecognizer```` usando o objeto ````SpeechTranslationConfig```` acima.
> * Usando o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
