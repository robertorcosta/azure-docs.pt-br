---
title: Instale recipientes de fala
titleSuffix: Azure Cognitive Services
description: Detalha as opções de configuração do gráfico do leme do guarda-chuva de fala.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 0257f3af44cc85d0a3656472db224ae5a7e19161
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874324"
---
### <a name="speech-umbrella-chart"></a>Fala (gráfico de guarda-chuvas)

Os valores no gráfico "guarda-chuva" de nível superior sobrepõem os valores de subgráfico correspondentes. Portanto, todos os valores personalizados no local devem ser adicionados aqui.

|Parâmetro|Descrição|Padrão|
| -- | -- | -- | -- |
| `speechToText.enabled` | Se o serviço **de fala para texto** está habilitado. | `true` |
| `speechToText.verification.enabled` | Se `helm test` a capacidade de serviço **de voz a texto** está ativada. | `true` |
| `speechToText.verification.image.registry` | O repositório de `helm test` imagem docker que usa para testar o serviço **de fala para texto.** Helm cria um pod separado dentro do cluster para testes e puxa a imagem *de uso de teste* deste registro. | `docker.io` |
| `speechToText.verification.image.repository` | O repositório de `helm test` imagem docker que usa para testar o serviço **de fala para texto.** O pod de teste helm usa este repositório para puxar a imagem *de uso de teste.* | `antsu/on-prem-client` |
| `speechToText.verification.image.tag` | A tag de `helm test` imagem docker usada para o serviço **de fala-texto.** O pod de teste helm usa esta tag para puxar a imagem *de uso de teste.* | `latest` |
| `speechToText.verification.image.pullByHash` | Se a imagem docker *de uso de teste* é puxada pelo hash. Se `true` `speechToText.verification.image.hash` , deve ser adicionado, com valor de hash de imagem válido. | `false` |
| `speechToText.verification.image.arguments` | Os argumentos usados para executar a imagem docker *de uso de teste.* O pod de teste helm passa `helm test`esses argumentos para o recipiente durante a execução . | `"./speech-to-text-client"`<br/> `"./audio/whatstheweatherlike.wav"` <br/> `"--expect=What's the weather like"`<br/>`"--host=$(SPEECH_TO_TEXT_HOST)"`<br/>`"--port=$(SPEECH_TO_TEXT_PORT)"` |
| `textToSpeech.enabled` | Se o **serviço texto-para-fala** está habilitado. | `true` |
| `textToSpeech.verification.enabled` | Se `helm test` a capacidade de serviço **de voz a texto** está ativada. | `true` |
| `textToSpeech.verification.image.registry` | O repositório de `helm test` imagem docker que usa para testar o serviço **de fala para texto.** Helm cria um pod separado dentro do cluster para testes e puxa a imagem *de uso de teste* deste registro. | `docker.io` |
| `textToSpeech.verification.image.repository` | O repositório de `helm test` imagem docker que usa para testar o serviço **de fala para texto.** O pod de teste helm usa este repositório para puxar a imagem *de uso de teste.* | `antsu/on-prem-client` |
| `textToSpeech.verification.image.tag` | A tag de `helm test` imagem docker usada para o serviço **de fala-texto.** O pod de teste helm usa esta tag para puxar a imagem *de uso de teste.* | `latest` |
| `textToSpeech.verification.image.pullByHash` | Se a imagem docker *de uso de teste* é puxada pelo hash. Se `true` `textToSpeech.verification.image.hash` , deve ser adicionado, com valor de hash de imagem válido. | `false` |
| `textToSpeech.verification.image.arguments` | Os argumentos a serem executados com a imagem docker *de uso de teste.* O pod de teste do leme `helm test`passa esses argumentos para o contêiner ao executar . | `"./text-to-speech-client"`<br/> `"--input='What's the weather like'"` <br/> `"--host=$(TEXT_TO_SPEECH_HOST)"`<br/>`"--port=$(TEXT_TO_SPEECH_PORT)"` |