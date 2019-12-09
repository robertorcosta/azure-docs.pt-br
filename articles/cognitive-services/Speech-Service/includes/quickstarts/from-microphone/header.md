---
title: 'Início Rápido: Reconhecer uma fala de um microfone – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: a72597163a8c11cd8b515d052dc69992784e61e0
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818817"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de uma entrada de microfone e obter a transcrição de texto do áudio capturado. É fácil integrar esse recurso a seus aplicativos ou dispositivos para tarefas de reconhecimento comuns, como transcrever conversas. Ele também pode ser usado para integrações mais complexas, como usar o Bot Framework com o SDK de Fala para criar assistentes de voz.

Depois de cumprir alguns pré-requisitos, é necessário seguir apenas quatro etapas para reconhecer a fala:

> [!div class="checklist"]
> * Crie um objeto `SpeechConfig` a partir da sua chave de assinatura e região.
> * Crie um objeto `SpeechRecognizer` usando o objeto `SpeechConfig` acima.
> * Com o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `SpeechRecognitionResult` retornado.
