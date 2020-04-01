---
title: Incorpore widgets de indexador de vídeo em seus aplicativos
titleSuffix: Azure Media Services
description: Aprenda a incorporar widgets do Video Indexer em seus aplicativos.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: 6ed5c509cf310b743e4ef52f411dfa34e5db09c1
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411618"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Incorpore widgets de indexador de vídeo em seus aplicativos

Este artigo mostra como você pode incorporar widgets do Video Indexer em seus aplicativos. O Video Indexer suporta a incorporação de três tipos de widgets em seus aplicativos: *Cognitive Insights*, *Player*e *Editor*.

A partir da versão 2, a URL base do widget inclui a região da conta especificada. Por exemplo, uma conta da região Oeste dos EUA gera: `https://wus2.videoindexer.ai/embed/insights/...`.

## <a name="widget-types"></a>Tipos de widget

### <a name="cognitive-insights-widget"></a>Widget de Insights Cognitivos

Um widget Insights cognitivos inclui todos os insights visuais que foram extraídos do processo de indexação do seu vídeo. O widget Cognitive Insights suporta os seguintes parâmetros opcionais de URL:

|Nome|Definição|Descrição|
|---|---|---|
|`widgets` | Cadeias de caracteres separadas por vírgula | Permite controlar os insights que deseja renderizar.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` torna apenas pessoas e palavras-chave insights de IA.<br/>Opções disponíveis: pessoas, personagens animados, palavras-chave, rótulos, sentimentos, emoções, tópicos, quadros-chave, transcrição, ocr, alto-falantes, cenas e entidades nomeadas.|
|`controls`|Cadeias de caracteres separadas por vírgula|Permite controlar os controles que deseja renderizar.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` renderiza apenas a opção de pesquisa e o botão de download.<br/>Opções disponíveis: pesquisa, download, predefinições, idioma.|
|`language`|Um código de idioma curto (nome do idioma)|Controla a linguagem de insights.<br/>Exemplo: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>ou `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | Um código de linguagem curto | Controla a linguagem da UI. O valor padrão é `en`. <br/>Exemplo: `locale=de`.|
|`tab` | A guia padrão selecionada | Controla a guia **Insights** renderizada por padrão. <br/>Exemplo: `tab=timeline` renderiza os insights com a guia **Linha do Tempo** selecionada.|

### <a name="player-widget"></a>Widget do player

Você pode usar o widget Player para transmitir vídeo usando taxa de bitadaptável. O widget Player suporta os seguintes parâmetros opcionais de URL.

|Nome|Definição|Descrição|
|---|---|---|
|`t` | Segundos desde o início | Faz com que o jogador comece a jogar a partir do ponto de tempo especificado.<br/> Exemplo: `t=60`. |
|`captions` | Um código de linguagem | Busca a legenda no idioma especificado durante o carregamento do widget para estar disponível no menu **Legendas.**<br/> Exemplo: `captions=en-US`. |
|`showCaptions` | Um valor booleano | Faz o player ser carregado com as legendas já habilitadas.<br/> Exemplo: `showCaptions=true`. |
|`type`| | Ativa uma pele de reprodutor de áudio (a parte do vídeo é removida).<br/> Exemplo: `type=audio`. |
|`autoplay` | Um valor booleano | Indica se o jogador deve começar a reproduzir o vídeo quando carregado. O valor padrão é `true`.<br/> Exemplo: `autoplay=false`. |
|`language`/`locale` | Um código de linguagem | Controla a linguagem do jogador. O valor padrão é `en-US`.<br/>Exemplo: `language=de-DE`.|

### <a name="editor-widget"></a>Widget do editor

Você pode usar o widget do Editor para criar novos projetos e gerenciar os insights de um vídeo. O widget do Editor suporta os seguintes parâmetros opcionais de URL.

|Nome|Definição|Descrição|
|---|---|---|
|`accessToken`<sup>*</sup> | String | Fornece acesso a vídeos que estão apenas na conta que é usada para incorporar o widget.<br> O widget editor `accessToken` requer o parâmetro. |
|`language` | Um código de linguagem | Controla a linguagem do jogador. O valor padrão é `en-US`.<br/>Exemplo: `language=de-DE`. |
|`locale` | Um código de linguagem curto | Controla a linguagem de insights. O valor padrão é `en`.<br/>Exemplo: `language=de`. |

<sup>*</sup>O proprietário `accessToken` deve fornecer cautela.

## <a name="embedding-public-content"></a>Inserindo conteúdo público

1. Faça login no site [do Video Indexer.](https://www.videoindexer.ai/)
2. Selecione o vídeo com o que deseja trabalhar.
3. Selecione o botão**</>** incorporar () que aparece sob o vídeo.

    Depois de selecionar o botão **Incorporar,** você pode selecionar o widget que deseja incorporar em seu aplicativo.
4. Selecione o tipo de widget que você deseja **(Cognitive Insights,** **Player**ou **Editor).**
5. Copie o código incorporado (aparece em **Copiar o código incorporado** na caixa de diálogo Compartilhar & **incorporar).**
6. Adicione o código ao seu aplicativo.

> [!NOTE]
> Se você tiver problemas para compartilhar suas `location` URLs de vídeo, adicione o parâmetro ao link. O parâmetro deve ser definido para as [regiões azure em que o Indexador de Vídeo existe](regions.md). Por exemplo: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

## <a name="embedding-private-content"></a>Inserindo conteúdo privado

Para incorporar um vídeo privado, você deve passar `src` um token de acesso no atributo do iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Para obter o conteúdo do widget Cognitive Insights, use um dos seguintes métodos:

- A API [get insights widget.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget)<br/>
- O [Token de acesso ao vídeo Get .](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?) Adicione-o como um parâmetro de consulta à URL. Especifique `src` esta URL como o valor para o iframe, como mostrado anteriormente.

Para fornecer recursos de edição de insights em seu widget incorporado, você deve passar por um token de acesso que inclui permissões de edição. Use [o Widget Get Insights](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) ou obtenha o [Token de Acesso de Vídeo](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) com `&allowEdit=true`.

## <a name="widgets-interaction"></a>Interação de widgets

O widget Cognitive Insights pode interagir com um vídeo em seu aplicativo. Esta seção mostra como conseguir essa interação.

![Indexador de vídeo widget cognitive insights](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="cross-origin-communications"></a>Comunicações entre origens

Para obter widgets do Indexador de vídeo para se comunicar com outros componentes, o serviço do Indexador de vídeo:

- Usa o método `postMessage`HTML5 de comunicação de origem cruzada .
- Valida a mensagem entre a origem do VideoIndexer.ai.

Se você implementar seu próprio código de jogador e se integrar com widgets Cognitive Insights, é sua responsabilidade validar a origem da mensagem que vem de VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Incorpore widgets em seu aplicativo ou blog (recomendado)

Esta seção mostra como obter interação entre dois widgets do Indexador de Vídeo para que, quando um usuário seleciona o controle de insights em seu aplicativo, o jogador pule para o momento relevante.

1. Copie o código de inserção do widget Player.
2. Copie o código de inserção dos Insights cognitivos.
3. Adicione [Arquivo mediador](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) para lidar com a comunicação entre os dois widgets:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Agora, quando um usuário seleciona o controle de insights em seu aplicativo, o jogador salta para o momento relevante.

Para obter mais informações, consulte o [Indexador de Vídeo - Incorporar ambas as demonstrações de Widgets](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Inserir o widget de Insights cognitivos e usar o Player de Mídia do Azure para reproduzir o conteúdo

Esta seção mostra como obter interação entre um widget Cognitive Insights e uma instância do Azure Media Player usando o [plug-in AMP](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Adicionar um plug-in do Indexador de vídeo para o player AMP:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instanciar o Azure Media Player com o plug-in do Indexador de Vídeo.

        // Init the source.
        function initSource() {
            var tracks = [{
            kind: 'captions',
            // To load vtt from VI, replace it with your vtt URL.
            src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
            srclang: 'en',
            label: 'English'
            }];

            myPlayer.src([
            {
                "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
                "type": "application/vnd.ms-sstr+xml"
            }
            ], tracks);
        }

        // Init your AMP instance.
        var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: true,
            controls: true,
            width: "640",
            height: "400",
            poster: "",
            plugins: {
            videobreakedown: {}
            }
        }, function () {
            // Activate the plug-in.
            this.videobreakdown({
            videoId: "c4c1ad4c9a",
            syncTranscript: true,
            syncLanguage: true,
            location: "trial" /* location option for paid accounts (default is trial) */
            });

            // Set the source dynamically.
            initSource.call(this);
        });

3. Copie o código de inserção dos Insights cognitivos.

Agora você pode se comunicar com o Azure Media Player.

Para obter mais informações, consulte a demonstração do [Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Incorpore o widget do Indexador de vídeo Cognitive Insights e use um reprodutor de vídeo diferente

Se você usar um reprodutor de vídeo diferente do Azure Media Player, você deve manipular manualmente o reprodutor de vídeo para alcançar a comunicação.

1. Insira seu player de vídeo.

    Por exemplo, um player HTML5 padrão:

        <video id="vid1" width="640" height="360" controls autoplay preload>
           <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
           Your browser does not support the video tag.
        </video>    

2. Insira o widget de Insights cognitivos.
3. Implemente a comunicação para seu player escutando o evento de "mensagem". Por exemplo: 

        <script>
    
            (function(){
            // Reference your player instance.
            var playerInstance = document.getElementById('vid1');
        
            function jumpTo(evt) {
              var origin = evt.origin || evt.originalEvent.origin;
        
              // Validate that the event comes from the videobreakdown domain.
              if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
                // Call your player's "jumpTo" implementation.
                playerInstance.currentTime = evt.data.time;
               
                // Confirm the arrival to us.
                if ('postMessage' in window) {
                  evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
                }
              }
            }
        
            // Listen to the message event.
            window.addEventListener("message", jumpTo, false);
          
            }())    
        
        </script>

Para obter mais informações, consulte a demonstração do [Azure Media Player + VI Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Adicionando legendas

Se você incorporar insights do Indexador de vídeo com seu `GetVttUrl` próprio [Azure Media Player,](https://aka.ms/azuremediaplayer)você pode usar o método para obter legendas fechadas (legendas). Você também pode chamar um método JavaScript a `getSubtitlesUrl` partir do plug-in AMP do Indexador de vídeo (como mostrado anteriormente).

## <a name="customizing-embeddable-widgets"></a>Personalizando widgets que permitem inserção

### <a name="cognitive-insights-widget"></a>Widget de Insights Cognitivos

Você pode escolher os tipos de insights que deseja. Para fazer isso, especifique-os como um valor para o seguinte parâmetro de URL adicionado ao código `&widgets=<list of wanted widgets>`de incorporação que você obtenha (da API ou do aplicativo web): .

Os valores `people`possíveis `animatedCharacters` `keywords`são: , , `transcript` `ocr`, `speakers` `labels`, `sentiments`, `emotions` `topics`, `keyframes`, , , , , `scenes`, e `namedEntities`.

Por exemplo, se você quiser incorporar um widget que contenha apenas insights de pessoas e palavras-chave, a URL incorporar iframe será assim:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

O título da janela do iframe também pode ser personalizado fornecendo `&title=<YourTitle>` à URL do iframe. (Ele personaliza `<title>` o valor HTML).
   
Por exemplo, se você quiser dar à sua janela do iframe o título "MeusInsights", a URL terá esta aparência:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observe que essa opção será relevante apenas em casos em que for necessário abrir os insights em uma nova janela.

### <a name="player-widget"></a>Widget do player

Se inserir o player do Video Indexer, você poderá escolher seu tamanho especificando o tamanho do iframe.

Por exemplo: 

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Por padrão, o player Video Indexer gerou legendas fechadas autogeradas com base na transcrição do vídeo. A transcrição é extraída do vídeo com a linguagem de origem que foi selecionada quando o vídeo foi carregado.

Se você quiser incorporar com um idioma `&captions=<Language Code>` diferente, você pode adicionar à URL do jogador incorporado. Se você quiser que as legendas sejam exibidas por padrão, você pode passar &showCaptions=true.

A URL de inserção terá esta aparência:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Reprodução Automática

Por padrão, o jogador começará a reproduzir o vídeo. você pode optar por `&autoplay=false` não passar para a URL de incorporação anterior.

## <a name="code-samples"></a>Exemplos de código

Consulte o repo amostras de [código](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Widgets) que contém amostras para API e Widgets do Indexador de Vídeo:

| Arquivo/pasta                       | Descrição                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Carregar vídeo indexador de vídeo em um Azure Media Player personalizado.                        |
| `azure-media-player-vi-insights`  | Incorpore VI Insights com um azure Media Player personalizado.                             |
| `control-vi-embedded-player`      | Incorporar VI Player e controlá-lo de fora.                                    |
| `custom-index-location`           | Incorpore iAs Insights de um local externo personalizado (pode ser um blob do cliente).     |
| `embed-both-insights`             | Uso básico do VI Insights tanto jogador quanto insights.                            |
| `embed-insights-with-AMP`         | Incorpore o widget VI Insights com um azure Media Player personalizado.                      |
| `customize-the-widgets`           | Incorporar widgets VI com opções personalizadas.                                     |
| `embed-both-widgets`              | Incorpore VI Player e Insights e se comunique entre eles.                      |
| `url-generator`                   | Gera widgets personalizados incorporam URL com base em opções especificadas pelo usuário.             |
| `html5-player`                    | Incorpore iI Insights com um reprodutor de vídeo HTML5 padrão.                           |

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre como visualizar e editar insights do Indexador de vídeo, consulte [Exibir e editar insights do Indexador de vídeo](video-indexer-view-edit.md).

Além disso, confira [o indexador de vídeo CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
