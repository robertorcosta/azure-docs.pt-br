---
title: Escolha um estilo de mapa no Azure Mapas
titleSuffix: Azure Maps
description: Saiba mais sobre as funcionalidades relacionadas ao estilo do Azure Mapas.
author: walsehgal
ms.author: v-musehg
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e1d3d1b5904eded9f35c5ba628bea6426e7afaf0
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531540"
---
# <a name="choose-a-map-style-in-azure-maps"></a>Escolha um estilo de mapa no Azure Mapas

Muitos dos [estilos de mapa com suporte no mapas do Azure](./supported-map-styles.md) estão disponíveis no SDK da Web. Este artigo mostra como usar as funcionalidades relacionadas ao estilo para definir um estilo no carregamento do mapa, definir um novo estilo e usar o controle selecionador de estilos.

## <a name="set-style-on-map-load"></a>Definir estilo no carregamento do mapa

No código a seguir, a opção `style` do mapa é definida como `grayscale_dark` na inicialização.

<br/>

<iframe height='500' scrolling='no' title='Configurando o estilo no carregamento do mapa' src='//codepen.io/azuremaps/embed/WKOQRq/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/WKOQRq/'>Configurar estilo no carregamento do mapa</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="update-the-style"></a>Atualizar o estilo

No código a seguir, depois que uma instância de mapa é carregada, o estilo de mapa  é atualizado de `road` para `satellite` usando a função [SetStyle](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) do mapa.

<br/>

<iframe height='500' scrolling='no' title='Atualizar o estilo' src='//codepen.io/azuremaps/embed/yqXYzY/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/yqXYzY/'>Atualizar o estilo</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-the-style-picker"></a>Adicionar o selecionador de estilos

O código a seguir adiciona um [StyleControl](/javascript/api/azure-maps-control/atlas.control.stylecontrol) ao mapa para que o usuário possa alternar facilmente entre os diferentes estilos de mapa. 

<br/>

<iframe height='500' scrolling='no' title='Adicionar o selecionador de estilos' src='//codepen.io/azuremaps/embed/OwgyvG/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/OwgyvG/'>Atualizar o selecionador de estilos</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Por padrão, o controle seletor de estilo lista todos os estilos disponíveis ao usar o tipo de preço S0 de mapas do Azure por padrão. Se você quiser reduzir o número de estilos nessa lista, passe uma matriz dos estilos que você deseja que apareça na lista na `mapStyle` opção do seletor de estilo. Se você estiver usando S1 e quiser mostrar todos os estilos disponíveis, defina a opção `mapStyles` do seletor de estilo como `"all"`.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as classes e os métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

Adicione controles aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar controles de mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Adicionar um PIN](map-add-pin.md)
