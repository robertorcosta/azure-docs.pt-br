---
title: 'Início Rápido: Reconhecer uma fala, intenções e entidades – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 1/02/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a833d39ab91cd803f066d707306a6ff648d37e8f
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660440"
---
Neste início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) e o serviço LUIS (Reconhecimento vocal) para reconhecer intenções em dados de áudio capturados de um microfone. Especificamente, você usará o SDK de Fala para capturar fala e um domínio predefinido do LUIS para identificar intenções de automação doméstica, assim como acender e apagar uma lâmpada. 

Depois de cumprir alguns pré-requisitos, é necessário seguir apenas algumas etapas para identificar intenções por meio de um microfone:

> [!div class="checklist"]
>
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Crie um objeto ````IntentRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Com o objeto ````IntentRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````IntentRecognitionResult```` retornado.
