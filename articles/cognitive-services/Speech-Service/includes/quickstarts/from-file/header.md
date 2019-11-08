---
title: 'Início Rápido: Reconhecer fala em um arquivo de áudio – Serviço de Fala'
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
ms.openlocfilehash: bbd3880ea679dc5fc86c0fc1ece101ca3fca74d9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506280"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer falas em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para reconhecer a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Criar um objeto ````AudioConfig```` que especifica o nome do arquivo .WAV.
> * Criar um objeto ````SpeechRecognizer```` usando os objetos ````SpeechConfig```` e ````AudioConfig```` acima.
> * Com o objeto ````SpeechRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````SpeechRecognitionResult```` retornado.
