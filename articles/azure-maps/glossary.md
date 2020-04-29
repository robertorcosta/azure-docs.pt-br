---
title: Glossário do Azure Mapas | Microsoft Docs
description: Um glossário de termos comumente usados associados com mapas do Azure, serviços baseados em local e GIS.
author: rbrundritt
ms.author: richbrun
ms.date: 09/18/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: c9d5c2efc14dec5beee0eaf81a4f628421ef3d49
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77657037"
---
# <a name="glossary"></a>Glossário

A lista a seguir descreve as palavras comuns usadas com os serviços do Azure Maps.

## <a name="a"></a>Um

<a name="address-validation"></a> **Validação de endereço**: O processo de verificar a existência de um endereço.

<a name="advanced-routing"></a>**Roteamento avançado**: uma coleção de serviços que executam operações avançadas usando dados de roteamento de estrada; como, calculando intervalos acessíveis (isócronas), matrizes de distância e solicitações de rota de lote.

<a name="aerial-imagery"></a> **Imagens aéreas**: consulte [imagens de satélites](#satellite-imagery). 

<a name="along-a-route-search"></a>**Ao longo de uma pesquisa de rota**: uma consulta espacial que procura dados em uma hora ou distância de desvio especificada de um caminho de rota.

<a name="altitude"></a> **Altitude**: A altura ou a elevação vertical de um ponto acima de uma superfície de referência. Medidas de altitude são baseadas no dado referência fornecida, como o nível médio do mar. Consulte também elevação.

<a name="ambiguous"></a> **Ambíguo**: estado de incerteza na classificação de dados que existe quando um objeto adequadamente pode ser atribuído dois ou mais valores para um determinado atributo. Por exemplo, ao geocodificar "CA", dois resultados ambíguos são retornados: "Canadá" e "Califórnia". "CA" é um país e um código de estado, para "Canadá" e "Califórnia", respectivamente. 

<a name="annotation"></a> **Anotação**: texto ou elementos gráficos exibidos no mapa para fornecer informações ao usuário. A anotação pode identificar ou descrever uma entidade de mapa específico, fornecer informações gerais sobre uma área do mapa ou fornecer informações sobre o mapa em si.

<a name="antimeridian"></a>**Antimeridiano**: também conhecido como<sup>o meridiano de</sup> 180. Esse é o ponto onde-180 graus e 180 graus de longitude se encontram. Que é o oposto do meridiano principal do globo.

<a name="application-programming-interface-api"></a> **Interface de Programação de Aplicativo (API)**: uma especificação que permite aos desenvolvedores criar aplicativos.

<a name="api-key"></a>**Chave de API**: consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="area-of-interest-aoi"></a>**Área de interesse (Aoi)**: a extensão usada para definir uma área de foco para um mapa ou uma produção de banco de dados.

<a name="asset-tracking"></a>**Acompanhamento de ativos**: o processo de rastrear o local de um ativo, como uma pessoa, um veículo ou algum outro objeto.

<a name="asynchronous-request"></a>**Solicitação assíncrona**: uma solicitação HTTP que abre uma conexão e faz uma solicitação ao servidor que retorna um identificador para a solicitação assíncrona e, em seguida, fecha a conexão. O servidor continua a processar a solicitação e o usuário pode verificar o status usando o identificador. Quando a solicitação terminar o processamento, o usuário pode então fazer o download da resposta. Esse tipo de solicitação é usado para processos de longa execução.

<a name="autocomplete"></a>**Preenchimento automático**: um recurso em um aplicativo que prevê o restante de uma palavra que um usuário está digitando. 

<a name="autosuggest"></a>**Sugestão**automática: um recurso em um aplicativo que prevê possibilidades lógicas para o que o usuário está digitando.

<a name="azure-location-based-services-lbs"></a> **Serviços do Azure Baseados na Localização (LBS)**: nome anterior do mapas do Azure Mapas quando estava em versão prévia.

<a name="azure-active-directory"></a>**Azure Active Directory (AD do Azure)**: o Azure AD é o serviço de gerenciamento de identidade e acesso baseado em nuvem da Microsoft. A integração do Azure AD com o Azure Maps está disponível atualmente em versão prévia para todas as APIs do Azure Maps. O Azure AD dá suporte ao RBAC (controle de acesso baseado em função) para permitir o acesso refinado aos recursos do Azure Maps. Para saber mais sobre a integração do Azure AD do Azure, consulte [Azure Maps e Azure ad](azure-maps-authentication.md) e [gerenciar a autenticação no Azure Maps](how-to-manage-authentication.md).

<a name="azure-maps-key"></a>**Chave do Azure Maps**: consulte [autenticação de chave compartilhada](#shared-key-authentication).

## <a name="b"></a>B

<a name="base-map"></a> **Mapa base**: parte de um aplicativo de mapa que exibe informações de referência do plano de fundo como estradas, referências e limites políticos.

<a name="batch-request"></a> **Solicitação de lote**: processo de combinar várias solicitações em uma única solicitação.

<a name="bearing"></a> **Influência**: direção horizontal de um ponto em relação a outro ponto. Isso é expresso como um ângulo em relação ao norte, de 0 a 360 graus em uma direção horária. 

<a name="boundary"></a>**Limite**: uma linha ou polígono que separa entidades políticas adjacentes, como países/regiões, distritos e propriedades. Um limite é uma linha que pode ou não pode seguir características físicas, como rios, montanhas ou muros.

<a name="bounds"></a> **Limites**: consulte [caixa delimitadora](#bounding-box).

<a name="bounding-box"></a> **Caixa delimitadora**: conjunto de coordenadas usado para representar uma área retangular no mapa. 

## <a name="c"></a>C

<a name="cadastre"></a> **Cadastro**: registro de terra registrada e propriedades. Consulte também [Lote](#parcel).

<a name="camera"></a> **Câmera**: no contexto de um controle de mapa interativo, uma câmera define o campo de visualização dos mapas. O visor da câmera é determinado com base em vários parâmetros de mapa: Centro, nível de zoom, pitch, rumo. 

<a name="centroid"></a> **Centroide**: O centro geométrico de um recurso. O centroide de uma linha seria o ponto médio, enquanto o centroide de um polígono seria seu centro da área.

<a name="choropleth-map"></a>**Mapa coropléticos**: um mapa temática no qual as áreas são sombreadas em proporção à medida de uma variável estatística. Essa variável estatística é exibida no mapa. Por exemplo, colorir o limite de cada estado dos EUA com base em sua população relativa a todos os outros estados.

<a name="concave-hull"></a> **Envoltória côncava**: forma que representa uma geometria côncava possível que inclui todas as formas no conjunto de dados especificado. A forma gerada é semelhante ao encapsulamento de dados com encapsulamento de plástico e, em seguida, aquecendo, assim causando extensões grandes entre os pontos para cavar em direção aos outros pontos de dados.

<a name="consumption-model"></a> **Modelo de consumo**: informações que definem a taxa em que um veículo consome combustível ou eletricidade. Consulte também a [documentação do modelo de consumo](consumption-model.md).

<a name="control"></a> **Controle**: um componente independente ou reutilizável consiste em uma interface gráfica do usuário que define um conjunto de comportamentos para a interface. Por exemplo, um controle de mapa, geralmente é a parte da interface do usuário que carrega um mapa interativo.

<a name="convex-hull"></a> **Envoltória convexa**: forma que representa uma geometria convexa possível que inclui todas as formas no conjunto de dados especificado. A forma gerada é semelhante ao encapsulamento de uma banda elástica ao torno do conjunto de dados.

<a name="coordinate"></a> **Coordenar**: consiste nos valores de longitude e latitude usados para representar um local em um mapa.

<a name="coordinate-system"></a> **Sistema de coordenadas**: uma estrutura de referência usada para definir as posições dos pontos no espaço em dois ou três dimensões.

<a name="country-code"></a>**Código do país**: um identificador exclusivo para um país/região com base no padrão ISO. ISO2 é um código de dois caracteres para um país (por exemplo, EUA), que representa um código de três caracteres ISO3 (por exemplo, EUA).

<a name="country-subdivision"></a>**Subdivisão do país**: uma subdivisão de primeiro nível de um país/região, normalmente conhecida como estado ou província.

<a name="country-secondary-subdivision"></a>**Subdivisão secundária do país**: uma subdivisão de segundo nível de um país/região, normalmente conhecida como um município.

<a name="country-tertiary-subdivision"></a>**Subdivisão terciário de país**: uma subdivisão de terceiro nível de um país/região, normalmente uma área nomeada, como uma à frente.

<a name="cross-street"></a> **Rua transversal**: ponto de interseção de duas ou mais ruas.

<a name="cylindrical-projection"></a> **Projeção cilíndrica**: uma projeção que transforma os pontos de um esferóide ou esfera em um cilindro tangente ou secante. O cilindro, em seguida, é dividido de cima para baixo e combinado em um plano.

## <a name="d"></a>D

<a name="datum"></a> **Dado**: as especificações de referência de uma sistema de medida, um sistema de posições coordenadas em uma superfície (um dado horizontal) ou alturas acima ou abaixo da superfície (um dado vertical).

<a name="dbf-file"></a> **Arquivo DBF**: um formato de arquivo de banco de dados que é usado em combinação com Shapefiles (SHP).

<a name="degree-minutes-seconds-dms"></a> **Segundos de Minutos de Grau (DMS)**: unidade de medida para descrever a latitude e a longitude. Um grau é 1/360<sup>º</sup> de um círculo. Um grau é dividido em 60 minutos e um minuto é dividido em 60 segundos.

<a name="delaunay-triangulation"></a> **Triangulação de Delaunay**: uma técnica para criar uma malha de triângulos contíguos, não sobrepostos de um conjunto de dados de pontos. O círculo circunscrito de cada triângulo não contém pontos do conjunto de dados em seu interior.

<a name="demographics"></a> **Dados demográficos**: características de estatísticas (como idade, taxa de nascimento e renda) de uma população humana.

<a name="destination"></a> **Destino**: um ponto de extremidade ou o local em que alguém está viajando.

<a name="digital-elevation-model-dem"></a> **Modelo de Elevação Digital (DEM)**: conjunto de dados dos valores de elevação relacionados a uma superfície, capturados ao longo de uma área em intervalos regulares usando uma referência comum. DEMs normalmente são usados para representar alívio de terreno.

<a name="dijkstra's-algorithm"></a> **Algoritmo de Dijkstra**: algoritmo que examina a conectividade de uma rede para localizar o caminho mais curto entre dois pontos.

<a name="distance-matrix"></a> **Matriz de distância**: uma matriz que contém informações de tempo e a distância de viagem entre um conjunto de origens e destinos. 

## <a name="e"></a>E

<a name="elevation"></a>**Elevação**: a distância vertical de um ponto ou um objeto acima ou abaixo de uma superfície de referência ou referência. Geralmente, a superfície de referência é o nível médio do mar. A elevação geralmente se refere à altura vertical de terra.

<a name="envelope"></a> **Limites**: consulte [Caixa delimitadora](#bounding-box).

<a name="extended-postal-code"></a> **Código postal estendido**: código postal que pode incluir informações adicionais. Por exemplo, nos EUA, CEPs têm cinco dígitos. Mas, um CEP estendido, conhecido como zip + 4, inclui quatro dígitos adicionais. Esses dígitos adicionais são usados para identificar um segmento geográfico dentro da área de entrega de cinco dígitos, como um bloco de cidade, um grupo de Apartments ou uma caixa de Post Office. Conhecer o segmento geográfico ajuda na classificação e entrega eficientes de emails.

<a name="extent"></a> **Limites**: consulte [Caixa delimitadora](#bounding-box).

## <a name="f"></a>F

<a name="federated-authentication"></a> **Autenticação federada**: método de autenticação que permite que um único logon/mecanismo de autenticação seja usado em vários aplicativos web e móveis. 

<a name="feature"></a> **Recurso**: objeto que combina uma geometria com informações de metadados adicionais. 

<a name="feature-collection"></a> **Coleção de recursos**: coleção de objetos de recurso.

<a name="find-along-route"></a> **Encontrar ao longo da rota**: uma consulta espacial que procura por dados que estão dentro de um desvio especificado de tempo ou a distância de um caminho de rota.

<a name="find-nearby"></a> **Localizar próximo**: uma consulta espacial que procura uma distância linear fixa (como um corvo voa) de um ponto.

<a name="fleet-management"></a> **Gerenciamento de Frota**: O gerenciamento de veículos comerciais, como carros, caminhões, navios e aviões. A gestão de frotas pode incluir uma variedade de funções, como financiamento de veículo, manutenção, telemática (rastreamento e diagnóstico) bem como driver, velocidade, combustível, gerenciamento de segurança e integridade. O gerenciamento de frota é um processo usado por empresas que dependem de transporte em seus negócios. As empresas desejam minimizar os riscos e reduzir os custos gerais com o transporte e a equipe, garantindo, ao mesmo tempo, a conformidade com a legislação governamental.

<a name="free-flow-speed"></a> **Velocidade de fluxo livre**: A velocidade do fluxo livre esperada em condições ideais. Normalmente, o limite de velocidade.

<a name="free-form-address"></a> **Endereço de forma livre**: um endereço completo que é representado como uma única linha de texto.

<a name="fuzzy-search"></a> **Pesquisa difusa**: pesquisa que usa uma cadeia de caracteres de formato livre de texto que pode ser um endereço ou ponto de interesse. 

## <a name="g"></a>G

<a name="geocode"></a> **Geocode**: endereço ou local que foi convertido em uma coordenada que pode ser usada para exibir o local em um mapa. 

<a name="geocoding"></a> **Geocodificação**: também conhecido como encaminhar geocodificação, é o processo de conversão de endereço dos dados de localização em coordenadas. 

<a name="geodesic-path"></a> **Caminho geodésica**: O caminho mais curto entre dois pontos em uma superfície curva. Quando renderizados no Azure Mapas esse caminho é exibido como uma linha curva devido a projeção Mercator.

<a name="geofence"></a>**Limite**geograficamente: uma região geográfica definida que pode ser usada para disparar eventos quando um dispositivo entra ou existe na região.

<a name="geojson"></a> **GeoJSON**: formato comum de arquivo baseado em JSON usado para armazenar dados de vetor geográficos, como pontos, linhas e polígonos. **Observação**: o Azure Mapas usa uma versão estendida do GeoJSON como [documentado aqui](extend-geojson.md).

<a name="geometry"></a> **Geometria**: representa um objeto espacial, como um ponto, linha ou polígono.

<a name="geometrycollection"></a> **GeometryCollection**: coleção de objetos de geometria.

<a name="geopol"></a> **GeoPol**: refere-se a dados confidenciais geopoliticamente, como bordas de território disputadas e nomes de locais.

<a name="georeference"></a> **Georeferenciar**: processo de alinhar dados geográficos ou imagens para um sistema de coordenadas conhecido. Esse processo pode consistir em mudança, girar, dimensionar ou distorcer os dados.

<a name="georss"></a> **GeoRSS**: extensão de um XML para adicionar dados espaciais ao RSS feeds.

<a name="gis"></a> **GIS**: abreviação para "Sistema de Informações Geográficas". Termo comum usado para descrever o setor de mapeamento.

<a name="gml"></a> **GML**: também conhecido como Linguagem de Marcação de Geografia. Uma extensão de arquivo XML para armazenar dados espaciais.

<a name="gps"></a> **GPS**: também conhecido como Global Positioning System, é um sistema de satélites usado para determinar uma posição de dispositivos na Terra. Os satélites em órbita transmiten sinais que um receptor GPS em qualquer lugar na Terra calcula seu próprio local por meio de trilateração.

<a name="gpx"></a> **GPX**: também conhecido como formato de troca GPS, é um formato de arquivo XML normalmente criado a partir de dispositivos GPS.  

<a name="great-circle-distance"></a> **Distância de grande círculo**: distância mais curta entre dois pontos na superfície de uma esfera.

<a name="greenwich-mean-time-gmt"></a> **Horário de Greenwich (GMT)**: hora em que o primeiro meridiano percorre o Observatório Real de Greenwich, Inglaterra.

<a name="guid"></a> **GUID**: identificador globalmente exclusivo. Uma cadeia de caracteres usada para identificar exclusivamente uma interface, a classe, a biblioteca de tipos, a categoria do componente ou o registro.

## <a name="h"></a>H

<a name="haversine-formula"></a> **Fórmula de Haversine**: uma equação comum usada para calcular a distância de grande círculo entre dois pontos em uma esfera.

<a name="hd-maps"></a> **Mapas HD**: também conhecidos como mapas de alta definição, consiste em informações de rede de estrada de alta fidelidade, como as marcas de pista da placa e faróis de direção necessários para impulso autônomo.

<a name="heading"></a> **Cabeçalho**: direção de algo que está apontando ou voltado. Consulte também [Influência](#heading).

<a name="heatmap"></a> **Mapa de calor**: visualização de dados no qual um intervalo de cores representa a densidade de pontos em uma determinada área. Consulte também Mapa temático.

<a name="hybrid-imagery"></a> **Imagens híbridas**: satélite ou imagens aéreas que tem dados de estrada e rótulos sobrepostos.

## <a name="i"></a>I

<a name="iana"></a> **IANA**: sigla Internet Assigned Numbers Authority. Um grupo sem fins lucrativos que supervisiona alocação de endereços IP global.

<a name="isochrone"></a> **Isócrono**: um isócrono define a área em que alguém pode viajar em um tempo especificado para um modo de transporte em qualquer direção de um determinado local. Consulte também [Intervalo Acessível](#reachable-range).

<a name="isodistance"></a> **Isodistância**: dada uma localização, um isócrono define a área em que alguém pode viajar dentro de uma distância especificada para um modo de transporte em qualquer direção. Consulte também [Intervalo Acessível](#reachable-range).

## <a name="k"></a>K

<a name="kml"></a> **KML**: também conhecido como linguagem de marcação de buraco de fechadura, é um formato de arquivo XML comum para armazenar dados geográficos de vetor, como pontos, linhas e polígonos. 

## <a name="l"></a>L

<a name="landsat"></a>**Landsat**: Multispectral, satélites de alta-órbita desenvolvidos pela NASA que coletam imagens de terra. Essa imagem é usada em muitos setores, como agricultura, florestal e cartografia.

<a name="latitude"></a> **Latitude**: A distância angular medido em graus do Equador em uma direção norte ou sul.

<a name="level-of-detail"></a>**Nível de detalhe**: consulte nível de zoom.

<a name="lidar"></a> **Lidar**: acrônimo para detecção e variação de luz. Uma técnica de detecção remota que usa lasers para medir a distância para superfícies refletivas.

<a name="linear-interpolation"></a> **Interpolação linear**: estimativa de um valor desconhecido usando a distância linear entre os valores conhecidos.

<a name="linestring"></a> **LineString**: geometria usada para representar uma linha. Também conhecida como uma polilinha. 

<a name="localization"></a> **Localização**: suporte para diferentes idiomas e culturas.

<a name="logistics"></a> **Logística**: processo de mover as pessoas, veículos, fontes ou ativos de maneira coordenada.

<a name="longitude"></a> **Longitude**: distância angular medida em graus a partir do meridiano principal em uma direção leste ou oeste.

## <a name="m"></a>M

<a name="map-tile"></a> **Peça de mapa**: uma imagem retangular que representa uma partição de uma tela de mapa. Para obter mais informações, consulte [Níveis de Zoom e grade de peças](zoom-levels-and-tile-grid.md).

<a name="marker"></a> **Marcador**: também conhecido como um pin ou uma anotação, é um ícone que representa um local de ponto em um mapa.

<a name="mercator-projection"></a> **Projeção de mercator**: uma projeção cilíndrica de mapa que se tornou a projeção de mapa padrão para fins náuticos por causa de sua capacidade de representar linhas de curso constante, conhecidas como linhas rhumb, como segmentos retos que conservam os ângulos com os meridianos. Todas as projeções de mapa simples distorcem as formas ou os tamanhos do mapa quando comparado com o layout verdadeiro da superfície da Terra. A projeção Mercator exagera áreas longe do Equador, de modo que as áreas pequenas pareçam maiores no mapa de como você aborda os polos. 

<a name="multilinestring"></a> **MultiLineString**: uma geometria que representa uma coleção de objetos de LineString. 

<a name="multipoint"></a> **MultiLineString**: geometria que representa uma coleção de objetos de LineString.

<a name="multipolygon"></a> **MultiPolygon**: geometria que representa uma coleção de objetos de Polígono. Por exemplo, para mostrar o limite do Havaí, cada ilha seria descrita com um polígono. Portanto, o limite do Havaí seria, portanto, um MultiPolygon.

<a name="municipality"></a> **Município**: uma cidade ou município. 

<a name="municipality-subdivision"></a> **Subdivisão de município**: uma subdivisão de um Município, como um ambiente ou o nome de rede local, como "centro".

## <a name="n"></a>N

<a name="navigation-bar"></a> **Barra de navegação**: conjunto de controles em um mapa usado para ajustar o nível de zoom, tom, rotação e alternar a camada do mapa base.

<a name="nearby-search"></a> **Localizar próximo**: consulta espacial que procura uma distância linear fixa (como o voo de um corvo) de um ponto.

<a name="neutral-ground-truth"></a> **Neutral Ground Truth**: mapa que renderiza os rótulos em idioma oficial da região em que ele representa em scripts locais, se disponível.

## <a name="o"></a>O

<a name="origin"></a> **Origem**: ponto de partida ou o local em que um usuário está.

## <a name="p"></a>P

<a name="panning"></a> **Movimento panorâmico**: processo de mover o mapa em qualquer direção, mantendo um nível de zoom constante.

<a name="parcel"></a> **Lote**: um gráfico de terra ou a propriedade de limite.

<a name="pitch"></a> **Tom**: quantidade de inclinação que o mapa tem em relação a vertical, onde 0 é examinar diretamente o mapa.

<a name="point"></a> **Ponto**: geometria que representa uma única posição no mapa. 

<a name="points-of-interest-poi"></a> **Pontos de interesse (POI)**: um ponto de referência comercial ou um lugar comum de interesse.

<a name="polygon"></a> **Polígono**: uma geometria sólida que representa uma área em um mapa. 

<a name="polyline"></a> **Polilinha**: uma geometria usada para representar uma linha. Também conhecida como um LineString. 

<a name="position"></a> **Posição**: longitude, latitude e altitude (x, y, z de coordenadas) de um ponto.

<a name="post-code"></a> **Código postal**: consulte [CEP](#postal-code).

<a name="postal-code"></a>**CEP**: uma série de letras ou números, ou ambos, em um formato específico. O código postal é usado pelo serviço postal de um país/região para dividir áreas geográficas em zonas a fim de simplificar a entrega de emails.

<a name="primary-key"></a>**Chave primária**: a primeira de duas chaves de assinaturas fornecidas para a autenticação de chave compartilhada do Azure Maps. Consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="prime-meridian"></a> **Meridiano primário**: linha de longitude que representa 0 graus de longitude. Em geral, os valores de longitude diminuem quando viajamos em uma direção de WESTERLY até 180 graus e aumentam ao viajar em direções de ponta a 180 graus. 

<a name="prj"></a>**PRJ**: um arquivo de texto que geralmente acompanha um arquivo de forma que contém informações sobre o sistema de coordenadas projetado no qual o conjunto de dados está.

<a name="projection"></a> **Projeção**: sistema de coordenadas projetado com base em uma projeção de mapa como área igual de Mercator, Albers e Robinson. Fornecem a capacidade de mapas de projeto de superfície esférica da Terra em um plano de coordenadas bidimensional de cartesianas. Sistemas de coordenadas projetados, às vezes, são chamados de projeções de mapa.

## <a name="q"></a>Q

<a name="quadkey"></a> **Quadkey**: índice de endereço de base de dados de 4 para uma peça em um sistema de lado a lado quadtree. Para obter mais informações, consulte [Níveis de Zoom e peça de blocos](zoom-levels-and-tile-grid.md).

<a name="quadtree"></a> **Quadtree**: estrutura de dados em que cada nó tem exatamente quatro filhos. O sistema de divisão usada no Azure Maps usa uma estrutura quadtree de modo que, como um usuário se aplique zoom em um nível, cada bloco de mapa é dividido em quatro subblocos.  Para obter mais informações, consulte [Níveis de Zoom e peça de blocos](zoom-levels-and-tile-grid.md).

<a name="queries-per-second-qps"></a> **Consultas por segundo (QPS)**: número de consultas ou solicitações que podem ser feitas a um serviço ou plataforma dentro de um segundo. 

## <a name="r"></a>R

<a name="radial-search"></a> **Pesquisa radial**: consulta espacial que procura uma distância linear fixa (como o voo de um corvo) de um ponto. 

<a name="raster-data"></a> **Dados de varredura**: matriz de células (ou em pixels) organizada em linhas e colunas (ou uma grade) em que cada célula contém um valor que representa informações, como temperatura. Varredura inclui fotografias aéreas digitais, imagens de satélites, imagens digitais e mapas digitalizados.

<a name="raster-layer"></a> **Camada de varredura**: uma camada de peça que consiste em imagens de varredura.

<a name="reachable-range"></a> **Intervalo pesquisável**: um intervalo pesquisável define a área em que alguém pode viajar em um tempo ou distância especificada para um modo de transporte em qualquer direção de um determinado local. Consulte também [Isócrono](#isochrone) e [Isodistância](#isodistance).

<a name="remote-sensing"></a> **Leitura remota**: processo de coletar e interpretar os dados do sensor de uma distância.

<a name="rest-service"></a> **Serviço REST**: a abreviação REST significa transferência representacional de estado. Um serviço REST é um serviço web baseado em URL que se baseia na tecnologia web básica para se comunicar, os métodos mais comuns que estão sendo solicitações HTTP GET e POST. Esses tipos de serviços costumam ser muito mais rápidos e menores do que os tradicionais serviços baseados em SOAP.

<a name="reverse-geocode"></a> **Inverter código geográfico**: processo de pegar uma coordenada e determinar o endereço no qual representa em um mapa.

<a name="reproject"></a> **Reprojetar**: consulte [transformação](#transformation).

<a name="rest-service"></a> **Serviço REST**: abreviação para Transferência de Estado Representacional. Uma arquitetura para a troca de informações entre pontos em um ambiente distribuído, descentralizado. O REST permite que programas em computadores diferentes se comuniquem independentemente de um sistema operacional ou plataforma. Um serviço pode enviar uma solicitação HTTP (Hypertext Transfer Protocol) para um Uniform Resource Locator (URL) e obter dados de volta.

<a name="route"></a> **Rota**: um caminho entre dois ou mais locais, que também pode incluir informações adicionais, como instruções para marcos ao longo da rota.

<a name="requests-per-second-rps"></a> **Solicitações por segundo (RPS)**: consulte [consultas por segundo (QPS)](#queries-per-second-qps). 

<a name="rss"></a> **RSS**: abreviação de Really Simple Syndication, resumo do Site do Framework de descrição de recurso (RDF) ou Rich Site Summary, dependendo da fonte. Formato XML estruturado simples para o compartilhamento de conteúdo entre diferentes sites da Web. Documentos do RSS incluem elementos de metadados de chave, como autor, data, título, uma breve descrição e um link de hipertexto. Essa informação ajuda um usuário (ou um serviço de editor RSS) a decidir quais materiais que valem a pena mais investigação.

## <a name="s"></a>S

<a name="satellite-imagery"></a> **Imagens de satélites**: imagens que foram capturadas por planos e satélites apontando diretamente para baixo.

<a name="secondary-key"></a>**Chave secundária**: a segunda de duas chaves de assinaturas fornecidas para a autenticação de chave compartilhada do Azure Maps. Consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="shapefile-shp"></a> **Shapefile (SHP)**: também conhecido como um Shapefile ESRI, é um formato de armazenamento de dados de vetor para armazenar o local, forma e atributos de recursos geográficos. Um shapefile é armazenado em um conjunto de arquivos relacionados.

<a name="shared-key-authentication"></a>**Autenticação de chave compartilhada**: a autenticação de chave compartilhada depende da passagem de chaves geradas pela conta do Azure Maps com cada solicitação para mapas do Azure. Essas chaves costumam ser chamadas de chaves de assinatura. É recomendável que as chaves sejam regeneradas regularmente para segurança. Duas chaves são fornecidas para que você possa manter conexões usando uma chave ao regenerar a outra. Ao regenerar suas chaves, você precisará atualizar os aplicativos que acessam essa conta para que usem as novas chaves. Para saber mais sobre a autenticação do Azure Maps, consulte [Azure Maps e Azure ad](azure-maps-authentication.md) e [gerenciar a autenticação no Azure Maps](how-to-manage-authentication.md).

<a name="software-development-kit-sdk"></a> **Kit de desenvolvimento de software (SDK)**: coleção de documentação, código de exemplo e aplicativos de exemplo para ajudar um desenvolvedor a usar uma API para criar aplicativos.

<a name="spherical-mercator-projection"></a> **Projeção de Mercator esférica**: consulte [Mercator Web](#web-mercator). 

<a name="spatial-query"></a> **Consulta espacial**: solicitação feita para um serviço que executa uma operação espacial. Como uma pesquisa radial ou ao longo de uma pesquisa de rota.

<a name="spatial-reference"></a> **Referência espacial**: baseado em coordenadas, regional, sistema local ou global usado para localizar entidades geográficas com precisão. Define o sistema de coordenadas usado para relacionar as coordenadas de mapa para locais no mundo real. As referências espaciais garantem que os dados espaciais de diferentes camadas, ou fontes, possam ser integrados para exibição ou análise precisa. O Azure Mapas usa o [EPSG:3857](https://epsg.io/3857) sistema de referência coordenada e o WGS 84 para entrada de dados geométricos.

<a name="sql-spatial"></a> **SQL espacial**: refere-se à funcionalidade espacial incorporada ao SQL Azure e SQL Server 2008 e acima. Essa funcionalidade espacial também está disponível como uma biblioteca .NET que pode ser usada independentemente do SQL Server. Para obter mais informações, veja [Documentação do ExpressRoute (Microsoft SQL Server)](https://docs.microsoft.com/sql/relational-databases/spatial/spatial-data-sql-server) para obter mais informações.

<a name="subscription-key"></a>**Chave de assinatura**: consulte [autenticação de chave compartilhada](#shared-key-authentication).

<a name="synchronous-request"></a> **Solicitação síncrona**: abre uma conexão de uma solicitação HTTP e aguarda uma resposta. Os navegadores limitam o número de solicitações HTTP simultâneas que podem ser feitas a partir de uma página. Se várias solicitações síncronas de execução longa forem feitas ao mesmo tempo, esse limite poderá ser atingido. As solicitações serão atrasadas até que uma das outras solicitações seja concluída.

## <a name="t"></a>T

<a name="telematics"></a> **Telemática**: enviar, receber e armazenar informações por meio de dispositivos de telecomunicações em conjunto com o controle efetivo sobre objetos remotos. 

<a name="temporal-data"></a> **Dados temporais**: dados referem-se especificamente a horários ou datas. Dados temporais podem se referir a eventos discretos, como quedas de raios; mover objetos, como trens; ou observações repetidas, como contagens de sensores de tráfego.

<a name="terrain"></a> **Terreno**: uma área de terra tendo uma característica específica, como terreno arenoso ou montanhoso.

<a name="thematic-maps"></a> **Mapas temáticos**: um mapa temático é um mapa simples feito para refletir um tema sobre uma área geográfica. Um cenário comum para esse tipo de mapa é colorir as regiões administrativas, como países/regiões com base em alguma métrica de dados.

<a name="tile-layer"></a> **Camada de peça**: camada exibida pelo montagem de blocos de mapa (seções retangulares) em uma camada contínua. As peças são qualquer peça de imagem ou peças de vetor. Camadas de bloco de varredura normalmente são renderizadas antecipadamente e são armazenadas como imagens em um servidor. Camadas de bloco de varredura podem usar um grande espaço de armazenamento. As camadas de bloco de vetor são renderizadas quase em tempo real dentro do aplicativo cliente. Portanto, os requisitos de armazenamento do lado do servidor são menores para camadas de bloco de vetor.

<a name="time-zone"></a> **Fuso horário**: uma região de todo o mundo que observa um horário padrão uniforme para fins legais, comerciais e sociais. Os fusos horários tendem a seguir os limites de países/regiões e suas subdivisãos.

<a name="transaction"></a> **Transação**: o Azure Mapas usa um modelo de licenciamento transacional onde;

- Uma transação é criada para cada 15 peças de mapa ou tráfego solicitados.
- Uma transação é criada para cada chamada à API para um dos serviços no Azure Maps. Pesquisa e roteamento são exemplos de serviço do Azure Maps.

<a name="transformation"></a> **Transformação**: O processo de conversão de dados entre diferentes sistemas de coordenadas geográficas. Por exemplo, você terá alguns dados que foram capturados no Reino Unido e com base no sistema de coordenadas geográficas OSGB 1936. O Azure Mapas usa o [EPSG:3857](https://epsg.io/3857) sistema de referência coordenada e o WGS 84 para entrada de dados geométricos. Como tal, para exibir os dados corretamente, precisará ter suas coordenadas transformadas de um sistema para outro.

<a name="traveling-salesmen-problem-tsp"></a> **Problema do Caixeiro Viajante (TSP)**: um problema de circuito Hamiltoniano em que um vendedor deve localizar a maneira mais eficiente para visitar uma série de limites, em seguida, retornar para o local inicial.  

<a name="trilateration"></a>**Trirecentes**: o processo de determinar a posição de um ponto na superfície da terra, com relação a dois outros pontos, medindo as distâncias entre os três pontos.

<a name="turn-by-turn-navigation"></a> **Navegação curva a curva**: a próxima manobra se aproxima de um aplicativo que fornece instruções de rota para cada etapa de uma rota dos usuários.

## <a name="v"></a>V

<a name="vector-data"></a> **Dados de vetor**: coordenadas com base em dados que são representados como pontos, linhas ou polígonos.

<a name="vector-tile"></a> **Bloco de vetor**: especificação de dados abertos para armazenar dados de vetor geoespaciais usando o mesmo sistema lado a lado como o controle de mapa. Consulte também [camada de peça lado a lado](#tile-layer).

<a name="vehicle-routing-problem-vrp"></a>**VRP (problema de roteamento de veículo)**: uma classe de problemas, na qual um conjunto de rotas ordenadas para uma frota de veículos é calculado enquanto leva em consideração como um conjunto de restrições. Essas restrições podem incluir janelas de tempo de entrega, vários recursos de rota e restrições de duração de viagem.

<a name="voronoi-diagram"></a>**Diagrama Voronoi**: uma partição de espaço em áreas, ou células, que envolvem um conjunto de objetos geométricos, geralmente apontam os recursos. Essas células ou polígonos, devem satisfazer os critérios para os triângulos de Delaunay. Todos os locais dentro de uma área são mais próximos para o objeto que ele envolva que para qualquer outro objeto no conjunto. Diagramas de Voronoi geralmente são usados para delinear as áreas de influência em torno de características geográficas. 

## <a name="w"></a>W

<a name="waypoint"></a> **Localizador**: um ponto de referência é um local geográfico especificado definido por longitude e latitude que é usado para fins de navegação. Geralmente usado para representar um ponto em que alguém navega por meio de uma rota.

<a name="waypoint-optimization"></a> **Otimização do marco**: processo de reorganização de um conjunto de marcos para minimizar o tempo de viagem ou a distância necessário para passar por todos os marcos fornecidos. Dependendo da complexidade da otimização, essa otimização é muitas vezes conhecida como problema de venda ou [problema de roteamento de veículo](#vehicle-routing-problem-vrp)em [viagem](#traveling-salesmen-problem-tsp) .

<a name="web-map-service-wms"></a> **Mapa de serviço (WMS) de Web**: WMS é um padrão de Consórcio Geográfico Aberto (OGC) que define os serviços de mapa baseado em imagem. Serviços WMS fornecem imagens de mapa para áreas específicas dentro de um mapa sob demanda. As imagens incluem simbologia pré-renderizada e podem ser renderizadas em um dos vários estilos nomeados se definidas pelo serviço.

<a name="web-mercator"></a>**Mercator Web**: também conhecido como projeção de Mercator esférica. É uma ligeira variante da projeção Mercator, usada principalmente em programas de mapeamento baseados na Web. Usa as mesmas fórmulas como a projeção Mercator padrão usados para mapas de pequena escala. No entanto, o Mercator da Web usa as fórmulas esféricas em todas as escalas, mas mapas de Mercator de grande escala normalmente usam a forma dados elipsoidais da projeção. A discrepância é imperceptível na escala global, mas faz com que os mapas das áreas locais se desviem ligeiramente do verdadeiro dados elipsoidais Mercator Maps, na mesma escala.

<a name="wgs84"></a> **WGS84**: um conjunto de constantes usado para relacionar as coordenadas espaciais para locais na superfície do mapa. A referência de WGS84 é o padrão usado por mais provedores de mapeamento online e os dispositivos GPS. O Azure Mapas usa o [EPSG:3857](https://epsg.io/3857) sistema de referência coordenada e o WGS 84 para entrada de dados geométricos.

## <a name="z"></a>Z

<a name="z-coordinate"></a> **A coordenada Z**: consulte [Altitude](#altitude). 

<a name="zip-code"></a> **Código postal**: consulte [CEP](#postal-code).

<a name="Zoom level"></a> **Nível de zoom**: especifica o nível de detalhe e quanto do mapa é visível. Quando ampliado até o nível 0, o mapa do mundo inteiro geralmente estará visível. No entanto, o mapa mostrará detalhes limitados, como nomes de país/região, bordas e nomes de oceano. Depois de ampliar o próximo nível 17, o mapa exibirá uma área de alguns blocos da cidade com informações detalhadas da estrada. No Azure Maps, o nível de zoom mais alto é 22. Para obter mais informações, consulte a documentação [níveis de zoom e grade de blocos](zoom-levels-and-tile-grid.md) .

