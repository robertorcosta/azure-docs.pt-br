---
title: 'Início Rápido: Sintetizar a fala – Serviço de Fala'
titleSuffix: Azure Cognitive Services
description: Saiba como sintetizar uma fala usando o SDK de Fala
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/20/2019
ms.author: yulili
ms.openlocfilehash: d19f779f67cc0dea8cc7f06aa275885d75c3092e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818350"
---
Neste guia de início rápido, você usará o [SDK de Fala](~/articles/cognitive-services/speech-service/speech-sdk.md) para converter texto em fala sintetizada. Depois de cumprir alguns pré-requisitos, é necessário seguir apenas 4 etapas para renderizar a fala sintetizada nos locutores padrão:
> [!div class="checklist"]
> * Crie um objeto ````SpeechConfig```` a partir da sua chave de assinatura e região.
> * Crie um objeto ````SpeechSynthesizer```` usando o objeto ````SpeechConfig```` acima.
> * Usando o objeto ````SpeechSynthesizer```` para falar o texto.
> * Inspecione o ````SpeechSynthesisResult```` retornado quando a erros.
