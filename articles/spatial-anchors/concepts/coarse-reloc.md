---
title: Relocalização grosseira
description: Aprenda a usar a relocalização grosseira para encontrar âncoras perto de você.
author: bucurb
manager: dacoghl
services: azure-spatial-anchors
ms.author: bobuc
ms.date: 09/18/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 4c1604eaad1ebdedf6a360a647fe5b9f95c829c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844387"
---
# <a name="coarse-relocalization"></a>Relocalização grosseira

A relocalização grosseira é um recurso que fornece uma resposta inicial à pergunta: *Onde está meu dispositivo agora / Que conteúdo devo observar?* A resposta não é precisa, mas sim está na forma: *Você está perto dessas âncoras; tente localizar uma delas.*

A relocalização grosseira funciona associando várias leituras de sensores no dispositivo com a criação e a consulta de âncoras. Para cenários externos, os dados do sensor são tipicamente a posição GPS (Global Positioning System) do dispositivo. Quando o GPS não está disponível ou não é confiável (como dentro de casa), os dados do sensor consistem nos pontos de acesso WiFi e nos faróis Bluetooth ao alcance. Todos os dados do sensor coletados contribuem para a manutenção de um índice espacial que é usado pelas âncoras espaciais do Azure para determinar rapidamente as âncoras que estão a aproximadamente 100 metros do seu dispositivo.

A rápida pesquisa de âncoras habilitadas pela relocalização grosseira simplifica o desenvolvimento de aplicações apoiadas por coleções em escala mundial de âncoras (digamos, milhões de âncoras geodistribuídas). A complexidade do gerenciamento de âncoras está toda escondida, permitindo que você se concentre mais em sua lógica de aplicação incrível. Toda a âncora de trabalho pesado é feita para você nos bastidores por Âncoras Espaciais Azure.

## <a name="collected-sensor-data"></a>Dados do sensor coletados

Os dados do sensor que você pode enviar para o serviço de âncora são um dos seguintes:

* Posição GPS: latitude, longitude, altitude.
* Força de sinal de pontos de acesso WiFi ao alcance.
* Força do sinal dos faróis Bluetooth ao alcance.

Em geral, seu aplicativo precisará adquirir permissões específicas do dispositivo para acessar dados GPS, WiFi ou BLE. Além disso, alguns dos dados do sensor acima não estão disponíveis por design em determinadas plataformas. Para dar conta dessas situações, a coleta de dados do sensor é opcional e é desligada por padrão.

## <a name="set-up-the-sensor-data-collection"></a>Configure a coleta de dados do sensor

Vamos começar criando um provedor de impressão digital do sensor e tornando a sessão ciente disso:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Create the sensor fingerprint provider
sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider = sensorProvider;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Create the sensor fingerprint provider
ASAPlatformLocationProvider *sensorProvider;
sensorProvider = [[ASAPlatformLocationProvider alloc] init];

// Create and configure the session
cloudSpatialAnchorSession = [[ASACloudSpatialAnchorSession alloc] init];

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.locationProvider = sensorProvider;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Create the sensor fingerprint provider
var sensorProvider: ASAPlatformLocationProvider?
sensorProvider = ASAPlatformLocationProvider()

// Create and configure the session
cloudSpatialAnchorSession = ASACloudSpatialAnchorSession()

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession!.locationProvider = sensorProvider
```

# <a name="java"></a>[Java](#tab/java)

```java
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = new PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = new CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.setLocationProvider(sensorProvider);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Create the sensor fingerprint provider
std::shared_ptr<PlatformLocationProvider> sensorProvider;
sensorProvider = std::make_shared<PlatformLocationProvider>();

// Create and configure the session
cloudSpatialAnchorSession = std::make_shared<CloudSpatialAnchorSession>();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession->LocationProvider(sensorProvider);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)
```cpp
// Create the sensor fingerprint provider
PlatformLocationProvider sensorProvider = PlatformLocationProvider();

// Create and configure the session
cloudSpatialAnchorSession = CloudSpatialAnchorSession();

// Inform the session it can access sensor data from your provider
cloudSpatialAnchorSession.LocationProvider(sensorProvider);
```
---

