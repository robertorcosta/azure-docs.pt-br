---
title: Início rápido do Player de Mídia do Azure
description: Conheça as etapas básicas para configurar o Player de Mídia do Azure.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: ac81832765f674e58ad6b3213238e9c68e04d2dc
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "81727473"
---
# <a name="azure-media-player-quickstart"></a>Início rápido do Player de Mídia do Azure
O Player de Mídia do Azure é fácil de configurar. São necessários apenas alguns minutos para obter a reprodução básica de conteúdo de mídia de sua conta dos Serviços de Mídia do Azure. Esta seção mostra as etapas básicas sem entrar em detalhes. As seções a seguir oferecem informações específicas sobre como instalar e configurar o Player de Mídia do Azure.  Basta adicionar as seguintes inclusões ao `<head>` de seu documento:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Você **NÃO** deve usar a versão `latest` na produção, pois ela está sujeita a alterações sob demanda. Substitua `latest` por uma versão do Player de Mídia do Azure, por exemplo, substitua `latest` por `1.0.0`. As versões do Player de Mídia do Azure podem ser consultadas [aqui](azure-media-player-changelog.md).

## <a name="use-the-video-element"></a>Usar o elemento de vídeo

Em seguida, basta usar o elemento `<video>` como você faria normalmente, mas com um atributo `data-setup` adicional contendo as opções que deseja usar. Essas opções podem incluir qualquer opção dos Serviços de Mídia do Azure em um objeto JSON válido.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Se você não quiser usar a instalação automática, poderá omitir o atributo `data-setup` e inicializar um elemento de vídeo manualmente.

```html
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            }
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Próximas etapas ##

- [Início rápido do Player de Mídia do Azure](azure-media-player-quickstart.md)