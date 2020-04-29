---
title: Ler e gravar dados espaciais | Mapas do Microsoft Azure
description: Saiba como ler e gravar dados usando o módulo de e/s espacial, fornecido pelo SDK da Web do Azure Maps.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334147"
---
# <a name="read-and-write-spatial-data"></a>Ler e gravar dados espaciais

A tabela a seguir lista os formatos de arquivo espaciais com suporte para leitura e gravação de operações com o módulo de e/s espacial.

| Formato de Dados       | Ler | Gravar |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| CSV espacial       | ✓  |  ✓  |
| Texto bem conhecido   | ✓  |  ✓  |

Estas seções a seguir descrevem todas as diferentes ferramentas para leitura e gravação de dados espaciais usando o módulo de e/s espacial.

## <a name="read-spatial-data"></a>Ler dados espaciais

A `atlas.io.read` função é a função principal usada para ler formatos de dados espaciais comuns, como arquivos KML, GPX, GeoRSS, geojson e CSV com dados espaciais. Essa função também pode ler versões compactadas desses formatos, como um arquivo zip ou um arquivo KMZ. O formato de arquivo KMZ é uma versão compactada do KML que também pode incluir ativos como imagens. Como alternativa, a função Read pode assumir uma URL que aponta para um arquivo em qualquer um desses formatos. As URLs devem ser hospedadas em um ponto de extremidade habilitado para CORS ou um serviço de proxy deve ser fornecido nas opções de leitura. O serviço de proxy é usado para carregar recursos em domínios que não estão habilitados para CORS. A função Read retorna uma promessa para adicionar os ícones de imagem ao mapa e processa os dados de forma assíncrona para minimizar o impacto no thread da interface do usuário.

Ao ler um arquivo compactado, como um zip ou um KMZ, ele será descompactado e examinará o primeiro arquivo válido. Por exemplo, Doc. KML, ou um arquivo com outra extensão válida, como:. KML,. xml, geojson,. JSON,. csv,. tsv ou. txt. Em seguida, as imagens referenciadas nos arquivos KML e GeoRSS são pré-carregadas para garantir que estejam acessíveis. Dados de imagem inacessíveis podem carregar uma imagem de fallback alternativa ou serão removidos dos estilos. Imagens extraídas de arquivos KMZ serão convertidas em URIs de dados.

O resultado da função de leitura é um `SpatialDataSet` objeto. Esse objeto estende a classe de recurso geojson. Ele pode ser facilmente passado para um `DataSource` as-é para renderizar seus recursos em um mapa. O `SpatialDataSet` não só contém informações sobre recursos, mas também pode incluir sobreposições de terra KML, métricas de processamento e outros detalhes, conforme descrito na tabela a seguir.

| Nome da propriedade | Type | Descrição | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Caixa delimitadora de todos os dados no conjunto de dados. |
| `features` | `Feature[]` | Recursos geojson dentro do conjunto de dados. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Uma matriz de KML GroundOverlays. |
| `icons` | Cadeia&lt;de caracteres de registro, Cadeia de caracteres&gt; | Um conjunto de URLs de ícone. Chave = nome do ícone, valor = URL. |
| properties | any | Informações de propriedade fornecidas no nível de documento de um conjunto de dados espaciais. |
| `stats` | `SpatialDataSetStats` | Estatísticas sobre o conteúdo e o tempo de processamento de um conjunto de dados espaciais. |
| `type` | `'FeatureCollection'` | Valor de tipo geojson de somente leitura. |

## <a name="examples-of-reading-spatial-data"></a>Exemplos de leitura de dados espaciais

O código a seguir mostra como ler um conjunto de dados espaciais e renderizá-lo no mapa usando `SimpleDataLayer` a classe. O código usa um arquivo GPX apontado por uma URL.

<br/>

<iframe height='500' scrolling='no' title='Carregar dados espaciais simples' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>carregar dados espaciais simples</a> pelo Azure Maps<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

A próxima demonstração de código mostra como ler e carregar KML, ou KMZ, no mapa. KML pode conter sobreposições de aterramento, que estarão na forma de um `ImageLyaer` ou `OgcMapLayer`. Essas sobreposições devem ser adicionadas ao mapa separadamente dos recursos. Além disso, se o conjunto de dados tiver ícones personalizados, esses ícones precisarão ser carregados nos recursos de mapas antes de os recursos serem carregados.

<br/>

<iframe height='500' scrolling='no' title='Carregar KML no mapa' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>carregar KML no mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcionalmente, você pode fornecer um serviço de proxy para acessar ativos entre domínios que podem não ter o CORS habilitado. A função de leitura tentará acessar os arquivos em outro domínio usando o CORS primeiro. Após a primeira vez em que falha ao acessar qualquer recurso em outro domínio usando CORS, ele solicitará arquivos adicionais somente se um serviço de proxy tiver sido fornecido. A função Read acrescenta a URL do arquivo ao final da URL do proxy fornecida. Este trecho de código mostra como passar um serviço de proxy para a função de leitura:

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

A demonstração a seguir mostra como ler um arquivo delimitado e renderizá-lo no mapa. Nesse caso, o código usa um arquivo CSV que tem colunas de dados espaciais.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um arquivo delimitado' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Adicionar um arquivo delimitado</a> pelo mapas do Azure<a href='https://codepen.io/azuremaps'>@azuremaps</a>() em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Gravar dados espaciais

