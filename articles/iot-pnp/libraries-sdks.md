---
title: SDKs e bibliotecas de Plug and Play de IoT
description: Informações sobre as bibliotecas de dispositivo e serviço disponíveis para o desenvolvimento de soluções habilitadas para IoT Plug and Play.
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3331a0a9a8d3fb5d028d801d334daf2dbfa25235
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337323"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>SDKs e bibliotecas de Plug and Play de IoT

As bibliotecas e SDKs do IoT Plug and Play permitem que os desenvolvedores criem soluções de IoT usando uma variedade de linguagens de programação em várias plataformas. A tabela a seguir inclui links para exemplos e guias de início rápido para ajudá-lo a começar:

## <a name="microsoft-sdks-for-iot-plug-and-play"></a>Microsoft SDKs para IoT Plug and Play

**SDKs de dispositivo**

| Linguagem | Pacote | Repositório de código | Exemplos | Guia de Início Rápido | Referência |
|---|---|---|---|---|---|
| Dispositivo C | [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [Amostras](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [Conectar ao Hub IoT](quickstart-connect-device-c.md) | [Referência](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-dispositivo | [1.27.0 NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [Conectar ao Hub IoT](quickstart-connect-device-csharp.md) | [Referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Dispositivo Java | [1.24.0 Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [Conectar ao Hub IoT](quickstart-connect-device-java.md) | [Referência](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-dispositivo | [2.1.4 Pip](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [Conectar ao Hub IoT](quickstart-connect-device-python.md) | [Referência](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| Nó-dispositivo | [NPM 1.17.0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [Conectar ao Hub IoT](quickstart-connect-device-node.md) | [Referência](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

**SDKs de serviço (versão prévia)**

| Linguagem | Pacote | Repositório de código | Exemplos | Guia de Início Rápido | Referência |
|---|---|---|---|---|---|
| .NET – visualização do serviço do Hub IoT | [NuGet 1.27.1-Preview-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [Amostras](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | na | [Referência](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet) |
| Java-visualização do serviço do Hub IoT | [Maven 1.1.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [Amostras](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | na | [Referência](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable) |
| Python – Hub IoT/visualização digital do serviço gêmeos | [Pip 2.2.1 RC1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [Amostras](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [Interagir com a API gêmeos digital do Hub IoT](quickstart-service-python.md) | [Referência](https://docs.microsoft.com/python/api/azure-iot-hub/?view=azure-python) |
| Nó – Hub IoT/visualização do serviço gêmeos digital | [NPM 1.0.0-PNP-Refresh. 3](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [Amostras](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [Interagir com a API gêmeos digital do Hub IoT](quickstart-service-node.md) | [Referência](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest) |

## <a name="next-steps"></a>Próximas etapas

Para experimentar os SDKs e as bibliotecas, consulte o [Guia do desenvolvedor](concepts-developer-guide.md) e os guias de início rápido do [dispositivo](quickstart-connect-device-c.md) e os guias de [início rápido do serviço](quickstart-service-node.md).
