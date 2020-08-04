---
title: Conectar o código do dispositivo de componente do Java de exemplo da versão prévia do IoT Plug and Play ao Hub IoT | Microsoft Docs
description: Compile e execute o código do dispositivo do Java de exemplo da versão prévia do IoT Plug and Play que usa vários componentes e se conecta a um hub IoT. Use a ferramenta Azure IoT Explorer para exibir as informações enviadas pelo dispositivo ao hub.
author: ericmitt
ms.author: ericmitt
ms.date: 07/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1d16d8c54939c4f659b6a1530e2d360b957a09ad
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87351690"
---
# <a name="tutorial-connect-a-sample-iot-plug-and-play-preview-multiple-component-device-application-to-iot-hub-java"></a>Tutorial: Conectar um aplicativo de dispositivo de vários componentes de exemplo da versão prévia do IoT Plug and Play ao Hub IoT (Java)

[!INCLUDE [iot-pnp-tutorials-device-selector.md](../../includes/iot-pnp-tutorials-device-selector.md)]

Este tutorial mostra como criar um aplicativo de exemplo de dispositivo de vários componentes do IoT Plug and Play, conectá-lo ao hub IoT e usar a CLI do Azure para exibir a telemetria que ele envia. O aplicativo de exemplo é escrito no Java e é incluído no SDK do dispositivo de IoT do Azure para Java. Um construtor de soluções pode usar a CLI do Azure para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

Este tutorial mostra como criar um aplicativo de dispositivo de exemplo do IoT Plug and Play com componentes e interface raiz, como conectá-lo ao hub IoT e como usar a ferramenta Azure IoT Explorer para exibir as informações que ele envia ao hub. O aplicativo de exemplo é escrito no Java e é incluído no SDK do dispositivo de IoT do Azure para Java. Um construtor de soluções pode usar a ferramenta Azure IoT Explorer para compreender as funcionalidades de um dispositivo IoT Plug and Play sem a necessidade de ver nenhum código de dispositivo.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Pré-requisitos

Para realizar este tutorial no Windows, instale o seguinte software em um ambiente Windows local:

* Java SE Development Kit 8. Em [Suporte de longo prazo do Java para o Azure e o Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable), em **Suporte de longo prazo**, selecione **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="azure-iot-explorer"></a>Azure IoT Explorer

Para interagir com o dispositivo de exemplo na segunda parte deste guia de início rápido, use a ferramenta **Azure IoT Explorer**. [Baixe e instale a versão mais recente do Azure IoT Explorer](./howto-use-iot-explorer.md) para o seu sistema operacional.

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

Execute o comando a seguir para obter a _cadeia de conexão do hub IoT_ para o seu hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste guia de início rápido:

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

> [!TIP]
> Você também pode usar a ferramenta Azure IoT Explorer para localizar a cadeia de conexão do hub IoT.

