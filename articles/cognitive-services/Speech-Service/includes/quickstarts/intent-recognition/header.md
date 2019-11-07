---
title: 'Início Rápido: Reconhecer fala, intenções e entidades – Serviço de fala'
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 3b50ba8f2c3d21fb5bb0ab2c26cedb6bda0fab16
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505960"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de dados de áudio capturados em um microfone. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas quatro etapas para reconhecer a fala:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` a partir de sua chave de assinatura e região.
> * Criar um objeto ````IntentRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````IntentRecognizer````, iniciar o processo de reconhecimento para um único enunciado.
> * Inspecionar o ````IntentRecognitionResult```` que retornar.