---
title: 'Início Rápido: Reconhecer fala, intenções e entidades – Serviço de Fala'
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
ms.openlocfilehash: df4604560e05899461b11ec0788f72b27241f1b9
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2019
ms.locfileid: "74125399"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para reconhecer de forma interativa a fala de dados de áudio capturados em um microfone. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas quatro etapas para reconhecer a fala:
> [!div class="checklist"]
>
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Crie um objeto ````IntentRecognizer```` usando o objeto ````SpeechConfig```` acima.
> * Com o objeto ````IntentRecognizer````, inicie o processo de reconhecimento para um único enunciado.
> * Inspecione o ````IntentRecognitionResult```` retornado.
