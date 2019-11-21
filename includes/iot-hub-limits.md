---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224536"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| Grupos | S1 Standard | S2 Standard | Padrão S3 | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400.000 |6\.000.000 |300.000.000 |8\.000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| Grupos | Limite |
| --- | --- |
| Máximo de hubs IoT por assinatura do Azure |100 |
| Máximo de hubs IoT por assinatura do Azure |1 |
| Maximum number of characters in a device ID | 128 |
| Número máximo de identidades de dispositivo<br/> retornadas em uma única chamada |1\.000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para nuvem |7 dias |
| Tamanho máximo da mensagem do dispositivo para a nuvem |256 KB |
| Tamanho máximo do lote do dispositivo para a nuvem |AMQP e HTTP: 256 KB para todo o lote <br/>MQTT: 256 KB para cada mensagem |
| Máximo de mensagens no lote do dispositivo para a nuvem |500 |
| Tamanho máximo da mensagem da nuvem para o dispositivo |64 KB |
| TTL máximo para mensagens da nuvem para o dispositivo |2 dias |
| Contagem máxima de entrega para mensagens <br/> da nuvem para o dispositivo |100 |
| Maximum cloud-to-device queue depth per device |50 |
| Contagem máxima de entrega de mensagens de comentários <br/> em resposta a uma mensagem da nuvem para o dispositivo |100 |
| TTL máximo de mensagens de comentários <br/> em resposta a uma mensagem da nuvem para o dispositivo |2 dias |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1 KB |
| Maximum length of device twin string value | 4 KB |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Tamanho máximo da carga do método direto | 128 KB |
| Máximo de retenção de histórico do trabalho | 30 dias |
| Máximo de trabalhos simultâneos | 10 (para S3), 5 para (S2), 1 (para S1) |
| Pontos de extremidade adicionais máximo | 10 (for S1, S2, and S3) |
| Máxima de regras de roteamento de mensagens | 100 (for S1, S2, and S3) |
| Número máximo de fluxos de dispositivos conectados simultaneamente | 50 (apenas para S1, S2, S3 e F1) |
| Transferência máxima de dados do fluxo do dispositivo | 300 MB por dia (apenas para S1, S2, S3 e F1) |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. Se você quiser aumentar esse limite, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

IoT Hub throttles requests when the following quotas are exceeded.

| Restrição | Valor por hub |
| --- | --- |
| Operações de registro de identidade <br/> (create, retrieve, list, update, and delete), <br/> importação/exportação em massa ou individual |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1,67/s/unidade (100/min/unidade) (para S1 e S2). |
| Conexões do dispositivo |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Mínimo de 100/s. |
| Envios do dispositivo para a nuvem |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Mínimo de 100/s. |
| Envios da nuvem para o dispositivo | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| Recebimentos da nuvem para o dispositivo |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| Operações de upload de arquivo |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> 10 URIs de SAS/dispositivo podem estar fora ao mesmo tempo. |
| Métodos diretos | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| Leituras de dispositivo gêmeo | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| Atualizações de dispositivos gêmeos | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| Operações de trabalhos <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| Taxa de transferência de operação de trabalhos por dispositivo | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| Taxa de inicialização do fluxo do dispositivo | 5 new streams/sec (for S1, S2, S3, and F1 only). |
