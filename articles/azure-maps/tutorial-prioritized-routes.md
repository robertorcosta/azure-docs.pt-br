---
title: 'Tutorial: Localizar várias rotas por modo de viagem | Microsoft Azure Mapas'
description: Neste tutorial, você aprenderá a encontrar rotas para diferentes modos de viagem usando os Microsoft Azure Mapas.
author: farah-alyasari
ms.author: v-faalya
ms.date: 01/14/2020
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 628a3003cec2cc2ca58f1b133cf3236417dfa94e
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209487"
---
# <a name="tutorial-find-routes-for-different-modes-of-travel-using-azure-maps"></a>Tutorial: Encontre rotas para diferentes modos de viagem usando os Mapas do Azure

Este tutorial mostra como usar a sua conta dos Azure Mapas e o serviço Roteiros. O serviço Roteiros pode encontrar a rota para seu ponto de interesse, priorizada pelo seu meio de transporte. Você poderá exibir duas rotas diferentes no mapa, uma para carros e outra para caminhões. O serviço Roteiros leva em consideração as limitações devido à altura e ao peso do veículo ou se o veículo está transportando uma carga perigosa. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

## <a name="prerequisites"></a>Prerequisites
Antes de continuar, siga as instruções em [Criar uma conta](quick-demo-map-app.md#create-an-account-with-azure-maps) e selecione o tipo de preço S1. Siga as etapas em [Obter chave primária](quick-demo-map-app.md#get-the-primary-key-for-your-account) para obter a chave primária da sua conta. Para obter mais informações sobre a autenticação nos Azure Mapas, confira [Gerenciar a autenticação nos Azure Mapas](how-to-manage-authentication.md).

## <a name="create-a-new-map"></a>Criar um novo mapa

As etapas a seguir mostra como criar uma página HTML estática inserida com a API do Controle de Mapeamento.

1. Em seu computador local, crie um novo arquivo e nomeie-o como **MapTruckRoute.html**.
2. Adicione os seguintes componentes HTML ao arquivo:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    Observe que o cabeçalho HTML inclui os arquivos de recurso CSS e JavaScript hospedados pela biblioteca de Controle de mapa do Azure. Observe o evento `onload` no corpo da página que chamará a função `GetMap` quando o corpo da página for carregado. Essa função conterá o código JavaScript embutido para acessar as APIs do Azure Mapas.

3. Adicione o código JavaScript a seguir à função `GetMap`. Substitua a cadeia de caracteres `<Your Azure Maps Key>` pela chave primária que você copiou da sua conta dos Mapas.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    A classe `atlas.Map` fornece o controle de um mapa Web visual e interativo, além de ser um componente da API de Controle de Mapeamento do Azure.

4. Salve o arquivo e abra-o em seu navegador. Agora você tem um mapa básico que você pode desenvolver ainda mais.

   ![Exibir mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar o fluxo de tráfego

1. Adicione a exibição do fluxo de tráfego ao mapa. O evento `ready` de mapas aguarda até que os recursos de mapas sejam carregados e prontos para interagir com segurança com ele.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    No manipulador `ready` de eventos de carregamento, a configuração do fluxo de tráfego no mapa é definida como `relative`, que é a velocidade da estrada em relação ao fluxo livre. Você também pode defini-lo na velocidade `absolute` do caminho, ou `relative-delay`, o qual exibe a velocidade relativa em que difere do fluxo contínuo.

2. Salve o arquivo **MapTruckRoute.html** e atualize a página em seu navegador. Se interagir com o mapa e ampliá-lo em Los Angeles, você deverá ver as ruas com os dados de tráfego atuais.

   ![Exibir o tráfego em um mapa](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definir como a rota será renderizada

Neste tutorial, duas rotas serão calculadas e renderizadas no mapa. Uma rota usando estradas acessíveis a carros e outra usando as acessíveis a caminhões. Quando elas forem renderizadas, exibiremos um ícone de símbolo para o início e o fim da rota e linhas de cores diferentes para cada caminho de rota.

1. Após inicializar o mapa, adicione o código JavaScript a seguir no evento de carregamento `ready` de mapa.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    No manipulador de eventos `ready` dos mapas, uma fonte de dados é criada para armazenar as linhas da rota e os pontos inicial e final. Uma camada de linhas é criada e anexada à fonte de dados para definir como a linha de rota será renderizada. Expressões são usadas para recuperar a largura da linha e a cor das propriedades no recurso de linha de rota. Quando você adiciona a camada ao mapa, um segundo parâmetro com o valor de `'labels'` é passado especificando que essa camada deve ser renderizada abaixo dos rótulos do mapa. Isso garante que a linha da rota não abranja os rótulos de estrada. Uma camada de símbolo é criada e anexada à fonte de dados. Essa camada especifica como os pontos inicial e final serão renderizados. Nesse caso, as expressões foram adicionadas para recuperar a imagem de ícone e as informações de rótulo de texto das propriedades em cada objeto de ponto. 
    
2. Neste tutorial, defina o ponto inicial como uma empresa fictícia em Seattle chamada Fabrikam e o ponto de destino como um escritório da Microsoft. No manipulador `ready` de eventos do mapa, adicione o código a seguir.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    Esse código cria dois [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar os pontos de partida e chegada da rota. As propriedades `title` e `icon` são adicionadas a cada ponto.

3. Em seguida, adicione o código JavaScript abaixo para adicionar os pinos dos pontos de partida e chegada ao mapa:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    Os pontos de partida e chegada são adicionados à fonte de dados. A caixa delimitadora dos pontos de partida e chegada é calculada com a função `atlas.data.BoundingBox.fromData`. Essa caixa delimitadora é usada para definir o modo de exibição das câmeras por toda a rota usando a função `map.setCamera`. Um preenchimento é adicionado para compensar as dimensões de pixel dos ícones de símbolo.

4. Salve o arquivo e atualize seu navegador para ver os pinos exibidos em seu mapa. Agora o mapa está centralizado sobre Seattle. Você pode ver o marcador azul redondo indicando o ponto inicial e o marcador azul indicando o ponto final.

   ![Exibir o mapa com os pontos inicial e final](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Renderizar rotas priorizadas por modo de viagem

Esta seção mostra como usar a API do Serviço de Roteiros dos Mapas. A API de rotas é usada para localizar várias rotas começando em um ponto de partida específico ao ponto de chegada, de acordo com o meio de transporte. O Serviço de Roteiros fornece APIs para planejar as rotas *mais rápida*, *mais curta*, *econômica* ou *emocionante*. As APIs não apenas planejam as rotas entre duas localizações, mas também consideram as condições de tráfego atuais. 

A API de rotas também permite que os usuários planejem rotas no futuro usando o amplo banco de dados de tráfego histórico do Azure. A API pode prever as durações de rota para uma hora e um dia específicos. Para saber mais, confira [Trajeto GetRoute](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). 

Todos os blocos de código a seguir devem ser adicionados **dentro do eventListener de carregamento do mapa** para garantir que eles sejam carregados após o carregamento total do mapa.

1. Na função GetMap, adicione o código JavaScript a seguir.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   O `SubscriptionKeyCredential` cria um `SubscriptionKeyCredentialPolicy` para autenticar solicitações HTTP para Azure Mapas com a chave de assinatura. O `atlas.service.MapsURL.newPipeline()` usa a `SubscriptionKeyCredential` política e cria uma instância de [Pipeline](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). O `routeURL` representa uma URL para as operações de [rota](https://docs.microsoft.com/rest/api/maps/route) do Azure Mapas.

2. Depois de configurar as credenciais e a URL, adicione o seguinte código JavaScript para construir uma rota do ponto inicial ao final para um caminhão transportando carga com classificação USHazmatClass2 e exibir os resultados.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    O snippet de código acima consulta o serviço de roteamento do Azure Mapas por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). A linha de rota, em seguida, é extraída da coleção de recursos GeoJSON, mais precisamente da resposta que é extraída usando o método `geojson.getFeatures()`. A linha da rota é então adicionada à fonte de dados. Um índice igual a 0 garante que ela será processada antes de qualquer outra linha na fonte de dados. Isso é feito, pois o cálculo de rota de caminhão geralmente será mais lento do que um cálculo de rota de carro. Se a linha de rota de caminhão for adicionada à fonte de dados após a rota de carro, ela será renderizada acima dela. Duas propriedades são adicionadas à linha de rota de caminhão: uma cor de traço que é um belo tom de azul e uma largura de traço de 9 pixels.

3. Adicione o código JavaScript a seguir para construir uma rota para um carro e exibir os resultados.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    O snippet de código acima consulta o serviço de roteamento do Azure Mapas por meio do método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). A linha de rota, em seguida, é extraída da coleção de recursos GeoJSON, mais precisamente da resposta que é extraída usando o método `geojson.getFeatures()`. A linha da rota é então adicionada à fonte de dados. Duas propriedades são adicionadas à linha de rota de carro: uma cor de traço que é um tom de roxo e uma largura de traço de 5 pixels.  

4. Salve o arquivo **MapTruckRoute.html** e atualize seu navegador para observar o resultado. Para uma conexão bem-sucedida com APIs dos Mapas, você deverá ver um mapa semelhante ao seguinte.

    ![Rotas priorizadas com o Serviço de Roteiros do Azure](./media/tutorial-prioritized-routes/prioritized-routes.png)

    A rota de caminhão é mostrada em uma linha azul espessa e a rota de carro, em uma linha roxa fina. A rota de carro passa pelo Lago Washington pela estrada I-90, que passa por túneis em áreas residenciais. Como os túneis estão próximos às áreas residenciais, a carga de resíduos perigosos é restrita. A rota de caminhão, que especifica um tipo de carga USHazmatClass2, é instruída a usar outra rodovia.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar uma nova página da Web usando a API de controle de mapeamento
> * Visualizar o fluxo de tráfego em seu mapa
> * Criar consultas de rota que declarem o modo de viagem
> * Exibir várias rotas em seu mapa

> [!div class="nextstepaction"]
> [Exibir código-fonte completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Exibir amostra dinâmica](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

O próximo tutorial demonstra o processo de criação de um localizador de lojas simples usando o Azure Mapas.

> [!div class="nextstepaction"]
> [Criar um localizador de lojas usando o Azure Mapas](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Usar expressões de estilo controladas por dados](data-driven-style-expressions-web-sdk.md)