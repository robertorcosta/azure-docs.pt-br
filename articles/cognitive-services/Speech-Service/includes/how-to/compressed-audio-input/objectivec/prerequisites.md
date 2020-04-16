---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422086"
---
O manuseio de áudio comprimido é implementado usando [o GStreamer](https://gstreamer.freedesktop.org). Por razões de licenciamento, os binários GStreamer não são compilados e vinculados ao Speech SDK. Em vez disso, uma biblioteca de invólucros contendo essas funções precisa ser construída e enviada com os aplicativos usando o SDK.

Para construir esta biblioteca de invólucros, primeiro baixe e instale o [GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Em seguida, baixe o projeto **Xcode** para a [biblioteca de invólucros](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Abra o projeto no **Xcode** e construa-o para o destino **do dispositivo genérico iOS** -- ele *não* funcionará para construí-lo para um alvo específico.

A etapa de construção gerará um pacote de estruturas dinâmicas `GStreamerWrapper.framework`com uma biblioteca dinâmica para todas as arquiteturas necessárias com o nome de .

Esta estrutura deve ser incluída em todos os aplicativos que usam fluxos de áudio compactados com o Serviço de Fala SDK.

Aplique as seguintes configurações em seu projeto **Xcode** para conseguir isso:

1. Copie `GStreamerWrapper.framework` o que você acabou de construir e a estrutura do Cognitive Services Speech SDK, que você pode baixar [daqui,](https://aka.ms/csspeech/iosbinary)para o diretório que contém seu projeto de amostra.
1. Ajuste os caminhos para as estruturas nas *Configurações*do projeto .
   1. Na guia **Geral** sob o cabeçalho **Binários Incorporados,** adicione a biblioteca SDK como uma estrutura: **Adicione binários incorporados** > **Adicione outros...** > Navegue ao diretório escolhido e selecione ambas as estruturas.
   1. Vá para a guia **Configurações de Build** e ative **Todas** as configurações.
1. Adicione o diretório `$(SRCROOT)/..` a _Caminhos de Pesquisa do Framework_ no cabeçalho **Caminhos de Pesquisa**.
