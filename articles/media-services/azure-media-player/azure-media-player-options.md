---
title: Opções do Azure Media Player
description: O código de incorporação do Azure Media Player é simplesmente uma tag de vídeo HTML5, portanto, para muitas das opções, você pode usar os atributos de tag padrão para definir as opções.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727158"
---
# <a name="options"></a>Opções #

## <a name="setting-options"></a>Opções de configuração ##

O código de incorporação do Azure Media Player é simplesmente uma tag de vídeo HTML5, portanto, para muitas das opções, você pode usar os atributos de tag padrão para definir as opções.

`<video controls autoplay ...>`

Alternativamente, você pode usar o atributo de configuração de dados para fornecer opções no formato [JSON.](http://json.org/example.html) É assim também que você definiria opções que não são padrão para a tag de vídeo.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Finalmente, se você não estiver usando o atributo de configuração de dados para ativar a configuração do jogador, você pode passar em um objeto com as opções do jogador como o segundo argumento na função de configuração JavaScript.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> As opções no construtor só são definidas na primeira inicialização antes que a fonte seja definida.  Se você deseja modificar as opções no mesmo elemento inicializado do Azure Media Player, você deve atualizar as opções antes de alterar a origem. Você pode atualizar as opções `myPlayer.options({/*updated options*/});`em JavaScript usando . Observe que apenas as opções alteradas serão afetadas, todas as outras opções definidas anteriormente persistirão.

## <a name="individual-options"></a>Opções individuais ##

> [!NOTE]
>Os atributos de tag de vídeo só podem ser verdadeiros ou falsos (booleanos), você simplesmente inclui o atributo (sem sinal igual) para atilá-lo ou excluí-lo para desligá-lo. Por exemplo, para ativar os `<video controls="true" ...>` `<video controls ...>` controles: ERRADO O maior problema que as pessoas têm é tentar definir esses valores como falsos usando falso como o valor (por exemplo, controles="falso") que realmente faz o oposto e define o valor como verdadeiro porque o atributo ainda está incluído.

### <a name="controls"></a>controls ###

A opção de controles define se o jogador tem ou não controles com os quais o usuário pode interagir. Sem controles a única maneira de iniciar a reprodução de vídeo é com o atributo de reprodução automática ou através da API.

`<video controls ...>` ou `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Se a reprodução automática for verdadeira, o vídeo começará a ser reproduzido assim que a página for carregada (sem qualquer interação do usuário).

> [!NOTE]
> Esta opção não é suportada por dispositivos móveis como Windows Phone, Apple iOS e Android. Os dispositivos móveis bloqueiam a funcionalidade de reprodução automática para evitar o uso excessivo dos planos de dados mensais do consumidor (muitas vezes caros). Um toque/clique do usuário é necessário para iniciar o vídeo neste caso.

`<video autoplay ...>`Ou`{ "autoplay": true }`

### <a name="poster"></a>Poster ###
O atributo do pôster define a imagem que é exibida antes do vídeo começar a ser reproduzido. Este é muitas vezes um quadro do vídeo ou uma tela de título personalizada. Assim que o usuário clicar em reproduzir a imagem desaparecerá.

`<video poster="myPoster.jpg" ...>` ou `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>width ###

O atributo de largura define a largura de exibição do vídeo.

`<video width="640" ...>` ou `{ "width": 640 }`

### <a name="height"></a>height ###

O atributo de altura define a altura do display do vídeo.

`<video height="480" ...>` ou `{ "height": 480 }`

### <a name="plugins"></a>plug-ins ###

Os plugins JSON determina quais plugins são carregados com essa instância de AMP permite configurar todas as opções que o plugin pode ter.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Para obter mais informações sobre o desenvolvimento e o uso do plugin, consulte [plugins de escrita](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>outras opções ###

Outras opções podem `<video>` ser definidas na tag usando o `data-setup` parâmetro que leva um JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Isso é explicitamente definido como falso. Ao definir como falso, ele permitirá que a pele do Azure Media Player seja renderizada da mesma forma em todas as plataformas.  Além disso, ao contrário do nome, o toque ainda será ativado.

### <a name="fluid"></a>Fluido ###

Ao definir esta opção para o elemento de vídeo verdadeiro, a largura total do recipiente pai e a altura será ajustada para caber um vídeo com uma proporção padrão de 16:9.

`<video ... data-setup='{"fluid": true}'>`

`fluid`opção substitui `width` explícitas e `height` configurações. Esta opção só está disponível na `2.0.0` versão do Azure Media Player e posteriormente.

### <a name="playbackspeed"></a>velocidade de reprodução ###

`playbackSpeed`opção controla reproduçãoControle de velocidade e conjunto de configurações de velocidade de reprodução disponíveis para o usuário. `playbackSpeed`leva um objeto. Para habilitar o controle de velocidade de `enabled` reprodução na barra de controle, a propriedade do objeto precisa ser definida como verdadeira. Um exemplo de habilitação da velocidade de reprodução na marcação:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Outras propriedades `playbackSpeed` da configuração são dadas pelo objeto [ReproduçãoSpeedOptions.](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)

Exemplo de definição de opções de velocidade de reprodução em JavaScript:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Esta opção só está disponível no Azure Media Player versão 2.0.0 e posterior.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

A `staleDataTimeLimitInSec` opção é uma otimização que permite configurar quantos segundos de dados obsoletos você gostaria de manter nos buffers mediaSource. Isso está desabilitado por padrão.

### <a name="cea708captionssettings"></a>cea708CaptionsConfigurações ###

A configuração habilitada para true permite que você exiba legendas cea ao vivo em suas transmissões ao vivo e arquivos ao vivo. O atributo do rótulo não é necessário, se não estiver incluído, o jogador voltará para um rótulo padrão.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Esta opção só está disponível no Azure Media Player versão 2.1.1 e posterior.

## <a name="next-steps"></a>Próximas etapas ##

- [Azure Media Player Quickstart](azure-media-player-quickstart.md)