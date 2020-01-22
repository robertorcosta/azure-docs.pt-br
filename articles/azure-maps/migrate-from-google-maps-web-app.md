---
title: 'Tutorial: Migrar um aplicativo Web do Google Maps | Microsoft Azure Mapas'
description: Como migrar um aplicativo Web do Google Maps para o Microsoft Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 08566283181a4bb15f77016834c4dc0dffc184b7
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910880"
---
# <a name="migrate-a-web-app-from-google-maps"></a>Migrar um aplicativo Web do Google Maps

A maioria dos aplicativos Web que usa o Google Maps está usando o SDK do JavaScript do Google Maps V3. O SDK da Web do Azure Mapas é o SDK baseado no Azure adequado para o qual migrar. O SDK da Web do Azure Mapas lhe permite personalizar mapas interativos com seu próprio conteúdo e imagens para exibição em seus aplicativos móveis ou Web. Esse controle utiliza WebGL, permitindo que você processe grandes conjuntos de dados com alto desempenho. Desenvolva com esse SDK usando JavaScript ou TypeScript.

Se estiver migrando um aplicativo Web existente, verifique se ele está usando uma biblioteca de controle de mapeamento de software livre, como Cesium, Leaflet e OpenLayers. Se for e você não quiser usar o SDK da Web do Azure Mapas, outra opção para migrar seu aplicativo será continuar usando o controle de mapeamento de software livre e conectá-lo aos serviços de bloco do Azure Mapas ([blocos de estrada](https://docs.microsoft.com/rest/api/maps/render/getmaptile) \| [blocos satélites](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile)). Veja a seguir os detalhes de como usar o Azure Mapas em algumas bibliotecas de controle de mapeamento de software livre comumente usadas.

- Cesium – um controle de mapeamento 3D para a Web. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20Cesium%20JS) \| [Documentação](https://cesiumjs.org/)
- Leaflet – controle de mapeamento 2D leve para a Web. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Azure%20Maps%20Raster%20Tiles%20in%20Leaflet%20JS) \| [Documentação](https://leafletjs.com/)
- OpenLayers – controle de mapeamento 2D para a Web compatível com projeções. [Exemplo de código](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Raster%20Tiles%20in%20OpenLayers) \| [Documentação](https://openlayers.org/)

## <a name="key-features-support"></a>Suporte aos principais recursos

A tabela a seguir lista os principais recursos da API no SDK do JavaScript do Google Maps V3 e o suporte de uma API semelhante no SDK da Web do Azure Mapas.

| Recurso do Google Maps     | Suporte do SDK da Web do Azure Mapas |
|-------------------------|:--------------------------:|
| Marcadores                 | ✓                          |
| Clustering de marcadores       | ✓                          |
| Polilinhas e polígonos    | ✓                          |
| Camadas de dados             | ✓                          |
| Sobreposições de piso         | ✓                          |
| Mapas de calor               | ✓                          |
| Camadas de bloco             | ✓                          |
| Camada KML               | ✓                          |
| Ferramentas de desenho           | ✓                          |
| Serviço geocodificador        | ✓                          |
| Serviço de instruções      | ✓                          |
| Serviço da matriz de distância | ✓                          |
| Serviço de elevação       | Planejado                    |

## <a name="notable-differences-in-the-web-sdks"></a>Diferenças notáveis nos SDKs da Web

A seguir estão algumas das principais diferenças entre os SDKs da Web do Google Maps e do Azure Mapas que você deve conhecer:

- Além de fornecer um ponto de extremidade hospedado para acessar o SDK da Web do Azure Mapas, também há um pacote NPM disponível para inserir o SDK da Web em aplicativos, se você preferir. Confira esta [documentação](how-to-use-map-control.md) para obter mais informações. Esse pacote também inclui definições de TypeScript.
- Depois de criar uma instância da classe Map no Azure Mapas, seu código deve aguardar até que o evento `ready` ou `load` dos mapas sejam disparados antes de interagir com o mapa. Isso garantirá que todos os recursos de mapa tenham sido carregados e estejam prontos para serem acessados.
- Ambas as plataformas usam um sistema de divisão semelhante para os mapas base, no entanto, os blocos no Google Maps têm uma dimensão de 256 pixels, enquanto os blocos no Azure Mapas têm uma dimensão de 512 pixels. Assim, para obter a mesma exibição de mapa no Azure Mapas que no Google Maps, um nível de zoom usado no Google Maps precisa ser subtraído em um no Azure Mapas.
- As coordenadas no Google Maps são chamadas de "latitude, longitude", enquanto o Azure Mapas usa "longitude, latitude". Isso está alinhado ao padrão `[x, y]`, seguido pela maioria das plataformas GIS.
- As formas no SDK da Web do Azure Mapas são baseadas no esquema GeoJSON. As classes auxiliares são expostas por meio do [namespace *atlas.data*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data?view=azure-iot-typescript-latest). Também há a classe [*atlas.Shape*](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.shape), que pode ser usada para encapsular objetos GeoJSON para facilitar a atualização e a manutenção desses objetos de uma maneira vinculável de dados.
- As coordenadas no Azure Mapas são definidas como objetos de posição que podem ser especificados como uma matriz de número simples no formato `[longitude, latitude]` ou o novo atlas.data.position(longitude, latitude).
    > [!TIP]
    > A classe Position tem um método auxiliar estático para importar coordenadas que estão no formato "latitude, longitude". O método [atlas.data.position.fromLatLng](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest) pode, muitas vezes, substituir o método `new google.maps.LatLng` no código do Google Maps.
- Em vez de especificar informações de estilo em cada forma adicionada ao mapa, o Azure Mapas separa os estilos dos dados. Os dados são armazenados em fontes de dados e conectados à renderização de camadas que o código do Azure Mapas usa para renderizar os dados. Essa abordagem oferece o benefício de desempenho aprimorado. Além disso, muitas camadas são compatíveis com o estilo controlado por dados, em que a lógica de negócios pode ser adicionada às opções de estilo de camada que mudarão a maneira como as formas individuais são renderizadas dentro de uma camada com base nas propriedades definidas na forma.

## <a name="web-sdk-side-by-side-examples"></a>Exemplos lado a lado do SDK da Web

Veja a seguir uma coleção de exemplos de código para cada plataforma que abrange casos de uso comuns para ajudá-lo a migrar seu aplicativo Web do SDK do JavaScript do Google Maps V3 para o SDK da Web do Azure Mapas. Exemplos de código relacionados a aplicativos Web são fornecidos em JavaScript. No entanto, o Azure Mapas também fornece definições de TypeScript como uma opção adicional por meio de um [módulo NPM](how-to-use-map-control.md).

### <a name="load-a-map"></a>Carregar um mapa

Carregar um mapa em ambos os SDKs segue o mesmo conjunto de etapas:

- Adicione uma referência ao SDK do Mapa.
- Adicione uma tag `div` ao corpo da página que atuará como um espaço reservado para o mapa.
- Crie uma função JavaScript chamada quando a página é carregada.
- Crie uma instância da respectiva classe de mapa.

**Algumas das principais diferenças**

- O Google Maps exige que uma chave de conta seja especificada na referência de script da API. As credenciais de autenticação para o Azure Mapas são especificadas como opções da classe do mapa. Pode ser uma chave de assinatura ou informações do Azure Active Directory.
- O Google Maps usa uma função de retorno de chamada na referência de script da API, que é usada para chamar uma função de inicialização para carregar o mapa. Com o Azure Mapas, o evento OnLoad da página deve ser usado.
- Ao fazer referência ao elemento `div` no qual o mapa será renderizado, a classe `Map` no Azure Mapas requer apenas o valor `id`, enquanto o Google Maps requer um objeto `HTMLElement`.
- As coordenadas no Azure Mapas são definidas como objetos de posição que podem ser especificados como uma matriz de número simples no formato `[longitude, latitude]`.
- O nível de zoom no Azure Mapas é um nível inferior ao exemplo do Google Maps devido à diferença nos tamanhos de sistema de divisão entre as plataformas.
- Por padrão, o Azure Mapas não adiciona nenhum controle de navegação à tela do mapa, como botões de zoom e botões de estilo de mapa. No entanto, há controles para adicionar um seletor de estilo de mapa, botões de zoom, controle de bússola ou rotação e um controle de densidade.
- Um manipulador de eventos é adicionado ao Azure Mapas para monitorar o evento `ready` da instância do mapa. Isso será disparado quando o mapa terminar de carregar o contexto do WebGL e todos os recursos necessários. Qualquer código pós-carregamento pode ser adicionado a esse manipulador de eventos.

Os exemplos a seguir mostram como carregar um mapa básico centralizado nas coordenadas de Nova York (longitude:-73,985, latitude: 40.747) e está no nível de zoom 12 no Google Maps.

**Antes: Google Maps**

O código a seguir é um exemplo de como exibir um Google Map centralizado e ampliado em uma localização.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.747, -73.985),
                zoom: 12
            });
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

