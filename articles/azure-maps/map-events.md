---
title: Lidar com eventos de mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como fazer um mapa Interativo do Web SDK com eventos de mapa usando o Microsoft Azure Maps web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 09/10/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: b97371d1b63ad4abfe1635e426df1449ab5f3f14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534891"
---
# <a name="interact-with-the-map"></a>Interagir com o mapa

Este artigo mostra como usar a [classe de eventos de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?#events). A propriedade destaca eventos no mapa e em diferentes camadas do mapa. Você também pode destacar eventos quando interage com um marcador HTML.

## <a name="interact-with-the-map"></a>Interagir com o mapa

Jogue com o mapa abaixo e veja os eventos correspondentes do mouse destacados à direita. Você pode clicar na **guia JS** para visualizar e editar o código JavaScript. Você também pode clicar em **Editar no CodePen** para modificar o código no CodePen.

<br/>

<iframe height='600' scrolling='no' title='Interagir com o mapa – eventos do mouse' src='//codepen.io/azuremaps/embed/bLZEWd/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/bLZEWd/'>Interagir com os eventos do mapa do mouse</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'> CodePen</a>.
</iframe>

## <a name="interact-with-map-layers"></a>Interagir com as camadas do mapa

O código a seguir destaca o evento de disparo à medida que você interage com a Camada símbolo. A camada símbolo, bolha, linha e polígono suportam o mesmo conjunto de eventos. O mapa de calor e as camadas de azulejo não suportam nenhum desses eventos.

<br/>

<iframe height='600' scrolling='no' title='Interação com o mapa – eventos de camada' src='//codepen.io/azuremaps/embed/bQRRPE/?height=600&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/bQRRPE/'>Interação com o mapa – eventos de camada</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="interact-with-html-marker"></a>Interagir com o marcador HTML

O código a seguir adiciona eventos de mapa Javascript a um marcador HTML. Ele também destaca o nome dos eventos que são acionados conforme você interage com o marcador HTML.

<br/>

<iframe height='500' scrolling='no' title='Interação com o mapa – eventos de marcador HTML' src='//codepen.io/azuremaps/embed/VVzKJY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/VVzKJY/'>Interação com o mapa – eventos de marcador HTML</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

A tabela a seguir lista todos os eventos de classe de mapa suportados.

| Evento               | Descrição |
|---------------------|-------------|
| `boxzoomend`        | Acionado quando termina a interação "box zoom".|
| `boxzoomstart`      | Acionado quando uma interação "box zoom" é iniciada.|
| `click`             | Disparado quando um dispositivo de apontar é pressionado e liberado no mesmo ponto do mapa.|
| `close`             | Disparado quando o pop-up é fechado manualmente ou programaticamente.|
| `contextmenu`       | Acionado quando o botão direito do mouse é clicado.|
| `data`              | Acionado quando qualquer mapa de dados carrega ou muda. |
| `dataadded`         | Disparado quando as formas `DataSource`são adicionadas ao .|
| `dataremoved`       | Disparado quando as formas `DataSource`são removidas do .|
| `datasourceupdated` | Disparado quando `DataSource` o objeto é atualizado.|
| `dblclick`          | Disparado quando um dispositivo de apontar é clicado duas vezes no mesmo ponto no mapa.|
| `drag`              | Disparado repetidamente durante uma interação "arrastar para pan" no mapa, popup ou marcador HTML.|
| `dragend`           | Acionado quando uma interação "arrastar para panela" termina no mapa, popup ou marcador HTML.|
| `dragstart`         | Acionado quando uma interação "arrastar para pan" começa no mapa, popup ou marcador HTML.|
| `error`             | Disparado quando ocorre um erro.|
| `idle`              | <p>Demitido após o último quadro renderizado antes do mapa entrar em um estado "ocioso":<ul><li>Nenhuma transição de câmera está em andamento.</li><li>Todas as telhas solicitadas no momento foram carregadas.</li><li>Todas as animações de fade/transição foram concluídas.</li></ul></p>|
| `keydown`           | Disparado quando uma chave é pressionada para baixo.|
| `keypress`          | Acionado quando uma tecla que produz um caractere tipiável (uma tecla ANSI) é pressionada.|
| `keyup`             | Disparado quando uma chave é liberada.|
| `layeradded`        | Disparado quando uma camada é adicionada ao mapa.|
| `layerremoved`      | Disparado quando uma camada é removida do mapa.|
| `load`              | Demitido imediatamente após todos os recursos necessários terem sido baixados e a primeira renderização visualmente completa do mapa ocorreu.|
| `mousedown`         | Acionado quando um dispositivo de apontar é pressionado dentro do mapa ou quando em cima de um elemento.|
| `mouseenter`        | Disparado quando um dispositivo apontador é inicialmente movido sobre o mapa ou um elemento. |
| `mouseleave`        | Disparado quando um dispositivo apontador é movido para fora do mapa ou de um elemento. |
| `mousemove`         | Disparado quando um dispositivo apontador é movido dentro do mapa ou de um elemento.|
| `mouseout`          | Disparado quando um dispositivo de ponto deixa a tela do mapa, nossas folhas são um elemento.|
| `mouseover`         | Disparado quando um dispositivo apontador é movido sobre o mapa ou um elemento.|
| `mouseup`           | Disparado quando um dispositivo de apontar é liberado dentro do mapa ou quando em cima de um elemento.|
| `move`              | Disparado repetidamente durante uma transição animada de uma visão para outra, como resultado de interação do usuário ou métodos.|
| `moveend`           | Disparado logo após o mapa concluir uma transição de uma visão para outra, como resultado de interação do usuário ou métodos.|
| `movestart`         | Disparado pouco antes do mapa iniciar uma transição de uma visão para outra, como resultado de interação do usuário ou métodos.|
| `open`              | Acionado quando o pop-up é aberto manualmente ou programaticamente.|
| `pitch`             | Acionado sempre que o tom (inclinação) do mapa muda como resultado da interação do usuário ou dos métodos.|
| `pitchend`          | Disparado imediatamente após o pitch (inclinação) do mapa terminar de mudar como resultado da interação do usuário ou dos métodos.|
| `pitchstart`        | Disparado sempre que o tom do mapa (inclinação) começa uma alteração como resultado da interação do usuário ou dos métodos.|
| `ready`             | Acionado quando os recursos mínimos necessários do mapa são carregados antes que o mapa esteja pronto para interagir programáticamente.|
| `render`            | <p>Disparado sempre que o mapa é atraído para a tela, como resultado de:<ul><li>Uma mudança na posição do mapa, zoom, tom ou rolamento.</li><li>Uma mudança no estilo do mapa.</li><li>Uma mudança `DataSource` para uma fonte.</li><li>O carregamento de uma telha vetorial, arquivo GeoJSON, glifo ou sprite.</li></ul></p>|
| `resize`            | Disparado imediatamente após o mapa ter sido redimensionado.|
| `rotate`            | Disparado repetidamente durante uma interação "arrastar para girar".|
| `rotateend`         | Acionado quando termina a interação "arrastar para girar".|
| `rotatestart`       | Acionado quando uma interação "arrastar para girar" é iniciada.|
| `shapechanged`      | Disparado quando uma propriedade de objeto de forma é alterada.|
| `sourcedata`        | Acionado quando uma das fontes do mapa carrega ou muda, incluindo se uma telha pertencente a uma fonte carrega ou muda. |
| `sourceadded`       | Disparado quando `DataSource` `VectorTileSource` um ou é adicionado ao mapa.|
| `sourceremoved`     | Disparado quando `DataSource` `VectorTileSource` um ou é removido do mapa.|
| `styledata`         | Disparado quando o estilo do mapa carrega ou muda.|
| `styleimagemissing` | Disparado quando uma camada tenta carregar uma imagem do sprite de imagem que não existe |
| `tokenacquired`     | Disparado quando um token de acesso AAD é obtido.|
| `touchcancel`       | Acionado quando ocorre um evento touchcancel dentro do mapa.|
| `touchend`          | Disparado quando ocorre um evento touchend dentro do mapa.|
| `touchmove`         | Disparado quando ocorre um evento touchmove dentro do mapa.|
| `touchstart`        | Disparado quando ocorre um evento touchstart dentro do mapa.|
| `wheel`             | Disparado quando ocorre um evento da roda do mouse dentro do mapa.|
| `zoom`              | Disparado repetidamente durante uma transição animada de um nível de zoom para outro, como resultado de interação do usuário ou métodos.|
| `zoomend`           | Disparado logo após o mapa completar uma transição de um nível de zoom para outro, como resultado de interação do usuário ou métodos.|
| `zoomstart`         | Disparado pouco antes do mapa iniciar uma transição de um nível de zoom para outro, como resultado de interação do usuário ou métodos.|


## <a name="next-steps"></a>Próximas etapas

Consulte os seguintes artigos para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Usando o módulo Azure Maps Services](./how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
