---
title: Adicione uma camada de dados simples | Mapas do Microsoft Azure
description: Aprenda a adicionar uma camada de dados simples usando o módulo Espacial IO, fornecido pelo Azure Maps Web SDK.
author: philmea
ms.author: philmea
ms.date: 02/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 8862c33b7660b8130f692dc4beea89a7b6b5f5ad
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804479"
---
# <a name="add-a-simple-data-layer"></a>Adicione uma camada de dados simples

O módulo de IO espacial fornece uma `SimpleDataLayer` classe. Esta classe facilita a renderização de recursos estilizados no mapa. Ele pode até renderizar conjuntos de dados que têm propriedades de estilo e conjuntos de dados que contêm tipos de geometria mista. A camada de dados simples alcança essa funcionalidade envolvendo várias camadas de renderização e usando expressões de estilo. As expressões de estilo buscam propriedades de estilo comum dos recursos dentro dessas camadas embrulhadas. A `atlas.io.read` função `atlas.io.write` e a função usam essas propriedades para ler e gravar estilos em um formato de arquivo suportado. Depois de adicionar as propriedades a um formato de arquivo suportado, o arquivo pode ser usado para vários propósitos. Por exemplo, o arquivo pode ser usado para exibir os recursos estilizados no mapa.

Além dos recursos de `SimpleDataLayer` estilo, o fornece um recurso pop-up incorporado com um modelo pop-up. O pop-up é exibido quando um recurso é clicado. O recurso pop-up padrão pode ser desativado, se desejado. Essa camada também suporta dados agrupados. Quando um cluster é clicado, o mapa se aproximará do cluster e o expandirá para pontos e subclusters individuais.

A `SimpleDataLayer` classe destina-se a ser usada em grandes conjuntos de dados com muitos tipos de geometria e muitos estilos aplicados nos recursos. Quando usado, esta classe adiciona uma sobrecarga de seis camadas contendo expressões de estilo. Então, há casos em que é mais eficiente usar as camadas de renderização do núcleo. Por exemplo, use uma camada central para renderizar alguns tipos de geometria e alguns estilos em um recurso

## <a name="use-a-simple-data-layer"></a>Use uma camada de dados simples

A `SimpleDataLayer` classe é usada como as outras camadas de renderização são usadas. O código abaixo mostra como usar uma camada de dados simples em um mapa:

```javascript
//Create a data source and add it to the map.
var datasource = new atlas.source.DataSource();
map.sources.add(datasource);

//Add a simple data layer for rendering data.
var layer = new atlas.layer.SimpleDataLayer(datasource);
map.layers.add(layer);
```

