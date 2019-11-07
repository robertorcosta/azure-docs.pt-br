---
title: 'Início Rápido: Sintetizar fala - Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar uma fala usando o SDK de Fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: 263a8e98c4c029728272c020efe00e82be20f5e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505072"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 4 etapas para renderizar a fala sintetizada nos locutores padrão:
> [!div class="checklist"]
> * Crie um objeto ````SpeechConfig```` a partir da sua chave de assinatura e região.
> * Crie um objeto ````SpeechSynthesizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````SpeechSynthesizer```` para falar o texto.
> * Inspecione o ````SpeechSynthesisResult```` retornado quando a erros.
