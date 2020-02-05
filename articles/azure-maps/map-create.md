---
title: Criar um mapa com mapas do Azure | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a renderizar um mapa em uma página da Web usando o SDK da Web do Microsoft Azure Maps.
author: jingjing-z
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 578abae5b206b31674b00b9d27ef34174b93759f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988576"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra maneiras de criar um mapa e animar um mapa.  

## <a name="loading-a-map"></a>Carregando um mapa

Para carregar um mapa, crie uma nova instância da [classe Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest). Ao inicializar o mapa, passe uma ID do elemento DIV para renderizar o mapa e passar um conjunto de opções a serem usadas ao carregar o mapa. Se as informações de autenticação padrão não forem especificadas no namespace `atlas`, essas informações precisarão ser especificadas nas opções de mapa ao carregar o mapa. O mapa carrega vários recursos de forma assíncrona para o desempenho. Assim, depois de criar a instância de mapa, anexe um evento de `ready` ou `load` ao mapa e, em seguida, adicione qualquer código adicional que interaja com o mapa para o manipulador de eventos. O evento `ready` é acionado assim que o mapa tem recursos suficientes carregados para interagir com programaticamente. O evento `load` é acionado depois que o modo de exibição do mapa inicial termina completamente de ser carregado. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carregamento de mapa básico" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>mapa básico</a> da caneta pelo mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Você pode carregar vários mapas na mesma página. Vários mapas na mesma página podem usar a mesma configuração e configurações de idioma e autenticação diferentes.

## <a name="show-a-single-copy-of-the-world"></a>Mostrar uma única cópia do mundo

Quando o mapa for ampliado em uma tela larga, várias cópias do mundo aparecerão horizontalmente. Essa opção é excelente para alguns cenários, mas para outros aplicativos, é desejável ver uma única cópia do mundo. Esse comportamento é implementado definindo a opção Maps `renderWorldCopies` como `false`.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = false" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Confira a caneta <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="controlling-the-map-camera"></a>Controlando a câmera do mapa

Há duas maneiras de definir a área exibida do mapa usando a câmera de um mapa. Você pode definir as opções de câmera ao carregar o mapa. Ou, você pode chamar a opção `setCamera` a qualquer momento depois que o mapa for carregado para atualizar programaticamente a exibição do mapa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

No código a seguir, um [objeto MAP](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é criado e as opções Center e zoom são definidas. As propriedades do mapa, como nível de centro e zoom, fazem parte das [câmerasoptions](/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Crie um mapa via `CameraOptions` </a> pelos Serviços Baseados em Localização do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Defina os limites de câmera

No código a seguir, um [objeto de mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) é construído por meio de `new atlas.Map()`. As propriedades do mapa, como `CameraBoundsOptions`, podem ser definidas por meio da função [ setCamera ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest) da classe Map. As propriedades de limites e preenchimento são definidas usando `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Crie um mapa via `CameraBoundsOptions` </a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animar a exibição do mapa

No código a seguir, o primeiro bloco de código cria um mapa e define os estilos de mapa de inserção e zoom. No segundo bloco de código, um manipulador de eventos de clique é criado para o botão de animação. Quando esse botão é clicado, a função `setCamera` é chamada com alguns valores aleatórios para as [câmerasoptions](/javascript/api/azure-maps-control/atlas.cameraoptions) e [animationoptions](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animar a exibição do mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animar a exibição do mapa</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Experimentar o código

Examine os exemplos de código. Você pode editar o código JavaScript dentro da **guia js** e ver as alterações na exibição do mapa na **guia resultado**. Você também pode clicar em **Editar em CodePen**, no canto superior direito, e modificar o código em CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)

> [!div class="nextstepaction"]
> [Câmara de câmera](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Animaçãooptions](/javascript/api/azure-maps-control/atlas.animationoptions)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Alterar o estilo do mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controles ao mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