<center>

![Google Maps simples](media/migrate-google-maps-web-app/simple-google-map.png)</center>

**Depois: Azure Mapas**

O código a seguir mostra como carregar um mapa com o mesmo modo de exibição no Azure Mapas, juntamente com um controle de estilo de mapa e botões de zoom.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-73.985, 40.747],  //Format coordinates as longitude, latitude.
                zoom: 11,   //Subtract the zoom level by one.

                //Specify authentication information when loading the map.
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {
                //Add zoom controls to bottom right of map.
                map.controls.add(new atlas.control.ZoomControl(), {
                    position: 'bottom-right'
                });

                //Add map style control in top left corner of map.
                map.controls.add(new atlas.control.StyleControl(), {
                    position: 'top-left'
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

<center>

![Tutorial simples para Azure Mapas](media/migrate-google-maps-web-app/simple-azure-maps.png)</center>

A documentação detalhada de como configurar e usar o controle de mapeamento do Azure Mapas em um aplicativo Web pode ser encontrada [aqui](how-to-use-map-control.md).

> [!NOTE]
> Ao contrário do Google Maps, o Azure Mapas não exige que um centro inicial e um nível de zoom sejam especificados durante o carregamento do mapa. Se essas informações não forem fornecidas ao carregar o mapa, o mapa tentará determinar em qual cidade o usuário está e centralizará e aplicará o zoom lá.

**Recursos adicionais:**

- O Azure Mapas também fornece controles de navegação para girar e pinçar a exibição de mapa conforme documentado [aqui](map-add-controls.md).

### <a name="localizing-the-map"></a>Como localizar o mapa

Se o público-alvo estiver espalhado em vários países ou falar idiomas diferentes, a localização será importante.

**Antes: Google Maps**

Para localizar os parâmetros do Google Maps, o idioma e a região são adicionados

```html
<script type="text/javascript" src=" https://maps.googleapis.com/maps/api/js?callback=initMap&key=[api_key]& language=[language_code]&region=[region_code]" async defer></script>
```

Aqui está um exemplo do Google Maps com o idioma definido como "fr-FR".

<center>

![Localização do Google Maps](media/migrate-google-maps-web-app/google-maps-localization.png)</center>

**Depois: Azure Mapas**

O Azure Mapas fornece duas maneiras diferentes de definir o idioma e a exibição regional do mapa. A primeira opção é adicionar essas informações ao namespace global *atlas*, o que resultará em todas as instâncias de controle de mapeamento em seu aplicativo usarem essas configurações como padrão. O seguinte define o idioma como francês ("fr-FR") e a exibição regional como "auto":

```javascript
atlas.setLanguage('fr-FR');
atlas.setView('auto');
```

A segunda opção é passar essas informações para as opções de mapa ao carregar o mapa, como:

```javascript
map = new atlas.Map('myMap', {
    language: 'fr-FR',
    view: 'auto',

    authOptions: {
        authType: 'subscriptionKey',
        subscriptionKey: '<Your Azure Maps Key>'
    }
});
```

> [!NOTE]
> Com o Azure Mapas, é possível carregar várias instâncias de mapa na mesma página com diferentes configurações de idioma e região. Além disso, também é possível atualizar essas configurações no mapa após ele ter sido carregado. Uma lista detalhada de idiomas compatíveis no Azure Mapas pode ser encontrada [aqui](supported-languages.md).

Aqui está um exemplo do Azure Mapas com o idioma definido como "fr" e a região do usuário definida como "fr-FR".

<center>

![Localização do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-localization.png)</center>

### <a name="setting-the-map-view"></a>Como configurar a exibição do mapa

É possível mover programaticamente mapas dinâmicos no Azure Mapas e no Google Maps para novas localizações geográficas chamando as funções adequadas em JavaScript. Os exemplos a seguir mostram como fazer com que o mapa exiba imagem aérea de satélite, centralize o mapa em uma localização com coordenadas (longitude: -111,0225, latitude: 35,0272) e altere o nível de zoom para 15 no Google Maps.

> [!NOTE]
> O Google Maps usa blocos de 256 pixels, enquanto o Azure Mapas usa um tamanho de bloco maior, de 512 pixels. Isso reduz o número de solicitações de rede necessárias para que o Azure Mapas carregue a mesma área do mapa que o Google Maps. No entanto, devido à maneira como as pirâmides de bloco funcionam nos controles de mapa, os blocos maiores no Azure Mapas significam que, ao usar o Azure Mapas, você precisa subtrair o nível de zoom usado no Google Maps em um para alcançar a mesma área visível que teria como um mapa no Google Maps.

**Antes: Google Maps**

O controle de mapeamento do Google Maps pode ser movido de maneira programática usando o método `setOptions`, que permite especificar o centro do mapa e um nível de zoom.

```javascript
map.setOptions({
    mapTypeId: google.maps.MapTypeId.SATELLITE,
    center: new google.maps.LatLng(35.0272, -111.0225),
    zoom: 15
});
```

<center>

![Exibição de conjunto do Google Maps](media/migrate-google-maps-web-app/google-maps-set-view.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, a posição do mapa pode ser alterada programaticamente usando o método `setCamera` do mapa e a alteração do estilo do mapa ser feita usando o método `setStyle`. Observe que as coordenadas no Azure Mapas estão no formato "longitude, latitude" e o valor do nível de zoom é subtraído em um.

```javascript
map.setCamera({
    center: [-111.0225, 35.0272],
    zoom: 14
});

map.setStyle({
    style: 'satellite'
});
```

<center>

![Definição de exibição do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-set-view.jpeg)</center>

**Recursos adicionais:**

- [Escolher um estilo de mapa](choose-map-style.md)
- [Estilos de mapa compatíveis](supported-map-styles.md)

### <a name="adding-a-marker"></a>Adicionar um marcador

No Azure Mapas, há várias maneiras de renderizar os dados de ponto no mapa.

- **Marcadores HTML** – renderiza pontos usando elementos DOM tradicionais. Os Marcadores de HTML dão suporte para arrastar.
- **Camada de Símbolos** – renderiza pontos com um ícone e/ou texto dentro do contexto WebGL.
- **Camada de Bolhas** – renderiza pontos como círculos no mapa. O raio dos círculos pode ser dimensionado com base nas propriedades dos dados.

As camadas de Símbolos e Bolhas são renderizadas no contexto WebGL e podem renderizar conjuntos muito grandes de pontos no mapa. Essas camadas exigem que os dados sejam armazenados em uma fonte de dados. As fontes de dados e as camadas de renderização devem ser adicionadas ao mapa depois que o evento `ready` é disparado. Os marcadores HTML são renderizados como elementos DOM dentro da página e não usam uma fonte de dados. Quanto mais elementos DOM uma página tiver, mais lenta ela será. Ao renderizar mais de algumas centenas de pontos em um mapa, é recomendável usar uma das camadas de renderização.

Os exemplos a seguir adicionam um marcador ao mapa em (longitude:-0,2, latitude: 51,5) com o número 10 sobreposto como um rótulo.

**Antes: Google Maps**

Com o Google Maps, os marcadores são adicionados ao mapa usando a classe `google.maps.Marker` e especificando o mapa como uma das opções.

```javascript
//Create a marker and add it to the map.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    label: '10',
    map: map
});
```

<center>

![Marcador do Google Maps](media/migrate-google-maps-web-app/google-maps-marker.png)</center>

**Depois: Azure Mapas usando Marcadores HTML**

No Azure Mapas, os marcadores HTML podem ser usados para exibir um ponto no mapa e são recomendados para aplicativos simples que só precisam exibir um pequeno número de pontos no mapa. Para usar um marcador HTML, basta criar uma instância da classe `atlas.HtmlMarker`, definir as opções de texto e de posição e adicionar o marcador ao mapa usando o método `map.markers.add`.

```javascript
//Create a HTML marker and add it to the map.
map.markers.add(new atlas.HtmlMarker({
    text: '10',
    position: [-0.2, 51.5]
}));
```

<center>

![Marcador HTML do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-html-marker.png)</center>

**Depois: Azure Mapas usando uma Camada de Símbolos**

Ao usar uma camada de símbolos, os dados devem ser adicionados a uma fonte de dados e a fonte de dados deve ser anexada à camada. Além disso, a fonte de dados e a camada devem ser adicionadas ao mapa depois de o evento de `ready` ser disparado. Para renderizar um valor de texto exclusivo acima de um símbolo, as informações de texto precisam ser armazenadas como uma propriedade do ponto de dados e essa propriedade deve ser referenciada na `textField` opção da camada. Isso é um pouco mais trabalhoso do que usar marcadores HTML, mas oferece muitas vantagens de desempenho.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Create a point feature, add a property to store a label for it, and add it to the data source.
                datasource.add(new atlas.data.Feature(new atlas.data.Point([-0.2, 51.5]), {
                    label: '10'
                }));

                //Add a layer for rendering point data as symbols.
                map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                    textOptions: {
                        //Use the label property to populate the text for the symbols.
                        textField: ['get', 'label'],
                        color: 'white',
                        offset: [0, -1]
                    }
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Camada de símbolos do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-symbol-layer.png)</center>

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicionar uma camada de Símbolo](map-add-pin.md)
- [Adicionar uma camada de Bolhas](map-add-bubble-layer.md)
- [Dados de ponto de cluster](clustering-point-data-web-sdk.md)
- [Adicionar Marcadores HTML](map-add-custom-html.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone da camada de Símbolos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opção de texto da camada de Símbolos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opções de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-custom-marker"></a>Adicionar um marcador personalizado

Imagens personalizadas podem ser usadas para representar pontos em um mapa. A imagem a seguir é usada nos exemplos abaixo, que usam uma imagem personalizada para exibir um ponto no mapa em (latitude: 51,5, longitude: -0,2) e desloca a posição do marcador para que o ponto do ícone de pino alinhe-se com a posição correta no mapa.

<center>

![imagem do pino amarelo](media/migrate-google-maps-web-app/ylw_pushpin.png)<br/>
ylw\_pushpin.png</center>

**Antes: Google Maps**

No Google Maps, um marcador personalizado é criado especificando um objeto `Icon` que contém a `url` da imagem, um ponto de `anchor` para alinhar o ponto da imagem do alfinete com a coordenada no mapa. O valor de âncora no Google Maps em relação ao canto superior esquerdo da imagem.

```javascript
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(51.5, -0.2),
    icon: {
        url: 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png',
        anchor: new google.maps.Point(5, 30)
    },
    map: map
});
```

<center>

![Marcador personalizado do Google Maps](media/migrate-google-maps-web-app/google-maps-custom-marker.png)</center>

**Depois: Azure Mapas usando Marcadores HTML**

Para personalizar um marcador HTML no Azure Mapas, um `string` HTML ou `HTMLElement` pode ser passado para a opção `htmlContent` do marcador. No Azure Mapas, uma opção `anchor` é usada para especificar a posição relativa do marcador em relação à coordenada de posição usando um dos nove pontos de referência definidos: "centro", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo", "inferior direito". O conteúdo é ancorado no centro inferior do conteúdo HTML por padrão. Para facilitar a migração de código do Google Maps, defina o `anchor` como "superior esquerdo" e, em seguida, use a opção `pixelOffset` com o mesmo deslocamento usado no Google Maps. Os deslocamentos no Azure Mapas são movidos na direção oposta do Google Maps, portanto, multiplique-os por menos um.

> [!TIP]
> Adicione `pointer-events:none` como um estilo no conteúdo HTML para desabilitar o comportamento de arrastar padrão no Microsoft Edge, que exibirá um ícone indesejado.

```javascript
map.markers.add(new atlas.HtmlMarker({
    htmlContent: '<img src="https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png" style="pointer-events: none;" />',
    anchor: 'top-left',
    pixelOffset: [-5, -30],
    position: [-0.2, 51.5]
}));
```

<center>

![Marcador HTML personalizado do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-custom-html-marker.png)</center>

**Depois: Azure Mapas usando uma Camada de Símbolos**

As camadas de símbolo no Azure Mapas também dão suporte a imagens personalizadas, mas é preciso primeiro que a imagem seja carregada nos recursos de mapa e que uma ID exclusiva seja atribuída a ela. Em seguida, a camada de símbolo pode fazer referência a essa ID. O símbolo pode ser deslocado para alinhar-se ao ponto correto na imagem pelo uso da opção `offset` de ícone. No Azure Mapas, uma opção `anchor` é usada para especificar a posição relativa do símbolo em relação à coordenada de posição usando um dos nove pontos de referência definidos: "centro", "superior", "inferior", "esquerda", "direita", "superior esquerdo", "superior direito", "inferior esquerdo", "inferior direito". O conteúdo é ancorado no centro inferior do conteúdo HTML por padrão. Para facilitar a migração de código do Google Maps, defina o `anchor` como "superior esquerdo" e, em seguida, use a opção `offset` com o mesmo deslocamento usado no Google Maps. Os deslocamentos no Azure Mapas são movidos na direção oposta do Google Maps, portanto, multiplique-os por menos um.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;

        function initMap() {
            map = new atlas.Map('myMap', {
                center: [-0.2, 51.5],
                zoom: 9,
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Load the custom image icon into the map resources.
                map.imageSprite.add('my-yellow-pin', 'https://azuremapscodesamples.azurewebsites.net/Common/images/icons/ylw-pushpin.png').then(function () {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Create a point and add it to the data source.
                    datasource.add(new atlas.data.Point([-0.2, 51.5]));

                    //Add a layer for rendering point data as symbols.
                    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            //Set the image option to the id of the custom icon that was loaded into the map resources.
                            image: 'my-yellow-pin',
                            anchor: 'top-left',
                            offset: [-5, -30]
                        }
                    }));
                });
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Camada de símbolos de ícone personalizado do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-custom-icon-symbol-layer.png)</center>

> [!TIP]
> Para criar uma renderização personalizada avançada de pontos, use várias camadas de renderização juntas. Por exemplo, se você quiser ter vários alfinetes com o mesmo ícone em diferentes círculos coloridos, em vez de criar várias imagens para cada sobreposição de cor de uma camada de símbolos na parte superior de uma camada de bolhas e fazer com que referenciem a mesma fonte de dados. Isso será muito mais eficiente do que criar e fazer com que o mapa mantenha várias imagens diferentes.

**Recursos adicionais:**

- [Criar uma fonte de dados](create-data-source-web-sdk.md)
- [Adicionar uma camada de Símbolo](map-add-pin.md)
- [Adicionar Marcadores HTML](map-add-custom-html.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)
- [Opções de ícone da camada de Símbolos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)
- [Opção de texto da camada de Símbolos](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)
- [Classe de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)
- [Opções de marcador HTML](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)

### <a name="adding-a-polyline"></a>Como adicionar uma polilinha

As polilinhas são usadas para representar uma linha ou um caminho no mapa. Os exemplos a seguir mostram como criar uma polilinha tracejada no mapa.

**Antes: Google Maps**

No Google Maps, a classe Polyline usa um conjunto de opções. Uma matriz de coordenadas é passada na opção `path` da polilinha.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Define a symbol using SVG path notation, with an opacity of 1.
var lineSymbol = {
    path: 'M 0,-1 0,1',
    strokeOpacity: 1,
    scale: 4
};

//Create the polyline.
var line = new google.maps.Polyline({
    path: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1)
    ],
    strokeColor: 'red',
    strokeOpacity: 0,
    strokeWeight: 4,
    icons: [{
        icon: lineSymbol,
        offset: '0',
        repeat: '20px'
    }]
});

