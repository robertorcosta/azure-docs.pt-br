---
title: Mude o estilo do mapa no Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre funcionalidades relacionadas ao estilo disponíveis no Microsoft Azure Maps web SDK.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335680"
---
# <a name="change-the-style-of-the-map"></a>Alterar o estilo do mapa

O mapa suporta várias [opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) de estilo diferentes que podem ser `setStyle` definidas quando o mapa está sendo inicializado ou posterior usando a função mapas. Este artigo mostra como usar essas opções de estilo para personalizar a aparência dos mapas. Aprenda a definir um estilo ao carregar um mapa e aprenda a definir um novo estilo de mapa usando o controle do seletor de estilo.

## <a name="set-the-style-options"></a>Defina as opções de estilo 

As opções de estilo podem ser passadas para o `setStyle` mapa quando ela é inicializada ou atualizada posteriormente usando a função mapas.

```javascript
//Set the style options when creating the map.
var map = new atlas.Map('map', {
    renderWorldCopies: false,
    showBuildingModels: true

    //Additional map options.
};

//Update the style options at anytime using setStyle function.
map.setStyle({
    renderWorldCopies: true,
    showBuildingModels: false
});
```

A ferramenta a seguir mostra como as diferentes opções de estilo mudam a forma como o mapa é renderizado. Para ver os edifícios 3D, aproxime-se de uma grande cidade. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de estilo de mapa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja as <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>opções</a> de estilo Do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Mapa da Caneta pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Escolha um estilo de mapa base

Uma das opções de estilo de mapa mais comuns é usada para alterar o estilo do mapa base que é estilizado. Muitos dos estilos de [mapa suportados no Azure Maps](supported-map-styles.md) estão disponíveis no Web SDK. 

### <a name="set-base-map-style-on-map-load"></a>Definir o estilo do mapa base na carga do mapa


O estilo do mapa pode ser especificado `style` ao inicializar o mapa definindo a opção. No código a `style` seguir, a opção `grayscale_dark` do mapa é definida como inicialização.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Configurando o estilo no carregamento do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Configurar estilo no carregamento do mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Atualize o estilo do mapa base

 O estilo do mapa `setStyle` pode ser `style` atualizado usando a função e definindo a opção para o estilo de mapa desejado.

```javascript
map.setStyle({ style: 'satellite' });
```

No código a seguir, depois que uma instância do `road` mapa `satellite` é carregada, o estilo do mapa é atualizado a partir do uso da função [setStyle.](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-)

<br/>

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizar o estilo</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Adicionar o selecionador de estilos

O controle do seletor de estilo fornece um botão fácil de usar com painel flyout que pode ser usado pelo usuário final para alterar o estilo do mapa. O seletor de estilo tem duas opções de layout diferentes. Por padrão, o seletor de estilos usa o `icons` layout e exibe todo o estilo do mapa como uma linha horizontal de ícones. 

<center>

![Layout do ícone do seletor de estilo](media/choose-map-style/style-picker-icon-layout.png)</center>

A segunda opção `list` de layout é chamada e exibe uma lista rolável de estilos de mapa.  

<center>

![Layout da lista de seletoreres de estilo](media/choose-map-style/style-picker-list-layout.png)</center>


O código a seguir mostra como criar uma instância do controle do seletor de estilo e adicioná-lo ao canto superior direito do mapa. O seletor de estilos está definido para ter um estilo escuro e mostrar alguns estilos de mapa selecionados usando a camada da lista.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

O código a seguir adiciona um controle de seletor de estilo com suas configurações padrão ao mapa, para que o usuário possa facilmente alternar entre os diferentes estilos do mapa. Alterne o estilo do mapa usando o controle de estilo do mapa perto do canto superior direito.

<br/>

<iframe height='500' scrolling='no' title='Adicionar o selecionador de estilos' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Atualizar o selecionador de estilos</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, ao usar o nível de preços S0 do Azure Maps, o controle do seletor de estilos lista todos os estilos disponíveis. Se você quiser reduzir o número de estilos nesta lista, passe uma matriz dos `mapStyle` estilos que deseja aparecer na lista na opção do seletor de estilos. Se você estiver usando o S1 e quiser `mapStyles` mostrar todos os estilos `"all"`disponíveis, defina a opção do seletor de estilo para .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de estilo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [Opções de EstiloControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Adicione controles aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Adicionar um PIN](map-add-pin.md)
