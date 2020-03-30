---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050452"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

Dispositivos gêmeos são documentos JSON que armazenam informações do estado do dispositivo, incluindo metadados, configurações e condições. O Hub IoT persiste um dispositivo gêmeo para cada dispositivo que você conecta a ele.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

Use os dispositivos gêmeos para:

* Armazene os metadados de dispositivo de seu back-end da solução.

* Informe as informações do estado atual, como recursos e condições disponíveis, por exemplo, o método de conectividade utilizado, a partir do aplicativo do seu dispositivo.

* Sincronize o estado dos fluxos de trabalho de longo prazo, como firmware e atualizações de configuração, entre um aplicativo de dispositivo e um aplicativo back-end.

* Consultar os metadados, a configuração ou o estado do seu dispositivo.

Dispositivos gêmeos são projetados para sincronização e para consultar condições e configurações de dispositivos. Mais informações sobre quando usar dispositivos gêmeos podem ser localizadas em [Entender dispositivos gêmeos](../articles/iot-hub/iot-hub-devguide-device-twins.md).

Os gêmeos do dispositivo são armazenados em um hub de IoT e contêm os seguintes elementos:

* **Tags**. Metadados do dispositivo acessíveis apenas pelo back-end da solução.

* **Propriedades desejadas**. Objetos JSON modificáveis pelo back-end da solução e observáveis pelo aplicativo do dispositivo.

* **Propriedades relatadas**. Objetos JSON modificáveis pelo aplicativo do dispositivo e legíveis pelo back-end da solução.

Marcas e propriedades não podem conter matrizes, mas objetos podem ser aninhados.

A ilustração a seguir mostra a organização do dispositivo gêmeo:

![Imagem de dispositivo gêmeo mostrando a funcionalidade](./media/iot-hub-selector-twin-get-started/twin.png)

Além disso, o back-end da solução pode consultar dispositivos gêmeos com base em todos os dados acima.
Para obter mais informações sobre gêmeos do dispositivo, consulte [Entenda os gêmeos do dispositivo](../articles/iot-hub/iot-hub-devguide-device-twins.md). Para obter mais informações sobre consulta, consulte [o idioma de consulta do IoT Hub](../articles/iot-hub/iot-hub-devguide-query-language.md).


Este tutorial mostra como:

* Criar um aplicativo de back-end que adiciona marcas a um dispositivo gêmeo e um aplicativo de dispositivo simulado que relata seu canal de conectividade como uma propriedade relatada no dispositivo gêmeo.

* Consulte dispositivos por meio do aplicativo de back-end usando filtros nas marcações e propriedades criadas anteriormente.