//Add the polyline to the map.
line.setMap(map);
```

<center>

![Polilinha do Google Maps](media/migrate-google-maps-web-app/google-maps-polyline.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, as polilinhas são chamadas de objetos LineString ou MultiLineString. Esses objetos podem ser adicionados a uma fonte de dados e renderizados usando uma camada de linha.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a line and add it to the data source.
datasource.add(new atlas.data.LineString([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5]
]));

//Add a layer for rendering line data.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 4,
    strokeDashArray: [3, 3]
}));
```

<center>

![Polilinha do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-polyline.png)</center>

**Recursos adicionais:**

- [Adicionar linhas ao mapa](map-add-line-layer.md)
- [Opções da camada de Linhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="adding-a-polygon"></a>Como adicionar um polígono

Os polígonos são usados para representar uma área no mapa. O Azure Mapas e o Google Maps fornecem suporte muito semelhante para polígonos. Os exemplos a seguir mostram como criar um polígono que forma um triângulo com base na coordenada central do mapa.

**Antes: Google Maps**

No Google Maps, a classe Polygon usa um conjunto de opções. Uma matriz de coordenadas é passada na opção `paths` do polígono.

```javascript
//Get the center of the map.
var center = map.getCenter();

//Create a polygon.
var polygon = new google.maps.Polygon({
    paths: [
        center,
        new google.maps.LatLng(center.lat() - 0.5, center.lng() - 1),
        new google.maps.LatLng(center.lat() - 0.5, center.lng() + 1),
        center
    ],
    strokeColor: 'red',
    strokeWeight: 2,
    fillColor: 'rgba(0, 255, 0, 0.5)'
});

//Add the polygon to the map
polygon.setMap(map);
```

<center>

![Polígono do Google Maps](media/migrate-google-maps-web-app/google-maps-polygon.png)</center>

**Depois: Azure Mapas**

Nos Azure Mapas, os objetos Polygon e MultiPolygon podem ser adicionados a uma fonte de dados e renderizados no mapa usando camadas. A área de um polígono pode ser renderizada em uma camada do polígono. O contorno de um polígono pode ser renderizado usando uma camada de linhas.

```javascript
//Get the center of the map.
var center = map.getCamera().center;

//Create a data source and add it to the map.
datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Create a polygon and add it to the data source.
datasource.add(new atlas.data.Polygon([
    center,
    [center[0] - 1, center[1] - 0.5],
    [center[0] + 1, center[1] - 0.5],
    center
]));

//Add a polygon layer for rendering the polygon area.
map.layers.add(new atlas.layer.PolygonLayer(datasource, null, {
    fillColor: 'rgba(0, 255, 0, 0.5)'
}));

//Add a line layer for rendering the polygon outline.
map.layers.add(new atlas.layer.LineLayer(datasource, null, {
    strokeColor: 'red',
    strokeWidth: 2
}));
```

<center>

![Polígono do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-polygon.png)</center>

**Recursos adicionais:**

- [Adicionar um polígono a um mapa](map-add-shape.md)
- [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)
- [Opções da camada de polígono](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)
- [Opções da camada de Linhas](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="display-an-info-window"></a>Exibir uma Janela de Informações

Informações adicionais para uma entidade podem ser exibidas no mapa como uma classe `google.maps.InfoWindow` no Google Maps. No Azure Mapas, isso pode ser feito usando a classe `atlas.Popup`. Os exemplos a seguir adicionam um marcador ao mapa e, quando o usuário clica nele, é exibida uma janela de informações/pop-up.

**Antes: Google Maps**

Com o Google Maps, uma janela de informações é criada usando o construtor `google.maps.InfoWindow`.

```javascript
//Add a marker in which to display an infowindow for.
var marker = new google.maps.Marker({
    position: new google.maps.LatLng(47.6, -122.33),
    map: map
});

//Create an infowindow.
var infowindow = new google.maps.InfoWindow({
    content: '<div style="padding:5px"><b>Hello World!</b></div>'
});

//Add a click event to the marker to open the infowindow.
marker.addListener('click', function () {
    infowindow.open(map, marker);
});
```

<center>

![Pop-up do Google Maps](media/migrate-google-maps-web-app/google-maps-popup.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, um pop-up pode ser usado para exibir informações adicionais para uma localização. Um objeto `string` ou `HTMLElement` HTML pode ser passado para a opção `content` do pop-up. Os pop-ups podem ser exibidos independentemente de qualquer forma, se desejado, portanto, exigem que um valor de `position` seja especificado. Para exibir um pop-up, chame o método `open` e passe o `map` no qual o pop-up deve ser exibido.

```javascript
//Add a marker to the map in which to display a popup for.
var marker = new atlas.HtmlMarker({
    position: [-122.33, 47.6]
});

//Add the marker to the map.
map.markers.add(marker);

//Create a popup.
var popup = new atlas.Popup({
    content: '<div style="padding:5px"><b>Hello World!</b></div>',
    position: [-122.33, 47.6],
    pixelOffset: [0, -35]
});

//Add a click event to the marker to open the popup.
map.events.add('click', marker, function () {
    //Open the popup
    popup.open(map);
});
```

<center>

![Pop-up Azure Mapas](media/migrate-google-maps-web-app/azure-maps-popup.png)</center>

> [!NOTE]
> Para fazer a mesma coisa com uma camada de símbolos, bolhas, linhas ou polígonos, basta passar a camada para o código do evento de mapas, em vez de um marcador.

**Recursos adicionais:**

- [Adicionar um pop-up](map-add-popup.md)
- [Pop-up com o Conteúdo de Mídia](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popup%20with%20Media%20Content)
- [Pop-ups em Formas](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Popups%20on%20Shapes)
- [Reutilizar pop-up com vários marcadores](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Reusing%20Popup%20with%20Multiple%20Pins)
- [Classe de pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)
- [Opções de pop-up](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popupoptions?view=azure-iot-typescript-latest)

### <a name="import-a-geojson-file"></a>Importar um arquivo GeoJSON

O Google Maps é compatível com carregar e definir dinamicamente o estilo de dados GeoJSON usando a classe `google.maps.Data`. A funcionalidade dessa classe está muito mais alinhada com o estilo controlado por dados do Azure Mapas. Uma das principais diferenças é que, com o Google Maps, você especifica uma função de retorno de chamada e a lógica de negócios para definir o estilo de cada recurso processado individualmente no thread da IU. No Azure Mapas, as camadas são compatíveis com a especificação de expressões controladas por dados como opções de estilo. Essas expressões são processadas no momento da renderização em um thread separado e proporcionam maior desempenho de renderização, além de permitirem o processamento mais rápido de conjuntos de dados maiores.

Os exemplos a seguir carregam um feed GeoJSON de todos os terremotos nos últimos sete dias do USGS e os renderiza como círculos dimensionados no mapa. A cor e a escala de cada círculo baseiam-se na magnitude de cada terremoto armazenado na propriedade `"mag"` de cada recurso no conjunto de dados. Se a magnitude for maior ou igual a cinco, o círculo será vermelho; se for maior ou igual a três, mas menor que cinco, o círculo será laranja; se for menor que três, o círculo será verde. O raio de cada círculo será o exponencial da magnitude multiplicado por 0,1.

**Antes: Google Maps**

No Google Maps, uma função de retorno de chamada pode ser especificada no método `map.data.setStyle`, que será usado para aplicar a lógica de negócios a cada recurso carregado do feed do GeoJSON usando o método `map.data.loadGeoJson`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Define a callback to style each feature.
            map.data.setStyle(function (feature) {

                //Extract the 'mag' property from the feature.
                var mag = parseFloat(feature.getProperty('mag'));

                //Set the color value to 'green' by default.
                var color = 'green';

                //If the mag value is greater than 5, set the color to 'red'.
                if (mag >= 5) {
                    color = 'red';
                }
                //If the mag value is greater than 3, set the color to 'orange'.
                else if (mag >= 3) {
                    color = 'orange';
                }

                return /** @type {google.maps.Data.StyleOptions} */({
                    icon: {
                        path: google.maps.SymbolPath.CIRCLE,

                        //Scale the radius based on an exponential of the 'mag' value.
                        scale: Math.exp(mag) * 0.1,
                        fillColor: color,
                        fillOpacity: 0.75,
                        strokeWeight: 2,
                        strokeColor: 'white'
                    }
                });
            });

            //Load the data feed.
            map.data.loadGeoJson(earthquakeFeed);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![GeoJSON do Google Maps](media/migrate-google-maps-web-app/google-maps-geojson.png)</center>

**Depois: Azure Mapas**

O GeoJSON é o tipo de dados base no Azure Mapas e pode ser facilmente importado para uma fonte de dados usando o método `datasource.importFromUrl`. Uma camada de bolhas fornece funcionalidade para renderizar círculos dimensionados com base nas propriedades dos recursos em uma fonte de dados. Em vez de ter uma função de retorno de chamada, a lógica de negócios é convertida em uma expressão e passada para as opções de estilo. As expressões definem como a lógica de negócios funciona para que possa ser passada para outro thread e avaliada em relação aos dados do recurso. Várias fontes e camadas de dados podem ser adicionadas ao Azure Mapas, cada uma com lógica de negócios diferente, permitindo que vários conjuntos de dados sejam renderizados no mapa de maneiras diferentes.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as scaled circles.
                map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
                    //Make the circles semi-transparent.
                    opacity: 0.75,

                    color: [
                        'case',

                        //If the mag value is greater than 5, return 'red'.
                        ['>=', ['get', 'mag'], 5],
                        'red',

                        //If the mag value is greater than 3, return 'orange'.
                        ['>=', ['get', 'mag'], 3],
                        'orange',

                        //Return 'green' as a fallback.
                        'green'
                    ],

                    //Scale the radius based on an exponential of the 'mag' value.
                    radius: ['*', ['^', ['e'], ['get', 'mag']], 0.1]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![GeoJSON do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-geojson.png)</center>

**Recursos adicionais:**

- [Adicionar uma camada de Símbolo](map-add-pin.md)
- [Adicionar uma camada de Bolhas](map-add-bubble-layer.md)
- [Dados de ponto de cluster](clustering-point-data-web-sdk.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="marker-clustering"></a>Clustering de marcadores

Ao visualizar muitos pontos de dados no mapa, os pontos se sobrepõem, o mapa fica confuso e torna-se difícil ver e usar. O clustering de dados de ponto pode ser usado para aprimorar essa experiência do usuário e também o desempenho. O clustering de dados de ponto são o processo de combinar dados de ponto que estão próximos uns dos outros e os representar no mapa como um ponto de dados clusterizado individual. À medida que o usuário aplica zoom ao mapa, os clusters se dividem em seus pontos de dados individuais.

Os exemplos a seguir carregam um feed GeoJSON de dados de terremoto da última semana e os adiciona ao mapa. Os clusters são renderizados como círculos em escala e coloridos, dependendo do número de pontos que eles contêm.

> [!NOTE]
> São usados vários algoritmos diferentes o clustering de marcador. O Google Maps e o Azure Mapas usam algoritmos ligeiramente diferentes. Dessa forma, às vezes a distribuição de ponto nos clusters pode variar.

**Antes: Google Maps**

No Google Maps, os marcadores podem ser clusterizados carregando a biblioteca MarkerClusterer. Os ícones de cluster são limitados a imagens que têm os números de um a cinco como seu nome e são hospedados no mesmo diretório.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2
            });

            //Download the GeoJSON data.
            fetch(earthquakeFeed)
                .then(function (response) {
                    return response.json();
                }).then(function (data) {
                    //Loop through the GeoJSON data and create a marker for each data point.
                    var markers = [];

                    for (var i = 0; i < data.features.length; i++) {

                        markers.push(new google.maps.Marker({
                            position: new google.maps.LatLng(data.features[i].geometry.coordinates[1], data.features[i].geometry.coordinates[0])
                        }));
                    }

                    //Create a marker clusterer instance and tell it where to find the cluster icons.
                    var markerCluster = new MarkerClusterer(map, markers,
                        { imagePath: 'https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/m' });
                });
        }
    </script>

    <!-- Load the marker cluster library. -->
    <script src="https://developers.google.com/maps/documentation/javascript/examples/markerclusterer/markerclusterer.js"></script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Clustering do Google Maps](media/migrate-google-maps-web-app/google-maps-clustering.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, os dados são adicionados e gerenciados por uma fonte de dados. As camadas conectam-se a fontes de dados e renderizam os dados nelas. A classe `DataSource` no Azure Mapas fornece várias opções de clustering.

- `cluster` – informa a fonte de dados para os dados do ponto de cluster.
- `clusterRadius` – o raio em pixels para os pontos de cluster juntos.
- `clusterMaxZoom` – o nível de zoom máximo no qual o clustering ocorre. Se você ampliar mais do que isso, todos os pontos serão renderizados como símbolos.
- `clusterProperties` – define as propriedades personalizadas que são calculadas usando expressões em todos os pontos de cada cluster e adicionadas às propriedades de cada ponto de cluster.

Quando o clustering estiver habilitado, a fonte de dados enviará pontos de dados clusterizados e não clusterizados para camadas para renderização. A fonte de dados é capaz de clusterizar centenas de milhares de pontos de dados. Um ponto de dados clusterizado tem as seguintes propriedades:

| Nome da propriedade             | Type    | Descrição   |
|---------------------------|---------|---------------|
| `cluster`                 | booleano | Indica se o recurso representa um cluster. |
| `cluster_id`              | string  | Uma ID exclusiva para o cluster que pode ser usada com os métodos DataSource `getClusterExpansionZoom`, `getClusterChildren` e `getClusterLeaves`. |
| `point_count`             | número  | O número de pontos que o cluster contém.  |
| `point_count_abbreviated` | string  | Uma cadeia de caracteres que abrevia o valor de `point_count` se for longo. (por exemplo, 4.000 torna-se 4K)  |

A classe `DataSource` tem a seguinte função auxiliar para acessar informações adicionais sobre um cluster usando o `cluster_id`.

| Método | Tipo de retorno | Descrição |
|--------|-------------|-------------|
| `getClusterChildren(clusterId: number)` | Promessa&lt;Matriz&lt;Recurso&lt;Geometria, qualquer&gt; \| Forma&gt;&gt; | Recupera os filhos do cluster fornecido no próximo nível de zoom. Esses filhos podem ser uma combinação de formas e subclusters. Os subclusters serão recursos com propriedades correspondentes a ClusteredProperties. |
| `getClusterExpansionZoom(clusterId: number)` | Promise&lt;number&gt; | Calcula um nível de zoom no qual o cluster começará a se expandir ou separar. |
| `getClusterLeaves(clusterId: number, limit: number, offset: number)` | Promessa&lt;Matriz&lt;Recurso&lt;Geometria, qualquer&gt; \| Forma&gt;&gt; | Recupera todos os pontos em um cluster. Defina o `limit` para retornar um subconjunto dos pontos e use `offset` para paginar os pontos. |

Ao renderizar dados clusterizados no mapa, geralmente é mais fácil usar duas ou mais camadas. O exemplo a seguir usa três camadas, uma camada de bolhas para desenhar círculos coloridos em escala com base no tamanho dos clusters, uma camada de símbolos para renderizar o tamanho do cluster como texto e uma segunda camada de símbolos para renderizar os pontos não clusterizados. Há muitas outras maneiras de renderizar dados clusterizados no Azure Mapas realçadas na documentação [Dados do ponto de cluster](clustering-point-data-web-sdk.md).

Os dados GeoJSON podem ser importados diretamente no Azure Mapas usando a função `importDataFromUrl` na classe `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map, datasource;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_week.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource(null, {
                    //Tell the data source to cluster point data.
                    cluster: true
                });
                map.sources.add(datasource);

                //Create layers for rendering clusters, their counts and unclustered points and add the layers to the map.
                map.layers.add([
                    //Create a bubble layer for rendering clustered data points.
                    new atlas.layer.BubbleLayer(datasource, null, {
                        //Scale the size of the clustered bubble based on the number of points inthe cluster.
                        radius: [
                            'step',
                            ['get', 'point_count'],
                            20,         //Default of 20 pixel radius.
                            100, 30,    //If point_count >= 100, radius is 30 pixels.
                            750, 40     //If point_count >= 750, radius is 40 pixels.
                        ],

                        //Change the color of the cluster based on the value on the point_cluster property of the cluster.
                        color: [
                            'step',
                            ['get', 'point_count'],
                            'lime',            //Default to lime green. 
                            100, 'yellow',     //If the point_count >= 100, color is yellow.
                            750, 'red'         //If the point_count >= 750, color is red.
                        ],
                        strokeWidth: 0,
                        filter: ['has', 'point_count'] //Only rendered data points which have a point_count property, which clusters do.
                    }),

                    //Create a symbol layer to render the count of locations in a cluster.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        iconOptions: {
                            image: 'none' //Hide the icon image.
                        },
                        textOptions: {
                            textField: ['get', 'point_count_abbreviated'],
                            offset: [0, 0.4]
                        }
                    }),

                    //Create a layer to render the individual locations.
                    new atlas.layer.SymbolLayer(datasource, null, {
                        filter: ['!', ['has', 'point_count']] //Filter out clustered points from this layer.
                    })
                ]);

                //Retrieve a GeoJSON data set and add it to the data source. 
                datasource.importDataFromUrl(earthquakeFeed);
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Clustering do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-clustering.png)</center>

**Recursos adicionais:**

- [Adicionar uma camada de Símbolo](map-add-pin.md)
- [Adicionar uma camada de Bolhas](map-add-bubble-layer.md)
- [Dados de ponto de cluster](clustering-point-data-web-sdk.md)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="add-a-heat-map"></a>Adicionar um mapa de calor

Mapas de calor, também conhecidos como ponto de mapas de densidade, são um tipo de visualização de dados usado para representar a densidade de dados usando um intervalo de cores. Geralmente são usados para mostrar os "pontos de acesso" dos dados em um mapa e são uma ótima maneira de renderizar grandes conjuntos de dados de ponto.

Os exemplos a seguir carregam um feed GeoJSON de todos os terremotos no último mês do USGS e os renderiza como um mapa de calor ponderado em que a propriedade `"mag"` é usada como o peso.

**Antes: Google Maps**

No Google Maps, para criar um mapa de calor, a biblioteca de "visualização" precisa ser carregada adicionando `&libraries=visualization` à URL do script da API. A camada do mapa de calor no Google Maps não é diretamente compatível com os dados GeoJSON; em vez disso, os dados precisam primeiro ser baixados e convertidos em uma matriz de pontos de dados ponderados.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {

            var map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(20, -160),
                zoom: 2,
                mapTypeId: 'satellite'
            });

            //Download the GeoJSON data.
            fetch(url).then(function (response) {
                return response.json();
            }).then(function (res) {
                var points = getDataPoints(res);

                var heatmap = new google.maps.visualization.HeatmapLayer({
                    data: points
                });
                heatmap.setMap(map);
            });
        }

        function getDataPoints(geojson, weightProp) {
            var points = [];

            for (var i = 0; i < geojson.features.length; i++) {
                var f = geojson.features[i];

                if (f.geometry.type === 'Point') {
                    points.push({
                        location: new google.maps.LatLng(f.geometry.coordinates[1], f.geometry.coordinates[0]),
                        weight: f.properties[weightProp]
                    });
                }
            }

            return points;
        } 
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]&libraries=visualization" async defer></script>
</head>
<body>
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mapa de calor do Google Maps](media/migrate-google-maps-web-app/google-maps-heatmap.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, carregue os dados GeoJSON em uma fonte de dados e conecte a fonte de dados a uma camada do mapa de calor. A propriedade que será usada para o peso pode ser passada para a opção `weight` usando uma expressão. Os dados GeoJSON podem ser importados diretamente no Azure Mapas usando a função `importDataFromUrl` na classe `DataSource`.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;
        var earthquakeFeed = 'https://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/all_month.geojson';

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-160, 20],
                zoom: 1,
                style: 'satellite',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Load the earthquake data.
                datasource.importDataFromUrl(earthquakeFeed);

                //Create a layer to render the data points as a heat map.
                map.layers.add(new atlas.layer.HeatMapLayer(datasource, null, {
                    weight: ['get', 'mag'],
                    intensity: 0.005,
                    opacity: 0.65,
                    radius: 10
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Mapa de calor do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-heatmap.png)</center>

**Recursos adicionais:**

- [Adicionar uma camada do mapa de calor](map-add-heat-map-layer.md)
- [Classe da camada do mapa de calor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.heatmaplayer?view=azure-iot-typescript-latest)
- [Opções da camada do mapa de calor](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions?view=azure-iot-typescript-latest)
- [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)

### <a name="overlay-a-tile-layer"></a>Sobrepor uma camada de peça

As camadas de peça, também conhecidas como sobreposições de Imagem no Google Maps, permitem que você sobreponha imagens grandes que foram divididas em imagens com peças menores, que se alinham com o sistema de mosaico de mapas. Essa é uma maneira comum de sobrepor imagens grandes ou conjuntos de dados muito grandes.

Os exemplos a seguir sobrepõem uma camada de peça de radar meteorológico da Iowa Environmental Mesonet of Iowa State University.

**Antes: Google Maps**

No Google Maps, camadas de bloco podem ser criadas usando a classe `google.maps.ImageMapType`.

```javascript
map.overlayMapTypes.insertAt(0, new google.maps.ImageMapType({
    getTileUrl: function (coord, zoom) {
        return "https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/" + zoom + "/" + coord.x + "/" + coord.y;
    },
    tileSize: new google.maps.Size(256, 256),
    opacity: 0.8
}));
```

<center>

![Camada de peças do Google Maps](media/migrate-google-maps-web-app/google-maps-tile-layer.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, uma camada de peças pode ser adicionada ao mapa praticamente da mesma forma que qualquer outra camada. Uma URL formatada que tem os espaços reservados em x, y e zoom (`{x}`, `{y}` e `{z}`, respectivamente) é usada para informar à camada na qual acessar as peças. As camadas de bloco no Azure Mapas também são compatíveis com os espaços reservados `{quadkey}`, `{bbox-epsg-3857}` e `{subdomain}`.

> [!TIP]
> No Azure Mapas, as camadas podem ser facilmente renderizadas sob outras camadas, incluindo as camadas do mapa base. Geralmente, é desejável renderizar camadas de peça abaixo dos rótulos de mapa para que elas sejam fáceis de ler. O método `map.layers.add` usa um segundo parâmetro que é a ID da camada na qual inserir a nova camada abaixo. Para inserir uma camada de peça abaixo dos rótulos de mapa, é possível usar o seguinte código: `map.layers.add(myTileLayer, "labels");`

```javascript
//Create a tile layer and add it to the map below the label layer.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

<center>

![Camada de peça do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-tile-layer.png)</center>

> [!TIP]
> As solicitações de bloco podem ser capturadas usando a opção `transformRequest` do mapa. Isso permitirá que você modifique ou adicione cabeçalhos à solicitação, se desejar.

**Recursos adicionais:**

- [Adicionar camadas de bloco](map-add-tile-layer.md)
- [Classe da camada do bloco](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)
- [Opções de camada de bloco](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest)

### <a name="show-traffic"></a>Mostrar o tráfego

Os dados de tráfego podem ser sobrepostos ao Azure Mapas e ao Google Maps.

**Antes: Google Maps**

No Google Maps, os dados de tráfego podem ser sobrepostos ao mapa usando a camada de tráfego.

```javascript
var trafficLayer = new google.maps.TrafficLayer();
trafficLayer.setMap(map);
```

<center>

![Tráfego do Google Maps](media/migrate-google-maps-web-app/google-maps-traffic.png)</center>

**Depois: Azure Mapas**

O Azure Mapas fornece várias opções diferentes para exibir o tráfego. Incidentes de tráfego, como bloqueios e acidentes na estrada, podem ser exibidos como ícones no mapa. O fluxo de tráfego, como estradas codificadas por cor, pode ser sobreposto ao mapa e as cores podem ser modificadas para se basearem na relação entre o limite de velocidade postado e o atraso, seja ele o normal esperado ou o atraso absoluto. Os dados de incidentes no Azure Mapas são atualizados a cada minuto e os dados de fluxo são atualizados a cada dois minutos.

```javascript
map.setTraffic({
    incidents: true,
    flow: 'relative'
});
```

<center>

![Tráfego do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-traffic.png)</center>

Se você clicar em um dos ícones de tráfego no Azure Mapas, informações adicionais serão exibidas em um pop-up.

<center>

![Incidente de trânsito do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-traffic-incident.png)</center>

**Recursos adicionais:**

- [Mostrar tráfego no mapa](map-show-traffic.md)
- [Opções de sobreposição de tráfego](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Traffic%20Overlay%20Options)

### <a name="add-a-ground-overlay"></a>Adicionar uma sobreposição de piso

Os mapas do Azure e do Google são compatíveis com a sobreposição de imagens com referência geográfica no mapa para que sejam movidas e dimensionadas conforme você faz panorâmica e aplica zoom no mapa. No Google Maps, isso é conhecido como sobreposições de piso, enquanto no Azure Mapas, é chamado de camadas de imagem. Esse recurso é ótimo para criar plantas baixas, sobrepor mapas antigos ou imagens de um drone.

**Antes: Google Maps**

Ao criar uma sobreposição de piso no Google Maps, você precisa especificar a URL para a imagem a ser sobreposta e uma caixa delimitadora à qual associar a imagem no mapa. Este exemplo sobrepõe uma imagem de mapa de [Newark, Nova Jersey, de 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) no mapa.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <script type='text/javascript'>
        var map, historicalOverlay;

        function initMap() {
            map = new google.maps.Map(document.getElementById('myMap'), {
                center: new google.maps.LatLng(40.740, -74.18),
                zoom: 12
            });

            var imageBounds = {
                north: 40.773941,
                south: 40.712216,
                east: -74.12544,
                west: -74.22655
            };

            historicalOverlay = new google.maps.GroundOverlay(
                'https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg',
                imageBounds);
            historicalOverlay.setMap(map);
        }
    </script>

    <!-- Google Maps Script Reference -->
    <script src="https://maps.googleapis.com/maps/api/js?callback=initMap&key=[Your Google Maps Key]" async defer></script>
</head>
<body>
    <div id="myMap" style="position:relative;width:600px;height:400px;"></div>
</body>
</html>
```

Executar esse código em um navegador exibirá um mapa semelhante à imagem a seguir:

<center>

![Sobreposição de imagem do Google Maps](media/migrate-google-maps-web-app/google-maps-image-overlay.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, as imagens com referência geográfica podem ser sobrepostas usando a classe `atlas.layer.ImageLayer`. Essa classe requer uma URL para uma imagem e um conjunto de coordenadas para os quatro cantos da imagem. A imagem deve ser hospedada no mesmo domínio ou ter CORs habilitados.

> [!TIP]
> Se você tiver apenas informações de norte, sul, leste, oeste e de rotação, e não coordenadas para cada canto da imagem, poderá usar o método [`atlas.layer.ImageLayer.getCoordinatesFromEdges`](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest#getcoordinatesfromedges-number--number--number--number--number-) estático.

```html
<!DOCTYPE html>
<html>
<head>
    <title></title>
    <meta charset="utf-8" />
    <meta http-equiv="x-ua-compatible" content="IE=Edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no" />

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

    <script type='text/javascript'>
        var map;

        function initMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                center: [-74.18, 40.740],
                zoom: 12,

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function () {

                //Create an image layer and add it to the map.
                map.layers.add(new atlas.layer.ImageLayer({
                    url: 'newark_nj_1922.jpg',
                    coordinates: [
                        [-74.22655, 40.773941], //Top Left Corner
                        [-74.12544, 40.773941], //Top Right Corner
                        [-74.12544, 40.712216], //Bottom Right Corner
                        [-74.22655, 40.712216]  //Bottom Left Corner
                    ]
                }));
            });
        }
    </script>
