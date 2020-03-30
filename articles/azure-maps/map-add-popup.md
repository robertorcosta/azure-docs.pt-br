---
title: Adicione um pop-up a um ponto em um mapa | Mapas do Microsoft Azure
description: Neste artigo, você aprenderá a adicionar um pop-up a um ponto usando o Microsoft Azure Maps Web SDK.
author: jinzh-azureiot
ms.author: jinzh
ms.date: 02/27/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: cf6424d2a6cbcfb7c5052201b5a9190c81fddaff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055945"
---
# <a name="add-a-popup-to-the-map"></a>Adicionar um pop-up ao mapa

Este artigo mostra como adicionar um pop-up para um ponto em um mapa.

## <a name="understand-the-code"></a>Compreender o código

O código a seguir adiciona `name` um `description` recurso de ponto, que tem e propriedades, ao mapa usando uma camada de símbolo. Um exemplo da [classe Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup) é criado, mas não exibido. Os eventos do mouse são adicionados à camada de símbolo para desencadear a abertura e o fechamento do pop-up. Quando o símbolo do marcador é pairado, a propriedade `position` do popup `content` é atualizada com a posição `name` `description` do marcador, e a opção é atualizada com algum HTML que envolve as propriedades do recurso de ponto que está sendo pairada. O pop-up é então exibido `open` no mapa usando sua função.

```javascript
//Define an HTML template for a custom popup content laypout.
var popupTemplate = '<div class="customInfobox"><div class="name">{name}</div>{description}</div>';

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

dataSource.add(new atlas.data.Feature(new atlas.data.Point([-122.1333, 47.63]), {
  name: 'Microsoft Building 41', 
  description: '15571 NE 31st St, Redmond, WA 98052'
}));

//Create a layer to render point data.
var symbolLayer = new atlas.layer.SymbolLayer(dataSource);

//Add the polygon and line the symbol layer to the map.
map.layers.add(symbolLayer);

//Create a popup but leave it closed so we can update it and display it later.
popup = new atlas.Popup({
  pixelOffset: [0, -18],
  closeButton: false
});

//Add a hover event to the symbol layer.
map.events.add('mouseover', symbolLayer, function (e) {
  //Make sure that the point exists.
  if (e.shapes && e.shapes.length > 0) {
    var content, coordinate;
    var properties = e.shapes[0].getProperties();
    content = popupTemplate.replace(/{name}/g, properties.name).replace(/{description}/g, properties.description);
    coordinate = e.shapes[0].getCoordinates();

    popup.setOptions({
      //Update the content of the popup.
      content: content,

      //Update the popup's position with the symbol's coordinate.
      position: coordinate

    });
    //Open the popup.
    popup.open(map);
  }
});

map.events.add('mouseleave', symbolLayer, function (){
  popup.close();
});
```

Abaixo está a amostra completa de código de execução da funcionalidade acima.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um pop até usando Azure Mapas' src='//codepen.io/azuremaps/embed/MPRPvz/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/MPRPvz/'>Adicionar um pop-up usando os Mapas do Azure</a> pelos Mapas do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reusing-a-popup-with-multiple-points"></a>Reutilizar um pop-up com vários pontos

Há casos em que a melhor abordagem é criar um popup e reutilizá-lo. Por exemplo, você pode ter um grande número de pontos e quer mostrar apenas um pop-up por vez. Ao reutilizar o popup, o número de elementos DOM criados pelo aplicativo é bastante reduzido, o que pode proporcionar um melhor desempenho. A amostra a seguir cria recursos de 3 pontos. Se você clicar em qualquer um deles, um pop-up será exibido com o conteúdo para esse recurso de ponto.

<br/>

<iframe height='500' scrolling='no' title='Reutilizar pop-up com vários marcadores' src='//codepen.io/azuremaps/embed/rQbjvK/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a Caneta <a href='https://codepen.io/azuremaps/pen/rQbjvK/'>Reutilizar pop-up com vários marcadores</a> pelo Azure Mapas (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customizing-a-popup"></a>Personalizando um pop-up

Por padrão, o pop-up tem um fundo branco, uma seta de ponteiro na parte inferior e um botão de fechamento no canto superior direito. A amostra a seguir altera a `fillColor` cor de fundo para preto usando a opção do popup. O botão de fechamento `CloseButton` é removido definindo a opção como falsa. O conteúdo HTML do popup usa acolchoado de 10 pixels das bordas do popup. O texto é feito branco, por isso aparece bem no fundo preto.  

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Popup personalizado" src="//codepen.io/azuremaps/embed/ymKgdg/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Consulte o <a href='https://codepen.io/azuremaps/pen/ymKgdg/'>Popup Personalizado da</a> Caneta<a href='https://codepen.io/azuremaps'>@azuremaps</a>pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="add-popup-templates-to-the-map"></a>Adicione modelos pop-up ao mapa

