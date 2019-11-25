---
title: Operar dispositivos offline – Azure IoT Edge | Microsoft Docs
description: Saiba como módulos e dispositivos IoT Edge podem operar sem conexão à Internet por longos períodos e como o IoT Edge pode permitir que dispositivos IoT regulares operem offline também.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b16a8d8ddd4ac23a59db8e7fed48f1c39752d130
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456880"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Understand extended offline capabilities for IoT Edge devices, modules, and child devices

Azure IoT Edge supports extended offline operations on your IoT Edge devices, and enables offline operations on non-IoT Edge child devices too. Contanto que um dispositivo do IoT Edge tenha tido uma oportunidade de se conectar ao Hub IoT, ele e todos os dispositivos filho podem continuar a funcionar com uma conexão com a Internet intermitente ou sem nenhuma conexão. 


## <a name="how-it-works"></a>Como funciona

Quando um dispositivo do IoT Edge entra em modo offline, o hub do IoT Edge assume três funções. Primeiro, ele armazena todas as mensagens que estão no sentido upstream e as salva até que o dispositivo se reconecte. Em segundo lugar, ele atua em nome do Hub IoT para autenticar dispositivos filho e módulos para que eles possam continuar a operar. Em terceiro lugar, ele permite a comunicação entre os dispositivos filho que normalmente passariam pelo Hub IoT. 

O exemplo a seguir mostra como um cenário de IoT Edge opera no modo offline:

1. **Configure devices**

   Dispositivos do IoT Edge automaticamente têm recursos offline habilitados. Para estender essa funcionalidade para outros dispositivos de IoT, você precisa declarar uma relação de pai-filho entre os dispositivos no Hub IoT. Then, you configure the child devices to trust their assigned parent device and route the device-to-cloud communications through the parent as a gateway. 

2. **Sync with IoT Hub**

   Pelo menos uma vez após a instalação do runtime do IoT Edge, o dispositivo do IoT Edge precisa estar online para sincronizar com o Hub IoT. Nessa sincronização, o dispositivo do IoT Edge obtém detalhes sobre todos os dispositivos filho atribuídos a ele. O dispositivo do IoT Edge também atualiza seu cache local com segurança para permitir operações offline e recupera as configurações para o armazenamento local de mensagens de telemetria. 

3. **Go offline**

   Enquanto estiver desconectado do Hub IoT, o dispositivo do IoT Edge, seus módulos implantados e quaisquer dispositivos de IoT filho poderão operar indefinidamente. Os módulos e dispositivos filho podem ser iniciados e reiniciados ao autenticarem com o hub do IoT Edge enquanto estiverem offline. A telemetria upstream associada para o Hub IoT é armazenada localmente. A comunicação entre os módulos ou entre dispositivos de IoT filho é mantida por meio de métodos diretos ou mensagens. 

4. **Reconnect and resync with IoT Hub**

   Quando a conexão com o Hub IoT for restaurada, o dispositivo do IoT Edge será sincronizado novamente. As mensagens armazenadas localmente são entregues na mesma ordem em que foram armazenadas. Todas as diferenças entre as propriedades desejadas e relatadas dos módulos e dispositivos são reconciliadas. O dispositivo do IoT Edge atualiza qualquer alteração de seu conjunto de dispositivos de IoT filho atribuídos.

## <a name="restrictions-and-limits"></a>Restrições e limites

