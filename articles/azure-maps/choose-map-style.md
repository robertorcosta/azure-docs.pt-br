---
title: Alterar o estilo do mapa no Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá sobre as funcionalidades relacionadas ao estilo disponíveis no SDK para Web do Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: b1c5d9b5cd2b6b9bfecf8a0af79699061003eec1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80335680"
---
# <a name="change-the-style-of-the-map"></a>Alterar o estilo do mapa

O mapa dá suporte a várias [Opções de estilo](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) diferentes que podem ser definidas quando o mapa está sendo inicializado ou mais tarde usando a função Maps `setStyle` . Este artigo mostra como usar essas opções de estilo para personalizar a aparência dos mapas. Aprenda a definir um estilo ao carregar um mapa e aprender a definir um novo estilo de mapa usando o controle do seletor de estilo.

## <a name="set-the-style-options"></a>Definir as opções de estilo 

As opções de estilo podem ser passadas para o mapa quando ele é inicializado ou atualizado mais tarde usando a `setStyle` função Maps.

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

A ferramenta a seguir mostra como as opções de estilo diferentes alteram a forma como o mapa é renderizado. Para ver os prédios 3D, amplie o próximo de uma grande cidade. 

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções de estilo de mapa" src="https://codepen.io/azuremaps/embed/eYNMjPb?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte as <a href='https://codepen.io/azuremaps/pen/eYNMjPb'>Opções de estilo de mapa</a> de caneta pelo Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="choose-a-base-map-style"></a>Escolher um estilo de mapa base

Uma das opções de estilo de mapa mais comuns é usada para alterar o estilo do mapa base com o estilo. Muitos dos [estilos de mapa com suporte no mapas do Azure](supported-map-styles.md) estão disponíveis no SDK da Web. 

### <a name="set-base-map-style-on-map-load"></a>Definir o estilo de mapa base na carga do mapa


O estilo de mapa pode ser especificado ao inicializar o mapa definindo a `style` opção. No código a seguir, a `style` opção do mapa é definida como `grayscale_dark` na inicialização.

```javascript
var map = new atlas.Map('map', {
    style: 'grayscale_dark',

    //Additiona map options
);
```

<br/>

<iframe height='500' scrolling='no' title='Configurando o estilo no carregamento do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Configurar estilo no carregamento do mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="update-the-base-map-style"></a>Atualizar o estilo do mapa base

 O estilo de mapa pode ser atualizado usando a `setStyle` função e definindo a `style` opção como o estilo de mapa desejado.

```javascript
map.setStyle({ style: 'satellite' });
```

No código a seguir, depois que uma instância de mapa é carregada, o estilo de mapa é atualizado de `road` para `satellite` usando a função [SetStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-maps-typescript-latest#setstyle-styleoptions-) .

<br/>

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizar o estilo</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="add-the-style-picker"></a>Adicionar o selecionador de estilos

O controle de seletor de estilo fornece um botão fácil de usar com o painel flutuante que pode ser usado pelo usuário final para alterar o estilo do mapa. O seletor de estilo tem duas opções de layout diferentes. Por padrão, o seletor de estilo usa o `icons` layout e exibe todo o estilo de mapa como uma linha horizontal de ícones. 

<center>

![Layout do ícone do seletor de estilo](media/choose-map-style/style-picker-icon-layout.png)</center>

A segunda opção de layout é chamada `list` e exibe uma lista rolável de estilos de mapa.  

<center>

![Layout da lista do seletor de estilo](media/choose-map-style/style-picker-list-layout.png)</center>


O código a seguir mostra como criar uma instância do controle seletor de estilo e adicioná-lo ao canto superior direito do mapa. O seletor de estilo é definido para ter um estilo escuro e mostrar alguns estilos de mapa selecionados usando a camada de lista.

```javascript
map.controls.add(new atlas.control.StyleControl({
    mapStyles: ['road', 'night', 'grayscale_dark', 'grayscale_light'],
    layout: 'list',
    style: 'dark'
}), {
    position: 'top-right'
}); 
```

O código a seguir adiciona um controle de seletor de estilo com suas configurações padrão ao mapa, para que o usuário possa alternar facilmente entre os diferentes estilos de mapa. Alterne o estilo de mapa usando o controle de estilo de mapa próximo ao canto superior direito.

<br/>

<iframe height='500' scrolling='no' title='Adicionar o selecionador de estilos' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Atualizar o selecionador de estilos</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, ao usar o tipo de preço S0 do Azure Maps, o controle seletor de estilo lista todos os estilos disponíveis. Se você quiser reduzir o número de estilos nessa lista, passe uma matriz dos estilos que você deseja que apareça na lista na `mapStyle` opção do seletor de estilo. Se você estiver usando S1 e quiser mostrar todos os estilos disponíveis, defina a `mapStyles` opção do seletor de estilo como `"all"` .

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Estilo da](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions)

> [!div class="nextstepaction"]
> [StyleControl](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.control.stylecontrol)

> [!div class="nextstepaction"]
> [StyleControlOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.stylecontroloptions)

Adicione controles aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Adicionar um PIN](map-add-pin.md)
