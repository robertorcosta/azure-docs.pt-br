---
title: Entender mensagens de nuvem para o dispositivo Hub IoT do Azure| Microsoft Docs
description: Este guia de desenvolvedores discute como usar mensagens nuvem-dispositivo com seu hub de IoT. Ele inclui informações sobre o ciclo de vida da mensagem e as opções de configuração.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 3a7254cc9de89a297811792b4dd64b4b669ba8e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271233"
---
# <a name="send-cloud-to-device-messages-from-an-iot-hub"></a>Envie mensagens nuvem-para-dispositivo de um hub de IoT

Para enviar notificações unidirecionais para um aplicativo de dispositivo a partir do back-end da solução, envie mensagens nuvem-para-dispositivo do seu hub de IoT para o seu dispositivo. Para uma discussão sobre outras opções de nuvem para dispositivo suportadas pelo Azure IoT Hub, consulte [orientação de comunicações nuvem-para-dispositivo](iot-hub-devguide-c2d-guidance.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Você envia mensagens nuvem-para-dispositivo através de um ponto final voltado para o serviço, */mensagens/dispositivobound*. Em seguida, um dispositivo recebe as mensagens através de um ponto final específico do dispositivo, */devices/{deviceId}/messages/devicebound*.

Para direcionar cada mensagem nuvem-dispositivo em um único dispositivo, seu hub IoT define a propriedade **to** */devices/{deviceId}/messages/devicebound*.

Cada fila de dispositivos contém, no máximo, 50 mensagens de nuvem para dispositivo. Tentar enviar mais mensagens para o mesmo dispositivo resulta em um erro.

## <a name="the-cloud-to-device-message-life-cycle"></a>O ciclo de vida da mensagem nuvem-para-dispositivo

Para garantir a entrega de mensagens pelo menos uma vez, seu hub de IoT persiste mensagens nuvem-dispositivo em filas por dispositivo. Para que o hub de IoT remova as mensagens da fila, os dispositivos devem reconhecer explicitamente a *conclusão*. Essa abordagem garante a resiliência contra conectividade e falhas de dispositivo.

O gráfico de estado do ciclo de vida é exibido no seguinte diagrama:

![Ciclo de vida da mensagem nuvem-para-dispositivo](./media/iot-hub-devguide-messages-c2d/lifecycle.png)

Quando o serviço de hub IoT envia uma mensagem para um dispositivo, o serviço define o estado de mensagem para *Enuued*. Quando um dispositivo deseja *receber* uma mensagem, o hub IoT *bloqueia* a mensagem definindo o estado como *Invisível*. Este estado permite que outros segmentos no dispositivo comecem a receber outras mensagens. Quando um segmento de dispositivo completa o processamento de uma mensagem, ele notifica o hub IoT *completando* a mensagem. O hub ioT então define o estado como *Concluído*.

Um dispositivo também pode:

* *Rejeite* a mensagem, o que faz com que o hub de IoT a defina para o estado *de letra de Morto.* Os dispositivos que se conectam ao Protocolo de Transporte de Telemetria de Fila de Mensagens (MQTT) não podem rejeitar mensagens de nuvem para dispositivo.

* *Abandone* a mensagem, o que faz com que o hub de IoT coloque a mensagem de volta na fila, com o estado definido como *Enuued*. Os dispositivos que se conectam pelo Protocolo MQTT não podem abandonar mensagens de nuvem para dispositivo.

Um thread pode falhar em processar uma mensagem sem notificar o hub IoT. Neste caso, as mensagens transitam automaticamente do estado *invisível* de volta para o estado *Enqueued* após um tempo de *visibilidade* *(ou* bloqueio de tempo). O valor deste intervalo é de um minuto e não pode ser alterado.

A propriedade **de contagem máxima** de entrega no hub IoT determina o número máximo de vezes que uma mensagem pode fazer a transição entre os estados *Enuued* e *Invisible.* Após esse número de transições, o hub ioT define o estado da mensagem para *Dead lettered*. Da mesma forma, o hub IoT define o estado de uma mensagem para *Dead lettered* após seu tempo de expiração. Para obter mais informações, consulte [Tempo de viver](#message-expiration-time-to-live).

O [artigo Como enviar mensagens nuvem-dispositivo com](iot-hub-csharp-csharp-c2d.md) o artigo do IoT Hub mostra como enviar mensagens de nuvem para dispositivo da nuvem e recebê-las em um dispositivo.

Um dispositivo normalmente completa uma mensagem nuvem-dispositivo quando a perda da mensagem não afeta a lógica do aplicativo. Um exemplo disso pode ser quando o dispositivo persistiu o conteúdo da mensagem localmente ou executou com sucesso uma operação. A mensagem também poderia levar informações transitórias, cuja perda não afetaria a funcionalidade do aplicativo. Às vezes, para tarefas de execução longa, você pode:

* Complete a mensagem nuvem-dispositivo depois que o dispositivo persistir a descrição da tarefa no armazenamento local.

* Notificar o back-end da solução com uma ou mais mensagens de dispositivo para a nuvem em vários estágios de progresso da tarefa.

## <a name="message-expiration-time-to-live"></a>Expiração da mensagem (vida útil)

Todas as mensagens da nuvem para o dispositivo têm um tempo de expiração. Este tempo é definido por qualquer um dos seguintes:

* A propriedade **ExpiryTimeUtc** no serviço
* O hub de IoT, usando o tempo padrão *para viver* que é especificado como uma propriedade de hub IoT

Consulte [Opções de configuração da nuvem para o dispositivo](#cloud-to-device-configuration-options).

Uma maneira comum de aproveitar o vencimento de uma mensagem e evitar o envio de mensagens para dispositivos desconectados é definir *pouco tempo para viver* valores. Essa abordagem alcança o mesmo resultado que manter o estado de conexão do dispositivo, mas é mais eficiente. Quando você solicita reconhecimentos de mensagens, o hub ioT notifica quais dispositivos são:

* São capazes de receber mensagens.
* Não estão online ou falharam.

## <a name="message-feedback"></a>Comentários da mensagem

Quando você envia uma mensagem nuvem-para-dispositivo, o serviço pode solicitar a entrega de feedback por mensagem sobre o estado final dessa mensagem. Você faz isso definindo a propriedade do aplicativo **iothub-ack** na mensagem nuvem-para-dispositivo que está sendo enviada para um dos quatro valores a seguir:

| Valor da propriedade de Ack | Comportamento |
| ------------ | -------- |
| none     | O hub de IoT não gera uma mensagem de feedback (comportamento padrão). |
| positivo | Se a mensagem nuvem-dispositivo atingir o estado *Concluído,* o hub IoT gerará uma mensagem de feedback. |
| negativo | Se a mensagem nuvem-para-dispositivo chegar ao estado *com letras mortas,* o hub IoT gerará uma mensagem de feedback. |
| completa     | O hub ioT gera uma mensagem de feedback em ambos os casos. |

Se o valor **de Ack** estiver *completo*e você não receber uma mensagem de feedback, significa que a mensagem de feedback expirou. O serviço não pode saber o que aconteceu com a mensagem original. Na prática, um serviço deve garantir que possa processar os comentários antes que eles expirem. O tempo máximo de validade é de dois dias, o que deixa o tempo para fazer o serviço funcionar novamente se ocorrer uma falha.

Como explicado no [Endpoints,](iot-hub-devguide-endpoints.md)o hub de IoT fornece feedback através de um ponto final voltado para o serviço, */messages/servicebound/feedback,* como mensagens. A semântica de recebimento dos comentários é a mesma das mensagens da nuvem para o dispositivo. Sempre que possível, os comentários de mensagem são feitos em lotes em uma única mensagem, com o seguinte formato:

| Propriedade     | Descrição |
| ------------ | ----------- |
| EnqueuedTime | Um carimbo de data e hora que indica quando a mensagem de feedback foi recebida pelo hub |
| UserId       | `{iot hub name}` |
| ContentType  | `application/vnd.microsoft.iothub.feedback.json` |

O corpo é uma matriz de registros serializada em JSON, cada um com as seguintes propriedades:

| Propriedade           | Descrição |
| ------------------ | ----------- |
| EnqueuedTimeUtc    | Um carimbo de data e hora que indica quando o resultado da mensagem aconteceu (por exemplo, o hub recebeu a mensagem de feedback ou a mensagem original expirou) |
| OriginalMessageId  | O *MessageId* da mensagem nuvem-para-dispositivo à qual essas informações de feedback se relacionam |
| StatusCode         | Uma seqüência necessária, usada em mensagens de feedback geradas pelo hub IoT: <br/> *Sucesso* <br/> *Expirado* <br/> *Contagem de entregasexcedida* <br/> *Rejeitado* <br/> *Purgado* |
| Descrição        | Valores de string para *StatusCode* |
| deviceId           | O *Dispositivo Id* do dispositivo de destino da mensagem nuvem-dispositivo a que este pedaço de feedback se relaciona |
| DeviceGenerationId | O *DeviceGenerationId* do dispositivo de destino da mensagem nuvem-para-dispositivo a que este pedaço de feedback se relaciona |

Para que a mensagem nuvem-dispositivo correlacione seu feedback com a mensagem original, o serviço deve especificar um *MessageId*.

O corpo de uma mensagem de feedback é mostrado no seguinte código:

```json
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0,
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

**Feedback pendente para dispositivos excluídos**

Quando um dispositivo é excluído, qualquer comentário pendente também é excluído. O feedback do dispositivo é enviado em lotes. Se um dispositivo for excluído na janela estreita (muitas vezes menos de 1 segundo) entre quando o dispositivo confirmar o recebimento da mensagem e quando o próximo lote de feedback for preparado, o feedback não ocorrerá.

Você pode abordar esse comportamento esperando um período de tempo para que o feedback pendente chegue antes de excluir seu dispositivo. O feedback de mensagem relacionado deve ser assumido perdido uma vez que um dispositivo é excluído.

## <a name="cloud-to-device-configuration-options"></a>Opções de configuração da nuvem para o dispositivo

Cada Hub IoT expõe as seguintes opções de configuração para mensagens de nuvem para o dispositivo:

| Propriedade                  | Descrição | Intervalo e padrão |
| ------------------------- | ----------- | ----------------- |
| defaultTtlAsIso8601       | TTL padrão para mensagens cloud-to-device | ISO_8601 intervalo de até 2 dias (mínimo de 1 minuto); padrão: 1 hora |
| maxDeliveryCount          | Contagem máxima de entrega para filas de nuvem para dispositivo por dispositivo | 1 a 100; padrão: 10 |
| feedback.ttlAsIso8601     | Retenção de mensagens de feedback vinculadas ao serviço | ISO_8601 intervalo de até 2 dias (mínimo de 1 minuto); padrão: 1 hora |
| feedback.maxDeliveryCount | Contagem máxima de entrega para a fila de feedback | 1 a 100; padrão: 10 |
| feedback.lockDurationAsIso8601 | Contagem máxima de entrega para a fila de feedback | ISO_8601 intervalo de 5 a 300 segundos (mínimo de 5 segundos); padrão: 60 segundos. |

Você pode definir as opções de configuração de uma das seguintes maneiras:

* **Portal Azure**: Em **Configurações** em seu hub ioT, selecione **pontos finais incorporados** e expanda **a Nuvem para mensagens de dispositivos**. (A definição das propriedades **feedback.maxDeliveryCount** e **feedback.lockDurationAsIso8601** não é suportada no portal Azure.)

    ![Defina opções de configuração para mensagens cloud-to-device no portal](./media/iot-hub-devguide-messages-c2d/c2d-configuration-portal.png)

* **Azure CLI**: Use o comando [az iot hub update:](https://docs.microsoft.com/cli/azure/iot/hub?view=azure-cli-latest#az-iot-hub-update)

    ```azurecli
    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.defaultTtlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.ttlAsIso8601=PT1H0M0S

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.maxDeliveryCount=10

    az iot hub update --name {your IoT hub name} \
        --set properties.cloudToDevice.feedback.lockDurationAsIso8601=PT0H1M0S
    ```

## <a name="next-steps"></a>Próximas etapas

Para obter informações sobre os SDKs que você pode usar para receber mensagens de nuvem para dispositivo, consulte [SDKs IoT do Azure](iot-hub-devguide-sdks.md).

Para experimentar a recepção de mensagens de nuvem para dispositivo, consulte o tutorial [Enviar de nuvem para dispositivo ](iot-hub-csharp-csharp-c2d.md).
