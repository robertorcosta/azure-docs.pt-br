---
title: Azure Media Player Setup completo
description: Saiba como configurar o Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: d4c2dc58ca341db7ba17dbaf6a5ce7c009983379
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727236"
---
# <a name="azure-media-player-full-setup"></a>Configuração completa do Azure Media Player #

O Player de Mídia do Azure é fácil de configurar. Leva apenas alguns momentos para obter a reprodução básica do conteúdo de mídia direto da sua conta do Azure Media Services. [As amostras](https://github.com/Azure-Samples/azure-media-player-samples) também são fornecidas no diretório de amostras da versão.


## <a name="step-1-include-the-javascript-and-css-files-in-the-head-of-your-page"></a>Passo 1: Inclua os arquivos JavaScript e CSS na cabeça da sua página ##

Com o Azure Media Player, você pode acessar os scripts da versão hospedada do CDN. É frequentemente recomendado agora colocar JavaScript antes da `<body>` tag `<head>`final do corpo em vez do , mas o Azure Media Player inclui um 'HTML5 Shiv', que precisa estar na cabeça para versões ie mais antigas para respeitar a tag de vídeo como um elemento válido.

> [!NOTE]
> Se você já estiver usando um shiv HTML5 como [modernizr,](http://modernizr.com/) você pode incluir o JavaScript do Azure Media Player em qualquer lugar. No entanto, certifique-se de que sua versão do Modernizr inclua a shiv para vídeo.

### <a name="cdn-version"></a>Versão CDN ###
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>

> [!IMPORTANT]
> Você **NÃO** deve `latest` usar a versão na produção, pois esta está sujeita a alterações sob demanda. Substitua por `latest` uma versão do Azure Media Player. Por exemplo, substitua `latest` por `2.1.1`. As versões do Azure Media Player podem ser consultadas a partir [daqui](azure-media-player-changelog.md).

> [!NOTE]
> Desde `1.2.0` o lançamento, não é mais necessário incluir a localização para os técnicos de retorno (ele pegará automaticamente a localização a partir do caminho relativo do arquivo azuremediaplayer.min.js). Você pode modificar a localização dos techs de retorno `<head>` adicionando o seguinte script no após os scripts acima.

> [!NOTE]
> Devido à natureza dos plugins Flash e Silverlight, os arquivos swf e xap devem ser hospedados em um domínio sem qualquer informação ou dados confidenciais - isso é automaticamente cuidado para você com a versão hospedada no Azure CDN.

```javascript
    <script>
      amp.options.flashSS.swf = "//amp.azure.net/libs/amp/latest/techs/StrobeMediaPlayback.2.0.swf"
      amp.options.silverlightSS.xap = "//amp.azure.net/libs/amp/latest/techs/SmoothStreamingPlayer.xap"
    </script>
```

## <a name="step-2-add-an-html5-video-tag-to-your-page"></a>Passo 2: Adicione uma tag de vídeo HTML5 à sua página ##

Com o Azure Media Player, você pode usar uma tag de vídeo HTML5 para incorporar um vídeo. O Azure Media Player lerá a tag e fará com que funcione em todos os navegadores, não apenas aqueles que suportam vídeo HTML5. Além da marcação básica, o Azure Media Player precisa de algumas peças extras.

1. O `<data-setup>` atributo `<video>` no diz ao Azure Media Player para configurar automaticamente o vídeo quando a página estiver pronta e ler qualquer (em formato JSON) a partir do atributo.
1. O `id` atributo: Deve ser usado e único para cada vídeo na mesma página.
1. O `class` atributo contém duas classes:
    - `azuremediaplayer`aplica estilos que são necessários para a funcionalidade do Azure Media Player UI
    - `amp-default-skin`aplica a skin padrão aos controles HTML5
1. O `<source>` inclui dois atributos necessários
    - `src`atributo pode incluir um arquivo **.ism/manifest* do Azure Media Services é adicionado, o Azure Media Player adiciona automaticamente as URLs para DASH, SMOOTH e HLS ao player
    - `type`atributo é o tipo MIME necessário do fluxo. O tipo MIME associado a *".ism/manifest"* é *"application/vnd.ms-sstr+xml"*
1. O *optional* `<data-setup>` atributo `<source>` opcional no atributo informa ao Azure Media Player se houver políticas de entrega exclusivas para o fluxo do Azure Media Services, incluindo, mas não se limitando a, tipo de criptografia (AES ou PlayReady, Widevine ou FairPlay) e token.

Inclua/exclua atributos, configurações, fontes e faixas exatamente como você faria para vídeo HTML5.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"], "nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Por padrão, o botão de reprodução grande está localizado no canto superior esquerdo para que não cubra as partes interessantes do pôster. Se você preferir centralizar o botão de reprodução `amp-big-play-centered` `class` grande, você pode adicionar um adicional ao seu `<video>` elemento.

### <a name="alternative-setup-for-dynamically-loaded-html"></a>Configuração alternativa para HTML carregado dinamicamente ###

Se sua página ou aplicativo da Web carregar a tag de vídeo dinamicamente (ajax, apêndice, etc.), de modo que ela possa não existir quando a página for carregada, você deverá configurar manualmente o player em vez de confiar no atributo de configuração de dados. Para fazer isso, primeiro remova o atributo de configuração de dados da tag para que não haja confusão quando o jogador é inicializado. Em seguida, execute o JavaScript a seguir algum tempo após o JavaScript do Azure Media Player ter sido carregado, e depois que a tag de vídeo tiver sido carregada no DOM.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"],
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
        src: "http://samplescdn.origin.mediaservices.windows.net/e0e820ec-f6a2-4ea2-afe3-1eed4e06ab2c/AzureMediaServices_Overview.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

O primeiro argumento `amp` na função é o ID da sua tag de vídeo. Substitua-o pelo seu.

O segundo argumento é um objeto de opções. Ele permite definir opções adicionais como você pode com o atributo de configuração de dados.

O terceiro argumento é um retorno de chamada "pronto". Uma vez que o Azure Media Player tenha inicializado, ele chamará essa função. No retorno de chamada pronto, 'este' objeto refere-se à instância do jogador.

Em vez de usar um ID de elemento, você também pode passar uma referência ao elemento em si.

```javascript

    amp(document.getElementById('example_video_1'), {/*Options*/}, function() {
        //This is functionally the same as the previous example.
    });
    myPlayer.src([{ src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml"]);
```

## <a name="next-steps"></a>Próximas etapas ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)