Em seguida, você precisará decidir quais sensores você gostaria de usar para relocalização grosseira. Esta decisão é específica para a aplicação que você está desenvolvendo, mas as recomendações na tabela a seguir devem lhe dar um bom ponto de partida:


|             | Dentro | Ao ar livre |
|-------------|---------|----------|
| Gps         | Desativado | Por |
| Wi-Fi        | Por | Em (opcional) |
| Sinalizadores BLE | Ligado (opcional com ressalvas, veja abaixo) | Desativado |


### <a name="enabling-gps"></a>Habilitando GPS

Supondo que seu aplicativo já tenha permissão para acessar a posição GPS do dispositivo, você pode configurar âncoras espaciais do Azure para usá-lo:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.GeoLocationEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.geoLocationEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.geoLocationEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setGeoLocationEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->GeoLocationEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.GeoLocationEnabled(true);
```

---

Ao usar o GPS em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* assíncrono e baixa frequência (menos de 1 Hz).
* não confiável / barulhento (em média 7 m desvio padrão).

Em geral, tanto o sistema operacional do dispositivo quanto as âncoras espaciais Azure farão alguma filtragem e extrapolação no sinal GPS bruto na tentativa de mitigar esses problemas. Este processamento extra requer tempo adicional para convergência, então para melhores resultados você deve tentar:

* criar um provedor de impressão digital sensor o mais cedo possível em sua aplicação
* manter o provedor de impressão digital do sensor vivo entre várias sessões
* compartilhar o provedor de impressão digital do sensor entre várias sessões

Se você planeja usar o provedor de impressão digital do sensor fora de uma sessão de âncora, certifique-se de iniciá-lo antes de solicitar estimativas do sensor. Por exemplo, o código a seguir cuidará da atualização da posição do seu dispositivo no mapa em tempo real:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(
        x: geoPose.Longitude,
        y: geoPose.Latitude,
        radius: geoPose.HorizontalError);
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
// Game about to start, start tracking the sensors
[sensorProvider start];

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    ASAGeoLocation *geoPose = [sensorProvider getLocationEstimate];

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError);
}

// Game ended, no need to track the sensors anymore
[sensorProvider stop];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
// Game about to start, start tracking the sensors
sensorProvider?.start()

// Game loop
while m_isRunning
{
    // Get the GPS estimate
    var geoPose: ASAGeoLocation?
    geoPose = sensorProvider?.getLocationEstimate()

    // Paint it on the map
    drawCircle(geoPose.longitude, geoPose.latitude, geoPose.horizontalError)
}

// Game ended, no need to track the sensors anymore
sensorProvider?.stop()
```

# <a name="java"></a>[Java](#tab/java)

```java
// Game about to start, start tracking the sensors
sensorProvider.start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.getLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.getLongitude(), geoPose.getLatitude(), geoPose.getHorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.stop();
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
// Game about to start, start tracking the sensors
sensorProvider->Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    std::shared_ptr<GeoLocation> geoPose = sensorProvider->GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose->Longitude(), geoPose->Latitude(), geoPose->HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider->Stop();
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
// Game about to start, start tracking the sensors
sensorProvider.Start();

// Game loop
while (m_isRunning)
{
    // Get the GPS estimate
    GeoLocation geoPose = sensorProvider.GetLocationEstimate();

    // Paint it on the map
    drawCircle(geoPose.Longitude(), geoPose.Latitude(), geoPose.HorizontalError());
}

// Game ended, no need to track the sensors anymore
sensorProvider.Stop();
```

---

### <a name="enabling-wifi"></a>Habilitando wi-fi

