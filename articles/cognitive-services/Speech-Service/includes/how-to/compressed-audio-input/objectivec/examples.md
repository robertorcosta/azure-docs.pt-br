---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: f0baac79bfbfc06b24d692caef87a20013c4654c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421843"
---
Para transmitir em um formato de áudio compactado para o serviço Speech, crie um `SPXPullAudioInputStream` ou `SPXPushAudioInputStream`.

O trecho a seguir mostra `SPXAudioConfiguration` como criar uma `SPXPushAudioInputStream`instância de a, especificando um MP3 como o formato de compactação do fluxo.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

O próximo trecho mostra como os dados de áudio compactados `SPXPushAudioInputStream`podem ser lidos a partir de um arquivo e bombeados para o .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
