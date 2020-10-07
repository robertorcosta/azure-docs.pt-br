---
title: Interagir com um dispositivo IoT Plug and Play conectado à sua solução de IoT do Azure (Java) | Microsoft Docs
description: Use o Java para se conectar com um dispositivo IoT Plug and Play conectado à solução de IOT do Azure e interagir com ele.
author: ericmitt
ms.author: ericmitt
ms.date: 9/17/2020
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: cd618cf5f2f82b9c87981e961ed401f3409ec9d4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91580761"
---
# <a name="quickstart-interact-with-an-iot-plug-and-play-device-thats-connected-to-your-solution-java"></a>Início Rápido: interagir com um dispositivo IoT Plug and Play conectado à sua solução (Java)

[!INCLUDE [iot-pnp-quickstarts-service-selector.md](../../includes/iot-pnp-quickstarts-service-selector.md)]

O IoT Plug and Play simplifica a IoT, permitindo que você interaja com as funcionalidades de um dispositivo sem conhecer a implementação do dispositivo subjacente. Este início rápido mostra como usar o Java para se conectar a um dispositivo IoT Plug and Play que está conectado à sua solução e controlá-lo.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Para concluir este início rápido no Windows, instale o seguinte software em um ambiente do Windows local:

* Java SE Development Kit 8. Em [Suporte de longo prazo do Java para o Azure e o Azure Stack](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true), em **Suporte de longo prazo**, selecione **Java 8**.
* [Apache Maven 3](https://maven.apache.org/download.cgi).

### <a name="clone-the-sdk-repository-with-the-sample-code"></a>Clonar o repositório do SDK com o código de exemplo

Se você concluiu o [Início Rápido: Conecte um aplicativo de exemplo de dispositivo do IoT Plug and Play em execução no Windows ao Hub IoT (Java)](quickstart-connect-device-java.md). Você já clonou o repositório.

Abra um prompt de comando no diretório de sua escolha. Execute o seguinte comando para clonar o repositório do GitHub [SDK de IoT do Microsoft Azure para Java](https://github.com/Azure/azure-iot-sdk-java) nesta localização:

```cmd
git clone https://github.com/Azure/azure-iot-sdk-java.git
```

## <a name="build-and-run-the-sample-device"></a>Compilar e executar o dispositivo de exemplo

[!INCLUDE [iot-pnp-environment](../../includes/iot-pnp-environment.md)]

Para saber mais sobre a configuração do exemplo, confira o [leiame de exemplo](https://github.com/Azure/azure-iot-sdk-java/blob/master/device/iot-device-samples/readme.md).

Neste início rápido, você usa um dispositivo de termostato de exemplo escrito em Java como o dispositivo IoT Plug and Play. Para executar o dispositivo de exemplo:

1. Abra uma janela do terminal e navegue até a pasta local que contém o repositório SDK de IoT do Microsoft Azure para Java clonado do GitHub.

1. Esta janela de terminal é usada como o seu terminal de **dispositivo**. Acesse a pasta raiz do repositório clonado. Instale todas as dependências executando o seguinte comando:

1. Execute o seguinte comando para compilar o aplicativo do dispositivo de exemplo:

    ```cmd
    mvn install -T 2C -DskipTests
    ```

1. Para executar o aplicativo do dispositivo de exemplo, navegue até a pasta *device\iot-device-samples\pnp-device-sample\thermostat-device-sample* e execute o seguinte comando:

    ```cmd
    mvn exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.device.Thermostat"
    ```

Agora, o dispositivo está pronto para receber comandos e atualizações de propriedade e começou a enviar dados telemétricos para o hub. Mantenha o dispositivo de exemplo em execução enquanto você realiza as próximas etapas.

## <a name="run-the-sample-solution"></a>Executar a solução de exemplo

Em [Configurar o ambiente para os inícios rápidos e os tutoriais do IoT Plug and Play](set-up-environment.md), você criou duas variáveis de ambiente para configurar o exemplo a ser conectado ao hub IoT e ao dispositivo:

* **IOTHUB_CONNECTION_STRING**: a cadeia de conexão do hub IoT anotada anteriormente.
* **DEVICE_ID**: `"my-pnp-device"`.

Neste início rápido, você usa uma solução de IoT de exemplo escrita em Java para interagir com o dispositivo de exemplo que acabou de configurar.

> [!NOTE]
> Este exemplo usa o namespace **com.microsoft.azure.sdk.iot.service.*;** do **cliente do serviço Hub IoT**. Para saber mais sobre como recuperar a ID do modelo, confira o [guia do desenvolvedor](concepts-developer-guide-device-csharp.md).

1. Abra outra janela de terminal para usar como o seu terminal de **serviço**.

1. No repositório do SDK do Java clonado, navegue até a pasta *service\iot-service-samples\pnp-service-sample\thermostat-service-sample*.

1. Para executar o aplicativo de serviço de exemplo, execute o seguinte comando:

    ```cmd
    mvm exec:java -Dexec.mainClass="samples.com.microsoft.azure.sdk.iot.service.Thermostat"
    ```

### <a name="get-digital-twin"></a>Obter o gêmeo digital

O seguinte snippet de código mostra como recuperar o dispositivo gêmeo no serviço:

```java
 // Get the twin and retrieve model Id set by Device client.
DeviceTwinDevice twin = new DeviceTwinDevice(deviceId);
twinClient.getTwin(twin);
System.out.println("Model Id of this Twin is: " + twin.getModelId());
```

### <a name="update-a-digital-twin"></a>Atualizar um gêmeo digital

O seguinte snippet de código mostra como usar um *patch* para atualizar as propriedades por meio do gêmeo digital:

```java
String propertyName = "targetTemperature";
double propertyValue = 60.2;
twin.setDesiredProperties(Collections.singleton(new Pair(propertyName, propertyValue)));
twinClient.updateTwin(twin);
```

A saída do dispositivo mostra como o dispositivo responde a essa atualização de propriedade.

### <a name="invoke-a-command"></a>Invocar um comando

O seguinte snippet de código mostra como chamar um comando no dispositivo:

```java
// The method to invoke for a device without components should be "methodName" as defined in the DTDL.
String methodToInvoke = "getMaxMinReport";
System.out.println("Invoking method: " + methodToInvoke);

Long responseTimeout = TimeUnit.SECONDS.toSeconds(200);
Long connectTimeout = TimeUnit.SECONDS.toSeconds(5);

// Invoke the command.
String commandInput = ZonedDateTime.now(ZoneOffset.UTC).minusMinutes(5).format(DateTimeFormatter.ISO_DATE_TIME);
MethodResult result = methodClient.invoke(deviceId, methodToInvoke, responseTimeout, connectTimeout, commandInput);
if(result == null)
{
    throw new IOException("Method result is null");
}

System.out.println("Method result status is: " + result.getStatus());
```

A saída do dispositivo mostra como o dispositivo responde a esse comando.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você aprendeu a conectar um dispositivo IoT Plug and Play a uma solução de IoT. Para saber mais sobre os modelos de dispositivos IoT Plug and Play, confira:

> [!div class="nextstepaction"]
> [Guia do desenvolvedor de modelagem do IoT Plug and Play](concepts-developer-guide-device-csharp.md)
