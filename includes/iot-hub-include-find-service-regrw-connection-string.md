---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756954"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Para criar uma política de acesso compartilhado que concede permissões **de gravação de conexão** e **registro** de serviços e obter uma seqüência de conexões para esta política, siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **grupos de recursos**. Selecione o grupo de recursos onde seu hub está localizado e, em seguida, selecione seu hub na lista de recursos.

1. No painel esquerdo do seu hub, selecione **políticas de acesso compartilhado**.

1. No menu superior acima da lista de políticas, selecione **Adicionar**.

1. Em **Adicionar uma diretiva de acesso compartilhado,** digite um nome descritivo para sua política, como *serviceAndRegistryReadWrite*. Em **Permissões,** selecione **Gravar gravação** e conectar **serviço**e selecione **Criar**. (A permissão **de leitura do Registro** é incluída automaticamente quando você **seleciona gravar registro**.)

    ![Mostrar como adicionar uma nova política de acesso compartilhado](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Selecione sua nova política na lista de políticas.

1. Em **Chaves de acesso compartilhadas,** selecione o ícone de cópia para a **seqüência de seqüência de conexões -- chave principal** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso compartilhadas do IoT Hub, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