Modelos pop-up facilitam a criação de layouts baseados em dados para popups. As seções abaixo demonstram o uso de vários modelos popup para gerar conteúdo formatado usando propriedades de recursos.

> [!NOTE]
> Por padrão, todo o conteúdo renderizado usar o modelo pop-up será sandboxdentro de um iframe como um recurso de segurança. No entanto, existem limitações:
>
> - Todos os scripts, formulários, bloqueio de ponteiros e funcionalidade de navegação superior estão desativados. Os links podem ser abertos em uma nova guia quando clicados. 
> - Os navegadores mais antigos `srcdoc` que não suportam o parâmetro em iframes serão limitados a renderizar uma pequena quantidade de conteúdo.
> 
> Se você confiar que os dados que estão sendo carregados nos popups e potencialmente quiser que esses scripts carregados `sandboxContent` em popups possam acessar seu aplicativo, você pode desativá-lo configurando a opção de modelos popup como falso. 

### <a name="string-template"></a>Modelo de string

O modelo String substitui os espaços reservados por valores das propriedades do recurso. As propriedades do recurso não têm que ser atribuídas um valor do tipo String. Por exemplo, `value1` segura um inteiro. Esses valores são então passados `popupTemplate`para a propriedade de conteúdo do . 

A `numberFormat` opção especifica o formato do número a ser exibido. Se `numberFormat` o não for especificado, o código usará o formato de data de modelos popup. A `numberFormat` opção formata números usando a função [Number.toLocaleString.](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) Para formatar números grandes, considere usar a `numberFormat` opção com funções de [NumberFormat.format](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/NumberFormat/format). Por exemplo, o trecho de `maximumFractionDigits` código abaixo é usa para limitar o número de dígitos de fração a dois.

> [!Note]
> Há apenas uma maneira em que o modelo String pode renderizar imagens. Primeiro, o modelo String precisa ter uma tag de imagem nele. O valor que está sendo passado para a tag de imagem deve ser uma URL para uma imagem. Em seguida, o modelo `isImage` String precisa `HyperLinkFormatOptions`ter definido como verdadeiro no . A `isImage` opção especifica que o hiperlink é para uma imagem, e o hiperlink será carregado em uma tag de imagem. Quando o hiperlink for clicado, a imagem será aberta.

```javascript
var templateOptions = {
  content: 'This template uses a string template with placeholders.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 1 - String template',
    value1: 1.2345678,
    value2: {
        subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6]
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="propertyinfo-template"></a>Modelo de PropertyInfo

O modelo PropertyInfo exibe as propriedades disponíveis do recurso. A `label` opção especifica o texto a ser exibido ao usuário. Se `label` não for especificado, o hiperlink será exibido. E, se o hiperlink for uma imagem, o valor atribuído à tag "alt" será exibido. O `dateFormat` especifica o formato da data e se o formato da data não for especificado, a data será renderizada como uma seqüência de string. A `hyperlinkFormat` opção renderiza links clicáveis, `email` da mesma forma, a opção pode ser usada para renderizar endereços de e-mail clicáveis.

Antes que o modelo PropertyInfo exiba as propriedades para o usuário final, ele verifica recursivamente se as propriedades são de fato definidas para esse recurso. Ele também ignora as propriedades de estilo e título de exibição. Por exemplo, não `color`exibirá, `anchor` `size` `strokeOpacity`e `visibility`. Assim, uma vez que a verificação do caminho da propriedade esteja concluída no back-end, o modelo PropertyInfo mostra o conteúdo em um formato de tabela.

```javascript
var templateOptions = {
  content: [
    {
        propertyPath: 'createDate',
        label: 'Created Date'
    },
    {
        propertyPath: 'dateNumber',
        label: 'Formatted date from number',
        dateFormat: {
          weekday: 'long',
          year: 'numeric',
          month: 'long',
          day: 'numeric',
          timeZone: 'UTC',
          timeZoneName: 'short'
        }
    },
    {
        propertyPath: 'url',
        label: 'Code samples',
        hideLabel: true,
        hyperlinkFormat: {
          lable: 'Go to code samples!',
          target: '_blank'
        }
    },
    {
        propertyPath: 'email',
        label: 'Email us',
        hideLabel: true,
        hyperlinkFormat: {
          target: '_blank',
          scheme: 'mailto:'
        }
    }
  ]
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 2 - PropertyInfo',
    createDate: new Date(),
    dateNumber: 1569880860542,
    url: 'https://aka.ms/AzureMapsSamples',
    email: 'info@microsoft.com'
}),

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="multiple-content-templates"></a>Vários modelos de conteúdo

