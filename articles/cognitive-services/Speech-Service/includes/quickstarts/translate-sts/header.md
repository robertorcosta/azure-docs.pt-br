---
title: 'Início Rápido: Traduzir fala em fala – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: c5f0a0fe032d18cd4f01aebe9a5c736d6d511a74
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74980488"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para traduzir de maneira interativa uma fala de um idioma para uma fala em outro idioma. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 6 etapas para traduzir fala em fala:
> [!div class="checklist"]
> * Criar um objeto ````SpeechTranslationConfig```` na chave de assinatura e na região.
> * Atualizar o objeto ````SpeechTranslationConfig```` para especificar os idiomas de origem e de destino.
> * Atualize o objeto ````SpeechTranslationConfig```` para especificar o nome da voz de fala.
> * Criar um objeto ````TranslationRecognizer```` usando o objeto ````SpeechTranslationConfig```` acima.
> * Com o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
