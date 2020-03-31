---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224536"
---
A tabela a seguir lista os limites associados aos diferentes níveis de serviço S1, S2, S3 e F1. Para obter informações sobre o custo de cada *unidade* em cada camada, consulte [os preços do Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 Standard | S2 Standard | Padrão S3 | F1 Gratuito |
| --- | --- | --- | --- | --- |
| Mensagens/dia |400.000 |6.000.000 |300.000.000 |8,000 |
| Máximo de unidades |200 |200 |10 |1 |

> [!NOTE]
> Se você antecipar o uso de mais de 200 unidades com um hub de nível S1 ou S2 ou 10 unidades com um hub de nível S3, entre em contato com o Suporte da Microsoft.
> 
> 

A tabela a seguir lista os limites aplicáveis aos recursos do IoT Hub.

| Recurso | Limite |
| --- | --- |
| Máximo de hubs IoT por assinatura do Azure |100 |
| Máximo de hubs IoT por assinatura do Azure |1 |
| Número máximo de caracteres em um ID de dispositivo | 128 |
| Número máximo de identidades de dispositivo<br/>  retornadas em uma única chamada |1,000 |
| Retenção máxima de mensagem do Hub IoT para mensagens do dispositivo para nuvem |7 dias |
| Tamanho máximo da mensagem do dispositivo para a nuvem |256 KB |
| Tamanho máximo do lote do dispositivo para a nuvem |AMQP e HTTP: 256 KB para todo o lote <br/>MQTT: 256 KB para cada mensagem |
| Máximo de mensagens no lote do dispositivo para a nuvem |500 |
| Tamanho máximo da mensagem da nuvem para o dispositivo |64 KB |
| TTL máximo para mensagens da nuvem para o dispositivo |2 dias |
| Contagem máxima de entrega para mensagens  <br/>  da nuvem para o dispositivo |100 |
| Profundidade máxima de fila de nuvem para dispositivo por dispositivo |50 |
| Contagem máxima de entrega de mensagens de comentários  <br/>  em resposta a uma mensagem da nuvem para o dispositivo |100 |
| TTL máximo de mensagens de comentários  <br/>  em resposta a uma mensagem da nuvem para o dispositivo |2 dias |
| [Tamanho máximo de dispositivo gêmeo](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB para seção de tags e 32 KB para seções de propriedades desejadas e relatadas cada |
| Comprimento máximo da chave de corda dupla do dispositivo | 1 KB |
| Comprimento máximo do valor de corda dupla do dispositivo | 4 KB |
| [Profundidade máxima de objeto em dispositivo gêmeo](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Tamanho máximo da carga do método direto | 128 KB |
| Máximo de retenção de histórico do trabalho | 30 dias |
| Máximo de trabalhos simultâneos | 10 (para S3), 5 para (S2), 1 (para S1) |
| Pontos de extremidade adicionais máximo | 10 (para S1, S2 e S3) |
| Máxima de regras de roteamento de mensagens | 100 (para S1, S2 e S3) |
| Número máximo de fluxos de dispositivos conectados simultaneamente | 50 (apenas para S1, S2, S3 e F1) |
| Transferência máxima de dados do fluxo do dispositivo | 300 MB por dia (apenas para S1, S2, S3 e F1) |

> [!NOTE]
> Se você precisar de mais de 100 hubs de IoT pagos em uma assinatura do Azure, entre em contato com o Microsoft Support.

> [!NOTE]
> Atualmente, o número total de dispositivos mais módulos que podem ser registrados em um único hub de IoT é limitado a 1.000.000. Se você quiser aumentar esse limite, entre em contato com o [Suporte da Microsoft](https://azure.microsoft.com/support/options/).

O IoT Hub acelera as solicitações quando as seguintes cotas são excedidas.

| Restrição | Valor por hub |
| --- | --- |
| Operações de registro de identidade <br/> (criar, recuperar, listar, atualizar e excluir), <br/>  importação/exportação em massa ou individual |83,33/seg/unidade (5.000/min/unidade) (para S3). <br/> 1,67/s/unidade (100/min/unidade) (para S1 e S2). |
| Conexões do dispositivo |6.000/seg/unidade (para S3), 120/seg/unidade (para S2), 12/seg/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios do dispositivo para a nuvem |6.000/seg/unidade (para S3), 120/seg/unidade (para S2), 12/seg/unidade (para S1). <br/>Mínimo de 100/s. |
| Envios da nuvem para o dispositivo | 83,33/seg/unidade (5.000/min/unidade) (para S3), 1,67/seg/unidade (100/min/unidade) (para S1 e S2). |
| Recebimentos da nuvem para o dispositivo |833,33/seg/unidade (50.000/min/unidade) (para S3), 16,67/seg/unidade (1.000/min/unidade) (para S1 e S2). |
| Operações de upload de arquivo |83.33 iniciações de upload de arquivo/seg/unidade (5.000/min/unidade) (para S3), iniciações de upload de arquivo 1.67/seg/unidade (100/min/unidade) (para S1 e S2). <br/> 10.000 URIs SAS podem estar fora de uma conta do Azure Storage de uma só vez.<br/>  10 URIs de SAS/dispositivo podem estar fora ao mesmo tempo. |
| Métodos diretos | 24 MB/seg/unidade (para S3), 480 KB/seg/unidade (para S2), 160 KB/seg/unidade (para S1).<br/> Com base no tamanho do medidor de estrangulamento de 8 KB. |
| Leituras de dispositivo gêmeo | 500/seg/unidade (para S3), Máximo de 100/seg ou 10/seg/unidade (para S2), 100/seg (para S1) |
| Atualizações de dispositivos gêmeos | 250/seg/unidade (para S3), Máximo de 50/seg ou 5/seg/unidade (para S2), 50/seg (para S1) |
| Operações de trabalhos <br/> (criar, atualizar, listar e excluir) | 83,33/seg/unidade (5.000/min/unidade) (para S3), 1,67/seg/unidade (100/min/unidade) (para S2), 1,67/seg/unidade (100/min/unidade) (para S1). |
| Taxa de transferência de operação de trabalhos por dispositivo | 50/seg/unidade (para S3), máximo de 10/seg ou 1/seg/unidade (para S2), 10/seg (para S1). |
| Taxa de inicialização do fluxo do dispositivo | 5 novos fluxos/seg (apenas para S1, S2, S3 e F1). |
