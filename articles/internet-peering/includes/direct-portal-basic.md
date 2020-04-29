---
title: incluir arquivo
titleSuffix: Azure
description: incluir arquivo
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 00c24212706555667ad4680c086ece24f15ca59a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81678555"
---
1. Selecione **criar um recurso** > **ver tudo**.

    > [!div class="mx-imgBorder"]
    > ![Pesquisar emparelhamento](../media/setup-seeall.png)

1. Pesquise **emparelhamento** na caixa de pesquisa e selecione **Enter** no teclado. Nos resultados, selecione um recurso de **emparelhamento** .

    > [!div class="mx-imgBorder"]
    > ![Iniciar emparelhamento](../media/setup-launch.png)

1. Depois que o **emparelhamento** for iniciado, examine a página para entender os detalhes. Quando estiver pronto, selecione **criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar emparelhamento](../media/setup-create.png)

1. Na página **criar um emparelhamento** , na guia **noções básicas** , preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Guia noções básicas de emparelhamento](../media/setup-basics-tab.png)

    * Selecione sua **assinatura**do Azure.
    * Para o **grupo de recursos**, você pode escolher um grupo de recursos existente na lista suspensa ou criar um novo grupo selecionando **criar novo**. Vamos criar um novo grupo de recursos para este exemplo.
    * **Nome** corresponde ao nome do recurso e pode ser qualquer coisa que você escolher.
    * A **região** será selecionada se você escolher um grupo de recursos existente. Se você optar por criar um novo grupo de recursos, também precisará escolher a região do Azure onde deseja que o recurso resida.

        > [!NOTE]
        > A região em que um grupo de recursos reside é independente do local onde você deseja criar o emparelhamento com a Microsoft. Mas é uma prática recomendada organizar seus recursos de emparelhamento em grupos de recursos que residem nas regiões do Azure mais próximas. Por exemplo, para emparelhamentos no Ashburn, você pode criar um grupo de recursos no leste dos EUA ou no leste dos EUA 2.

    * Selecione seu ASN na caixa de **rede de mesmo nível ASN** .

        > [!IMPORTANT]
        > * Você só pode escolher um ASN com ValidationState como aprovado antes de enviar uma solicitação de emparelhamento. Se você acabou de enviar sua solicitação PeerAsn, aguarde 12 horas ou para que a associação ASN seja aprovada. Se o ASN selecionado tiver validação pendente, você verá uma mensagem de erro. 
        > * Se você não vir o ASN que precisa escolher, verifique se selecionou a assinatura correta. Nesse caso, verifique se você já criou o PeerAsn usando [associar o ASN do peer à assinatura do Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Noções básicas de emparelhamento preenchidas](../media/setup-direct-basics-filled-tab.png)

    * Selecione **Avançar: configuração >** para continuar.
