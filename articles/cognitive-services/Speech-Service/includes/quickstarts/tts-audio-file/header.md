---
title: 'Início Rápido: Sintetizar fala em um arquivo de áudio – Serviço de Fala'
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
ms.openlocfilehash: 7d9a03f5a57473ce651560b261a4cf84b4ca4824
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818011"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada em um arquivo de áudio. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas cinco etapas para sintetizar a fala em um arquivo:
> [!div class="checklist"]
> * Criar um objeto ````SpeechConfig```` a partir de sua chave de assinatura e região.
> * Criar um objeto de Configuração de Áudio que especifique o nome do arquivo .WAV.
> * Criar um objeto ````SpeechSynthesizer```` usando os objetos de configuração acima.
> * Usando o objeto ````SpeechSynthesizer````, converter o texto em fala sintetizada, salvando-o no arquivo de áudio especificado.
> * Inspecionar o ````SpeechSynthesizer```` que retornar para verificar se não há erros.
