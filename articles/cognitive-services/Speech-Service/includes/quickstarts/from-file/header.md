---
title: 'Início Rápido: Reconhecer fala em um arquivo de áudio – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: trbye
ms.openlocfilehash: a7c91775411f100a92dfcb0a7199dd4b25f6eca4
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81400453"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer falas em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas algumas etapas para reconhecer a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto `AudioConfig` que especifica o nome do arquivo .WAV.
> * Criar um objeto `SpeechRecognizer` usando os objetos `SpeechConfig` e `AudioConfig` acima.
> * Com o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `SpeechRecognitionResult` retornado.
