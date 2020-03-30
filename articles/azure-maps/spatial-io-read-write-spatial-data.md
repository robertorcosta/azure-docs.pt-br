---
title: Ler e escrever dados espaciais | Mapas do Microsoft Azure
description: Aprenda a ler e gravar dados usando o módulo Espacial IO, fornecido pelo Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4c47335689401ebce98224992c74c3396821a1dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334147"
---
# <a name="read-and-write-spatial-data"></a>Ler e escrever dados espaciais

A tabela abaixo lista os formatos de arquivo espacial suportados para operações de leitura e escrita com o módulo IO espacial.

| Formato de Dados       | Ler | Gravar |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| Georss            | ✓  |  ✓  |
| Gml               | ✓  |  ✓  |
| Gpx               | ✓  |  ✓  |
| Kml               | ✓  |  ✓  |
| Kmz               | ✓  |  ✓  |
| CSV Espacial       | ✓  |  ✓  |
| Texto bem conhecido   | ✓  |  ✓  |

Essas próximas seções descrevem todas as diferentes ferramentas para leitura e escrita de dados espaciais usando o módulo IO espacial.

## <a name="read-spatial-data"></a>Leia dados espaciais

A `atlas.io.read` função é a principal função usada para ler formatos de dados espaciais comuns como kml, GPX, GeoRSS, GeoJSON e arquivos CSV com dados espaciais. Esta função também pode ler versões compactadas desses formatos, como um arquivo zip ou um arquivo KMZ. O formato de arquivo KMZ é uma versão compactada do KML que também pode incluir ativos como imagens. Alternativamente, a função leitura pode levar em uma URL que aponta para um arquivo em qualquer um desses formatos. Os URLs devem ser hospedados em um ponto final habilitado para CORS ou um serviço proxy deve ser fornecido nas opções de leitura. O serviço proxy é usado para carregar recursos em domínios que não estão habilitados para CORS. A função leitura retorna a promessa de adicionar os ícones de imagem ao mapa e processa os dados de forma assíncrona para minimizar o impacto no segmento de IA.

Ao ler um arquivo compactado, seja como um zip ou um KMZ, ele será descompactado e digitalizado para o primeiro arquivo válido. Por exemplo, doc.kml, ou um arquivo com outra extensão válida, tais como: .kml, .xml, geojson, .json, .csv, .tsv, ou .txt. Em seguida, as imagens referenciadas em arquivos KML e GeoRSS são pré-carregadas para garantir que estejam acessíveis. Dados de imagem inacessíveis podem carregar uma imagem alternativa de recuo ou serão removidos dos estilos. As imagens extraídas dos arquivos KMZ serão convertidas em URIs de dados.

O resultado da função `SpatialDataSet` de leitura é um objeto. Este objeto estende a classe GeoJSON FeatureCollection. Ele pode ser facilmente `DataSource` passado para um como-é para renderizar suas características em um mapa. O `SpatialDataSet` não só contém informações de recurso, mas também pode incluir sobreposições de solo KML, métricas de processamento e outros detalhes conforme descrito na tabela a seguir.

| Nome da propriedade | Type | Descrição | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | Caixa delimitador a todos os dados do conjunto de dados. |
| `features` | `Feature[]` | GeoJSON apresenta dentro do conjunto de dados. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | Uma matriz de GROUNDOverlays KML. |
| `icons` | Seqüência&lt;de gravação, string&gt; | Um conjunto de URLs de ícones. Tecla = nome do ícone, Valor = URL. |
| properties | any | Informações de propriedade fornecidas no nível do documento de um conjunto de dados espaciais. |
| `stats` | `SpatialDataSetStats` | Estatísticas sobre o tempo de conteúdo e processamento de um conjunto de dados espaciais. |
| `type` | `'FeatureCollection'` | Valor do tipo GeoJSON somente leitura. |

## <a name="examples-of-reading-spatial-data"></a>Exemplos de leitura de dados espaciais

O código a seguir mostra como ler um conjunto de `SimpleDataLayer` dados espaciais e renderiza-lo no mapa usando a classe. O código usa um arquivo GPX apontado por uma URL.

<br/>

<iframe height='500' scrolling='no' title='Carregar dados espaciais simples' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o Sistema <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>de Dados Espaciais</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>da Carga da Caneta Simples pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

A próxima demonstração de código mostra como ler e carregar KML, ou KMZ, para o mapa. KmL pode conter sobreposições de solo, que `ImageLyaer` serão `OgcMapLayer`na forma de um ou . Essas sobreposições devem ser adicionadas no mapa separadamente das características. Além disso, se o conjunto de dados tiver ícones personalizados, esses ícones precisam ser carregados nos recursos dos mapas antes que os recursos sejam carregados.

<br/>

