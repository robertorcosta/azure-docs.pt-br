---
title: Métodos de API do Azure Media Player
description: A API do Azure Media Player permite que você interaja com o vídeo através do JavaScript, seja o navegador reproduzindo o vídeo através de vídeo HTML5, Flash, Silverlight ou quaisquer outras tecnologias de reprodução suportadas.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: d1ad0cbe30ce20e61a31f0534a47fe031f6f257b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727262"
---
# <a name="api"></a>API #

A API do Azure Media Player permite que você interaja com o vídeo através do JavaScript, seja o navegador reproduzindo o vídeo através de vídeo HTML5, Flash, Silverlight ou quaisquer outras tecnologias de reprodução suportadas.

## <a name="referencing-the-player"></a>Referenciando o jogador ##

Para usar as funções da API, você precisa acessar o objeto do jogador. Felizmente é fácil de conseguir. Você só precisa ter certeza de que sua tag de vídeo tem uma identificação. O exemplo de incorporar código `vid1`tem um ID de . Se você tiver vários vídeos em uma página, certifique-se de que cada tag de vídeo tenha uma identificação única.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Se o jogador ainda não foi inicializado através do atributo de configuração de dados ou outro método, isso também inicializará o jogador.

## <a name="wait-until-the-player-is-ready"></a>Espere até que o jogador esteja pronto ##

O tempo que o Azure Media Player leva para configurar o vídeo e a API vai variar dependendo da tecnologia de reprodução que está sendo usada. HTML5 será muitas vezes muito mais rápido de carregar do que Flash ou Silverlight. Por essa razão, a função 'pronta' do jogador deve ser usada para acionar qualquer código que exija a API do jogador.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

OU

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>Métodos de API ##

Agora que você tem acesso a um player pronto, você pode controlar o vídeo, obter valores ou responder a eventos de vídeo. Os nomes da função Azure Media Player API tentam seguir a [API de mídia HTML5](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). A principal diferença é que as funções getter/setter são usadas para propriedades de vídeo.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Inscrição para eventos ##
Os eventos devem ser registrados diretamente após a inicialização do jogador pela primeira vez para garantir que todos os eventos sejam devidamente relatados ao aplicativo, e devem ser feitos fora do evento pronto.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Próximas etapas ##

<!---Some context for the following links goes here--->
- [Azure Media Player Quickstart](azure-media-player-quickstart.md)