Há duas funções de gravação principais no módulo de e/s espacial. A `atlas.io.write` função gera uma cadeia de caracteres, `atlas.io.writeCompressed` enquanto a função gera um arquivo ZIP compactado. O arquivo ZIP compactado conterá um arquivo baseado em texto com os dados espaciais nele. Essas duas funções retornam uma promessa para adicionar os dados ao arquivo. E ambos podem gravar qualquer um dos seguintes dados `SpatialDataSet`:, `DataSource`, `ImageLayer`, `OgcMapLayer`, coleção de recursos, recurso, geometria ou uma matriz de qualquer combinação desses tipos de dados. Ao escrever usando qualquer uma das funções, você pode especificar o formato de arquivo desejado. Se o formato de arquivo não for especificado, os dados serão gravados como KML.

A ferramenta a seguir demonstra a maioria das opções de gravação que podem ser usadas com `atlas.io.write` a função.

<br/>

<iframe height='700' scrolling='no' title='Opções de gravação de dados espaciais' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>Opções de gravação de dados espaciais</a> da caneta<a href='https://codepen.io/azuremaps'>@azuremaps</a>pelo Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Exemplo de gravação de dados espaciais

O exemplo a seguir permite arrastar e soltar e, em seguida, carregar arquivos espaciais no mapa. Você pode exportar dados geojson do mapa e escrevê-los em um dos formatos de dados espaciais com suporte como uma cadeia de caracteres ou como um arquivo compactado.

<br/>

<iframe height='700' scrolling='no' title='Arrastar e soltar arquivos espaciais no mapa' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta, <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>arrastar e soltar arquivos espaciais em mapear</a> pelo<a href='https://codepen.io/azuremaps'>@azuremaps</a>Azure Maps () em <a href='https://codepen.io'>CodePen</a>.
</iframe>

Opcionalmente, você pode fornecer um serviço de proxy para acessar ativos entre domínios que podem não ter o CORS habilitado. Este trecho de código mostra que você pode incorporar um serviço de proxy:

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>Ler e gravar texto bem conhecido (WKT)

WKT ( [Well-Known Text](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) ) é um padrão de Open GEOSPATIAL CONSORTIUM (OGC) para representar geometrias espaciais como texto. Muitos sistemas geoespaciais dão suporte a WKT, como SQL Azure e Azure PostgreSQL usando o plug-in PostGIS. Como a maioria dos padrões de OGC, as coordenadas são formatadas como "latitude de longitude" para alinhar com a Convenção "x y". Por exemplo, um ponto em longitude-110 e Latitude 45 pode ser escrito como `POINT(-110 45)` usando o formato WKT.

O texto bem conhecido pode ser lido usando a `atlas.io.ogc.WKT.read` função e escrito usando a `atlas.io.ogc.WKT.write` função.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Exemplos de leitura e gravação de texto bem conhecido (WKT)

O código a seguir mostra como ler a cadeia de caracteres `POINT(-122.34009 47.60995)` de texto bem conhecida e renderizá-la no mapa usando uma camada de bolha.

<br/>

<iframe height='500' scrolling='no' title='Ler texto bem conhecido' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Veja a caneta <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>ler o texto bem conhecido</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código a seguir demonstra como ler e gravar texto bem conhecido de volta e para trás.

<br/>

<iframe height='700' scrolling='no' title='Ler e escrever texto bem conhecido' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>ler e escrever texto bem conhecido</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) em <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Ler e gravar GML

GML é uma especificação de arquivo XML espacial que geralmente é usada como uma extensão para outras especificações de XML. Os dados geojson podem ser gravados como XML com marcas GML `atlas.io.core.GmlWriter.write` usando a função. O XML que contém GML pode ser lido usando a `atlas.io.core.GmlReader.read` função. A função de leitura tem duas opções:

- A `isAxisOrderLonLat` opção-a ordem do eixo das coordenadas "latitude, longitude" ou "longitude, Latitude" pode variar entre os conjuntos de dados e nem sempre é bem definida. Por padrão, o leitor de GML lê os dados de coordenadas como "latitude, longitude", mas a definição dessa opção como true irá lê-lo como "longitude, Latitude".
- A `propertyTypes` opção-essa opção é uma tabela de pesquisa de valor de chave em que a chave é o nome de uma propriedade no conjunto de dados. O valor é o tipo de objeto para o qual converter o valor durante a análise. Os valores de tipo com suporte `string`são `number`: `boolean`,, `date`e. Se uma propriedade não estiver na tabela de pesquisa ou o tipo não estiver definido, a propriedade será analisada como uma cadeia de caracteres.

A `atlas.io.read` função usará como padrão `atlas.io.core.GmlReader.read` a função quando detectar que os dados de entrada são XML, mas os dados não são um dos outros formatos XML espaciais de suporte.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [funções estáticas atlas.io](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [SpatialDataSet](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [funções do Atlas. IO. OGC. WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicionar uma camada do mapa do OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Conectar-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Aproveitar as principais operações](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes de formato de dados com suporte](spatial-io-supported-data-format-details.md)
