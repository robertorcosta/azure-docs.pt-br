---
title: Usar a extensão do Azure IoT para a CLI do Azure para interagir com dispositivos IoT Plug and Play Preview | Microsoft Docs
description: Instale a extensão do Azure IoT para a CLI do Azure e use-a para interagir com os dispositivos IoT Plug and Play conectados ao meu Hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 3699213fe61c64d7677ba026a8df54ccbbfe4b33
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352093"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instalar e usar a extensão do Azure IoT para a CLI do Azure

A [CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure como o Hub IoT. A CLI do Azure está disponível no Windows, Linux e MacOS. A CLI do Azure também está pré-instalada no [Azure Cloud Shell](https://shell.azure.com). A CLI do Azure permite gerenciar recursos do Hub IoT do Azure, instâncias do Serviço de Provisionamento de Dispositivos e hubs vinculados sem instalar extensões.

A extensão do Azure IoT para a CLI do Azure é uma ferramenta de linha de comando para interagir com dispositivos IoT Plug and Play Preview e testá-los. É possível usar a extensão para:

- Conectar-se a um dispositivo.
- Exibir a telemetria enviada pelo dispositivo.
- Trabalhar com propriedades do dispositivo.
- Chamar os comandos do dispositivo.

Este artigo mostra como:

- Instalar e configurar a extensão do Azure IoT para a CLI do Azure.
- Usar a extensão para interagir com seus dispositivos e testá-los.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instalar a extensão do Azure IoT para a CLI do Azure

### <a name="step-1---install-the-azure-cli"></a>Etapa 1 – Instalar a CLI do Azure

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar a CLI do Azure no seu ambiente. Para obter a melhor experiência, sua versão de CLI do Azure deve ser a versão 2.9.1 ou superior. Use `az -–version` para validar.

### <a name="step-2---install-iot-extension"></a>Etapa 2 – Instalar a extensão de IoT

[O Leiame da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias maneiras de instalar a extensão. A maneira mais simples é executar `az extension add --name azure-iot`. Após a instalação, você pode usar `az extension list` para validar as extensões instaladas no momento ou `az extension show --name azure-iot` para ver os detalhes sobre a extensão de IoT. No momento da gravação, o número de versão da extensão é `0.9.7` .

Para remover a extensão, você pode usar `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Usar a extensão do Azure IoT para a CLI do Azure

### <a name="prerequisites"></a>Pré-requisitos

Para entrar em sua assinatura do Azure, execute o seguinte comando:

```azurecli
az login
```

> [!NOTE]
> Se você estiver usando o Azure Cloud Shell, estará automaticamente conectado e não precisará executar o comando anterior.

Para usar a extensão do Azure IoT para a CLI do Azure, você precisa de:

- Um Hub IoT do Azure. Há várias maneiras de adicionar um hub IoT à sua assinatura do Azure, como [Criar um hub IoT usando o CLI do Azure](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de conexão do hub IoT para executar os comandos de extensão do Azure IoT. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um dispositivo registrado em seu hub IoT. É possível usar o seguinte comando da CLI do Azure para registrar um dispositivo. Não se esqueça de substituir os espaços reservados `{YourIoTHubName}` e `{YourDeviceID}` pelos seus valores:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Interagir com um dispositivo

É possível usar a extensão para exibir e interagir com dispositivos IoT Plug and Play conectados a um hub IoT. A extensão funciona com o gêmeo digital que representa o dispositivo IoT Plug and Play.

#### <a name="list-devices"></a>Lista de dispositivos

Listar todos os dispositivos em um Hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Exibir o comutador digital

Exibir a exibição digital de um dispositivo:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Propriedades

Defina o valor de uma propriedade de leitura/gravação:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Comandos

Invocar um comando:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Eventos de gêmeo digital

Monitorar todos os eventos de gêmeo digital do IoT Plug and Play em um dispositivo e uma interface específicos que vão para o grupo de consumidores do hub de eventos **$Default**:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo de instruções, você aprendeu a instalar e a usar a extensão do Azure IoT para a CLI do Azure a fim de interagir com seus dispositivos Plug and Play. Uma próxima etapa sugerida é aprender a usar o [Azure IOT Explorer com seus dispositivos](./howto-use-iot-explorer.md).
