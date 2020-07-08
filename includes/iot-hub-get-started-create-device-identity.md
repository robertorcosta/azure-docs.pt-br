---
title: incluir arquivo
description: incluir arquivo
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 68260bf8aafbbe5afd46ec7dfb763eb88ee2123e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78893040"
---
Nesta seção, você usa a CLI do Azure para criar uma identidade do dispositivo para este artigo. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Abra o [Azure Cloud Shell](https://shell.azure.com/).

1. No Azure Cloud Shell, execute o seguinte comando para instalar a Extensão de IoT do Microsoft Azure para a CLI do Azure:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Crie uma identidade do dispositivo chamada `myDeviceId` e recupere a cadeia de conexão do dispositivo com estes comandos:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anote a cadeia de conexão do dispositivo no resultado. Essa cadeia de conexão é usada pelo aplicativo do dispositivo para se conectar ao seu Hub IoT como um dispositivo.

<!-- images and links -->
