---
title: Requisitos do pacote de desenho no Criador do Azure Mapas
description: Saiba mais sobre os requisitos do pacote de desenho para converter os arquivos de design da sua instalação para mapear dados usando o Serviço de conversão do Azure Mapas
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 1ba9edba97ce89cede54287076e50eb587af10f3
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86242467"
---
# <a name="drawing-package-requirements"></a>Requisitos do pacote de desenho

O [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion) permite converter pacotes de desenho carregados em dados de mapa. Este artigo descreve os requisitos de pacote de desenho para a API de Conversão. Para exibir um pacote de exemplo, você pode baixar o [Pacote de desenho](https://github.com/Azure-Samples/am-creator-indoor-data-examples) de amostra.

## <a name="prerequisites"></a>Pré-requisitos

O pacote de desenho inclui desenhos salvos no formato DWG, que é o formato de arquivo nativo para o AutoCAD® software da Autodesk, uma [marca comercial da Autodesk, Inc](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12).

Você pode usar qualquer software CAD para produzir os desenhos no pacote de desenho.  

O [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion) converte o pacote de desenho em dados de mapa.  O Serviço de conversão foi desenvolvido e testado usando o formato de arquivo DWG do AutoCAD. `AC1032` é a versão do formato interno dos arquivos DWG. Você é encorajado a selecionar `AC1032` como a versão interna do formato de arquivo DWG.  

Glossário dos termos usados neste documento.

| Termo  | Definição |
|:-------|:------------|
| Camada | Uma camada de DWG do AutoCAD.|
| Nível | Uma área de uma construção com uma elevação definida. Por exemplo, o andar de uma construção. |
| Xref  |Um arquivo no formato de arquivo do DWG (.dwg) do AutoCAD anexado ao desenho primário como uma referência externa.  |
| Recurso | Um objeto que combina uma geometria com informações de metadados adicionais. |
| Classes de recurso | Um blueprint comum dos recursos. Por exemplo, uma Unidade é uma classe de recurso e um escritório é um recurso. |

## <a name="drawing-package-structure"></a>Estrutura do pacote de desenho

Um pacote de desenho é um arquivo .zip que contém os seguintes arquivos:

* Arquivos DWG com formato de arquivo DWG do AutoCAD.
* Um arquivo _manifest.json_ para uma única instalação.

Os arquivos DWG podem ser organizados de qualquer forma dentro da pasta, mas o arquivo de manifesto deve ficar no diretório raiz da pasta. A pasta deve ser compactada em um único arquivo morto, com uma extensão .zip. As seções a seguir detalham os requisitos para arquivos DWG, o arquivo de manifesto e o conteúdo desses arquivos.  

## <a name="dwg-files-requirements"></a>Requisitos para arquivos DWG

Apenas um arquivo DWG é necessário para cada nível da instalação. Os dados do nível devem estar contidos em um único arquivo DWG. Todas as referências externas (_xrefs_) devem estar ligadas ao desenho pai. Além disso, cada arquivo DWG:

* Deve definir as camadas _Exterior_ e _Unidade_. Como opção, pode definir as seguintes camadas opcionais: _Parede_, _Porta_, _UnitLabel_, _Zona_ e _ZoneLabel_.
* Não deve conter recursos de vários níveis.
* Não deve conter recursos de várias instalações.

O [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion) pode extrair as seguintes classes de recurso de um arquivo DWG:

* Levels
* Unidades
* Zonas
* Aberturas
* Paredes
* Penetrações verticais

Todos os trabalhos de conversão resultam em um conjunto mínimo de categorias padrão: sala, estrutura.parede, abertura.porta, zona e instalação. As categorias adicionais são para cada Nome de categoria referenciado por objetos.  

Uma camada do DWG deve conter recursos de uma única classe. As classes não devem compartilhar uma camada. Por exemplo, unidades e paredes não podem compartilhar uma camada.

As camadas do DWG também devem seguir os seguintes critérios:

* As origens de desenhos para todos os arquivos DWG devem se alinhar à mesma latitude e longitude.
* Cada nível deve estar na mesma orientação dos outros níveis.
* Os polígonos com interseção automática serão reparados automaticamente, e o [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion) emitirá um aviso. É recomendável inspecionar os resultados reparados manualmente, pois eles podem não corresponder aos resultados esperados.

Todas as entidades de camada devem ser um dos seguintes tipos: linha, polilinha, polígono, arco circular, círculo, texto (linha única). Todos os outros tipos de entidade serão ignorados.

A tabela a seguir descreve os tipos de entidade e recursos com suporte de cada camada. Se uma camada contiver tipos de entidade sem suporte, o [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion) ignorará essas entidades.  

| Camada | Tipos de entidade | Recursos |
| :----- | :-------------------| :-------
| [Exterior](#exterior-layer) | Polígono, polilinha (fechada), círculo | Levels
| [Unidade](#unit-layer) |  Polígono, polilinha (fechada), círculo | Penetrações verticais, unidades
| [Parede](#wall-layer)  | Polígono, polilinha (fechada), círculo | Não aplicável. Para obter mais informações, consulte [Camada Parede](#wall-layer).
| [Porta](#door-layer) | Polígono, polilinha, linha, CircularArc, círculo | Aberturas
| [Zona](#zone-layer) | Polígono, polilinha (fechada), círculo | Zona
| [UnitLabel](#unitlabel-layer) | Texto (linha única) | Não aplicável. Essa camada só pode adicionar propriedades aos recursos da unidade a partir da camada Unidades. Para obter mais informações, consulte [Camada UnitLabel](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Texto (linha única) | Não aplicável. Essa camada só pode adicionar propriedades a recursos de zona da ZonesLayer. Para obter mais informações, consulte [Camada ZoneLabel](#zonelabel-layer)

As seções a seguir detalham os requisitos de cada camada.

### <a name="exterior-layer"></a>Camada Exterior

O arquivo DWG de cada nível deve conter uma camada para definir o perímetro desse nível. Essa camada é conhecida como a camada Exterior. Por exemplo, se uma instalação contiver dois níveis, ela precisará ter dois arquivos DWG, com uma camada Exterior para cada arquivo.

Independentemente de quantos desenhos de entidade estiverem na camada Exterior, o [conjunto de dados resultante da instalação](tutorial-creator-indoor-maps.md#create-a-feature-stateset) conterá apenas **um** recurso de nível de para cada arquivo DWG. Adicionalmente:

* Os exteriores devem ser desenhados como polígono, polilinha (fechada) e círculo.

* Os exteriores podem se sobrepor, mas serão dissolvidos em uma geometria.

Se a camada contiver várias polilinhas sobrepostas, elas serão dissolvidas em um único recurso de nível. Como alternativa, se a camada contiver várias polilinhas não sobrepostas, o recurso de nível resultante terá uma representação multipolígono.

Um exemplo de camada Exterior pode ser visto como a camada CONTORNO de tópicos no [pacote de desenho de amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Camada Unidade

O arquivo DWG de cada nível deve definir uma camada que contenha unidades.  As unidades são espaços navegáveis da construção, como escritórios, corredores, escadas e elevadores. A camada Unidades deve atender aos seguintes requisitos:

* As unidades devem ser desenhadas como polígono, polilinha (fechada) e círculo.
* As unidades devem estar dentro dos limites do perímetro externo da instalação.
* As unidades não devem se sobrepor parcialmente.
* As unidades não devem conter nenhuma geometria com autointerseção.

 Nomeie uma unidade criando um objeto de texto na camada _UnitLabel_, depois coloque o objeto dentro dos limites da unidade. Para obter mais informações, consulte [Camada UnitLabel](#unitlabel-layer).

Um exemplo da camada Unidades pode ser visto como a camada UNIDADES no [pacote de desenho de amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Camada Parede

O arquivo DWG de cada nível pode conter uma camada que defina as extensões físicas de paredes, colunas e outras estruturas da construção.

* As paredes devem ser desenhadas como polígono, polilinha (fechada) e círculo.
* As camadas de parede só devem conter uma geometria que seja interpretada como estrutura da construção.

Um exemplo da camada Paredes pode ser visto como a camada PAREDES no [pacote de desenho de amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Camada Porta

Você pode incluir uma camada do DWG que contenha portas. Cada porta deve sobrepor a borda de uma unidade da camada Unidade.

As aberturas de portas em um conjunto de dados do Azure Mapas são representadas como um segmento de linha única que sobrepõe vários limites de unidade. As etapas a seguir são executadas para converter a geometria na camada Porta para recursos de abertura em um conjunto de dados.

![Etapas para gerar aberturas](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Camada Zona

O arquivo DWG de cada nível pode conter uma camada Zona que defina as extensões físicas das zonas. Uma zona pode ser um espaço interno vazio ou um quintal.

* As zonas devem ser desenhadas como polígono, polilinha (fechada) e círculo.
* As zonas podem se sobrepor.
* As zonas devem estar dentro ou fora do perímetro externo da instalação.

Para nomear uma zona, crie um objeto de texto na camada _zoneLabel_ e colocando o objeto de texto dentro dos limites da zona. Para obter mais informações, consulte [Camada ZoneLabel](#zonelabel-layer).

Um exemplo da camada Zonas pode ser visto como a camada ZONAS no [pacote de desenho de amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>Camada UnitLabel

O arquivo DWG de cada nível pode conter uma camada de rótulo de unidade. A camada de rótulo de unidade adiciona uma propriedade name às unidades extraídas da camada Unidade. As unidades com uma propriedade name podem ter detalhes adicionais especificados no arquivo de manifesto.

* Os rótulos de unidade devem ser entidades de texto de linha única.
* Os rótulos de unidade devem estar dentro dos limites de sua unidade.
* As unidades não devem conter várias entidades de texto na camada de rótulos de unidade.

Um exemplo da camada UnitLabel pode ser visto como a camada UNITLABEL no [pacote de desenho de amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>Camada ZoneLabel

O arquivo DWG de cada nível pode conter uma camada de rótulo de zona. Essa camada adiciona uma propriedade name às zonas extraídas da camada Zona. As zonas com uma propriedade name podem ter detalhes adicionais especificados no arquivo de manifesto.

* Os rótulos de zonas devem ser entidades de texto de linha única.
* Os rótulos de zonas devem estar dentro dos limites de sua zona.
* As zonas não devem conter várias entidades de texto na camada de rótulos de zona.

Um exemplo da camada ZoneLabel pode ser visto como a camada ZONELABELS no [pacote de desenho de amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Requisitos do arquivo de manifesto

A pasta zip deve conter um arquivo de manifesto no nível raiz do diretório, e o arquivo deve ser nomeado como **manifest.json**. Ele descreve os arquivos DWG para permitir que o [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion) analise seu conteúdo. Apenas os arquivos identificados pelo manifesto serão ingeridos. Serão ignorados os arquivos que estiverem na pasta zip, mas não estiverem listados corretamente no manifesto.

Os caminhos de arquivo, no objeto **buildingLevels** do arquivo de manifesto, devem estar relacionados à raiz da pasta zip. O nome do arquivo DWG deve corresponder exatamente ao nome do nível da instalação. Por exemplo, um arquivo DWG para o nível “Porão” seria “Porão.dwg”. Um arquivo DWG para o nível 2 seria nomeado como “nível_2.dwg”. Use um sublinhado se o nome do seu nível tiver um espaço.

Embora haja requisitos para usar os objetos de manifesto, nem todos os objetos são necessários. A tabela a seguir mostra os objetos obrigatórios e opcionais para a versão 1.1 do [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion).

| Objeto | Obrigatório | Descrição |
| :----- | :------- | :------- |
| version | true |Versão do esquema de manifesto. Atualmente, há suporte apenas para a versão 1,1.|
| directoryInfo | true | Descreve as informações geográficas e de contato da instalação. Também pode ser usado para descrever informações geográficas de um ocupante ou de um contato. |
| buildingLevels | true | Especifica os níveis das construções e os arquivos que contêm o design dos níveis. |
| georeference | true | Contém informações geográficas numéricas do desenho da instalação. |
| dwgLayers | true | Lista os nomes das camadas, e cada camada lista os nomes de seus próprios recursos. |
| unitProperties | false | Pode ser usado para inserir metadados adicionais dos recursos de unidade. |
| zoneProperties | false | Pode ser usado para inserir metadados adicionais dos recursos de zona. |

As seções a seguir detalham os requisitos de cada objeto.

### <a name="directoryinfo"></a>directoryInfo

| Propriedade  | type | Obrigatório | Descrição |
|-----------|------|----------|-------------|
| name      | string | true   |  Nome da construção. |
| streetAddress|    string |    false    | Endereço da construção. |
|unit     | string    |  false    |  Unidade na construção. |
| localidade |    string |    false |    Nome de uma área, vizinhança ou região. Por exemplo, "Overlake" ou "Distrito Central". A localidade não faz parte do endereço de correspondência. |
| adminDivisions |    Matriz JSON de strings |    false     | Uma matriz que contém designações de endereço (país, estado, cidade) ou (país, prefeitura, cidade, município). Use códigos de países ISO 3166 e códigos de estados/regiões ISO 3166-2. |
| postalCode |    string    | false    | O código de classificação de correspondência. |
| hoursOfOperation |    string |     false | Adere ao formato [horário de funcionamento OSM](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification). |
| phone    | string |    false |    Número de telefone associado à construção. Deve incluir o código do país. |
| site    | string |    false    | Site associado à construção. Deve começar com http ou https. |
| nonPublic |    bool    | false | Sinalizador especificando se a construção está aberta para o público. |
| anchorLatitude | numeric |    false | Latitude de uma âncora de instalação (pino). |
| anchorLongitude | numeric |    false | Longitude de uma âncora de instalação (pino). |
| anchorHeightAboveSeaLevel  | numeric | false | Altura em metros do térreo da instalação em relação ao nível do mar. |
| defaultLevelVerticalExtent | numeric | false | Altura padrão (espessura) de um nível dessa instalação e que deve ser usada quando o `verticalExtent` de um nível estiver indefinido. |

### <a name="buildinglevels"></a>buildingLevels

O objeto `buildingLevels` contém uma matriz JSON de níveis de construções.

| Propriedade  | Type | Necessária | Descrição |
|-----------|------|----------|-------------|
|levelName    |string    |true |    Nome do nível descritivo. Por exemplo:  1º andar, lobby, estacionamento azul, porão e assim por diante.|
|ordinal | inteiro |    true | O ordinal é usado para determinar a ordem vertical dos níveis. Cada instalação deve ter um nível com o ordinal 0. |
|heightAboveFacilityAnchor | numeric | false |    Altura do nível acima da âncora em metros. |
| verticalExtent | numeric | false | A altura em metros do chão até o teto (espessura) do nível. |
|nome do arquivo |    string |    true |    Caminho do sistema de arquivos do desenho do CAD para um nível da construção. Ele estar relacionado à raiz do arquivo zip da construção. |

### <a name="georeference"></a>georeference

| Propriedade  | Type | Obrigatório | Descrição |
|-----------|------|----------|-------------|
|lat    | numeric |    true |    Representação decimal da latitude em graus na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator (`EPSG:3857`).|
|lon    |numeric|    true|    Representação decimal da longitude em graus na origem do desenho da instalação. As coordenadas de origem devem estar no WGS84 Web Mercator (`EPSG:3857`). |
|angle|    numeric|    true|   O ângulo no sentido horário, em graus, entre o norte verdadeiro e o eixo vertical (Y) do desenho.   |

### <a name="dwglayers"></a>dwgLayers

| Propriedade  | Type | Obrigatório | Descrição |
|-----------|------|----------|-------------|
|exterior    |Matriz de cadeia de caracteres|    true|    Nomes de camada(s) que definem o perfil de construção exterior.|
|unit|    Matriz de cadeia de caracteres|    true|    Nomes de camada(s) que definem unidades.|
|parede|    Matriz de cadeias de caracteres    |false|    Nomes de camada(s) que definem paredes.|
|porta    |Matriz de cadeias de caracteres|    false   | Nomes de camada(s) que definem portas.|
|unitLabel    |Matriz de cadeias de caracteres|    false    |Nomes de camada(s) que definem nomes de unidades.|
|zona | Matriz de cadeias de caracteres    | false    | Nomes de camada(s) que definem zonas.|
|zoneLabel | Matriz de cadeias de caracteres |     false |    Nomes de camada(s) que definem nomes de zonas.|

### <a name="unitproperties"></a>unitProperties

O objeto `unitProperties` contém uma matriz JSON das propriedades da unidade.

| Propriedade  | Type | Obrigatório | Descrição |
|-----------|------|----------|-------------|
|unitName    |string    |true    |Nome da unidade a ser associada a esse registro `unitProperty`. Esse registro só é válido quando um rótulo correspondente a `unitName` for encontrado na(s) camada(s) `unitLabel`. |
|categoryName|    string|    false    |Nome da categoria. Para obter uma lista completa das categorias, consulte [categorias](https://aka.ms/pa-indoor-spacecategories). |
|navigableBy| Matriz de cadeias de caracteres |    false    |Indica os tipos de agentes de navegação que podem atravessar a unidade. Por exemplo, “pedestres”. Essa propriedade informará os recursos de wayfinding.  Os valores permitidos são `pedestrian`, `wheelchair`, `machine`, `bicycle`, `automobile`, `hiredAuto`, `bus`, `railcar`, `emergency`, `ferry`, `boat` e `disallowed`.|
|routeThroughBehavior|    string|    false    |O comportamento da rota da unidade. Os valores permitidos são `disallowed`, `allowed` e `preferred`. O valor padrão é `allowed`.|
|occupants    |Matriz de objetos directoryInfo |false    |Lista de ocupantes da unidade. |
|nameAlt|    string|    false|    Nome alternativo da unidade. |
|nameSubtitle|    string    |false|    Subtítulo da unidade. |
|addressRoomNumber|    string|    false|    Número da sala, da unidade, do apartamento ou da suíte da unidade.|
|verticalPenetrationCategory|    string|    false| Quando essa propriedade for definida, o recurso resultante será uma penetração vertical (VRT) em vez de uma unidade. VRTs podem ser usados para navegar até outros recursos de VRT nos níveis acima ou abaixo dele. Penetração vertical é um nome de [Categoria](https://aka.ms/pa-indoor-spacecategories). Se essa propriedade for definida, a propriedade categoryName será substituída por verticalPenetrationCategory. |
|verticalPenetrationDirection|    string|    false    |Se `verticalPenetrationCategory` for definida, como opção, defina a direção válida da viagem. Os valores permitidos são `lowToHigh`, `highToLow`, `both` e `closed`. O valor padrão é `both`.|
| nonPublic | bool | false | Indica se a unidade está aberta ao público. |
| isRoutable | bool | false | Quando definida como `false`, não é possível navegar até ou pela unidade. O valor padrão é `true`. |
| isOpenArea | bool | false | Permite que o agente de navegação Insira a unidade sem a necessidade de uma abertura anexada à unidade. Por padrão, esse valor é definido como `true` para unidades sem aberturas; `false` para unidades com aberturas.  Definir manualmente `isOpenArea` como `false` em uma unidade sem aberturas resulta em um aviso. Isso ocorre porque a unidade resultante não poderá ser acessada por um agente de navegação.|

### <a name="the-zoneproperties-object"></a>O objeto zoneProperties

O objeto `zoneProperties` contém uma matriz JSON das propriedades da zona.

| Propriedade  | Type | Obrigatório | Descrição |
|-----------|------|----------|-------------|
|zoneName        |string    |true    |Nome da zona a ser associada ao registro `zoneProperty`. Esse registro só é válido quando um rótulo correspondente a `zoneName` for encontrado na camada `zoneLabel` da zona.  |
|categoryName|    string|    false    |Nome da categoria. Para obter uma lista completa das categorias, consulte [categorias](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    string|    false    |Nome alternativo da zona.  |
|zoneNameSubtitle|    string |    false    |Subtítulo da zona. |
|zoneSetId|    string |    false    | Defina a ID para estabelecer a relação entre várias zonas para que elas possam ser consultadas ou selecionadas como um grupo. Por exemplo, zonas que abrangem vários níveis. |

### <a name="sample-drawing-package-manifest"></a>Manifesto do pacote de desenho de amostra

A seguir, há um arquivo de manifesto de amostra para o pacote de desenho de amostra. Para baixar o pacote inteiro, clique em [pacote de desenho de amostra](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

#### <a name="manifest-file"></a>Arquivo de manifesto

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddresss": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonWheelchairAccessible": false, 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

Depois que o pacote de desenho atender aos requisitos, você poderá usar o [Serviço de conversão do Azure Mapas](https://docs.microsoft.com/rest/api/maps/conversion) para converter o pacote em um conjunto de dados de mapa. Em seguida, você pode usar o conjunto de dados para gerar um mapa interno usando o módulo de Mapas internos. Leia os artigos a seguir para saber mais sobre como usar o módulo de Mapas internos:

> [!div class="nextstepaction"]
>[Criador de mapas internos](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Tutorial: criar um mapa interno do Criador](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Estilos dinâmicos para mapas internos](indoor-map-dynamic-styling.md)