Supondo que seu aplicativo já tenha permissão para acessar o estado WiFi do dispositivo, você pode configurar âncoras espaciais do Azure para usá-lo:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.WifiEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.wifiEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.wifiEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setWifiEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->WifiEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors()
sensors.WifiEnabled(true);
```

---

Ao usar wi-fi em seu aplicativo, tenha em mente que as leituras fornecidas pelo hardware são tipicamente:

* assíncrono e baixa frequência (menos de 0,1 Hz).
* potencialmente estrangulado no nível do SO.
* não confiável / barulhento (em média 3-dBm desvio padrão).

As âncoras espaciais do Azure tentarão construir um mapa de força de sinal WiFi filtrado durante uma sessão na tentativa de mitigar esses problemas. Para obter melhores resultados, você deve tentar:

* criar a sessão bem antes de colocar a primeira âncora.
* manter a sessão viva pelo maior tempo possível (ou seja, criar todas as âncoras e consultas em uma sessão).

### <a name="enabling-bluetooth-beacons"></a>Ativando sinalizadores Bluetooth

Supondo que seu aplicativo já tenha permissão para acessar o estado Bluetooth do dispositivo, você pode configurar âncoras espaciais do Azure para usá-lo:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.BluetoothEnabled = true;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.bluetoothEnabled = true;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let sensors = locationProvider?.sensors
sensors.bluetoothEnabled = true
```

# <a name="java"></a>[Java](#tab/java)

```java
SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setBluetoothEnabled(true);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->BluetoothEnabled(true);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
SensorCapabilities sensors = sensorProvider.Sensors();
sensors.BluetoothEnabled(true);
```

---

Os beacons são tipicamente dispositivos versáteis, onde tudo - incluindo UUIDs e endereços MAC - pode ser configurado. Essa flexibilidade pode ser problemática para as âncoras espaciais do Azure, pois considera que os faróis são identificados exclusivamente por seus UUIDs. Não garantir essa singularidade provavelmente causará buracos de minhoca espaciais. Para obter melhores resultados, você deve:

* atribuir UUIDs exclusivos aos seus faróis.
* implantá-los - tipicamente em um padrão regular, como uma grade.
* passar a lista de UUIDs de farol exclusivos para o provedor de impressão digital do sensor:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
sensorProvider.Sensors.KnownBeaconProximityUuids = new[]
{
    "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1",
    "a63819b9-8b7b-436d-88ec-ea5d8db2acb0",
    . . .
};
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
NSArray *uuids = @[@"22e38f1a-c1b3-452b-b5ce-fdb0f39535c1", @"a63819b9-8b7b-436d-88ec-ea5d8db2acb0"];

ASASensorCapabilities *sensors = locationProvider.sensors;
sensors.knownBeaconProximityUuids = uuids;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let uuids = [String]()
uuids.append("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1")
uuids.append("a63819b9-8b7b-436d-88ec-ea5d8db2acb0")

let sensors = locationProvider?.sensors
sensors.knownBeaconProximityUuids = uuids
```

# <a name="java"></a>[Java](#tab/java)

```java
String uuids[] = new String[2];
uuids[0] = "22e38f1a-c1b3-452b-b5ce-fdb0f39535c1";
uuids[1] = "a63819b9-8b7b-436d-88ec-ea5d8db2acb0";

SensorCapabilities sensors = sensorProvider.getSensors();
sensors.setKnownBeaconProximityUuids(uuids);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
std::vector<std::string> uuids;
uuids.push_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.push_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

const std::shared_ptr<SensorCapabilities>& sensors = sensorProvider->Sensors();
sensors->KnownBeaconProximityUuids(uuids);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
std::vector<winrt::hstring> uuids;
uuids.emplace_back("22e38f1a-c1b3-452b-b5ce-fdb0f39535c1");
uuids.emplace_back("a63819b9-8b7b-436d-88ec-ea5d8db2acb0");

SensorCapabilities sensors = sensorProvider.Sensors();
sensors.KnownBeaconProximityUuids(uuids);
```

---

As âncoras espaciais do Azure só rastrearão os faróis Bluetooth que estão na lista de UIDs de proximidade de farol conhecido. Os beacons maliciosos programados para ter UUIDs listados como permitidos ainda podem afetar negativamente a qualidade do serviço. Por essa razão, você deve usar beacons apenas em espaços curados onde você pode controlar sua implantação.

## <a name="querying-with-sensor-data"></a>Consultando com dados do sensor

Depois de criar âncoras com dados de sensores associados, você pode começar a recuperá-los usando as leituras de sensores relatadas pelo seu dispositivo. Você não é mais obrigado a fornecer ao serviço uma lista de âncoras conhecidas que você espera encontrar - em vez disso, você apenas adite o serviço saber a localização do seu dispositivo conforme relatado por seus sensores a bordo. As âncoras espaciais do Azure descobrirão o conjunto de âncoras perto do seu dispositivo e tentarão combiná-las visualmente.

Para ter consultas, use os dados do sensor, inicie criando critérios de "dispositivo próximo":

# <a name="c"></a>[C #](#tab/csharp)

```csharp
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters = 5;

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount = 25;

anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.NearDevice = nearDeviceCriteria;
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
ASANearDeviceCriteria *nearDeviceCriteria = [[ASANearDeviceCriteria alloc] init];

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0f;

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25;

ASAAnchorLocateCriteria *anchorLocateCriteria = [[ASAAnchorLocateCriteria alloc] init];
anchorLocateCriteria.nearDevice = nearDeviceCriteria;
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let nearDeviceCriteria = ASANearDeviceCriteria()!

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.distanceInMeters = 5.0

// Cap the number of anchors returned
nearDeviceCriteria.maxResultCount = 25

let anchorLocateCriteria = ASAAnchorLocateCriteria()!
anchorLocateCriteria.nearDevice = nearDeviceCriteria
```

# <a name="java"></a>[Java](#tab/java)

```java
NearDeviceCriteria nearDeviceCriteria = new NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.setDistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.setMaxResultCount(25);

AnchorLocateCriteria anchorLocateCriteria = new AnchorLocateCriteria();
anchorLocateCriteria.setNearDevice(nearDeviceCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto nearDeviceCriteria = std::make_shared<NearDeviceCriteria>();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria->DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria->MaxResultCount(25);

auto anchorLocateCriteria = std::make_shared<AnchorLocateCriteria>();
anchorLocateCriteria->NearDevice(nearDeviceCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
NearDeviceCriteria nearDeviceCriteria = NearDeviceCriteria();

// Choose a maximum exploration distance between your device and the returned anchors
nearDeviceCriteria.DistanceInMeters(5.0f);

// Cap the number of anchors returned
nearDeviceCriteria.MaxResultCount(25);

// Set the session's locate criteria
anchorLocateCriteria = AnchorLocateCriteria();
anchorLocateCriteria.NearDevice(nearDeviceCriteria);
```

---

O `DistanceInMeters` parâmetro controla até onde vamos explorar o gráfico âncora para recuperar conteúdo. Assuma, por exemplo, que você tenha povoado algum espaço com âncoras a uma densidade constante de 2 a cada metro. Além disso, a câmera do seu dispositivo está observando uma única âncora e o serviço localizou-a com sucesso. Você provavelmente está interessado em recuperar todas as âncoras que você colocou nas proximidades em vez da única âncora que você está observando no momento. Supondo que as âncoras que você colocou estejam conectadas em um gráfico, o serviço pode recuperar todas as âncoras próximas para você seguindo as bordas do gráfico. A quantidade de gráficos transversais é controlada por; `DistanceInMeters` você terá todas as âncoras conectadas ao que você localizou, `DistanceInMeters`que estão mais perto do que .

Tenha em mente que `MaxResultCount` grandes valores para pode afetar negativamente o desempenho. Defina-o como um valor sensato para sua aplicação.

Finalmente, você precisará dizer à sessão para usar o visual baseado em sensores:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

# <a name="objc"></a>[ObjC](#tab/objc)

```objc
[cloudSpatialAnchorSession createWatcher:anchorLocateCriteria];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
cloudSpatialAnchorSession!.createWatcher(anchorLocateCriteria)
```

# <a name="java"></a>[Java](#tab/java)

```java
cloudSpatialAnchorSession.createWatcher(anchorLocateCriteria);
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
cloudSpatialAnchorSession->CreateWatcher(anchorLocateCriteria);
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
cloudSpatialAnchorSession.CreateWatcher(anchorLocateCriteria);
```

---

## <a name="expected-results"></a>Resultados esperados

Os dispositivos GPS de nível de consumo são tipicamente bastante imprecisos. Um estudo realizado por [Zandenbergen e Barbeau (2011)][6] relata que a precisão mediana dos telefones celulares com GPS assistido (A-GPS) é de cerca de 7 metros - um valor muito grande a ser ignorado! Para explicar esses erros de medição, o serviço trata as âncoras como distribuições de probabilidade no espaço GPS. Como tal, uma âncora é agora a região do espaço que provavelmente (ou seja, com mais de 95% de confiança) contém sua verdadeira e desconhecida posição GPS.

O mesmo raciocínio é aplicado ao consultar com GPS. O dispositivo é representado como outra região de confiança espacial em torno de sua posição GPS verdadeira e desconhecida. Descobrir âncoras próximas traduz-se em simplesmente encontrar as âncoras com regiões de confiança *próximas o suficiente* à região de confiança do dispositivo, conforme ilustrado na imagem abaixo:

![Seleção de candidatos âncoras com GPS](media/coarse-reloc-gps-separation-distance.png)

A precisão do sinal GPS, tanto na criação da âncora como durante as consultas, tem uma grande influência sobre o conjunto de âncoras devolvidas. Em contraste, consultas baseadas em WiFi/beacons considerarão todas as âncoras que tenham pelo menos um ponto de acesso/farol em comum com a consulta. Nesse sentido, o resultado de uma consulta baseada em WiFi/beacons é determinado principalmente pela faixa física dos pontos de acesso/balizas, e obstruções ambientais.

A tabela abaixo estima o espaço de busca esperado para cada tipo de sensor:

| Sensor      | Raio de espaço de busca (aprox.) | Detalhes |
|-------------|:-------:|---------|
| Gps         | 20 m - 30 m | Determinado pela incerteza do GPS entre outros fatores. Os números relatados são estimados para a precisão mediana do GPS de telefones celulares com A-GPS, que é de 7 metros. |
| Wi-Fi        | 50 m - 100 m | Determinado pela faixa dos pontos de acesso sem fio. Depende da freqüência, força do transmissor, obstruções físicas, interferências, e assim por diante. |
| Sinalizadores BLE |  70 m | Determinado pelo alcance do farol. Depende da freqüência, força de transmissão, obstruções físicas, interferências, e assim por diante. |

## <a name="per-platform-support"></a>Suporte por plataforma

A tabela a seguir resume os dados do sensor coletados em cada uma das plataformas suportadas, juntamente com quaisquer ressalvas específicas da plataforma:


|             | HoloLens | Android | iOS |
|-------------|----------|---------|-----|
| Gps         | N/D | Suportado através de APIs [do LocationManager][3] (tanto GPS quanto NETWORK) | Suportado através de APIs [cllocationmanager][4] |
| Wi-Fi        | Suportado a uma taxa de aproximadamente uma varredura a cada 3 segundos |  Com suporte. Começando com a API nível 28, as varreduras WiFi são aceleradas para 4 chamadas a cada 2 minutos. A partir do Android 10, o estrangulamento pode ser desativado no menu de configurações do Desenvolvedor. Para obter mais informações, consulte a [documentação][5]do Android . | N/A - sem API pública |
| Sinalizadores BLE | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] | Limitado a [Eddystone][1] e [iBeacon][2] |

## <a name="next-steps"></a>Próximas etapas

Use relocalização grosseira em um aplicativo.

> [!div class="nextstepaction"]
> [Unity](../how-tos/set-up-coarse-reloc-unity.md)

> [!div class="nextstepaction"]
> [Objective-C](../how-tos/set-up-coarse-reloc-objc.md)

> [!div class="nextstepaction"]
> [Swift](../how-tos/set-up-coarse-reloc-swift.md)

> [!div class="nextstepaction"]
> [Java](../how-tos/set-up-coarse-reloc-java.md)

> [!div class="nextstepaction"]
> [C++/NDK](../how-tos/set-up-coarse-reloc-cpp-ndk.md)

> [!div class="nextstepaction"]
> [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md)

<!-- Reference links in article -->
[1]: https://developers.google.com/beacons/eddystone
[2]: https://developer.apple.com/ibeacon/
[3]: https://developer.android.com/reference/android/location/LocationManager
[4]: https://developer.apple.com/documentation/corelocation/cllocationmanager?language=objc
[5]: https://developer.android.com/guide/topics/connectivity/wifi-scan
[6]: https://www.cambridge.org/core/journals/journal-of-navigation/article/positional-accuracy-of-assisted-gps-data-from-highsensitivity-gpsenabled-mobile-phones/E1EE20CD1A301C537BEE8EC66766B0A9
