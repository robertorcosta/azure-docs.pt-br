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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "78893040"
---
Nesta seção, você usa o Azure CLI para criar uma identidade de dispositivo para este artigo. As IDs de Dispositivo diferenciam maiúsculas de minúsculas.

1. Abra [a Cloud Shell Do Azure](https://shell.azure.com/).

1. No Azure Cloud Shell, execute o seguinte comando para instalar a extensão De IoT do Microsoft Azure para Azure CLI:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

2. Crie uma nova `myDeviceId` identidade de dispositivo chamada e recupere a seqüência de conexão do dispositivo com esses comandos:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

Anote a cadeia de conexão do dispositivo no resultado. Essa cadeia de conexão é usada pelo aplicativo do dispositivo para se conectar ao seu Hub IoT como um dispositivo.

<!-- images and links -->
