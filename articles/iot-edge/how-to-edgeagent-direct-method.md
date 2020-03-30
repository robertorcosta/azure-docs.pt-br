---
title: Métodos diretos built-in edgeAgent - Azure IoT Edge
description: Monitore e gerencie uma implantação de IoT Edge usando métodos diretos incorporados no módulo de tempo de execução do agente IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240357"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunique-se com edgeAgent usando métodos diretos incorporados

Monitore e gerencie implantações de IoT Edge usando os métodos diretos incluídos no módulo de agente IoT Edge. Métodos diretos são implementados no dispositivo e, em seguida, podem ser invocados a partir da nuvem. O agente IoT Edge inclui métodos diretos que ajudam você a monitorar e gerenciar seus dispositivos IoT Edge remotamente.

Para obter mais informações sobre métodos diretos, como usá-los e como implementá-los em seus próprios módulos, consulte [Entender e invocar métodos diretos do IoT Hub](../iot-hub/iot-hub-devguide-direct-methods.md).

Os nomes desses métodos diretos são tratados como insensíveis a casos.

## <a name="ping"></a>Ping

O método **de ping** é útil para verificar se o IoT Edge está sendo executado em um dispositivo ou se o dispositivo tem uma conexão aberta ao IoT Hub. Use este método direto para ping arrematar o agente IoT Edge e obter o seu status. Um ping bem sucedido retorna uma carga vazia e **"status": 200**.

Por exemplo: 

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

No portal Azure, invoque o `ping` método com o nome `{}`do método e uma carga json vazia .

![Invoque o método direto 'ping' no portal Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Módulo de reinicialização

O método **RestartModule** permite o gerenciamento remoto de módulos em execução em um dispositivo IoT Edge. Se um módulo estiver relatando um estado falho ou outro comportamento insalubre, você pode acionar o agente ioT Edge para reiniciá-lo. Um comando de reinicialização bem-sucedido retorna uma carga útil vazia e **"status": 200**.

O método RestartModule está disponível na versão 1.0.9 do IoT Edge e posterior. 

Você pode usar o método direto RestartModule em qualquer módulo em execução em um dispositivo IoT Edge, incluindo o próprio módulo edgeAgent. No entanto, se você usar este método direto para desligar o edgeAgent, você não receberá um resultado de sucesso, uma vez que a conexão é interrompida enquanto o módulo é reiniciado.

Por exemplo: 

```azurecli
az iot hub invoke-module-method --method-name 'RestartModule' -n <hub name> -d <device name> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "id": "<module name>"
    }
'
```

No portal Azure, invoque o `RestartModule` método com o nome do método e a seguinte carga JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Invoque o método direto 'RestartModule' no portal Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Métodos experimentais

Novas opções de método direto estão disponíveis como recursos experimentais para testar, incluindo:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): Recupere os registros do módulo e carregue-os no Azure Blob Storage.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Verifique o status de uma solicitação de logs de upload.
* [GetLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): Recuperar logs de módulo inline na resposta do método direto.

## <a name="next-steps"></a>Próximas etapas

[Propriedades do agente do IoT Edge e dos gêmeos de módulo do hub do IoT Edge](module-edgeagent-edgehub.md)
