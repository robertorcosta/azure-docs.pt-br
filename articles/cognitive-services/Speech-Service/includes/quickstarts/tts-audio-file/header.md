---
title: 'Início Rápido: Sintetizar fala em um arquivo de áudio – Serviço de fala'
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
ms.openlocfilehash: f69c00f9cf787a192c62f12a707927c5c51a1d31
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504920"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para sintetizar a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` a partir de sua chave de assinatura e região.
> * Criar um objeto de Configuração de Áudio que especifique o nome do arquivo .WAV.
> * Criar um objeto ````SpeechSynthesizer```` usando os objetos de configuração acima.
> * Usando o objeto ````SpeechSynthesizer````, converter o texto em fala sintetizada, salvando-o no arquivo de áudio especificado.
> * Inspecionar o ````SpeechSynthesizer```` que retornar para verificar se não há erros.
