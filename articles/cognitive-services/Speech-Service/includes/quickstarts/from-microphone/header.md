---
title: 'Início Rápido: Reconhecer a fala de um microfone - Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: 64f084f58ae4b12d92d6dee343a59dd56b1c351e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505560"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de dados de áudio capturados em um microfone. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 4 etapas para reconhecer a fala:
> [!div class="checklist"]
> * Crie um objeto ````SpeechConfig```` a partir da sua chave de assinatura e região.
> * Crie um objeto ````SpeechRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````SpeechRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````SpeechRecognitionResult```` retornado.
