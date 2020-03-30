---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "68883839"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

Para obter a seqüência de conexões IoT Hub para a diretiva **registryReadWrite,** siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **grupos de recursos**. Selecione o grupo de recursos onde seu hub está localizado e, em seguida, selecione seu hub na lista de recursos.

2. No painel esquerdo do seu hub, selecione **políticas de acesso compartilhado**.

3. Na lista de políticas, selecione a diretiva **registroReadWrite.**

4. Em **Chaves de acesso compartilhadas,** selecione o ícone de cópia para a **seqüência de seqüência de conexões -- chave principal** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

Para obter mais informações sobre as políticas e permissões de acesso compartilhadas do IoT Hub, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