</head>
<body onload="initMap()">
    <div id='myMap' style='position:relative;width:600px;height:400px;'></div>
</body>
</html>
```

<center>

![Sobreposição de imagem do Azure Mapas](media/migrate-google-maps-web-app/azure-maps-image-overlay.png)</center>

**Recursos adicionais:**

- [Sobrepor uma imagem](map-add-image-layer.md)
- [Classe da camada de imagem](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)

## <a name="additional-code-samples"></a>Exemplos de código adicionais

A seguir estão alguns exemplos de código adicionais relacionados à migração do Google Maps:

- [Ferramentas de desenho](map-add-drawing-toolbar.md)
- [Limitar o Mapa a Panorâmica de Dois Dedos](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Map%20to%20Two%20Finger%20Panning)
- [Limitar zoom da roda de rolagem](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Limit%20Scroll%20Wheel%20Zoom)
- [Criar um Controle de Tela Inteira](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Create%20a%20Fullscreen%20Control)

**Serviços:**

- [Como usar o módulo de serviços do Azure Mapas](how-to-use-services-module.md)
- [Pesquisar pontos de interesse](map-search-location.md)
- [Obter informações de uma coordenada (inverter geocódigo)](map-get-information-from-coordinate.md)
- [Mostrar trajetos de A para B](map-route.md)
- [Pesquisar a sugestão automática com a interface do usuário do JQuery](https://azuremapscodesamples.azurewebsites.net/index.html?sample=Search%20Autosuggest%20and%20JQuery%20UI)

## <a name="google-maps-v3-to-azure-maps-web-sdk-class-mapping"></a>Mapeamento de classe do SDK da Web do Google Maps V3 para Azure Mapas

O apêndice a seguir fornece um mapeamento de referência cruzada das classes usadas com mais frequência no Google Maps V3 para seus equivalentes do SDK da Web do Azure Mapas.

### <a name="core-classes"></a>Classes de núcleo

| Google Maps   | Mapas do Azure  |
|---------------|-------------|
| `google.maps.Map` | [atlas.Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindow` | [atlas.Popup](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.popup?view=azure-iot-typescript-latest)  |
| `google.maps.InfoWindowOptions` | [atlas.PopupOptions](https://docs.microsoft.com/) |
| `google.maps.LatLng`  | [atlas.data.Position](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.position?view=azure-iot-typescript-latest)  |
| `google.maps.LatLngBounds` | [atlas.data.BoundingBox](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.boundingbox?view=azure-iot-typescript-latest) |
| `google.maps.MapOptions`  | [atlas.CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraoptions?view=azure-iot-typescript-latest)<br/>[atlas.CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.cameraboundsoptions?view=azure-iot-typescript-latest)<br/>[atlas.ServiceOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.serviceoptions?view=azure-iot-typescript-latest)<br/>[atlas.StyleOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.styleoptions?view=azure-iot-typescript-latest)<br/>[atlas.UserInteractionOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.userinteractionoptions?view=azure-iot-typescript-latest) |
| `google.maps.Point`  | [atlas.Pixel](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.pixel?view=azure-iot-typescript-latest)   |

## <a name="overlay-classes"></a>Classes de Sobreposição

| Google Maps  | Mapas do Azure  |
|--------------|-------------|
| `google.maps.Marker` | [atlas.HtmlMarker](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarker?view=azure-iot-typescript-latest)<br/>[atlas.data.Point](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.point?view=azure-iot-typescript-latest)  |
| `google.maps.MarkerOptions`  | [atlas.HtmlMarkerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.htmlmarkeroptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.SymbolLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.symbollayer?view=azure-iot-typescript-latest)<br/>[atlas.SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions?view=azure-iot-typescript-latest)<br/>[atlas.IconOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.iconoptions?view=azure-iot-typescript-latest)<br/>[atlas.TextOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.textoptions?view=azure-iot-typescript-latest)<br/>[atlas.layer.BubbleLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.bubblelayer?view=azure-iot-typescript-latest)<br/>[atlas.BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.Polygon`  | [atlas.data.Polygon](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.polygon?view=azure-iot-typescript-latest)               |
| `google.maps.PolygonOptions` |[atlas.layer.PolygonLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.polygonlayer?view=azure-iot-typescript-latest)<br/> [atlas.PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions?view=azure-iot-typescript-latest)<br/> [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-iot-typescript-latest)<br/> [atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-iot-typescript-latest)|
| `google.maps.Polyline` | [atlas.data.LineString](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.data.linestring?view=azure-iot-typescript-latest)         |
| `google.maps.PolylineOptions` | [atlas.layer.LineLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.linelayer?view=azure-maps-typescript-latest)<br/>[atlas.LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions?view=azure-maps-typescript-latest) |
| `google.maps.Circle`  | Confira [Adicionar um círculo ao mapa](map-add-shape.md#add-a-circle-to-the-map)                                     |
| `google.maps.ImageMapType`  | [atlas.TileLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.tilelayer?view=azure-iot-typescript-latest)         |
| `google.maps.ImageMapTypeOptions` | [atlas.TileLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.tilelayeroptions?view=azure-iot-typescript-latest) |
| `google.maps.GroundOverlay`  | [atlas.layer.ImageLayer](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.layer.imagelayer?view=azure-iot-typescript-latest)<br/>[atlas.ImageLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.imagelayeroptions?view=azure-iot-typescript-latest) |

## <a name="service-classes"></a>Classes de Serviço

O SDK da Web do Azure Mapas inclui um [módulo de serviço](how-to-use-services-module.md) que pode ser carregado separadamente. Esse módulo encapsula os serviços REST do Azure Mapas com uma API Web e pode ser usado em aplicativos JavaScript, TypeScript e Node.js.

| Google Maps | Mapas do Azure  |
|-------------|-------------|
| `google.maps.Geocoder` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |
| `google.maps.GeocoderRequest`  | [atlas.SearchAddressOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressRevrseOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreverseoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressReverseCrossStreetOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressreversecrossstreetoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAddressStructuredOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchaddressstructuredoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchAlongRouteOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchalongrouteoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchFuzzyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchfuzzyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchInsideGeometryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchinsidegeometryoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchNearbyOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchnearbyoptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOIOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoioptions?view=azure-iot-typescript-latest)<br/>[atlas.SearchPOICategoryOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchpoicategoryoptions?view=azure-iot-typescript-latest) |
| `google.maps.DirectionsService`  | [atlas.service.RouteUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.routeurl?view=azure-iot-typescript-latest)  |
| `google.maps.DirectionsRequest`  | [atlas.CalculateRouteDirectionsOptions](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.calculateroutedirectionsoptions?view=azure-iot-typescript-latest) |
| `google.maps.places.PlacesService` | [atlas.service.SearchUrl](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.searchurl?view=azure-iot-typescript-latest)  |

## <a name="libraries"></a>Bibliotecas

As bibliotecas adicionam funcionalidade extra ao mapa. Muitas delas estão no SDK principal do Azure Mapas. Aqui estão algumas classes equivalentes a serem usadas no lugar dessas bibliotecas do Google Maps

| Google Maps           | Mapas do Azure   |
|-----------------------|--------------|
| Biblioteca de desenhos       | [Módulo de ferramentas de desenho](set-drawing-options.md) |
| Biblioteca de geometrias      | [atlas.math](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.math?view=azure-iot-typescript-latest)   |
| Biblioteca de visualizações | [Camada do mapa de calor](map-add-heat-map-layer.md) |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o SDK da Web do Azure Mapas.

> [!div class="nextstepaction"]
> [Como usar o Controle de Mapeamento](how-to-use-map-control.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de serviços](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de ferramentas de desenho](set-drawing-options.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)

