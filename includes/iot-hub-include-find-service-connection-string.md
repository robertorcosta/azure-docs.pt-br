---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "69558461"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

Para obter a seqüência de conexões IoT Hub para a política **de serviço,** siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **grupos de recursos**. Selecione o grupo de recursos onde seu hub está localizado e, em seguida, selecione seu hub na lista de recursos.

1. No painel esquerdo do seu hub ioT, selecione **políticas de acesso compartilhado**.

1. Na lista de políticas, selecione a política **de serviço.**

1. Em **Chaves de acesso compartilhadas,** selecione o ícone de cópia para a **seqüência de seqüência de conexões -- chave principal** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso compartilhadas do IoT Hub, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
