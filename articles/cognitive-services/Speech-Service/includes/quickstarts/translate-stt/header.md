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
ms.date: 12/09/2019
ms.author: yulili
ms.openlocfilehash: d4781808ce8e80f62e86ce1d0c6db9c38b2636d0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980592"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para traduzir de maneira interativa uma fala de um idioma para um texto em outro idioma. Após cumprir alguns pré-requisitos, traduzir uma fala em texto requer apenas cinco etapas:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Atualizar o objeto ````SpeechConfig```` para especificar os idiomas de origem e de destino.
> * Criar um objeto ````TranslationRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Com o objeto ````TranslationRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````TranslationRecognitionResult```` retornado.
