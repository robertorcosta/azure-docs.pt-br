---
title: 'Início Rápido: Reconhecer uma fala de um microfone – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 11/20/2019
ms.author: erhopf
ms.openlocfilehash: ab51fe0323ea23c16cb52aa7a0570f8d51d40b2f
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75466672"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de uma entrada de microfone e obter a transcrição de texto do áudio capturado. É fácil integrar esse recurso a seus aplicativos ou dispositivos para tarefas de reconhecimento comuns, como transcrever conversas. Ele também pode ser usado para integrações mais complexas, como usar o Bot Framework com o SDK de Fala para criar assistentes de voz.

Depois de cumprir alguns pré-requisitos, é necessário seguir apenas quatro etapas para reconhecer a fala:

> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto `SpeechRecognizer` usando o objeto `SpeechConfig` acima.
> * Com o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `SpeechRecognitionResult` retornado.
