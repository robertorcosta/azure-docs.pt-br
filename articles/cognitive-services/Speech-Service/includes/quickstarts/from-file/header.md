---
title: 'Início Rápido: Reconhecer fala em um arquivo de áudio – Serviço de Fala'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/14/2020
ms.author: dapine
ms.openlocfilehash: 2b6270535c0cf69549a7412bd38d9207454e5500
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76037671"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer falas em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas algumas etapas para reconhecer a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto `SpeechConfig` na chave de assinatura e na região.
> * Criar um objeto `AudioConfig` que especifica o nome do arquivo .WAV.
> * Criar um objeto `SpeechRecognizer` usando os objetos `SpeechConfig` e `AudioConfig` acima.
> * Com o objeto `SpeechRecognizer`, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o `SpeechRecognitionResult` retornado.