<iframe height='500' scrolling='no' title='Carregar KML no mapa' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o Mapa de Carga de Caneta <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>KML</a> no Mapa do Azure (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Você pode fornecer opcionalmente um serviço proxy para acessar ativos de domínio cruzado que podem não ter o CORS ativado. A função leitura tentará acessar arquivos em outro domínio usando o CORS primeiro. Após a primeira vez que ele não acessar qualquer recurso em outro domínio usando cors, ele só solicitará arquivos adicionais se um serviço proxy tiver sido fornecido. A função de leitura anexa a URL do arquivo ao final da URL proxy fornecida. Este trecho de código mostra como passar um serviço proxy na função de leitura:

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

A demonstração abaixo mostra como ler um arquivo delimitado e renderiza-lo no mapa. Neste caso, o código usa um arquivo CSV que tem colunas de dados espaciais.

<br/>

<iframe height='500' scrolling='no' title='Adicionar um arquivo delimitado' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>Adicionar um arquivo delimitado</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="write-spatial-data"></a>Escrever dados espaciais

Existem duas funções principais de gravação no módulo io espacial. A `atlas.io.write` função gera uma `atlas.io.writeCompressed` seqüência, enquanto a função gera um arquivo zip compactado. O arquivo zip compactado conteria um arquivo baseado em texto com os dados espaciais nele. Ambas as funções retornam uma promessa de adicionar os dados ao arquivo. E, ambos podem escrever qualquer um `SpatialDataSet` `DataSource`dos `ImageLayer` `OgcMapLayer`seguintes dados: , , , coleta de recursos, recurso, geometria ou uma matriz de qualquer combinação desses tipos de dados. Ao escrever usando ambas as funções, você pode especificar o formato de arquivo desejado. Se o formato do arquivo não for especificado, os dados serão gravados como KML.

A ferramenta abaixo demonstra a maioria das opções `atlas.io.write` de gravação que podem ser usadas com a função.

<br/>

<iframe height='700' scrolling='no' title='Opções de gravação de dados espaciais' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte as <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>opções de gravação de dados pen spatial</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="example-of-writing-spatial-data"></a>Exemplo de escrita de dados espaciais

A amostra a seguir permite arrastar e soltar e, em seguida, carregar arquivos espaciais no mapa. Você pode exportar dados GeoJSON do mapa e escrevê-los em um dos formatos de dados espaciais suportados como uma seqüência ou como um arquivo compactado.

<br/>

<iframe height='700' scrolling='no' title='Arraste e solte arquivos espaciais no mapa' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte o Pen <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>Drag e solte arquivos espaciais no mapa</a> pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

Você pode fornecer opcionalmente um serviço proxy para acessar ativos de domínio cruzado que podem não ter o CORS ativado. Este trecho de código mostra que você pode incorporar um serviço proxy:

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

## <a name="read-and-write-well-known-text-wkt"></a>Ler e escrever texto bem conhecido (WKT)

[O Texto Bem Conhecido](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry) (WKT) é um padrão do Consórcio Geoespacial Aberto (OGC) para representar geometrias espaciais como texto. Muitos sistemas geoespaciais suportam o WKT, como o Azure SQL e o Azure PostgreSQL usando o plugin PostGIS. Como a maioria dos padrões ogc, as coordenadas são formatadas como "latitude de longitude" para se alinhar em relação à convenção "x y". Como exemplo, um ponto em longitude -110 e `POINT(-110 45)` latitude 45 pode ser escrito como usando o formato WKT.

Texto sinuoso pode `atlas.io.ogc.WKT.read` ser lido usando `atlas.io.ogc.WKT.write` a função e escrito usando a função.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>Exemplos de leitura e escrita de texto sinfóris (WKT)

O código a seguir mostra como ler `POINT(-122.34009 47.60995)` a seqüência de texto conhecida e renderiza-la no mapa usando uma camada de bolha.

<br/>

<iframe height='500' scrolling='no' title='Leia texto bem conhecido' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>Ler texto bem conhecido</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>do Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O código a seguir demonstra a leitura e a escrita de textos bem conhecidos para frente e para trás.

<br/>

<iframe height='700' scrolling='no' title='Ler e escrever textos bem conhecidos' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte a caneta <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>Ler e escrever texto bem</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a>conhecido pelo Azure Maps ( ) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="read-and-write-gml"></a>Ler e escrever GML

GML é uma especificação de arquivo XML espacial que é frequentemente usada como uma extensão para outras especificações XML. Os dados geoJSON podem ser escritos como `atlas.io.core.GmlWriter.write` XML com tags GML usando a função. O XML que contém GML `atlas.io.core.GmlReader.read` pode ser lido usando a função. A função de leitura tem duas opções:

- A `isAxisOrderLonLat` opção - A ordem do eixo das coordenadas "latitude, longitude" ou "longitude, latitude" pode variar entre conjuntos de dados, e nem sempre é bem definida. Por padrão, o leitor GML lê os dados de coordenadas como "latitude, longitude", mas definir essa opção como true irá lê-lo como "longitude, latitude".
- A `propertyTypes` opção - Esta opção é uma tabela de pesquisa de valor chave onde a chave é o nome de uma propriedade no conjunto de dados. O valor é o tipo de objeto para lançar o valor ao analisar. Os valores de `string`tipo `number` `boolean`suportados `date`são: , , e . Se uma propriedade não estiver na tabela de aparência ou o tipo não estiver definido, a propriedade será analisado como uma string.

A `atlas.io.read` função será `atlas.io.core.GmlReader.read` padrão para a função quando detectar que os dados de entrada são XML, mas os dados não são um dos outros formatos de XML espacial.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [atlas.io funções estáticas](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io)

> [!div class="nextstepaction"]
> [Conjunto de dados espaciais](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

> [!div class="nextstepaction"]
> [SpatialDataSetStats](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

> [!div class="nextstepaction"]
> [GmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [GmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter?view=azure-maps-typescript-latest)

> [!div class="nextstepaction"]
> [funções atlas.io.ogc.WKT](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Adicione uma camada de mapa OGC](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Conecte-se a um serviço WFS](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Alavancar operações principais](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)
