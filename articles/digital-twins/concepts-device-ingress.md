---
title: Conectividade do dispositivo e entrada de telemetria-Azure digital gêmeos | Microsoft Docs
description: Saiba como conectar, carregar e enviar telemetria de um dispositivo IoT no Azure digital gêmeos.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 5c2c519ece9806b92c3e455d5f550bc2abfc9f3b
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75862468"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Conectividade do dispositivo e entrada de telemetria

Os dados de telemetria enviados por dispositivos e sensores formam o backbone de qualquer solução de IoT. Como representar esses recursos diferentes e gerenciá-los dentro do contexto de um local são as principais preocupações no desenvolvimento de aplicativos IoT. Os Gêmeos Digitais do Azure simplifica o processo de desenvolvimento de soluções de IoT unindo dispositivos e sensores com um gráfico de inteligência espacial.

Para começar, crie um recurso do Azure Hub IoT na raiz do gráfico espacial. O recurso Hub IoT permite que todos os dispositivos abaixo do espaço raiz enviem mensagens. Depois que o Hub IoT for criado, registre os dispositivos com sensores dentro da instância de Gêmeos Digitais do Azure. Os dispositivos podem enviar dados para um serviço Digital Twins por meio do [SDK do dispositivo IoT do Azure](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks).

Para obter um guia passo a passo sobre como colocar dispositivos integrados, leia o [tutorial para implantar e configurar o digital gêmeos](tutorial-facilities-setup.md). Em uma visão geral, as etapas são:

- Implante uma instância Digital Twins do [portal do Microsoft Azure](https://portal.azure.com).
- Crie espaços no seu gráfico.
- Crie um recurso Hub IoT e atribua-o a um espaço em seu gráfico.
- Crie dispositivos e sensores em seu gráfico e atribua-os aos espaços criados nas etapas anteriores.
- Crie um combinador para filtrar mensagens de telemetria com base nas condições.
- Crie uma [função definida pelo usuário](concepts-user-defined-functions.md) e atribua-a num espaço no gráfico para processamento personalizado de suas mensagens de telemetria.
- Atribuir uma função para permitir que a função definida pelo usuário acesse os dados do gráfico.
- Obtenha a cadeia de conexão do dispositivo Hub IoT nas APIs do Digital Twins Management.
- Configure a cadeia de conexão do dispositivo no dispositivo com o SDK do dispositivo IoT do Azure.

Nas seções a seguir, você aprende como obter a cadeia de conexão do dispositivo Hub IoT na Digital Twins Management API. Você também aprenderá a usar o formato de mensagem de telemetria Hub IoT para enviar telemetria baseada em sensores. Os Gêmeos Digitais exigem que cada parte da telemetria recebida seja associada a um sensor dentro do gráfico espacial. Esse requisito garante que os dados sejam processados e roteados dentro do contexto espacial apropriado.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Obter a cadeia de conexão de dispositivo do Hub IoT da API de Gerenciamento

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

Fazer uma chamada GET na API do Dispositivo com um parâmetro `includes=ConnectionString` para obter a cadeia de conexão do dispositivo do Hub IoT. Filtre pelo GUID do dispositivo ou pelo ID do hardware para localizar o dispositivo fornecido.

```plaintext
YOUR_MANAGEMENT_API_URL/devices/YOUR_DEVICE_GUID?includes=ConnectionString
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_DEVICE_GUID* | A ID do dispositivo |

```plaintext
YOUR_MANAGEMENT_API_URL/devices?HardwareIds=YOUR_DEVICE_HARDWARE_ID&includes=ConnectionString
```

| Valor de parâmetro | Substitua por |
| --- | --- |
| *YOUR_DEVICE_HARDWARE_ID* | A ID de hardware do dispositivo |

Na carga útil de resposta, copie a propriedade **connectionString** do dispositivo. Você o usa quando liga para o SDK do dispositivo IoT do Azure para enviar dados a gêmeos digitais.

## <a name="device-to-cloud-message"></a>Mensagem de dispositivo para nuvem

Você pode personalizar o payload e o formato de mensagem do dispositivo para atender às necessidades da sua solução. Use qualquer contrato de dados que possa ser serializado em uma matriz de bytes ou fluxo compatível com a classe [Mensagem do Cliente de Dispositivo do IoT do Azure, Message (byte [] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). A mensagem pode ser um formato binário personalizado de sua escolha, desde que você decodifique o contrato de dados em uma função definida pelo usuário correspondente. Há apenas um requisito para uma mensagem de dispositivo para nuvem. Mantenha um conjunto de propriedades para certificar-se de que sua mensagem seja roteada adequadamente para o mecanismo de processamento.

### <a name="telemetry-properties"></a>Propriedades de telemetria

 O conteúdo da carga útil de uma **Mensagem** pode ser um dado arbitrário de até 256 KB de tamanho. Existem alguns requisitos esperados para as propriedades do tipo [`Message.Properties`](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet). A tabela mostra as propriedades obrigatórias e opcionais com suporte do sistema.

| Nome da propriedade | Valor | Obrigatório | Description |
|---|---|---|---|
| **DigitalTwins-Telemetry** | 1.0 | Sim | Um valor constante que identifica uma mensagem para o sistema. |
| **DigitalTwins-SensorHardwareId** | `string(72)` | Sim | Um identificador exclusivo do sensor que envia a **Mensagem**. Esse valor deve corresponder a uma propriedade **HardwareId** do objeto para o sistema para processá-lo. Por exemplo, `00FF0643BE88-CO2`. |
| **CreationTimeUtc** | `string` | Não | Uma cadeia de data formatada [ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html) que identifica o tempo de amostragem da carga útil. Por exemplo, `2018-09-20T07:35:00.8587882-07:00`. |
| **CorrelationId** | `string` | Não | Um UUID que foi usado para eventos de rastreamento em todo o sistema. Por exemplo, `cec16751-ab27-405d-8fe6-c68e1412ce1f`.

### <a name="send-your-message-to-digital-twins"></a>Envie sua mensagem para Gêmeos Digitais

Use a chamada DeviceEvent [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) ou [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet) para enviar sua mensagem para Gêmeos Digitais.

## <a name="next-steps"></a>Próximos passos

- Para aprender sobre o processamento de dados do Azure Digital Twins e os recursos de funções definidas pelo usuário, leia [Processamento de dados do Gêmeos Digitais do Azure e funções definidas pelo usuário](concepts-user-defined-functions.md).
