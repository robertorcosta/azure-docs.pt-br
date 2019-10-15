---
title: Rotear veículos elétricos usando o Azure Notebooks (Python) | Microsoft Docs
description: Roteie veículos elétricos usando as APIs de roteamento dos Azure Mapas e do Azure Notebooks.
author: walsehgal
ms.author: v-musehg
ms.date: 10/01/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: 9cc7fc1ba8c7f55700505ea8fca0dea4f836e333
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243293"
---
# <a name="route-electric-vehicles-by-using-azure-notebooks-python"></a>Rotear veículos elétricos usando o Azure Notebooks (Python)

Os Azure Mapas são um portfólio de APIs de serviço geoespacial que são nativamente integradas ao Azure. Com essas APIs, os desenvolvedores, as empresas e os ISVs podem criar aplicativos com reconhecimento de localização e soluções de IoT, mobilidade, logística e acompanhamento de ativos. 

As APIs REST dos Azure Mapas podem ser chamadas em linguagens como Python e R para habilitar a análise de dados geoespaciais e cenários de machine learning. Os Azure Mapas oferecem um conjunto robusto de [APIs de roteamento](https://docs.microsoft.com/rest/api/maps/route) que permitem aos usuários calcular rotas entre vários pontos de dados. Os cálculos se baseiam em várias condições, como tipo de veículo ou área acessível. 

Neste tutorial, você verá um cenário usado para ajudar um motorista de veículo elétrico cuja carga da bateria está fraca a encontrar o posto de recarga mais próximo possível, de acordo com o tempo de condução partindo da localização do veículo.

Neste tutorial, você irá:

> [!div class="checklist"]
> * Criar e executar um Jupyter Notebook no [Azure Notebooks](https://docs.microsoft.com/azure/notebooks) na nuvem.
> * Chamar as APIs REST dos Azure Mapas no Python.
> * Pesquisar por um intervalo alcançável com base no modelo de consumo do veículo elétrico.
> * Pesquisar postos de recarga de veículos elétricos dentro do intervalo acessível ou isócrono.
> * Renderizar o limite do intervalo alcançável e as estações de carregamento em um mapa.
> * Localizar e visualizar uma rota para o posto de recarga de veículos elétricos mais próximo de acordo com o tempo de condução.


## <a name="prerequisites"></a>Pré-requisitos 

Para concluir este tutorial, primeiro, é necessário criar uma conta dos Azure Mapas e obter a chave primária (chave de assinatura). 

Para criar uma assinatura da conta dos Azure Mapas no tipo de preço S1, siga as instruções em [Gerenciar sua conta dos Azure Mapas](https://docs.microsoft.com/azure/azure-maps/how-to-manage-account-keys#create-a-new-account). 

Para obter a chave de assinatura primária para a sua conta, siga as instruções em [Pesquisar pontos de interesse próximos usando os Azure Mapas](./tutorial-search-location.md#getkey).

## <a name="create-an-azure-notebook"></a>Criar um Azure Notebook

Para acompanhar este tutorial, você precisará criar um projeto do Azure Notebook e baixar e executar o arquivo do Jupyter Notebook. O arquivo do notebook contém o código Python, que implementa o cenário neste tutorial. Para criar um projeto do Azure Notebook e fazer upload do documento do Jupyter Notebook nele, faça o seguinte:

1. Vá até [Azure Notebooks](https://notebooks.azure.com) e entre. Para saber mais, confira [Início Rápido: Entrar e definir uma ID de usuário](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks).
1. Na parte superior da página de seu perfil público, selecione **Meus Projetos**.

    ![O botão Meus Projetos](./media/tutorial-ev-routing/myproject.png)

1. Na página **Meus Projetos**, selecione **Novo Projeto**.
 
   ![O botão Novo Projeto](./media/tutorial-ev-routing/create-project.png)

1. No painel **Criar Projeto**, insira um nome e uma ID de projeto.
 
    ![O painel Criar Projeto](./media/tutorial-ev-routing/create-project-window.png)

1. Selecione **Criar**.

1. Após a criação do projeto, baixe o [arquivo de documento do Jupyter Notebook](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/EVrouting.ipynb) do [repositório do Jupyter Notebook dos Azure Mapas](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook). 

1. Na lista de projetos da página **Meus Projetos**, selecione o projeto e, em seguida, selecione **Upload** para fazer upload do arquivo de documento do Jupyter Notebook. 

    ![fazer upload do notebook](./media/tutorial-ev-routing/upload-notebook.png)

1. Faça upload do arquivo do computador e, em seguida, selecione **Concluído**.

1. Depois que o upload for concluído com êxito, o arquivo será exibido na página do projeto. Selecione o arquivo para abri-lo como um Jupyter Notebook.

Para ajudar você a entender melhor a funcionalidade implementada no arquivo de notebook, recomendamos que você execute o código no notebook, uma célula por vez. Execute o código em cada célula selecionando o botão **Executar** na parte superior do aplicativo do notebook.

  ![O botão Executar](./media/tutorial-ev-routing/run.png)

## <a name="install-project-level-packages"></a>Instalar pacotes do nível do projeto

Para executar o código no notebook, instale os pacotes no nível do projeto fazendo o seguinte:

1. Baixe o arquivo [*requirements.txt*](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook/blob/master/AzureMapsJupyterSamples/Tutorials/EV%20Routing%20and%20Reachable%20Range/requirements.txt) do [repositório do Jupyter Notebook nos Azure Mapas](https://github.com/Azure-Samples/Azure-Maps-Jupyter-Notebook) e, em seguida, faça upload dele no projeto.
1. No painel do projeto, selecione **Configurações do Projeto**. 
1. No painel **Configurações do Projeto**, selecione a guia **Ambiente** e, em seguida, selecione **Adicionar**.
1. Em **Etapas de Instalação do Ambiente**, faça o seguinte:   
    a. Na primeira lista suspensa, selecione **Requirements.txt**.  
    b. Na segunda lista suspensa, selecione o arquivo *requirements.txt*.  
    c. Na terceira lista suspensa, selecione **Python Versão 3.6** como a versão.
1. Clique em **Salvar**.

    ![Instalar Pacotes](./media/tutorial-ev-routing/install-packages.png)

## <a name="load-the-required-modules-and-frameworks"></a>Carregar as estruturas e os módulos necessários

Para carregar todas as estruturas e todos os módulos necessários, execute o seguinte script:

```python
import time
import aiohttp
import urllib.parse
from IPython.display import Image, display
```

## <a name="request-the-reachable-range-boundary"></a>Solicitar o limite do intervalo acessível

Em nosso cenário, uma empresa de entrega de pacotes tem alguns veículos elétricos na frota. Durante o dia, os veículos elétricos precisam ser recarregados sem a necessidade de retornar ao depósito. Sempre que a carga restante atual cai para menos de uma hora (ou seja, a bateria está com pouca carga), você pesquisa um conjunto de postos de recarga que estão dentro de um intervalo acessível e obtém as informações de limite para esse intervalo. 

Como a empresa prefere usar rotas que exigem um equilíbrio entre economia e velocidade, o routeType solicitado é *eco*. O script a seguir chama a [API Obter Intervalo de Rotas](https://docs.microsoft.com/rest/api/maps/route/getrouterange) do serviço de roteamento dos Azure Mapas usando parâmetros para o modelo de consumo do veículo. Em seguida, o script analisa a resposta para criar um objeto de polígono do formato GeoJSON, que representa o intervalo máximo acessível do carro.

Para determinar os limites do intervalo acessível do veículo elétrico, execute o script na seguinte célula:

```python
subscriptionKey = "Your Azure Maps primary subscription key"
currentLocation = [34.028115,-118.5184279]
session = aiohttp.ClientSession()

# Parameters for the vehicle consumption model 
travelMode = "car"
vehicleEngineType = "electric"
currentChargeInkWh=45
maxChargeInkWh=80
timeBudgetInSec=550
routeType="eco"
constantSpeedConsumptionInkWhPerHundredkm="50,8.2:130,21.3"


# Get boundaries for the electric vehicle's reachable range.
routeRangeResponse = await (await session.get("https://atlas.microsoft.com/route/range/json?subscription-key={}&api-version=1.0&query={}&travelMode={}&vehicleEngineType={}&currentChargeInkWh={}&maxChargeInkWh={}&timeBudgetInSec={}&routeType={}&constantSpeedConsumptionInkWhPerHundredkm={}"
                                              .format(subscriptionKey,str(currentLocation[0])+","+str(currentLocation[1]),travelMode, vehicleEngineType, currentChargeInkWh, maxChargeInkWh, timeBudgetInSec, routeType, constantSpeedConsumptionInkWhPerHundredkm))).json()

polyBounds = routeRangeResponse["reachableRange"]["boundary"]

for i in range(len(polyBounds)):
    coordList = list(polyBounds[i].values())
    coordList[0], coordList[1] = coordList[1], coordList[0]
    polyBounds[i] = coordList

polyBounds.pop()
polyBounds.append(polyBounds[0])

boundsData = {
               "geometry": {
                 "type": "Polygon",
                 "coordinates": 
                   [
                      polyBounds
                   ]
                }
             }
```

## <a name="search-for-electric-vehicle-charging-stations-within-the-reachable-range"></a>Pesquisar postos de recarga de veículos elétricos dentro do intervalo acessível

Depois de determinar o intervalo acessível (isócrono) para o veículo elétrico, pesquise os postos de recarga nesse intervalo. 

O script a seguir chama a [API Pós-pesquisa dentro da Geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry) dos Azure Mapas. Ela pesquisa os postos de recarga de veículos elétricos dentro dos limites do intervalo máximo acessível do carro e, em seguida, analisa a resposta para uma matriz de localizações acessíveis.

Para pesquisar os postos de recarga de veículos elétricos dentro do intervalo acessível, execute o seguinte script:

```python
# Search for electric vehicle stations within reachable range.
searchPolyResponse = await (await session.post(url = "https://atlas.microsoft.com/search/geometry/json?subscription-key={}&api-version=1.0&query=electric vehicle station&idxSet=POI&limit=50".format(subscriptionKey), json = boundsData)).json() 

reachableLocations = []
for loc in range(len(searchPolyResponse["results"])):
                location = list(searchPolyResponse["results"][loc]["position"].values())
                location[0], location[1] = location[1], location[0]
                reachableLocations.append(location)
```

## <a name="upload-the-reachable-range-and-charging-points-to-azure-maps-data-service"></a>Faça upload do intervalo acessível e os pontos de recarga no Serviço de Dados dos Azure Mapas

O ideal é visualizar em um mapa os postos de recarga e os limites para o intervalo máximo acessível do veículo elétrico. Para fazer isso, faça upload dos dados de limite e os dados dos postos de recarga como objetos GeoJSON no Serviço de Dados dos Azure Mapas usando a [API de Upload de Dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview). 

Para fazer upload dos dados de pontos de recarga e de limite no Serviço de Dados dos Azure Mapas, execute as duas seguintes células:

```python
rangeData = {
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "properties": {},
      "geometry": {
        "type": "Polygon",
        "coordinates": [
          polyBounds
        ]
      }
    }
  ]
}

# Upload the range data to Azure Maps Data Service.
uploadRangeResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = rangeData)

rangeUdidRequest = uploadRangeResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getRangeUdid = await (await session.get(rangeUdidRequest)).json()
    if 'udid' in getRangeUdid:
        break
    else:
        time.sleep(0.2)
rangeUdid = getRangeUdid["udid"]
```

```python
poiData = {
    "type": "FeatureCollection",
    "features": [
      {
        "type": "Feature",
        "properties": {},
        "geometry": {
            "type": "MultiPoint",
            "coordinates": reachableLocations
        }
    }
  ]
}

# Upload the electric vehicle charging station data to Azure Maps Data Service.
uploadPOIsResponse = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = poiData)

poiUdidRequest = uploadPOIsResponse.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    getPoiUdid = await (await session.get(poiUdidRequest)).json()
    if 'udid' in getPoiUdid:
        break
    else:
        time.sleep(0.2)
poiUdid = getPoiUdid["udid"]
```

## <a name="render-the-charging-stations-and-reachable-range-on-a-map"></a>Renderizar os postos de recarga e o intervalo acessível em um mapa

Depois que os dados forem carregados para o Serviço de Dados, chame o [serviço Obter Imagem do Mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage) dos Azure Mapas para renderizar os pontos de recarga e o limite máximo acessível na imagem do mapa estático executando o seguinte script:

```python
# Get boundaries for the bounding box.
def getBounds(polyBounds):
    maxLon = max(map(lambda x: x[0], polyBounds))
    minLon = min(map(lambda x: x[0], polyBounds))

    maxLat = max(map(lambda x: x[1], polyBounds))
    minLat = min(map(lambda x: x[1], polyBounds))
    
    # Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
    lonBuffer = (maxLon-minLon)*0.1
    minLon -= lonBuffer
    maxLon += lonBuffer

    latBuffer = (maxLat-minLat)*0.1
    minLat -= latBuffer
    maxLat += latBuffer
    
    return [minLon, maxLon, minLat, maxLat]

minLon, maxLon, minLat, maxLat = getBounds(polyBounds)

path = "lcff3333|lw3|la0.80|fa0.35||udid-{}".format(rangeUdid)
pins = "custom|an15 53||udid-{}||https://raw.githubusercontent.com/Azure-Samples/AzureMapsCodeSamples/master/AzureMapsCodeSamples/Common/images/icons/ev_pin.png".format(poiUdid)

encodedPins = urllib.parse.quote(pins, safe='')

# Render the range and electric vehicle charging points on the map.
staticMapResponse =  await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&pins={}&path={}&bbox={}&zoom=12".format(subscriptionKey,encodedPins,path,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

poiRangeMap = await staticMapResponse.content.read()

display(Image(poiRangeMap))
```

![Um mapa que mostra o intervalo de localização](./media/tutorial-ev-routing/location-range.png)


## <a name="find-the-optimal-charging-station"></a>Encontrar o posto de recarga ideal

Depois que você determinar todos os possíveis postos de recarga dentro do intervalo acessível, o ideal será saber a qual deles se pode chegar no menor período de tempo. 

O script a seguir chama a [API de Roteamento de Matriz](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview) dos Azure Mapas, que retorna para a localização especificada do veículo o tempo de viagem e a distância para cada posto de recarga. O script na próxima célula analisa a resposta para localizar o posto de recarga acessível mais próximo com relação ao tempo.

Para encontrar o posto de recarga acessível mais próximo ao qual se pode chegar no menor período de tempo, execute o script na seguinte célula:

```python
locationData = {
            "origins": {
              "type": "MultiPoint",
              "coordinates": [[currentLocation[1],currentLocation[0]]]
            },
            "destinations": {
              "type": "MultiPoint",
              "coordinates": reachableLocations
            }
         }

# Get the travel time and distance to each specified charging station.
searchPolyRes = await (await session.post(url = "https://atlas.microsoft.com/route/matrix/json?subscription-key={}&api-version=1.0&routeType=shortest&waitForResults=true".format(subscriptionKey), json = locationData)).json()

distances = []
for dist in range(len(reachableLocations)):
    distances.append(searchPolyRes["matrix"][0][dist]["response"]["routeSummary"]["travelTimeInSeconds"])

minDistLoc = []
minDistIndex = distances.index(min(distances))
minDistLoc.extend([reachableLocations[minDistIndex][1], reachableLocations[minDistIndex][0]])
closestChargeLoc = ",".join(str(i) for i in minDistLoc)
```

## <a name="calculate-the-route-to-the-closest-charging-station"></a>Calcular a rota para o posto de recarga mais próximo

Agora que você encontrou o posto de recarga mais próximo, chame a [API Obter Trajetos de Rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections) para solicitar a rota detalhada partindo da localização atual do veículo elétrico ao posto de recarga.

Para obter a rota para o posto de recarga e analisar a resposta a fim de criar um objeto GeoJSON que representa a rota, execute o script na seguinte célula:

```python
# Get the route from the electric vehicle's current location to the closest charging station. 
routeResponse = await (await session.get("https://atlas.microsoft.com/route/directions/json?subscription-key={}&api-version=1.0&query={}:{}".format(subscriptionKey, str(currentLocation[0])+","+str(currentLocation[1]), closestChargeLoc))).json()

route = []
for loc in range(len(routeResponse["routes"][0]["legs"][0]["points"])):
                location = list(routeResponse["routes"][0]["legs"][0]["points"][loc].values())
                location[0], location[1] = location[1], location[0]
                route.append(location)

routeData = {
         "type": "LineString",
         "coordinates": route
     }
```

## <a name="visualize-the-route"></a>Visualizar a rota

Para ajudar a visualizar a rota, primeiro faça upload dos dados de rota como um objeto GeoJSON no Serviço de Dados dos Azure Mapas usando a [API de Upload de Dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview) dos Azure Mapas. Em seguida, chame o serviço de renderização, a [API Obter Imagem do Mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage), para renderizar a rota no mapa e visualizá-la.

Para obter uma imagem para a rota renderizada no mapa, execute o seguinte script:

```python
# Upload the route data to Azure Maps Data Service.
routeUploadRequest = await session.post("https://atlas.microsoft.com/mapData/upload?subscription-key={}&api-version=1.0&dataFormat=geojson".format(subscriptionKey), json = routeData)

udidRequestURI = routeUploadRequest.headers["Location"]+"&subscription-key={}".format(subscriptionKey)

while True:
    udidRequest = await (await session.get(udidRequestURI)).json()
    if 'udid' in udidRequest:
        break
    else:
        time.sleep(0.2)

udid = udidRequest["udid"]

destination = route[-1]

destination[1], destination[0] = destination[0], destination[1]

path = "lc0f6dd9|lw6||udid-{}".format(udid)
pins = "default|codb1818||{} {}|{} {}".format(str(currentLocation[1]),str(currentLocation[0]),destination[1],destination[0])


# Get boundaries for the bounding box.
minLat, maxLat = (float(destination[0]),currentLocation[0]) if float(destination[0])<currentLocation[0] else (currentLocation[0], float(destination[0]))
minLon, maxLon = (float(destination[1]),currentLocation[1]) if float(destination[1])<currentLocation[1] else (currentLocation[1], float(destination[1]))

# Buffer the bounding box by 10 percent to account for the pixel size of pins at the ends of the route.
lonBuffer = (maxLon-minLon)*0.1
minLon -= lonBuffer
maxLon += lonBuffer

latBuffer = (maxLat-minLat)*0.1
minLat -= latBuffer
maxLat += latBuffer

# Render the route on the map.
staticMapResponse = await session.get("https://atlas.microsoft.com/map/static/png?api-version=1.0&subscription-key={}&&path={}&pins={}&bbox={}&zoom=16".format(subscriptionKey,path,pins,str(minLon)+", "+str(minLat)+", "+str(maxLon)+", "+str(maxLat)))

staticMapImage = await staticMapResponse.content.read()

await session.close()
display(Image(staticMapImage))
```

![Um mapa que mostra a rota](./media/tutorial-ev-routing/route.png)

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a chamar as APIs REST dos Azure Mapas diretamente e a visualizar os dados dos Azure Mapas usando o Python.

Para explorar as APIs dos Azure Mapas que são usadas neste tutorial, confira:

* [Obter intervalo de rotas](https://docs.microsoft.com/rest/api/maps/route/getrouterange)
* [Pesquisa POST dentro da geometria](https://docs.microsoft.com/rest/api/maps/search/postsearchinsidegeometry)
* [Upload de dados](https://docs.microsoft.com/rest/api/maps/data/uploadpreview)
* [Renderizar – obter imagem do mapa](https://docs.microsoft.com/rest/api/maps/render/getmapimage)
* [Matrix de rota POST](https://docs.microsoft.com/rest/api/maps/route/postroutematrixpreview)
* [Obter Trajeto de Rota](https://docs.microsoft.com/rest/api/maps/route/getroutedirections)

Para obter uma lista completa das APIs REST dos Azure Mapas, confira [APIs REST dos Azure Mapas](https://docs.microsoft.com/azure/azure-maps/#reference).

Para saber mais sobre o Azure Notebooks, confira [Azure Notebooks](https://docs.microsoft.com/azure/notebooks).
