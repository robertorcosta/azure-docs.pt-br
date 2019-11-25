---
title: Desenvolver módulos para o Azure IoT Edge | Microsoft Docs
description: Desenvolver módulos personalizados para o Azure IoT Edge que podem se comunicar com o runtime e o IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 37f55165d1fea8a69d10003baeb0006199326cba
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456609"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolva seus próprios módulos do IoT Edge

Os módulos do Azure IoT Edge podem conectar outros serviços do Azure e contribuir para o pipeline de dados em nuvem maior. Este artigo descreve como é possível desenvolver módulos para comunicação com o runtime do IoT Edge e Hub IoT e, portanto, com o restante da nuvem do Azure. 

## <a name="iot-edge-runtime-environment"></a>Ambiente de runtime do IoT Edge
O runtime do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implantá-los nos dispositivos IoT Edge. Em um nível alto, qualquer programa pode ser empacotado como um módulo do IoT Edge. No entanto, para aproveitar ao máximo as funcionalidades de gerenciamento e comunicação do IoT Edge, um programa em execução em um módulo pode se conectar ao hub do IoT Edge local, integrado no runtime do IoT Edge.

## <a name="using-the-iot-edge-hub"></a>Usar o hub do IoT Edge
O hub do IoT Edge fornece duas funcionalidades principais: proxy para o Hub IoT e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos do Hub IoT
O Hub IoT vê uma instância de módulo de forma análoga a um dispositivo, no sentido de que:

* ele tem um módulo gêmeo que é distinto e isolado do [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md) e dos outros módulos gêmeos desse dispositivo;
* ele pode enviar [mensagens de dispositivo para nuvem](../iot-hub/iot-hub-devguide-messaging.md);
* ele pode receber [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) direcionados especificamente para sua identidade.

No momento, um módulo não pode receber mensagens de nuvem para dispositivo nem usar o recurso de carregamento de arquivo.

Ao gravar um módulo, você pode usar o [Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) para se conectar ao hub IoT Edge e usar a funcionalidade acima como faria ao usar o IoT Hub com um aplicativo de dispositivo, a única diferença sendo que, de seu back-end de aplicativo, você deve se referir à identidade do módulo em vez da identidade do dispositivo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem
Para habilitar o processamento complexo de mensagens de dispositivo para nuvem, o Hub do IoT Edge fornece roteamento declarativo de mensagens entre módulos e entre módulos e Hub IoT. O roteamento declarativo permite que os módulos interceptem e processem mensagens enviadas por outros módulos e os propaguem em pipelines complexos. Para obter mais informações, consulte [implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

Um módulo IoT Edge, em oposição a um aplicativo de dispositivo Hub IoT normal, pode receber mensagens de dispositivo para nuvem que estão sendo intermediadas por proxy por seu hub IoT Edge local para processá-las.

O Hub do IoT Edge propaga as mensagens para o módulo com base nas rotas declarativas descritas no [manifesto de implantação](module-composition.md). Ao desenvolver um módulo IoT Edge, você pode receber essas mensagens definindo manipuladores de mensagens.

Para simplificar a criação de rotas, o IoT Edge inclui o conceito de pontos de extremidade de *entrada* e *saída*. Um módulo pode receber todas as mensagens de dispositivo para nuvem roteadas a ele sem especificar nenhuma entrada e pode enviar mensagens de dispositivo para nuvem sem especificar nenhuma saída. Usar entradas e saídas explícitas, porém, torna as regras de roteamento mais simples de entender. 

Por fim, as mensagens de dispositivo para nuvem tratadas pelo hub do Edge são marcadas com as seguintes propriedades de sistema:

| Propriedade | Descrição |
| -------- | ----------- |
| $connectionDeviceId | A ID do dispositivo do cliente que enviou a mensagem |
| $connectionDeviceId | A ID do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode ser um vazia. |
| $outputName | A saída usada para enviar a mensagem. Pode ser um vazia. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Conectar-se ao hub do IoT Edge a partir de um módulo
Conectar-se ao hub IoT Edge local a partir de um módulo envolve duas etapas: 
1. Crie uma instância ModuleClient no aplicativo.
2. Verifique se que seu aplicativo aceita o certificado apresentado pelo hub do IoT Edge nesse dispositivo.

Crie uma instância ModuleClient para conectar o módulo ao Hub do IoT Edge em execução no dispositivo, de modo semelhante como as instâncias DeviceClient conectam dispositivos ao Hub IoT. For more information about the ModuleClient class and its communication methods, see the API reference for your preferred SDK language: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), or [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Language and architecture support

IoT Edge supports multiple operating systems, device architectures, and development languages so that you can build the scenario that matches your needs. Use this section to understand your options for developing custom IoT Edge modules. You can learn more about tooling support and requirements for each language in [Prepare your development and test environment for IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

For all languages in the following table, IoT Edge supports development for AMD64 and ARM32 Linux devices. 

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Código do Visual Studio<br>Visual Studio 2017/2019 |
| C# | Código do Visual Studio<br>Visual Studio 2017/2019 |
| Java | Código do Visual Studio |
| Node.js | Código do Visual Studio |
| Python | Código do Visual Studio |

>[!NOTE]
>Develop and debugging support for ARM64 Linux devices is in [public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, confira [Desenvolver e depurar módulos do IoT Edge ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

For all languages in the following table, IoT Edge supports development for AMD64 Windows devices.

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (no debugging capabilities)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Próximos passos

[Prepare your development and test environment for IoT Edge](development-environment.md)

[Use Visual Studio to develop C# modules for IoT Edge](how-to-visual-studio-develop-module.md)

[Use Visual Studio Code to develop modules for IoT Edge](how-to-vs-code-develop-module.md)

[Understand and use Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md)