Um recurso também pode exibir conteúdo usando uma combinação do modelo String e do modelo PropertyInfo. Neste caso, o modelo String renderiza valores de espaços reservados em um fundo branco.  E, o modelo PropertyInfo renderiza uma imagem de largura total dentro de uma tabela. As propriedades desta amostra são semelhantes às propriedades que explicamos nas amostras anteriores.

```javascript
var templateOptions = {
  content: [
    'This template has two pieces of content; a string template with placeholders and a array of property info which renders a full width image.<br/><br/> - Value 1 = {value1}<br/> - Value 2 = {value2/subValue}<br/> - Array value [2] = {arrayValue/2}',
    [{
      propertyPath: 'imageLink',
      label: 'Image',
      hideImageLabel: true,
      hyperlinkFormat: {
        isImage: true
      }
    }]
  ],
  numberFormat: {
    maximumFractionDigits: 2
  }
};

var feature = new atlas.data.Feature(new atlas.data.Point([0, 0]), {
    title: 'Template 3 - Multiple content template',
    value1: 1.2345678,
    value2: {
    subValue: 'Pizza'
    },
    arrayValue: [3, 4, 5, 6],
    imageLink: 'https://azuremapscodesamples.azurewebsites.net/common/images/Pike_Market.jpg'
});

var popup = new atlas.Popup({
  content: atlas.PopupTemplate.applyTemplate(feature.properties, templateOptions),
  position: feature.geometry.coordinates
});
```

### <a name="points-without-a-defined-template"></a>Pontos sem um modelo definido

Quando o modelo Popup não é definido como um modelo string, um modelo PropertyInfo ou uma combinação de ambos, então ele usa as configurações padrão. Quando `title` as `description` propriedades e são as únicas atribuídas, o modelo pop-up mostra um fundo branco, um botão de fechamento no canto superior direito. E, em telas pequenas e médias, mostra uma seta na parte inferior. As configurações padrão mostram dentro de uma `title` tabela `description`para todas as propriedades que não forem as e . Mesmo quando recuando para as configurações padrão, o modelo pop-up ainda pode ser manipulado programáticamente. Por exemplo, os usuários podem desativar a detecção de hiperlink e as configurações padrão ainda se aplicariam a outras propriedades.

Clique nos pontos no mapa no CodePen. Há um ponto no mapa para cada um dos seguintes modelos popup: Modelo de string, modelo PropertyInfo e modelo de conteúdo múltiplo. Há também três pontos para mostrar como os modelos renderizam usando as configurações padrão.

<br/>

<iframe height='500' scrolling='no' title='Modelos pop-up' src='//codepen.io/azuremaps/embed/dyovrzL/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o Pen <a href='https://codepen.io/azuremaps/pen/dyovrzL/'>PopupTemplates</a> do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="reuse-popup-template"></a>Reutilizar modelo popup

Semelhante ao reutilizar popup, você pode reutilizar modelos popup. Essa abordagem é útil quando você só quer mostrar um modelo pop-up de cada vez, para vários pontos. Ao reutilizar o modelo pop-up, o número de elementos DOM criados pelo aplicativo é reduzido, o que melhora o desempenho do aplicativo. A amostra a seguir usa o mesmo modelo popup para três pontos. Se você clicar em qualquer um deles, um pop-up será exibido com o conteúdo para esse recurso de ponto.

<br/>

<iframe height='500' scrolling='no' title='ReutilizaçãodoPopupTemplate' src='//codepen.io/azuremaps/embed/WNvjxGw/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o Recurso <a href='https://codepen.io/azuremaps/pen/WNvjxGw/'>de Reutilização de Caneta saqueado</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="popup-events"></a>Eventos pop-up

Popups podem ser abertos, fechados e arrastados. A classe popup fornece eventos para ajudar os desenvolvedores a reagir a esses eventos. A amostra a seguir destaca quais eventos disparam quando o usuário abre, fecha ou arrasta o pop-up. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Eventos pop-up" src="//codepen.io/azuremaps/embed/BXrpvB/?height=500&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true">
Veja os eventos Pen <a href='https://codepen.io/azuremaps/pen/BXrpvB/'>Popup</a> do<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [Pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup)

> [!div class="nextstepaction"]
> [PopupOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions)

> [!div class="nextstepaction"]
> [Modelo popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popuptemplate)

Consulte os seguintes artigos excelentes para obter exemplos de código completo:

> [!div class="nextstepaction"]
> [Adicione uma camada de símbolo](./map-add-pin.md)

> [!div class="nextstepaction"]
> [Adicionar um marcador HTML](./map-add-custom-html.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de linha](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Adicionar uma camada de polígono](map-add-shape.md)