The extended offline capabilities described in this article are available in [IoT Edge version 1.0.7 or higher](https://github.com/Azure/azure-iotedge/releases). As versões anteriores têm um subconjunto de recursos offline. Os dispositivos do IoT Edge existentes que não têm recursos offline estendidos não podem ser atualizados alterando a versão de runtime, mas devem ser reconfigurados com uma nova identidade do dispositivo do IoT Edge para obter esses recursos. 

O suporte offline estendido está disponível em todas as regiões onde o Hub IoT está disponível, **exceto** leste dos EUA.

Only non-IoT Edge devices can be added as child devices. 

IoT Edge devices and their assigned child devices can function indefinitely offline after the initial, one-time sync. However, storage of messages depends on the time to live (TTL) setting and the available disk space for storing the messages. 

## <a name="set-up-parent-and-child-devices"></a>Set up parent and child devices

For an IoT Edge device to extend its extended offline capabilities to child IoT devices, you need to complete two steps. First, declare the parent-child relationships in the Azure portal. Second, create a trust relationship between the parent device and any child devices, then configure device-to-cloud communications to go through the parent as a gateway. 

### <a name="assign-child-devices"></a>Atribuir dispositivos filho

Child devices can be any non-IoT Edge device registered to the same IoT Hub. Parent devices can have multiple child devices, but a child device only has one parent. There are three options to set child devices to an edge device: through the Azure portal, using the Azure CLI, or using the IoT Hub service SDK. 

The following sections provide examples of how you can declare the parent/child relationship in IoT Hub for existing IoT devices. If you're creating new device identities for your child devices, see [Authenticate a downstream device to Azure IoT Hub](how-to-authenticate-downstream-device.md) for more information.

#### <a name="option-1-iot-hub-portal"></a>Option 1: IoT Hub Portal

You can declare the parent-child relationship when creating a new device. Or for existing devices, you can declare the relationship from the device details page of either the parent IoT Edge device or the child IoT device. 

   ![Gerenciar dispositivos filho da página de detalhes do dispositivo do IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Option 2: Use the `az` command-line tool

Using the [Azure command-line interface](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) with [IoT extension](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 or newer), you can manage parent child relationships with the [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) subcommands. The example below uses a query to assign all non-IoT Edge devices in the hub to be child devices of an IoT Edge device. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

You can modify the [query](../iot-hub/iot-hub-devguide-query-language.md) to select a different subset of devices. The command may take several seconds if you specify a large set of devices.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Option 3: Use IoT Hub Service SDK 

Finally, you can manage parent child relationships programmatically using either C#, Java or Node.js IoT Hub Service SDK. Here is an [example of assigning a child device](https://aka.ms/set-child-iot-device-c-sharp) using the C# SDK.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Set up the parent device as a gateway

You can think of a parent/child relationship as a transparent gateway, where the child device has its own identity in IoT Hub but communicates through the cloud via its parent. For secure communication, the child device needs to be able to verify that the parent device comes from a trusted source. Otherwise, third-parties could set up malicious devices to impersonate parents and intercept communications. 

One way to create this trust relationship is described in detail in the following articles: 
* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Connect a downstream (child) device to an Azure IoT Edge gateway](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especificar servidores de DNS 

To improve robustness, it is highly recommended you specify the DNS server addresses used in your environment. To set your DNS server for IoT Edge, see the resolution for [Edge Agent module continually reports 'empty config file' and no modules start on device](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device) in the troubleshooting article.

## <a name="optional-offline-settings"></a>Configurações offline opcionais

If your devices go offline, the IoT Edge parent device stores all device-to-cloud messages until the connection is reestablished. The IoT Edge hub module manages the storage and forwarding of offline messages. For devices that may go offline for extended periods of time, optimize performance by configuring two IoT Edge hub settings. 

First, increase the time to live setting so that the IoT Edge hub will keep messages long enough for your device to reconnect. Em seguida, adicione mais espaço em disco para o armazenamento de mensagens. 

### <a name="time-to-live"></a>Vida útil

A configuração de vida útil é a quantidade de tempo (em segundos) que uma mensagem pode esperar para ser entregue antes de expirar. O padrão é 7200 segundos (duas horas). The maximum value is only limited by the maximum value of an integer variable, which is around 2 billion. 

Essa configuração é uma propriedade desejada do hub do IoT Edge, que é armazenada no gêmeo do módulo. You can configure it in the Azure portal or directly in the deployment manifest. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Host storage for system modules

Messages and module state information are stored in the IoT Edge hub's local container filesystem by default. For improved reliability, especially when operating offline, you can also dedicate storage on the host IoT Edge device. For more information, see [Give modules access to a device's local storage](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>Próximos passos

Learn more about how to set up a transparent gateway for your parent/child device connections: 

* [Configure um dispositivo IoT Edge para atuar como um gateway transparente](how-to-create-transparent-gateway.md)
* [Autenticar um dispositivo downstream no Hub IoT do Azure](how-to-authenticate-downstream-device.md)
* [Conectar um dispositivo downstream para um gateway do Azure IoT Edge](how-to-connect-downstream-device.md)