Execute o comando a seguir para obter a _cadeia de conexão de dispositivo_ do dispositivo que você adicionou ao hub. Anote essa cadeia de conexão, pois você a usará posteriormente neste guia de início rápido:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```

[!INCLUDE [iot-pnp-download-models.md](../../includes/iot-pnp-download-models.md)]

## <a name="download-the-code"></a>Baixar o código

Neste tutorial, você preparará um ambiente de desenvolvimento que pode ser usado para clonar e compilar o SDK do Java do dispositivo do Hub IoT do Azure.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório GitHub dos [SDKs de Java e as Bibliotecas de IoT do Azure](https://github.com/Azure/azure-iot-sdk-java) nesta localização:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

Essa operação deve demorar alguns minutos.

## <a name="build-the-code"></a>Compilar o código

No Windows, navegue até a pasta raiz do repositório do SDK do Java clonado. Em seguida, navegue até a pasta *\device\iot-device-samples\pnp-device-sample\temerature-controller-device-sample*.

Execute o seguinte comando para compilar o aplicativo de exemplo:

```java
mvn clean package
```

## <a name="run-the-device-sample"></a>Executar o exemplo de dispositivo

Crie uma variável de ambiente chamada **IOTHUB_DEVICE_CONNECTION_STRING** para armazenar a cadeia de conexão do dispositivo anotada anteriormente.

Para executar o aplicativo de exemplo, execute o seguinte comando:

```java
mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.TemperatureController"
```

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o exemplo em execução enquanto você conclui as próximas etapas.

## <a name="use-azure-iot-explorer-to-validate-the-code"></a>Usar o Azure IoT Explorer para validar o código

Após iniciar o exemplo de cliente do dispositivo, use a ferramenta Azure IoT Explorer para verificar se ele está funcionando.

[!INCLUDE [iot-pnp-iot-explorer.md](../../includes/iot-pnp-iot-explorer.md)]

## <a name="review-the-code"></a>Examine o código

Este exemplo implementa um dispositivo controlador de temperatura do IoT Plug and Play. O modelo que este exemplo implementa usa [vários componentes](concepts-components.md). O [arquivo de modelo de DTDL (linguagem de definição de Gêmeos Digitais) do dispositivo de temperatura](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) define a telemetria, as propriedades e os comandos que o dispositivo implementa.

O código do dispositivo usa a classe `DeviceClient` padrão para se conectar ao seu hub IoT. O dispositivo envia a ID de modelo do modelo de DTDL que ele implementa na solicitação de conexão. Um dispositivo que envia uma ID de modelo é um dispositivo IoT Plug and Play:

```java
private static void initializeDeviceClient() throws URISyntaxException, IOException {
    ClientOptions options = new ClientOptions();
    options.setModelId(MODEL_ID);
    deviceClient = new DeviceClient(deviceConnectionString, protocol, options);

    deviceClient.registerConnectionStatusChangeCallback((status, statusChangeReason, throwable, callbackContext) -> {
        log.debug("Connection status change registered: status={}, reason={}", status, statusChangeReason);

        if (throwable != null) {
            log.debug("The connection status change was caused by the following Throwable: {}", throwable.getMessage());
            throwable.printStackTrace();
        }
    }, deviceClient);

    deviceClient.open();
}
```

A ID do modelo é armazenada no código, conforme mostrado no seguinte snippet:

```java
private static final String MODEL_ID = "dtmi:com:example:Thermostat;1";
```

Depois que o dispositivo se conectar ao hub IoT, o código registrará os manipuladores de comando.

```java
deviceClient.subscribeToDeviceMethod(new MethodCallback(), null, new MethodIotHubEventCallback(), null);
```

Há manipuladores separados para as atualizações de propriedade desejadas nos dois componentes de termostato:

```java
deviceClient.startDeviceTwin(new TwinIotHubEventCallback(), null, new GenericPropertyUpdateCallback(), null);
Map<Property, Pair<TwinPropertyCallBack, Object>> desiredPropertyUpdateCallback = Stream.of(
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_1, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_1)),
        new AbstractMap.SimpleEntry<Property, Pair<TwinPropertyCallBack, Object>>(
                new Property(THERMOSTAT_2, null),
                new Pair<>(new TargetTemperatureUpdateCallback(), THERMOSTAT_2))
).collect(Collectors.toMap(AbstractMap.SimpleEntry::getKey, AbstractMap.SimpleEntry::getValue));

deviceClient.subscribeToTwinDesiredProperties(desiredPropertyUpdateCallback);
```

O código de exemplo envia telemetria de cada componente do termostato:

```java
sendTemperatureReading(THERMOSTAT_1);
sendTemperatureReading(THERMOSTAT_2);
```

O método `sendTemperatureReading` usa a classe `PnpHhelper` para criar mensagens para cada componente:

```java
Message message = PnpHelper.createIotHubMessageUtf8(telemetryName, currentTemperature, componentName);
```

A classe `PnpHelper` contém outros métodos de exemplo que você pode usar com um modelo de vários componentes.

Use a ferramenta do Azure IoT Explorer para ver a telemetria e as propriedades dos dois componentes do termostato:

:::image type="content" source="media/tutorial-multiple-components-java/multiple-component.png" alt-text="Vários dispositivos de componente no Azure IoT Explorer":::

Você também pode usar a ferramenta Azure IoT Explorer para chamar comandos em qualquer um dos dois componentes do termostato ou na interface raiz.

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a conectar um dispositivo IoT Plug and Play com componentes a um hub IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem de versão prévia do IoT Plug and Play](concepts-developer-guide.md)
