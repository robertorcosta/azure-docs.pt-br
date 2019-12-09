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
ms.openlocfilehash: 2f7ec24667514fb131af29321a53f97210e58fc9
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74819421"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer falas em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para reconhecer a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Criar um objeto ````AudioConfig```` que especifica o nome do arquivo .WAV.
> * Criar um objeto ````SpeechRecognizer```` usando os objetos ````SpeechConfig```` e ````AudioConfig```` acima.
> * Com o objeto ````SpeechRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````SpeechRecognitionResult```` retornado.
