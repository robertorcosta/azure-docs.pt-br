---
title: 'Início Rápido: Traduzir fala em texto – Serviço de Fala'
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
ms.openlocfilehash: c45e75038d1731c933ccf8bf26f9de573e409292
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504736"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para traduzir de maneira interativa uma fala de um idioma para um texto em outro idioma. Após cumprir alguns pré-requisitos, traduzir uma fala em texto requer apenas cinco etapas:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Atualizar o objeto ````SpeechConfig```` para especificar os idiomas de origem e de destino.
> * Criar um objeto ````TranslationRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Com o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
