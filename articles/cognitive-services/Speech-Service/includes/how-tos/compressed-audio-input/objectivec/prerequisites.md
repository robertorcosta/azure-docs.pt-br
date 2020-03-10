---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943745"
---
O tratamento de áudio compactado é implementado usando o [GStreamer](https://gstreamer.freedesktop.org). Por motivos de licenciamento, os binários GStreamer não são compilados e vinculados ao SDK de fala. Em vez disso, uma biblioteca de wrapper que contém essas funções precisa ser compilada e enviada com os aplicativos usando o SDK.

Para criar essa biblioteca de wrapper, primeiro Baixe e instale o [SDK do GStreamer](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Em seguida, baixe o projeto **Xcode** para a [biblioteca de wrapper](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Abra o projeto no **Xcode** e compile-o para o destino do **dispositivo IOS genérico** --ele *não* funcionará para compilá-lo para um destino específico.

A etapa de compilação irá gerar um pacote de estrutura dinâmica com uma biblioteca dinâmica para todas as arquiteturas necessárias com o nome de `GStreamerWrapper.framework`.

Essa estrutura deve ser incluída em todos os aplicativos que usam fluxos de áudio compactados com o SDK do serviço de fala.

Aplique as seguintes configurações em seu projeto **Xcode** para fazer isso:

1. Copie o `GStreamerWrapper.framework` que você acabou de criar e a estrutura do SDK de fala dos serviços cognitivas, que pode ser baixado [aqui](https://aka.ms/csspeech/iosbinary)para o diretório que contém o projeto de exemplo.
1. Ajuste os caminhos para as estruturas nas configurações do *projeto*.
   1. Na guia **geral** sob o cabeçalho **binários inseridos** , adicione a biblioteca do SDK como uma estrutura: **adicionar binários inseridos** > **Adicionar outro...** > Navegue até o diretório escolhido e selecione ambas as estruturas.
   1. Vá para a guia **Configurações de Build** e ative **Todas** as configurações.
1. Adicione o diretório `$(SRCROOT)/..` a _Caminhos de Pesquisa do Framework_ no cabeçalho **Caminhos de Pesquisa**.
