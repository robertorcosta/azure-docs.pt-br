---
title: 'Início Rápido: Sintetizar fala em um arquivo de áudio – Serviço de Fala'
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
ms.openlocfilehash: 9b13d8fc3b77426a59dea5399223b79c4bb4b1a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75468904"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para sintetizar a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` na chave de assinatura e na região.
> * Criar um objeto de Configuração de Áudio que especifique o nome do arquivo .WAV.
> * Criar um objeto ````SpeechSynthesizer```` usando os objetos de configuração acima.
> * Usando o objeto ````SpeechSynthesizer````, converter o texto em fala sintetizada, salvando-o no arquivo de áudio especificado.
> * Inspecionar o ````SpeechSynthesizer```` que retornar para verificar se não há erros.
