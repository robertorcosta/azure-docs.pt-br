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
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271376"
---
# <a name="develop-your-own-iot-edge-modules"></a>Desenvolva seus próprios módulos do IoT Edge

Os módulos do Azure IoT Edge podem conectar outros serviços do Azure e contribuir para o pipeline de dados em nuvem maior. Este artigo descreve como é possível desenvolver módulos para comunicação com o runtime do IoT Edge e Hub IoT e, portanto, com o restante da nuvem do Azure.

## <a name="iot-edge-runtime-environment"></a>Ambiente de runtime do IoT Edge

O runtime do IoT Edge fornece a infraestrutura para integrar a funcionalidade de vários módulos do IoT Edge e implantá-los nos dispositivos IoT Edge. Qualquer programa pode ser empacotado como um módulo IoT Edge. Para aproveitar ao máximo as funcionalidades de IoT Edge de comunicação e gerenciamento, um programa em execução em um módulo pode usar o SDK do dispositivo IoT do Azure para se conectar ao Hub de IoT Edge local.

## <a name="using-the-iot-edge-hub"></a>Usar o hub do IoT Edge

O hub do IoT Edge fornece duas funcionalidades principais: proxy para o Hub IoT e comunicações locais.

### <a name="iot-hub-primitives"></a>Primitivos do Hub IoT

O Hub IoT vê uma instância de módulo de forma análoga a um dispositivo, no sentido de que:

* ele tem um módulo gêmeo que é distinto e isolado do [dispositivo gêmeo](../iot-hub/iot-hub-devguide-device-twins.md) e dos outros módulos gêmeos desse dispositivo;
* ele pode enviar [mensagens de dispositivo para nuvem](../iot-hub/iot-hub-devguide-messaging.md);
* ele pode receber [métodos diretos](../iot-hub/iot-hub-devguide-direct-methods.md) direcionados especificamente para sua identidade.

Atualmente, os módulos não podem receber mensagens da nuvem para o dispositivo ou usam o recurso de carregamento de arquivo.

Ao gravar um módulo, você pode usar o [SDK do dispositivo IOT do Azure](../iot-hub/iot-hub-devguide-sdks.md) para se conectar ao hub de IOT Edge e usar a funcionalidade acima como você usaria ao usar o Hub IOT com um aplicativo de dispositivo. A única diferença entre os módulos IoT Edge e os aplicativos de dispositivo IoT é que você precisa se referir à identidade do módulo em vez da identidade do dispositivo.

### <a name="device-to-cloud-messages"></a>Mensagens do dispositivo para a nuvem

Para habilitar o processamento complexo de mensagens de dispositivo para nuvem, o Hub do IoT Edge fornece roteamento declarativo de mensagens entre módulos e entre módulos e Hub IoT. O roteamento declarativo permite que os módulos interceptem e processem mensagens enviadas por outros módulos e os propaguem em pipelines complexos. Para obter mais informações, consulte [implantar módulos e estabelecer rotas no IoT Edge](module-composition.md).

Um módulo IoT Edge, em oposição a um aplicativo de dispositivo do Hub IoT normal, pode receber mensagens do dispositivo para a nuvem que estão sendo modificadas por proxy por seu hub de IoT Edge local para processá-las.

O Hub do IoT Edge propaga as mensagens para o módulo com base nas rotas declarativas descritas no [manifesto de implantação](module-composition.md). Ao desenvolver um módulo IoT Edge, você pode receber essas mensagens definindo manipuladores de mensagens.

Para simplificar a criação de rotas, o IoT Edge inclui o conceito de pontos de extremidade de *entrada* e *saída*. Um módulo pode receber todas as mensagens de dispositivo para nuvem roteadas a ele sem especificar nenhuma entrada e pode enviar mensagens de dispositivo para nuvem sem especificar nenhuma saída. Usar entradas e saídas explícitas, porém, torna as regras de roteamento mais simples de entender.

Por fim, as mensagens de dispositivo para nuvem tratadas pelo hub do Edge são marcadas com as seguintes propriedades de sistema:

| Propriedade | DESCRIÇÃO |
| -------- | ----------- |
| $connectionDeviceId | A ID do dispositivo do cliente que enviou a mensagem |
| $connectionDeviceId | A ID do módulo que enviou a mensagem |
| $inputName | A entrada que recebeu esta mensagem. Pode ser um vazia. |
| $outputName | A saída usada para enviar a mensagem. Pode ser um vazia. |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Conectar-se ao hub do IoT Edge a partir de um módulo

Conectar-se ao hub IoT Edge local a partir de um módulo envolve duas etapas:

1. Crie uma instância ModuleClient no aplicativo.
2. Verifique se que seu aplicativo aceita o certificado apresentado pelo hub do IoT Edge nesse dispositivo.

Crie uma instância ModuleClient para conectar o módulo ao Hub do IoT Edge em execução no dispositivo, de modo semelhante como as instâncias DeviceClient conectam dispositivos ao Hub IoT. Para obter mais informações sobre a classe ModuleClient e seus métodos de comunicação, consulte a referência de API para sua linguagem [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)de SDK preferencial:, [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)ou [node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

## <a name="language-and-architecture-support"></a>Suporte à linguagem e à arquitetura

O IoT Edge dá suporte a vários sistemas operacionais, arquiteturas de dispositivo e linguagens de desenvolvimento para que você possa criar o cenário que atenda às suas necessidades. Use esta seção para entender as opções para o desenvolvimento de módulos de IoT Edge personalizados. Você pode saber mais sobre o suporte a ferramentas e requisitos para cada idioma em [preparar seu ambiente de desenvolvimento e teste para IOT Edge](development-environment.md).

### <a name="linux"></a>Linux

Para todos os idiomas na tabela a seguir, IoT Edge dá suporte ao desenvolvimento de dispositivos AMD64 e ARM32 Linux.

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>O desenvolvimento e a depuração de suporte para dispositivos ARM64 Linux estão em [Visualização pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Para obter mais informações, confira [Desenvolver e depurar módulos do IoT Edge ARM64 no Visual Studio Code (versão prévia)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

Para todos os idiomas na tabela a seguir, IoT Edge dá suporte ao desenvolvimento de dispositivos AMD64 Windows.

| Linguagem de desenvolvimento | Ferramentas de desenvolvimento |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (sem recursos de depuração)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>Próximas etapas

[Prepare seu ambiente de desenvolvimento e teste para IoT Edge](development-environment.md)

[Use o Visual Studio para C# desenvolver módulos para IOT Edge](how-to-visual-studio-develop-module.md)

[Use Visual Studio Code para desenvolver módulos para IoT Edge](how-to-vs-code-develop-module.md)

[Entender e usar SDKs do Hub IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md)
