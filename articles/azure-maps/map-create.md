---
title: Crie um mapa com o Azure Maps | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá como renderizar um mapa em uma página da Web usando o Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: c85d6078fce7fc8e5a5b5d8485517a8b262044a9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802324"
---
# <a name="create-a-map"></a>Criar um mapa

Este artigo mostra maneiras de criar um mapa e animar um mapa.  

## <a name="loading-a-map"></a>Carregando um mapa

Para carregar um mapa, crie uma nova instância da [classe Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map). Ao inicializar o mapa, passe um ID de elemento DIV para renderizar o mapa e passar um conjunto de opções para usar ao carregar o mapa. Se as informações de autenticação padrão `atlas` não forem especificadas no namespace, essas informações precisarão ser especificadas nas opções do mapa ao carregar o mapa. O mapa carrega vários recursos assíncronamente para desempenho. Como tal, depois de criar `ready` a `load` instância do mapa, anexe um ou evento ao mapa e, em seguida, adicione qualquer código adicional que interaja com o mapa ao manipulador de eventos. O `ready` evento é acionado assim que o mapa tem recursos suficientes carregados para serem interagidos com a programação. O `load` evento é acionado após a exibição inicial do mapa ter terminado completamente o carregamento. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Carga básica do mapa" src="//codepen.io/azuremaps/embed/rXdBXx/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a <a href='https://codepen.io/azuremaps/pen/rXdBXx/'>carga do mapa</a> Caneta<a href='https://codepen.io/azuremaps'>@azuremaps</a>Basic do Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

> [!TIP]
> Você pode carregar vários mapas na mesma página. Vários mapas na mesma página podem usar as mesmas ou diferentes configurações de autenticação e idioma.

## <a name="show-a-single-copy-of-the-world"></a>Mostre uma única cópia do mundo

Quando o mapa é ampliado em uma tela wide, várias cópias do mundo aparecerão horizontalmente. Esta opção é ótima para alguns cenários, mas para outras aplicações é desejável ver uma única cópia do mundo. Esse comportamento é implementado `renderWorldCopies` definindo `false`a opção mapas para .

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="renderWorldCopies = falso" src="//codepen.io/azuremaps/embed/eqMYpZ/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte a caneta <a href='https://codepen.io/azuremaps/pen/eqMYpZ/'>renderWorldCopies = false</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>by Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="map-options"></a>Opções de mapa

Ao criar um mapa lá, existem vários tipos diferentes de opções que podem ser passadas para personalizar como o mapa funciona conforme listado abaixo.

- [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions) e [CameraBoundOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions) são usados para especificar a área que o mapa deve exibir.
- [ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions) são usados para especificar como o mapa deve interagir com serviços que alimentam o mapa.
- [StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions) são usados para especificar que o mapa deve ser estilizado e renderizado.
- [Interação do usuárioAs opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions) são usadas para especificar como o mapa deve chegar quando o usuário está interagindo com o mapa. 

Essas opções também podem ser atualizadas depois `setCamera` `setServiceOptions`que `setStyle`o `setUserInteraction` mapa for carregado usando as funções e funções. 

## <a name="controlling-the-map-camera"></a>Controlando a câmera do mapa

Há duas maneiras de definir a área exibida do mapa usando a câmera de um mapa. Você pode definir as opções da câmera ao carregar o mapa. Ou, você pode `setCamera` chamar a opção a qualquer momento após o mapa ter carregado para atualizar programáticamente a visualização do mapa.  

<a id="setCameraOptions"></a>

### <a name="set-the-camera"></a>Definir a câmera

A câmera do mapa controla o que é exibido no viewport da tela do mapa. As opções de câmera podem ser passadas para `setCamera` as opções de mapa ao serem inicializadas ou passadas para a função de mapas.

```javascript
//Set the camera options when creating the map.
var map = new atlas.Map('map', {
    center: [-122.33, 47.6],
    zoom: 12

    //Additional map options.
};

//Update the map camera at anytime using setCamera function.
map.setCamera({
    center: [-110, 45],
    zoom: 5 
});
```

No código a seguir, um [objeto Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) é criado e as opções centro e zoom são definidas. As propriedades do mapa, como o centro e o nível de zoom, fazem parte do [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions).

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Crie um mapa via `CameraOptions` </a> pelos Serviços Baseados em Localização do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="setCameraBoundsOptions"></a>

### <a name="set-the-camera-bounds"></a>Defina os limites de câmera

Uma caixa delimitador pode ser usada para atualizar a câmera do mapa. Se a caixa delimitador foi calculada a partir de dados de ponto, muitas vezes é útil também especificar um valor de preenchimento de pixels nas opções de câmera para explicar o tamanho do ícone. Isso ajudará a garantir que os pontos não caiam da borda do ponto de exibição do mapa.

```javascript
map.setCamera({
    bounds: [-122.4, 47.6, -122.3, 47.7],
    padding: 10
});
```

No código a seguir, um objeto `new atlas.Map()` [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) é construído via . As propriedades do mapa, como `CameraBoundsOptions`, podem ser definidas por meio da função [ setCamera ](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map) da classe Map. As propriedades de limites e preenchimento são definidas usando `setCamera`.

<br/>

<iframe height='500' scrolling='no' title='Criar um mapa por meio de CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a Caneta <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Crie um mapa via `CameraBoundsOptions` </a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

### <a name="animate-map-view"></a>Animar a exibição do mapa

Ao definir as opções de câmera do mapa, [as opções](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.animationoptions) de animação também podem ser definidas. Essas opções especificam o tipo de animação e duração que deve levar para mover a câmera.

```javascript
map.setCamera({
    center: [-122.33, 47.6],
    zoom: 12,
    duration: 1000,
    type: 'fly'  
});
```

No código a seguir, o primeiro bloco de código cria um mapa e define os estilos de mapa de entrada e zoom. No segundo bloco de código, um manipulador de eventos de clique é criado para o botão animado. Quando este botão é `setCamera` clicado, a função é chamada com alguns valores aleatórios para as [Opções de Câmera](/javascript/api/azure-maps-control/atlas.cameraoptions) e [Opções de Animação](/javascript/api/azure-maps-control/atlas.animationoptions).

<br/>

<iframe height='500' scrolling='no' title='Animar a exibição do mapa' src='//codepen.io/azuremaps/embed/WayvbO/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/WayvbO/'>animar a exibição do mapa</a> por mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="try-out-the-code"></a>Experimentar o código

Olhe para as amostras de código. Você pode editar o código JavaScript dentro da **guia JS** e ver as alterações de exibição do mapa na **guia Resultado**. Você também pode clicar **em Editar no CodePen**, no canto superior direito, e modificar o código no CodePen.

<a id="relatedReference"></a>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Mapa](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Opções de câmeras](/javascript/api/azure-maps-control/atlas.cameraoptions)

> [!div class="nextstepaction"]
> [Opções de animação](/javascript/api/azure-maps-control/atlas.animationoptions)

Consulte exemplos de código para adicionar funcionalidade ao seu aplicativo:

> [!div class="nextstepaction"]
> [Alterar estilo do mapa](choose-map-style.md)

> [!div class="nextstepaction"]
> [Adicionar controles ao mapa](map-add-controls.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)
