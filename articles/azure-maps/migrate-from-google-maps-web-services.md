---
title: 'Tutorial: Migrar serviços Web do Google Maps | Microsoft Azure Mapas'
description: Como migrar serviços Web do Google Maps para Microsoft Azure Mapas.
author: rbrundritt
ms.author: richbrun
ms.date: 12/17/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.custom: ''
ms.openlocfilehash: 51c00524c781d9af58f60b36aa3baeb079c6eafa
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910750"
---
# <a name="migrate-web-service-from-google-maps"></a>Migrar serviço Web do Google Maps

O Azure Mapas e o Google Maps dão acesso a APIs espaciais por meio de serviços Web REST. As interfaces de API para essas plataformas executam funcionalidades semelhantes, mas usam diferentes convenções de nomenclatura e objetos de resposta.

A tabela a seguir apresenta as APIs de serviço do Azure Mapas que fornecem funcionalidade semelhante às APIs de serviço do Google Maps listadas.

| API de serviço do Google Maps | API de serviço do Azure Mapas                                                                      |
|-------------------------|---------------------------------------------------------------------------------------------|
| Instruções              | [Route](https://docs.microsoft.com/rest/api/maps/route)                               |
| Matriz de Distância         | [Matrix de Rota](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) |
| Geocodificação               | [Pesquisar](https://docs.microsoft.com/rest/api/maps/search)                             |
| Pesquisa de locais           | [Pesquisar](https://docs.microsoft.com/rest/api/maps/search)                             |
| Preenchimento automático de locais      | [Pesquisar](https://docs.microsoft.com/rest/api/maps/search)                             |
| Mapa estático              | [Render](https://docs.microsoft.com/rest/api/maps/render/getmapimage)                 |
| Fuso horário               | [Fuso horário](https://docs.microsoft.com/rest/api/maps/timezone)                        |

As seguintes APIs de serviço não estão disponíveis atualmente no Azure Mapas:

- Elevação
- Geolocalização
- Detalhes e fotos dos locais. Números de telefone e URL do site disponíveis na API de pesquisa do Azure Mapas.
- URLs de mapa
- Estradas – os dados de limite de velocidade estão disponíveis por meio das APIs de rota e de geocódigo inverso no Azure Mapas.
- Exibição estática de rua

O Azure Mapas tem vários serviços Web REST adicionais que podem ser interessantes:

- [Operações Espaciais](https://docs.microsoft.com/rest/api/maps/spatial): Descarregue operações e cálculos espaciais complexos, como cerca geográfica, para um serviço.
- [Tráfego](https://docs.microsoft.com/rest/api/maps/traffic): Acesse o fluxo de tráfego e os dados de incidentes em tempo real.

## <a name="geocoding-addresses"></a>Como geocodificar endereços

A geocodificação é o processo de converter um endereço (como "1 Microsoft way, Redmond, WA") em uma coordenada (como longitude: -122,1298, latitude: 47,64005). As coordenadas então costumam ser usadas para posicionar um marcador em um mapa ou centralizar um mapa.

O Azure Mapas fornece vários métodos para endereços com geocódigo:

- [**Geocódigo de endereço em forma livre**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddress): Especifique uma cadeia de caracteres de endereço (como "1 Microsoft way, Redmond, WA") e processe a solicitação imediatamente. Isso é recomendado se você precisa geocodificar endereços individuais rapidamente.
- [**Geocódigo de endereço estruturado**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressstructured): Especifique as partes de um endereço (como o nome da rua, a cidade, o país e o CEP) e processe a solicitação imediatamente. Isso é recomendado se você precisa geocodificar endereços individuais rapidamente e os dados já foram analisados em suas partes de endereço individuais.
- [**Geocódigo de endereço de lote**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressbatchpreview): Crie uma solicitação contendo até 10 mil endereços e faça com que elas sejam processadas durante um período de tempo. Todos os endereços serão geocodificados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado. Isso é recomendado para geocódigo de conjuntos de dados grandes.
- [**Pesquisa difusa**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres com forma livre que pode ser um endereço, um local, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse e processa a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem pesquisar endereços ou pontos de interesse usando a mesma caixa de texto.
- [**Pesquisa de lote difusa**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10 mil endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas  |
|---------------------------|--------------------------------------|
| `address`                   | `query`                            |
| `bounds`                    | `topLeft` e `btmRight`           |
| `components`                | `streetNumber`<br/>`streetName`<br/>`crossStreet`<br/>`postalCode`<br/>`municipality` – cidade<br/>`municipalitySubdivision` – bairro, sub/supercidade<br/>`countrySubdivision` – Estado ou província<br/>`countrySecondarySubdivision` – município<br/>`countryTertiarySubdivision` – distrito<br/>`countryCode` – código do país com duas letras |
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `region`                    | `countrySet`                       |

Um exemplo de como usar o serviço de pesquisa está documentado [aqui](how-to-search-for-address.md). Examine a documentação de [melhores práticas para pesquisar](how-to-use-best-practices-for-search.md).

> [!TIP]
> As APIs de pesquisa difusa e geocódigo de endereço com forma livre podem ser usadas no modo de preenchimento automático adicionando `&amp;typeahead=true` à URL de solicitação. Isso dirá ao servidor que o texto de entrada é provavelmente parcial e entrará em modo de previsão.

## <a name="reverse-geocode-a-coordinate"></a>Geocodificação inversa de uma coordenada

O geocódigo inverso é o processo de conversão de coordenadas geográficas (como longitude: -122,1298, latitude: 47,64005) em seu endereço aproximado (como "1 Microsoft way, Redmond, WA").

O Azure Mapas fornece vários métodos de geocódigo inverso:

- [**Geocodificador inverso de endereço**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreverse): Especifique uma coordenada geográfica para obter seu endereço aproximado e processar a solicitação imediatamente.
- [**Geocodificador inverso entre ruas**](https://docs.microsoft.com/rest/api/maps/search/getsearchaddressreversecrossstreet): Especifique uma coordenada geográfica para obter informações de rua cruzada próximas (por exemplo, 1ª e principal) e processar a solicitação imediatamente.
- [**Geocodificador inverso de endereço de lote**](https://docs.microsoft.com/rest/api/maps/search/postsearchaddressreversebatchpreview): Crie uma solicitação contendo até 10 mil coordenadas e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps   | Parâmetro comparável da API do Azure Mapas   |
|-----------------------------|---------------------------------------|
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `latlng`                    | `query`  |
| `location_type`             | *N/A*     |
| `result_type`               | `entityType`    |

Examine a documentação de [melhores práticas para pesquisar](how-to-use-best-practices-for-search.md).

A API de geocódigo inverso do Azure Mapas tem alguns recursos adicionais não disponíveis no Google Maps que podem ser úteis integrar ao migrar seu aplicativo:

- Recupere dados de limite de velocidade.
- Recuperar informações de uso de estrada: estrada local, arterial, acesso limitado, rampa etc.
- O lado da rua em que a coordenada cai.

## <a name="search-for-points-of-interest"></a>Pesquisar pontos de interesse

Dados de ponto de interesse podem ser pesquisados no Google Maps usando sua API de Pesquisa de Locais. Essa API fornece três maneiras diferentes de pesquisar pontos de interesse:

- **Localizar local do texto:** Pesquisa um ponto de interesse com base em seu nome, endereço ou número de telefone.
- **Pesquisa de Proximidade**: Pesquisa pontos de interesse dentro de uma determinada distância de uma localização.
- **Pesquisa de Texto:** Pesquisa locais usando um texto com forma livre que inclui informações de ponto de interesse e localização. Por exemplo, "pizza em Nova York" ou "restaurantes perto da main st".

O Azure Mapas fornece várias APIs de pesquisa para pontos de interesse:

- [**Pesquisa de POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi): Pesquise pontos de interesse pelo nome. Por exemplo, "Starbucks".
- [**Pesquisa de categoria de POI**](https://docs.microsoft.com/rest/api/maps/search/getsearchpoicategory): Pesquise pontos de interesse pela categoria. Por exemplo, "restaurante".
- [**Pesquisa de proximidade**](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby): Pesquisa pontos de interesse dentro de uma determinada distância de uma localização.
- [**Pesquisa difusa**](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy): Essa API combina geocódigo com pesquisa de ponto de interesse. Essa API usa uma cadeia de caracteres com forma livre que pode ser um endereço, um local, um ponto de referência, um ponto de interesse ou uma categoria de ponto de interesse e processa a solicitação imediatamente. Essa API é recomendada para aplicativos em que os usuários podem pesquisar endereços ou pontos de interesse usando a mesma caixa de texto.
- [**Pesquisar dentro da geometria**](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry): Pesquisar pontos de interesse dentro de uma geometria (polígono) especificada.
- [**Pesquisar ao longo da rota**](https://docs.microsoft.com/rest/api/maps/search/postsearchalongroute): Pesquise pontos de interesse que estejam ao longo de um caminho de rota especificado.
- [**Pesquisa de lote difusa**](https://docs.microsoft.com/rest/api/maps/search/postsearchfuzzybatchpreview): Crie uma solicitação contendo até 10 mil endereços, locais, pontos de referência ou pontos de interesse e faça com que eles sejam processados durante um período. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.

Atualmente, o Azure Mapas não tem uma API comparável à API de pesquisa de texto do Google Maps.

> [!TIP]
> As APIs de pesquisa de POI, pesquisa de categoria de POI e pesquisa difusa podem ser usadas no modo de preenchimento automático adicionando `&amp;typeahead=true` à URL de solicitação. Isso dirá ao servidor que o texto de entrada é provavelmente parcial e entrará em modo de previsão.

Examine a documentação de [melhores práticas para pesquisar](how-to-use-best-practices-for-search.md).

### <a name="find-place-from-text"></a>Localizar local do texto

Para pesquisar pontos de interesse por nome ou endereço, é possível usar as APIs [Pesquisa de POI](https://docs.microsoft.com/rest/api/maps/search/getsearchpoi) e [Pesquisa difusa](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy) do Azure Mapas.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas |
|---------------------------|-------------------------------------|
| `fields`                  | *N/A*                               |
| `input`                   | `query`                             |
| `inputtype`               | *N/A*                               |
| `key`                     | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `locationbias`            | `lat`, `lon` e `radius`<br/>`topLeft` e `btmRight`<br/>`countrySet`  |

### <a name="nearby-search"></a>Pesquisa de proximidade

Pontos de interesse próximos podem ser recuperados no Azure Mapas usando a API [Pesquisa de proximidade](https://docs.microsoft.com/rest/api/maps/search/getsearchnearby).

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas  |
|---------------------------|--------------------------------------|
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `keyword`                   | `categorySet` e `brandSet`        |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `location`                  | `lat` e `lon`                     |
| `maxprice`                  | *N/A*                               |
| `minprice`                  | *N/A*                               |
| `name`                      | `categorySet` e `brandSet`        |
| `opennow`                   | *N/A*                               |
| `pagetoken`                 | `ofs` e `limit`                   |
| `radius`                    | `radius`                            |
| `rankby`                    | *N/A*                               |
| `type`                      | `categorySet –` Confira a documentação de [categorias de pesquisa compatíveis](supported-search-categories.md).   |

## <a name="calculate-routes-and-directions"></a>Calcular rotas e instruções

O Azure Mapas pode ser usado para calcular rotas e direções. O Azure Mapas tem muitas das mesmas funcionalidades que o serviço de roteiros do Google Maps, como:

- horários de chegada e partida.
- rotas de tráfego com base em tempo real e preditivas.
- diferentes meios de transporte, como automóvel, a pé, bicicleta.

> [!NOTE]
> O Azure Mapas exige que todos os marcos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

O serviço de roteiros do Azure Mapas fornece as seguintes APIs para calcular rotas:

- [**Calcular rota**](https://docs.microsoft.com/rest/api/maps/route/getroutedirections): Calcule uma rota e tenha a solicitação seja processada imediatamente. Essa API é compatível com solicitações GET e POST. Solicitações POST são recomendadas ao especificar um grande número de marcos ou ao usar muitas das opções de rota para garantir que a solicitação de URL não se torne longa demais e cause problemas.
- [**Rota de lote**](https://docs.microsoft.com/rest/api/maps/route/postroutedirectionsbatchpreview): Crie uma solicitação contendo até 1.000 solicitação de rota e faça com que elas sejam processadas durante um período de tempo. Todos os dados serão processados em paralelo no servidor e, quando concluído, o conjunto de resultados completo poderá ser baixado.
- [**Serviços de mobilidade**](https://docs.microsoft.com/rest/api/maps/mobility): Calcule rotas e instruções usando o trânsito público.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps    | Parâmetro comparável da API do Azure Mapas  |
|------------------------------|--------------------------------------|
| `alternatives`                 | `maxAlternatives`                  |
| `arrival_time`                | `arriveAt`                          |
| `avoid`                        | `avoid`                            |
| `departure_time`              | `departAt`                          |
| `destination`                  | `query` – coordenadas no formato `"lat0,lon0:lat1,lon1…."`  |
| `key`                          | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                     | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).   |
| `mode`                         | `travelMode`                       |
| `optimize`                     | `computeBestOrder`                 |
| `origin`                       | `query`                            |
| `region`                       | *N/A* – esse é um recurso relacionado a geocódigo. Use o parâmetro *countrySet* ao usar a API de geocódigo do Azure Mapas.  |
| `traffic_model`               | *N/A* – só é possível especificar se os dados de tráfego devem ser usados com o parâmetro *traffic*. |
| `transit_mode`                | Confira a [documentação de serviços de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility) |
| `transit_routing_preference` | Confira a [documentação de serviços de mobilidade](https://docs.microsoft.com/rest/api/maps/mobility) |
| `units`                        | *N/A* – o Azure Mapas usa apenas o sistema métrico.  |
| `waypoints`                    | `query`                            |

> [!TIP]
> Por padrão, a API de rotas do Azure Mapas retorna apenas um resumo (distância e tempos) e as coordenadas para o caminho da rota. Use o parâmetro `instructionsType` para recuperar as instruções curva a curva. O parâmetro `routeRepresentation` pode ser usado para filtrar o resumo e o caminho da rota.

A API de roteiros do Azure Mapas tem muitos recursos adicionais não disponíveis no Google Maps que podem ser úteis integrar ao migrar seu aplicativo:

- Suporte para tipo de rota: mais curta, mais rápida, emocionante e mais econômica no consumo de combustível.
- Suporte para modos de viagem adicionais: ônibus, motocicleta, táxi, caminhão e van.
- Suporte para 150 marcos.
- Calcular vários tempos de viagem em uma solicitação; tráfego histórico, tráfego ao vivo, sem tráfego.
- Evite tipos de estrada adicionais: estradas com pista para veículos de alta ocupação, estradas não pavimentadas e estradas já usadas.
- Especifique áreas personalizadas a serem evitadas.
- Limite a elevação até a qual a rota pode subir.
- Roteiros baseados na especificação do mecanismo. Calcule as rotas para veículos elétricos ou de combustão com base nas especificações de combustível/carga restantes e motor.
- Suporte ao parâmetro de roteiro de veículo comercial; dimensões do veículo, peso, número de eixos e tipo de carga.
- Especifique a velocidade máxima do veículo.

Além disso, o Serviço de Roteiros no Azure Mapas também dá suporte a [calcular intervalos roteáveis](https://docs.microsoft.com/rest/api/maps/route/getrouterange), também conhecido como isócronas, que gera um polígono que abrange uma área que pode ser percorrida em qualquer direção de um ponto de origem dentro de um período ou de uma quantidade de combustível/carga especificado.

## <a name="retrieve-a-map-image"></a>Recuperar uma imagem do mapa

O Azure Mapas fornece uma API para renderizar as imagens de mapa estático contendo dados sobrepostos. A API [Renderização de imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile) do Azure Mapas é comparável à API de mapa estático no Google Maps.

> [!NOTE]
> O Azure Mapas exige que as localizações de caminho, todos os marcadores e centro sejam coordenadas no formato de "longitude latitude", enquanto o Google Maps usa o formato "latitude,longitude". Os endereços precisarão ser geocodificados primeiro.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas  |
|---------------------------|--------------------------------------|
| `center`                    | `center`                           |
| `format`                    | `format` – especificado como parte do caminho da URL. No momento, há suporte apenas para PNG. |
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `maptype`                   | `layer` e `style` – confira a documentação de [Estilos de mapa compatíveis](supported-map-styles.md). |
| `markers`                   | `pins`                             |
| `path`                      | `path`                             |
| `region`                    | *N/A* – esse é um recurso relacionado a geocódigo. Use o parâmetro `countrySet` ao usar a API de geocódigo do Azure Mapas.  |
| `scale`                     | *N/A*                              |
| `size`                      | `width` e `height` – pode ter um tamanho de até 8192x8192. |
| `style`                     | *N/A*                              |
| `visible`                   | *N/A*                              |
| `zoom`                      | `zoom`                             |

> [!NOTE]
> O Azure Mapas usa um sistema com peças que são duas vezes o tamanho das peças de mapa usadas no Google Maps. Assim, o valor do nível de zoom no Azure Mapas aparecerá um nível de zoom mais próximo no Azure Mapas em comparação ao Google Maps. Reduza o nível de zoom em um nas solicitações que você está migrando para compensar isso.

Para obter mais informações, confira o [Guia de instruções sobre a API de renderização de imagem do mapa](how-to-render-custom-data.md).

Além de poder gerar uma imagem de mapa estático, o serviço de renderização do Azure Mapas também permite acessar diretamente os blocos de mapa em formato de varredura (PNG) e vetor:

- [**Bloco de mapa**](https://docs.microsoft.com/rest/api/maps/render/getmaptile): Recupere blocos de varredura (PNG) e de vetor para os mapas base (estradas, limites, segundo plano).
- [**Bloco de imagens do mapa**](https://docs.microsoft.com/rest/api/maps/render/getmapimagerytile): Recupere blocos de imagem aérea e de satélite.

> [!TIP]
> Muitos aplicativos Google Maps mudaram de experiências de mapa interativo para imagens de mapa estático há alguns anos para economizar custos. No Azure Mapas, geralmente é muito mais econômico usar o controle de mapeamento interativo no SDK da Web, pois ele cobra com base em carregamentos de bloco do mapa. Os blocos de mapa no Azure Mapas são grandes e muitas vezes são necessários apenas alguns para recriar a mesma exibição de mapa que um mapa estático e o navegador armazena os blocos de mapa em cache automaticamente. Assim, o controle de mapeamento interativo normalmente gera apenas uma fração de uma transação ao reproduzir uma exibição de mapa estático. Panorâmica e zoom carregarão mais blocos, no entanto, há opções no controle de mapeamento para desabilitar esse comportamento, se desejado. O controle de mapeamento interativo também fornece muito mais opções de visualização do que os serviços de mapa estáticos.

### <a name="marker-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL do marcador

**Antes: Google Maps**

No Google Maps, é possível adicionar marcadores a uma imagem de mapa estático usando o parâmetro `markers` na URL. O parâmetro `markers` usa um estilo e uma lista de locais a serem renderizados no mapa com esse estilo, conforme mostrado abaixo:

```
&markers=markerStyles|markerLocation1|markerLocation2|...
```

Estilos adicionais podem ser usados adicionando parâmetros `markers` extras à URL com um estilo e um conjunto de locais diferentes.

Os locais do marcador são especificados com o formato "latitude,longitude".

Os estilos de marcador no Google Maps são adicionados com o formato `optionName:value`, com vários estilos separados por caracteres de barra vertical (\|) como este "optionName1:value1\|optionName2:value2". Observe que os nomes e os valores de opção são separados por dois-pontos (:). Os seguintes nomes de opção de estilo podem ser usados para marcadores de estilo no Google Maps:

- `color` – a cor do ícone do marcador padrão. Pode ser uma cor hexadecimal de 24 bits (`0xrrggbb`) ou um dos valores a seguir: `black`, `brown`, `green`, `purple`, `yellow`, `blue`, `gray`, `orange`, `red`, `white`.
- `label` – um caractere alfanumérico maiúsculo para exibir na parte superior do ícone.
- `size` – o tamanho do marcador. Pode ser `tiny`, `mid` ou `small`.

Os ícones personalizados também podem ser usados no Google Maps com os seguintes nomes de opção de estilo:

- `anchor` – especifica como alinhar a imagem do ícone à coordenada. Pode ser um valor de pixel (x,y) ou um dos valores a seguir: `top`, `bottom`, `left`, `right`, `center`, `topleft`, `topright`, `bottomleft` ou `bottomright`.
- `icon` – uma URL que aponta para a imagem do ícone.

Por exemplo, no Google Maps, um marcador vermelho de tamanho médio pode ser adicionado ao mapa em coordenadas (longitude:-110, latitude: 45) com o seguinte parâmetro de URL:

```
&markers=color:red|size:mid|45,-110
```

<center>

![Marcador do Google Maps](media/migrate-google-maps-web-services/google-maps-marker.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, os marcadores também podem ser adicionados a uma imagem de mapa estático especificando o parâmetro `pins` na URL. Como o Google Maps, um estilo e uma lista de localizações podem ser especificados nesse parâmetro, sendo que o parâmetro `pins` pode ser especificado várias vezes para dar suporte a marcadores com estilos diferentes.

```
&pins=iconType|pinStyles||pinLocation1|pinLocation2|...
```

Estilos adicionais podem ser usados adicionando parâmetros `pins` extras à URL com um estilo e um conjunto de locais diferentes.

Quando se trata de fixar localizações, o Azure Mapas exige que as coordenadas estejam no formato "latitude de longitude", enquanto o Google Maps usa o formato "latitude, longitude". Observe também que há um espaço, não uma vírgula, separando a longitude e a latitude no Azure Mapas.

O valor `iconType` especifica o tipo de marcador a ser criado e pode ter os seguintes valores:

- `default` – o ícone de marcação padrão.
- `none` – nenhum ícone é exibido, somente os rótulos serão renderizados.
- `custom` – especifica que um ícone personalizado deve ser usado. Uma URL que aponta para a imagem do ícone pode ser adicionada ao final do parâmetro `pins` após as informações de localização do marcador.
- `{udid}` – uma UDID (ID de dados exclusiva) para um ícone armazenado na plataforma de armazenamento de dados do Azure Mapas.

Os estilos de marcador no Azure Mapas são adicionados com o formato `optionNameValue`, com vários estilos separados por caracteres de barra vertical (\|) como este `iconType|optionName1Value1|optionName2Value2`. Observe que os nomes e os valores de opção não são separados. Os seguintes nomes de opção de estilo podem ser usados para marcadores de estilo no Azure Mapas:

- `al` – especifica a opacidade (alfa) do marcador. Pode ser um número entre 0 e 1.
- `an` – especifica a âncora do marcador. Valores de pixel x e y especificados no formato "x y".
- `co` – a cor do marcador. Deve ser uma cor hexadecimal de 24 bits: `000000` a `FFFFFF`.
- `la` – especifica a âncora do rótulo. Valores de pixel x e y especificados no formato "x y".
- `lc` – a cor do rótulo. Deve ser uma cor hexadecimal de 24 bits: `000000` a `FFFFFF`.
- `ls` – o tamanho do rótulo em pixels. Pode ser um número maior que 0.
- `ro` – um valor em graus para girar o ícone. Pode ser um número entre -360 e 360.
- `sc` – um valor de escala para o ícone de marcador. Pode ser um número maior que 0.

Os valores de rótulo são especificados para cada localização do marcador, em vez de ter um valor de rótulo que se aplica a todos os marcadores na lista de locais. O valor do rótulo pode ser uma cadeia de vários caracteres e ser encapsulado entre aspas simples para garantir que não seja confundido como um valor de estilo ou localização.

Por exemplo, no Azure Mapas, adicionar um ícone padrão (`FF0000`) vermelho com o rótulo "Agulha de Espaço" posicionado abaixo (15 50) do ícone em coordenadas (longitude:-122,349300, latitude: 47,620180) pode ser feito com o seguinte parâmetro de URL:

```
&pins=default|coFF0000|la15 50||'Space Needle' -122.349300 47.620180
```

<center>

![Marcador do Azure Mapas](media/migrate-google-maps-web-services/azure-maps-marker.png)</center>

O exemplo a seguir adiciona três alfinetes com os valores de rótulo '1', '2' e '3':

```
&pins=default||'1'-122 45|'2'-119.5 43.2|'3'-121.67 47.12
```

<center>

![Vários marcadores do Azure Mapas](media/migrate-google-maps-web-services/azure-maps-multiple-markers.png)</center>

### <a name="path-url-parameter-format-comparison"></a>Comparação de formato de parâmetro de URL do caminho

**Antes: Google Maps**

No Google Maps, é possível adicionar linhas e polígonos a uma imagem de mapa estático usando o parâmetro `path` na URL. O parâmetro `path` usa um estilo e uma lista de locais a serem renderizados no mapa com esse estilo, conforme mostrado abaixo:

```
&path=pathStyles|pathLocation1|pathLocation2|...
```

Estilos adicionais podem ser usados adicionando parâmetros `path` extras à URL com um estilo e um conjunto de locais diferentes.

Os locais de caminho no Google Maps são especificados com o formato `latitude1,longitude1|latitude2,longitude2|…`. Os caminhos podem ser codificados ou conter endereços para pontos.

Os estilos de caminho no Google Maps são adicionados com o formato `optionName:value`, com vários estilos separados por caracteres de barra vertical (\|) como este `optionName1:value1|optionName2:value2`. Observe que os nomes e os valores de opção são separados por dois-pontos (:). Os seguintes nomes de opção de estilo podem ser usados para caminhos de estilo no Google Maps:

- `color` – a cor do caminho ou do contorno do polígono. Pode ser uma cor hexadecimal de 24 bits (`0xrrggbb`), uma cor hexadecimal de 32 bits (`0xrrggbbbaa`) ou um dos valores a seguir: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `fillColor` – a cor com a qual preencher a área de caminho (polígono). Pode ser uma cor hexadecimal de 24 bits (`0xrrggbb`), uma cor hexadecimal de 32 bits (`0xrrggbbbaa`) ou um dos valores a seguir: preto, marrom, verde, roxo, amarelo, azul, cinza, laranja, vermelho, branco.
- `geodesic` – indica se o caminho deve ser uma linha que segue a curvatura da terra.
- `weight` – a espessura da linha do caminho em pixels.

Por exemplo, no Google Maps, uma linha vermelha com opacidade de 50% e uma espessura de 4 pixels pode ser adicionada ao mapa entre coordenadas (longitude: -110, latitude: 45 e longitude: -100, latitude: 50) com o seguinte parâmetro de URL:

```
&path=color:0xFF000088|weight:4|45,-110|50,-100
```

<center>

![Polilinha do Google Maps](media/migrate-google-maps-web-services/google-maps-polyline.png)</center>

**Depois: Azure Mapas**

No Azure Mapas, linhas e polígonos também podem ser adicionados a uma imagem de mapa estático especificando o parâmetro `path` na URL. Como o Google Maps, um estilo e uma lista de locais podem ser especificados nesse parâmetro, sendo que o parâmetro `path` pode ser especificado várias vezes para renderizar vários círculos, linhas e polígonos com estilos diferentes.

```
&path=pathStyles||pathLocation1|pathLocation2|...
```

Quando se tratam de locais de caminho, o Azure Mapas exige que as coordenadas estejam no formato de "longitude latitude", enquanto o Google Maps usa o formato "latitude,longitude". Observe também que há um espaço, não uma vírgula, separando a longitude e a latitude no Azure Mapas. O Azure Mapas não é compatível com caminhos ou endereços codificados para pontos. Conjuntos de dados maiores podem ser carregados como preenchimentos GeoJSON na API de Armazenamento de Dados do Azure Mapas, conforme documentado [aqui](how-to-render-custom-data.md#get-data-from-azure-maps-data-storage).

Os estilos de caminho no Azure Mapas são adicionados com o formato `optionNameValue`, com vários estilos separados por caracteres de barra vertical (\|) como este `optionName1Value1|optionName2Value2`. Observe que os nomes e os valores de opção não são separados. Os seguintes nomes de opção de estilo podem ser usados para caminhos de estilo no Azure Mapas:

- `fa` – a opacidade da cor de preenchimento (alfa) usada ao renderizar polígonos. Pode ser um número entre 0 e 1.
- `fc` – a cor de preenchimento usada para renderizar a área de um polígono.
- `la` – a linha de opacidade (alfa) usada ao renderizar linhas e o contorno de polígonos. Pode ser um número entre 0 e 1.
- `lc` – a cor da linha usada para renderizar linhas e o contorno de polígonos.
- `lw` – a largura da linha em pixels.
- `ra` – especifica um raio de círculos em metros.

Por exemplo, no Azure Mapas, uma linha vermelha com opacidade de 50% e uma espessura de 4 pixels pode ser adicionada ao mapa entre coordenadas (longitude: -110, latitude: 45 e longitude: -100, latitude: 50) com o seguinte parâmetro de URL:

```
&path=lcFF0000|la.5|lw4||-110 45|-100 50
```

<center>

![Polilinha do Azure Mapas](media/migrate-google-maps-web-services/azure-maps-polyline.png)</center>

## <a name="calculate-a-distance-matrix"></a>Calcular uma matriz de distância

O Azure Mapas fornece uma API para calcular os tempos de viagem e as distâncias entre um conjunto de locais como uma matriz de distância. A API de matriz de distância do Azure Mapas é comparável à API de matriz de distância no Google Maps.

- [**Matriz de rota**](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview): calcula de maneira assíncrona os tempos de viagem e as distâncias de um conjunto de origens e destinos. Há suporte para até 700 células por solicitação (o número de origens multiplicado pelo número de destinos). Com essa restrição em mente, exemplos de possíveis dimensões de matriz são: 700x1, 50x10, 10x10, 28x25, 10x70.

> [!NOTE]
> Uma solicitação para a API de matriz de distância só pode ser feita usando uma solicitação POST com as informações de origem e destino no corpo da solicitação. Além disso, o Azure Mapas requer que todas as origens e destinos sejam coordenadas. Os endereços precisarão ser geocodificados primeiro.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps      | Parâmetro comparável da API do Azure Mapas  |
|--------------------------------|--------------------------------------|
| `arrivial_time`                | `arriveAt`                           |
| `avoid`                        | `avoid`                              |
| `depature_time`                | `departAt`                           |
| `destinations`                 | `destination` – especifique no corpo da solicitação POST como GeoJSON. |
| `key`                          | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md). |
| `language`                     | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).  |
| `mode`                         | `travelMode`                         |
| `origins`                      | `origins` – especifique no corpo da solicitação POST como GeoJSON.  |
| `region`                       | *N/A* – esse é um recurso relacionado a geocódigo. Use o parâmetro `countrySet` ao usar a API de geocódigo do Azure Mapas. |
| `traffic_model`                | *N/A* – só é possível especificar se os dados de tráfego devem ser usados com o parâmetro `traffic`. |
| `transit_mode`                 | *N/A* – no momento, não há suporte para matrizes de distância com base em trânsito.  |
| `transit_routing_preference`   | *N/A* – no momento, não há suporte para matrizes de distância com base em trânsito.  |
| `units`                        | *N/A* – o Azure Mapas usa apenas o sistema métrico. |

> [!TIP]
> Todas as opções de roteiros avançadas disponíveis na API de roteiros do Azure Mapas (roteiros de caminhão, especificações do motor, evitar etc.) são compatíveis com a API da matriz de distância do Azure Mapas.

## <a name="get-a-time-zone"></a>Obter um fuso horário

O Azure Mapas fornece uma API para recuperar o fuso horário em que uma coordenada se encontra. A API de fuso horário do Azure Mapas é comparável à API de fuso horário no Google Maps:

- [**Fuso horário por coordenada**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebycoordinates): Especifique uma coordenada e obtenha os detalhes do fuso horário em que ela está.

A tabela a seguir faz referência cruzada aos parâmetros da API do Google Maps com os parâmetros comparáveis da API do Azure Mapas.

| Parâmetro da API do Google Maps | Parâmetro comparável da API do Azure Mapas   |
|---------------------------|---------------------------------------|
| `key`                       | `subscription-key` – confira também a documentação [Autenticação com o Azure Mapas](azure-maps-authentication.md).       |
| `language`                  | `language` – confira a documentação de [idiomas compatíveis](supported-languages.md).    |
| `location`                  | `query`             |
| `timestamp`                 | `timeStamp`         |

Além disso, a plataforma Azure Mapas também fornece várias APIs de fuso horário adicionais para ajudar com conversões com nomes e IDs de fuso horário:

- [**Fuso horário por ID**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonebyid): Retorna informações de fuso horário atuais, históricas e futuras para a ID de fuso horário do IANA especificada.
- [**IANA de enumeração de fuso horário**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumiana): Retorna uma lista completa de IDs de fuso horário do IANA. As atualizações ao serviço IANA são refletidas no sistema dentro de um dia.
- [**Enumeração de fuso horário no Windows**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneenumwindows): Retorna uma lista completa de IDs de fuso horário do Windows.
- [**Fuso horário versão IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezoneianaversion): Retorna o número de versão atual do IANA usado pelo Azure Mapas.
- [**Fuso horário Windows para IANA**](https://docs.microsoft.com/rest/api/maps/timezone/gettimezonewindowstoiana): Retorna uma ID do IANA correspondente, dada uma ID de fuso horário do Windows válida. Várias IDs do IANA podem ser retornadas para uma ID do Windows individual.

## <a name="client-libraries"></a>Bibliotecas de cliente

O Azure Mapas fornece bibliotecas de cliente para as seguintes linguagens de programação:

- JavaScript, TypeScript, Node.js – [documentação](how-to-use-services-module.md) \| [pacote NPM](https://www.npmjs.com/package/azure-maps-rest)

Bibliotecas de cliente de software livre para outras linguagens de programação:

- .NET Standard 2.0 – [projeto GitHub](https://github.com/perfahlen/AzureMapsRestServices) \| [pacote NuGet](https://www.nuget.org/packages/AzureMapsRestToolkit/)

## <a name="additional-resources"></a>Recursos adicionais

Veja a seguir algumas documentações e recursos adicionais sobre os serviços REST do Azure Mapas.

- [Melhores práticas para pesquisar](how-to-use-best-practices-for-search.md)
- [Pesquisar um endereço](how-to-search-for-address.md)
- [Documentação de referência da API do Serviço REST do Azure Mapas](https://docs.microsoft.com/rest/api/maps/)

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços REST do Azure Mapas.

> [!div class="nextstepaction"]
> [Melhores práticas para usar o serviço de pesquisa](how-to-use-best-practices-for-search.md)

> [!div class="nextstepaction"]
> [Como usar o módulo de serviços (SDK da Web)](how-to-use-services-module.md)

> [!div class="nextstepaction"]
> [Exemplos de código](https://docs.microsoft.com/samples/browse/?products=azure-maps)