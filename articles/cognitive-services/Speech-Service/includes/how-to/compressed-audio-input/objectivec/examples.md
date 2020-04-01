---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 788903d802ca47c763734e7cf6ddbbf3b0032203
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409608"
---
Para transmitir em um formato de áudio compactado para o serviço Speech, crie um `SPXPullAudioInputStream` ou `SPXPushAudioInputStream`.

O trecho a seguir mostra `SPXAudioConfiguration` como criar uma `SPXPushAudioInputStream`instância de a, especificando um MP3 como o formato de compactação do fluxo.

[!code-objectivec[Set up the input stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=67-76&highlight=2-11)]

O próximo trecho mostra como os dados de áudio compactados `SPXPushAudioInputStream`podem ser lidos a partir de um arquivo e bombeados para o .

[!code-objectivec[Push compressed audio data into the stream](~/samples-cognitive-services-speech-sdk/samples/objective-c/ios/compressed-streams/CompressedStreamsSample/CompressedStreamsSample/ViewController.m?range=106-150&highlight=19-44)]