Adicione recursos à fonte de dados. Em seguida, a camada de dados simples descobrirá a melhor forma de renderizar os recursos. Estilos para recursos individuais podem ser definidos como propriedades no recurso. O código a seguir mostra um recurso `color` de `red`ponto GeoJSON com uma propriedade definida como . 

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [0, 0]
    },
    "properties": {
        "color": "red"
    }
}
```

O código a seguir renderiza o recurso de ponto acima usando a camada de dados simples. 

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Use a camada de dados simples" src="//codepen.io/azuremaps/embed/zYGzpQV/?height=500&theme-id=0&default-tab=js,result&editable=true" frameborder="no" allowtransparency="true" allowfullscreen="true"> Veja a caneta <a href='https://codepen.io/azuremaps/pen/zYGzpQV/'>Use a camada de dados simples</a> do Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>

O poder real da camada de dados simples vem quando:

- Existem vários tipos diferentes de recursos em uma fonte de dados; Ou
- Os recursos no conjunto de dados têm várias propriedades de estilo definidas individualmente sobre eles; Ou
- Você não tem certeza do que o conjunto de dados contém exatamente.

Por exemplo, ao analisar feeds de dados XML, você pode não saber os estilos exatos e os tipos de geometria dos recursos. A amostra a seguir mostra a potência da camada de dados simples, renderizando os recursos de um arquivo KML. Ele também demonstra várias opções que a classe simples de camada de dados fornece.

<br/>

<iframe height="700" style="width: 100%;" scrolling="no" title="Opções simples de camada de dados" src="//codepen.io/azuremaps/embed/gOpRXgy/?height=700&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true"> Consulte as <a href='https://codepen.io/azuremaps/pen/gOpRXgy/'>opções de camada de dados Simples</a> da Caneta pelo Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) no <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!NOTE]
> Esta camada de dados simples usa a classe [de modelo popup](map-add-popup.md#add-popup-templates-to-the-map) para exibir balões KML ou propriedades de recurso como uma tabela. Por padrão, todo o conteúdo renderizado no popup será sandbox dentro de um iframe como um recurso de segurança. No entanto, existem limitações:
>
> - Todos os scripts, formulários, bloqueio de ponteiros e funcionalidade de navegação superior estão desativados. Os links podem ser abertos em uma nova guia quando clicados. 
> - Os navegadores mais antigos `srcdoc` que não suportam o parâmetro em iframes serão limitados a renderizar uma pequena quantidade de conteúdo.
> 
> Se você confiar que os dados que estão sendo carregados nos popups e potencialmente quiser que esses scripts carregados `sandboxContent` em popups possam acessar seu aplicativo, você pode desativá-lo configurando a opção de modelos popup como falso. 

## <a name="default-supported-style-properties"></a>Propriedades de estilo suportadas por padrão

Como mencionado anteriormente, a camada de dados simples envolve várias das camadas de renderização do núcleo: bolha, símbolo, linha, polígono e polígono extrudado. Em seguida, ele usa expressões para procurar propriedades de estilo válidas em recursos individuais.

Mapas do Azure e propriedades de estilo GitHub são os dois principais conjuntos de nomes de propriedades suportados. A maioria dos nomes de propriedade das diferentes opções de camada de mapas azure são suportados como propriedades de estilo de recursos na camada de dados simples. Expressões foram adicionadas a algumas opções de camada para suportar nomes de propriedade de estilo que são comumente usados pelo GitHub. Esses nomes de propriedades são definidos pelo [suporte ao mapa GeoJSON do GitHub,](https://help.github.com/en/github/managing-files-in-a-repository/mapping-geojson-files-on-github)e são usados para estilizar arquivos GeoJSON que são armazenados e renderizados dentro da plataforma. Todas as propriedades de estilo do GitHub são suportadas `marker-symbol` na camada de dados simples, exceto as propriedades de estilo.

Se o leitor se deparar com uma propriedade de estilo menos comum, ele irá convertê-lo para a propriedade de estilo Azure Maps mais próxima. Além disso, as expressões de estilo padrão `getLayers` podem ser substituídas usando a função da camada de dados simples e atualizando as opções em qualquer uma das camadas.

As seções a seguir fornecem detalhes sobre as propriedades de estilo padrão suportadas pela camada de dados simples. A ordem do nome da propriedade suportada também é a prioridade do imóvel. Se duas propriedades de estilo forem definidas para a mesma opção de camada, então a primeira da lista tem maior precedência.

### <a name="bubble-layer-style-properties"></a>Propriedades do estilo da camada de bolha

Se um recurso `Point` for `MultiPoint`um ou um , `image` e o recurso não tiver uma propriedade que seria usada como um ícone `BubbleLayer`personalizado para renderizar o ponto como um símbolo, então o recurso será renderizado com um .

| Opção de camada | Nome da propriedade suportado | Valor padrão |
|--------------|----------------------------|---------------|
| `color` | `color`, `marker-color` | `'#1A73AA'` |
| `radius` | `size`<sup>1,</sup> `marker-size` <sup>2,</sup> `scale` <sup>1</sup> | `8` |
| `strokeColor` | `strokeColor`, `stroke` | `'#FFFFFF'` |

\[1\] `size` Os `scale` valores e valores são considerados valores escalares, e eles serão multiplicados por`8`

\[2\] Se a `marker-size` opção GitHub for especificada, os seguintes valores serão usados para o raio.

| Tamanho do marcador | Raio |
|-------------|--------|
| `small`     | `6`    |
| `medium`    | `8`    |
| `large`     | `12`   |

Os clusters também são renderizados usando a camada de bolha. Por padrão, o raio de `16`um cluster é definido como . A cor do cluster varia dependendo do número de pontos no cluster, conforme definido abaixo:

| # de pontos | Color    |
|-------------|----------|
| &gt;= 100   | `red`    |
| &gt;= 10    | `yellow` |
| &lt;10     | `green`  |

### <a name="symbol-style-properties"></a>Propriedades do estilo símbolo

Se um recurso `Point` for `MultiPoint`um ou um `image` , e o recurso e tiver uma propriedade que seria usada como um `SymbolLayer`ícone personalizado para renderizar o ponto como um símbolo, então o recurso será renderizado com um .

| Opção de camada | Nome da propriedade suportado | Valor padrão |
|--------------|----------------------------|---------------|
| `image` | `image` | ``none`` |
| `size` | `size``marker-size` <sup>1</sup> | `1` |
| `rotation` | `rotation` | `0` |
| `offset` | `offset` | `[0, 0]` |
| `anchor` | `anchor` | `'bottom'` |

\[1\] Se a `marker-size` opção GitHub for especificada, os seguintes valores serão usados para a opção tamanho do ícone.

| Tamanho do marcador | Tamanho do símbolo |
|-------------|-------------|
| `small`     | `0.5`       |
| `medium`    | `1`         |
| `large`     | `2`         |

Se o recurso de ponto `point_count_abbreviated` for um cluster, a propriedade será renderizada como um rótulo de texto. Nenhuma imagem será feita.

### <a name="line-style-properties"></a>Propriedades de estilo de linha

Se o recurso `LineString` `MultiLineString`for `Polygon`um `MultiPolygon`, , ou , então `LineLayer`o recurso será renderizado com um .

| Opção de camada | Nome da propriedade suportado | Valor padrão |
|--------------|----------------------------|---------------|
| `strokeColor` | `strokeColor`, `stroke` | `'#1E90FF'` |
| `strokeWidth` | `strokeWidth`, `stroke-width`, `stroke-thickness` | `3` |
| `strokeOpacity` | `strokeOpacity`, `stroke-opacity` | `1` |

### <a name="polygon-style-properties"></a>Propriedades do estilo polígono

Se o recurso `Polygon` for `MultiPolygon`um ou um , e `height` o `height` recurso não tiver um imóvel ou a `PolygonLayer`propriedade for zero, então o recurso será renderizado com um .

| Opção de camada | Nome da propriedade suportado | Valor padrão |
|--------------|----------------------------|---------------|
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `fillOpacity` | `fillOpacity`, '`fill-opacity` | `0.5` |

### <a name="extruded-polygon-style-properties"></a>Propriedades de estilo polígono extrudado

Se o recurso `Polygon` for `MultiPolygon`um ou `height` um , e tiver um imóvel com um `PolygonExtrusionLayer`valor maior que 0, o recurso será renderizado com um .

| Opção de camada | Nome da propriedade suportado | Valor padrão |
|--------------|----------------------------|---------------|
| `base` | `base` | `0` |
| `fillColor` | `fillColor`, `fill` | `'#1E90FF'` |
| `height` | `height` | `0` |

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as classes e métodos usados neste artigo:

> [!div class="nextstepaction"]
> [SimpleDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.layer.simpledatalayer)

> [!div class="nextstepaction"]
> [Opções simplesdeDataLayer](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.simpledatalayeroptions)

Consulte os artigos a seguir para obter mais exemplos de código para adicionar aos seus mapas:

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
