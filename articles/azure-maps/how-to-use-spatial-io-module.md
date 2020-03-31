---
title: Como usar o módulo Espacial IO do Azure Maps | Mapas do Microsoft Azure
description: Aprenda a usar o módulo Espacial IO fornecido pelo Web SDK do Azure Maps. Este módulo fornece recursos robustos para facilitar a integração de dados espaciais aos desenvolvedores com o web sdk do Azure Maps.
author: philmea
ms.author: philmea
ms.date: 02/28/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 5bcfeebc1fcb96cfdf6ea802293eb4027f339815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335207"
---
# <a name="how-to-use-the-azure-maps-spatial-io-module"></a>Como usar o módulo IO espacial do Azure Maps

O Azure Maps Web SDK fornece o **módulo Espacial IO**, que integra dados espaciais com o Web SDK do Azure Maps usando JavaScript ou TypeScript. Os recursos robustos neste módulo permitem que os desenvolvedores:

- [Ler e gravar arquivos de dados espaciais comuns](spatial-io-read-write-spatial-data.md). Os formatos de arquivo suportados incluem: KML, KMZ, GPX, GeoRSS, GML, GeoJSON e CSV arquivos contendo colunas com informações espaciais. Também suporta texto bem conhecido (WKT).
- [Conecte-se aos serviços do Open Geospatial Consortium (OGC) e integre-se ao Web SDK do Azure Maps. Sobrepor os Serviços de Mapa web (WMS) e o Web Map Tile Services (WMTS) como camadas no mapa](spatial-io-add-ogc-map-layer.md).
- [Consultar dados em um WfS (Web Feature Service, serviço de recursos da Web)](spatial-io-connect-wfs-service.md).
- [Sobrepor conjuntos de dados complexos que contenham informações de estilo e que sejam renderizados automaticamente usando código mínimo](spatial-io-add-simple-data-layer.md).
- [Aproveite o XML de alta velocidade e as classes de leitor de arquivos e escritores delimitados](spatial-io-core-operations.md).

Neste guia, aprenderemos como integrar e usar o módulo IO espacial em um aplicativo web.

> [!WARNING]
> Use apenas dados e serviços que são de uma fonte de sua confiança, especialmente se fizer referência a ele de outro domínio. O módulo io espacial toma medidas para minimizar o risco, no entanto, a abordagem mais segura também não permite que nenhum danagerous dados em sua aplicação para começar. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de usar o módulo IO espacial, você precisará [fazer uma conta do Azure Maps](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) e obter a chave de assinatura principal da sua [conta](https://docs.microsoft.com/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## <a name="installing-the-spatial-io-module"></a>Instalação do módulo IO espacial

Você pode carregar o módulo IO espacial do Azure Maps usando uma das duas opções:

* O CDN azure hospedado globalmente para o módulo IO espacial do Azure Maps. Para esta opção, você adiciona uma referência `<head>` ao JavaScript no elemento do arquivo HTML.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

* O código fonte [para azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) pode ser carregado localmente e, em seguida, hospedado com o seu aplicativo. Esse pacote também inclui definições de TypeScript. Para esta opção, use o seguinte comando para instalar o pacote:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Em seguida, adicione uma referência `<head>` ao JavaScript no elemento do documento HTML:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

## <a name="using-the-spatial-io-module"></a>Usando o módulo IO espacial

1. Criar um novo arquivo HTML.

2. Carregue o Web SDK do Azure Maps e inicialize o controle do mapa. Consulte o guia de controle do [mapa do Azure Maps](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control) para obter os detalhes. Uma vez que você terminar com esta etapa, seu arquivo HTML deve ficar assim:

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title></title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <script type='text/javascript'>

            var map;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    // Write your code here to make sure it runs once the map resources are ready

                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

2. Carregue o módulo IO espacial do Azure Maps. Para este exercício, use o CDN para o módulo IO espacial Do Azure Maps. Adicione a referência `<head>` abaixo ao elemento do seu arquivo HTML:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

3. Inicialize `datasource`a e adicione a fonte de dados ao mapa. Inicialize `layer`a e adicione a fonte de dados à camada do mapa. Em seguida, renderize tanto a fonte de dados quanto a camada. Antes de descer para ver o código completo na próxima etapa, pense nos melhores lugares para colocar os trechos de código de origem de dados e camada. Lembre-se que, antes de manipular o mapa, devemos esperar até que o recurso do mapa esteja pronto.

    ```javascript
    var datasource, layer;
    ```

    e

    ```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);
    
    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
    ```

4. Juntando tudo, seu código HTML deve parecer o seguinte código. Esta amostra demonstra como ler um arquivo XML de uma URL. Em seguida, carregue e exiba os dados do recurso do arquivo no mapa. 

    ```html
    <!DOCTYPE html>
    <html>

    <head>
        <title>Spatial IO Module Example</title>

        <meta charset="utf-8">

        <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
        <meta http-equiv="x-ua-compatible" content="IE=Edge">

        <!-- Ensures the web page looks good on all screen sizes. -->
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

        <!-- Add reference to the Azure Maps Spatial IO module. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

        <script type='text/javascript'>
            var map, datasource, layer;

            function GetMap() {
                //Initialize a map instance.
                map = new atlas.Map('myMap', {
                    view: 'Auto',

                    //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                    authOptions: {
                        authType: 'subscriptionKey',
                        subscriptionKey: '<Your Azure Maps Key>'
                    }
                });

                //Wait until the map resources are ready.
                map.events.add('ready', function() {

                    //Create a data source and add it to the map.
                    datasource = new atlas.source.DataSource();
                    map.sources.add(datasource);

                    //Add a simple data layer for rendering the data.
                    layer = new atlas.layer.SimpleDataLayer(datasource);
                    map.layers.add(layer);

                    //Read an XML file from a URL or pass in a raw XML string.
                    atlas.io.read('superCoolKmlFile.xml').then(r => {
                        if (r) {
                            //Add the feature data to the data source.
                            datasource.add(r);

                            //If bounding box information is known for data, set the map view to it.
                            if (r.bbox) {
                                map.setCamera({
                                    bounds: r.bbox,
                                    padding: 50
                                });
                            }
                        }
                    });
                });
            }
        </script>
    </head>

    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>

    </html>
    ```

5. Lembre-se `<Your Azure Maps Key>` de substituir com sua chave principal. Abra seu arquivo HTML e verá resultados semelhantes à seguinte imagem:

    <center>

    ![Exemplo de Dados Espaciais](./media/how-to-use-spatial-io-module/spatial-data-example.png)

    </center>

## <a name="next-steps"></a>Próximas etapas

O recurso que demonstramos aqui é apenas um dos muitos recursos disponíveis no módulo IO espacial. Leia os guias abaixo para saber como usar outras funcionalidades no módulo IO espacial:

> [!div class="nextstepaction"]
> [Adicione uma camada de dados simples](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Ler e escrever dados espaciais](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Adicione uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Conecte-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alavancar operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)

Consulte a documentação de IO espacial do Azure Maps:

> [!div class="nextstepaction"]
> [Pacote De IO espacial do Azure Maps](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/)
