---
title: Escrevendo plugins para Azure Media Player
description: Aprenda a escrever um plugin com o Azure Media Player com JavaScript
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: b7aac80b19a7c30d994f3c14e19047583d5334ac
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727106"
---
# <a name="writing-plugins-for-azure-media-player"></a>Escrevendo plugins para Azure Media Player #

Um plugin é JavaScript escrito para estender ou melhorar o player. Você pode escrever plugins que alteram a aparência do Azure Media Player, sua funcionalidade ou até mesmo tê-lo interface com outros serviços. Você pode fazer isso em dois passos fáceis:

## <a name="step-1"></a>Etapa 1 ##

Escreva seu JavaScript em uma função como essa:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Você pode escrever seu código diretamente `<script>` em sua página HTML dentro de tags ou em um arquivo JavaScript externo. Se você fizer o último, certifique-se de `<head>` incluir o arquivo JavaScript na página de HTML *após* o script AMP.

Exemplo:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Etapa 2 ##
Inicialize o plugin com JavaScript de duas maneiras:

Método 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Método 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

As opções de plugin não são necessárias, incluindo elas apenas permite que os desenvolvedores que usam seu plugin configurem seu comportamento sem ter que alterar o código-fonte.

Para inspiração e mais exemplos sobre a criação de um plugin dê uma olhada em nossa [galeria](azure-media-player-plugin-gallery.md)

>[!NOTE]
> O código de plugin altera dinamicamente itens no DOM durante a vida útil da experiência do jogador, ele nunca faz alterações permanentes no código-fonte do jogador. É aí que a compreensão das ferramentas de desenvolvedor do seu navegador é útil. Por exemplo, se você quiser alterar a aparência de um elemento no jogador, você pode encontrar seu elemento HTML pelo seu nome de classe e, em seguida, adicionar ou alterar atributos a partir daí. Aqui está um grande recurso sobre [a mudança de atributos HTML.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Plugins integrados ###

 Atualmente, existem dois plugins assados em AMP: a [ponta de tempo](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) e as [teclas de atalho](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Estes plugins foram originalmente desenvolvidos para serem plugins modulares para o jogador, mas agora estão incluídos no código fonte do jogador.

### <a name="plugin-gallery"></a>Galeria Plugin ###

A [galeria plugin](http//:aka.ms/ampplugins) tem vários plugins que a comunidade já contribuiu para recursos como marcadores de linha do tempo, zoom, analytics e muito mais. A página fornece acessos aos plugins e instruções sobre como configurá-lo, bem como uma demonstração que mostra o plugin em ação. Se você criar um plugin legal que você acha que deve ser incluído em nossa galeria, sinta-se livre para enviá-lo para que possamos conferir.

## <a name="next-steps"></a>Próximas etapas ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)