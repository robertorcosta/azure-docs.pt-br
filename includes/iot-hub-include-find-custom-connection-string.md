---
title: incluir arquivo
description: incluir arquivo
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "70050467"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Para criar uma política de acesso compartilhado que concede permissões de leitura **de conexão** e **registro** de serviços e obter uma seqüência de conexões para esta política, siga estas etapas:

1. No [portal Azure,](https://portal.azure.com)selecione **grupos de recursos**. Selecione o grupo de recursos onde seu hub está localizado e, em seguida, selecione seu hub na lista de recursos.

1. No painel esquerdo do seu hub, selecione **políticas de acesso compartilhado**.

1. No menu superior acima da lista de políticas, selecione **Adicionar**.

1. Em **Adicionar uma política de acesso compartilhado,** digite um nome descritivo para sua política, como *serviceAndRegistryRead*. Em **Permissões,** selecione **'Registro ler** **e'conectar serviço'** e, em seguida, selecione **Criar**.

    ![Mostrar como adicionar uma nova política de acesso compartilhado](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Selecione sua nova política na lista de políticas.

1. Em **Chaves de acesso compartilhadas,** selecione o ícone de cópia para a **seqüência de seqüência de conexões -- chave principal** e salve o valor.

    ![Mostrar como recuperar a cadeia de conexão](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

Para obter mais informações sobre as políticas e permissões de acesso compartilhadas do IoT Hub, consulte [controle de acesso e permissões](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
