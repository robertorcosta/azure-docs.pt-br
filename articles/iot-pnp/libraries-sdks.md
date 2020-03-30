---
title: Bibliotecas e SDKs de Plug and Play ioT
description: Informações sobre as bibliotecas de dispositivos e serviços disponíveis para o desenvolvimento de soluções habilitadas para IoT Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064341"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>Bibliotecas e SDKs de Plug and Play ioT

As bibliotecas ioT Plug and Play e SDKs permitem que os desenvolvedores construam soluções de IoT usando uma variedade de linguagens de programação em várias plataformas. A tabela a seguir inclui links para amostras e partidas rápidas para ajudá-lo a começar:

## <a name="microsoft-supported-libraries-and-sdks"></a>Bibliotecas e SDKs suportados pela Microsoft

| Plataforma | Biblioteca/pacote | Código-fonte | Amostra | Guia de Início Rápido | Referência |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Dispositivo SDK no apt-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Da Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Conectar ao Hub IoT](./quickstart-connect-pnp-device-c-linux.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Dispositivo SDK em Vcpkg](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Da Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [Conectar ao Hub IoT](./quickstart-connect-pnp-device-c-windows.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [Dispositivo SDK em EMBED](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Da Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Dispositivo SDK em Arduino IDE](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Da Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [Dispositivo SDK no CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [Amostras de clientes Da Digital Twin](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [Amostras de Gêmeos Digitais](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [Conectar ao Hub IoT](./quickstart-connect-pnp-device-csharp.md) | [Referência](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [Amostras de Gêmeos Digitais](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [Conectar ao Hub IoT](./quickstart-connect-pnp-device-java.md) | [Referência](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [Amostras de Gêmeos Digitais](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [Conectar ao Hub IoT](./quickstart-connect-pnp-device-node.md) | [Referência](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>Suporte ao Hub IoT

Os recursos dos dispositivos IoT Plug and Play são suportados apenas por [hubs IoT gratuitos e padrão.](../iot-hub/iot-hub-scaling.md)

## <a name="next-steps"></a>Próximas etapas

Além dos SDKs e bibliotecas do dispositivo, você pode usar APIs REST para interagir com os repositórios do modelo.