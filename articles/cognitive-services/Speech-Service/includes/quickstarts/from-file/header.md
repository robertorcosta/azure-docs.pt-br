---
title: 'Início Rápido: Reconhecer fala em um arquivo de áudio – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
ms.openlocfilehash: e333b156eaf9c4b6e09e631513ea099018f0691b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468615"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer falas em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para reconhecer a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Criar um objeto ````AudioConfig```` que especifica o nome do arquivo .WAV.
> * Criar um objeto ````SpeechRecognizer```` usando os objetos ````SpeechConfig```` e ````AudioConfig```` acima.
> * Com o objeto ````SpeechRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````SpeechRecognitionResult```` retornado.
