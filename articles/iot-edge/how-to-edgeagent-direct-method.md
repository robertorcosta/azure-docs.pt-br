---
title: Métodos diretos internos do edgeAgent-Azure IoT Edge
description: Monitorar e gerenciar uma implantação de IoT Edge usando métodos diretos internos no módulo de tempo de execução do agente de IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/02/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57b9d46918414cef9e8cbcffb941b98c98f985ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80240357"
---
# <a name="communicate-with-edgeagent-using-built-in-direct-methods"></a>Comunicar-se com o edgeAgent usando métodos diretos internos

Monitore e gerencie implantações de IoT Edge usando os métodos diretos incluídos no módulo IoT Edge Agent. Os métodos diretos são implementados no dispositivo e, em seguida, podem ser invocados na nuvem. O agente de IoT Edge inclui métodos diretos que ajudam a monitorar e gerenciar seus dispositivos de IoT Edge remotamente.

Para obter mais informações sobre métodos diretos, como usá-los e como implementá-los em seus próprios módulos, consulte [entender e invocar métodos diretos do Hub IOT](../iot-hub/iot-hub-devguide-direct-methods.md).

Os nomes desses métodos diretos são tratados sem diferenciação de maiúsculas e minúsculas.

## <a name="ping"></a>Ping

O método **ping** é útil para verificar se IOT Edge está em execução em um dispositivo ou se o dispositivo tem uma conexão aberta com o Hub IOT. Use esse método direto para executar ping no agente de IoT Edge e obter seu status. Um ping bem-sucedido retorna uma carga vazia e **"status": 200**.

Por exemplo:

```azurecli
az iot hub invoke-module-method --method-name 'ping' -n <hub name> -d <device name> -m '$edgeAgent'
```

Na portal do Azure, invoque o método com o nome `ping` do método e uma carga `{}`JSON vazia.

![Invocar método direto ' ping ' em portal do Azure](./media/how-to-edgeagent-direct-method/ping-direct-method.png)

## <a name="restart-module"></a>Reiniciar o módulo

O método **RestartModule** permite o gerenciamento remoto de módulos em execução em um dispositivo IOT Edge. Se um módulo estiver relatando um estado de falha ou outro comportamento não íntegro, você poderá disparar o agente de IoT Edge para reiniciá-lo. Um comando de reinicialização bem-sucedido retorna uma carga vazia e **"status": 200**.

O método RestartModule está disponível em IoT Edge versão 1.0.9 e posterior. 

Você pode usar o método direto RestartModule em qualquer módulo em execução em um dispositivo IoT Edge, incluindo o próprio módulo edgeAgent. No entanto, se você usar esse método direto para desligar o edgeAgent, você não receberá um resultado de êxito, pois a conexão é interrompida enquanto o módulo é reiniciado.

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

Na portal do Azure, invoque o método com o nome `RestartModule` do método e o seguinte conteúdo JSON:

```json
{
    "schemaVersion": "1.0",
    "id": "<module name>"
}
```

![Invocar o método direto ' RestartModule ' no portal do Azure](./media/how-to-edgeagent-direct-method/restartmodule-direct-method.png)

## <a name="experimental-methods"></a>Métodos experimentais

Novas opções de método direto estão disponíveis como recursos experimentais para teste, incluindo:

* [UploadLogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md): recuperar logs de módulo e carregá-los no armazenamento de BLOBs do Azure.
* [GetTaskStatus](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#gettaskstatus): Verifique o status de uma solicitação de logs de upload.
* [Getlogs](https://github.com/Azure/iotedge/blob/master/doc/built-in-logs-pull.md#getlogs): recuperar logs de módulo embutidos na resposta do método direto.

## <a name="next-steps"></a>Próximas etapas

[Propriedades do agente do IoT Edge e dos gêmeos de módulo do hub do IoT Edge](module-edgeagent-edgehub.md)
