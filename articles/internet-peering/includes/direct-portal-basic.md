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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678555"
---
1. Selecione **Criar um recurso** > **Ver tudo**.

    > [!div class="mx-imgBorder"]
    > ![Peering de pesquisa](../media/setup-seeall.png)

1. Procure **por Peering** na caixa de pesquisa e **selecione Enter** em seu teclado. A partir dos resultados, selecione um recurso **peering.**

    > [!div class="mx-imgBorder"]
    > ![Peering de lançamento](../media/setup-launch.png)

1. Depois **de iniciar o Peering,** revise a página para entender os detalhes. Quando estiver pronto, selecione **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar peering](../media/setup-create.png)

1. Na **página Criar um peering,** na guia **Básico,** preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Guia De fundamentos de peering](../media/setup-basics-tab.png)

    * Selecione sua **Assinatura**Azure .
    * Para **o grupo Resource,** você pode escolher um grupo de recursos existente na lista de paradas ou criar um novo grupo selecionando **Criar novo**. Criaremos um novo grupo de recursos para este exemplo.
    * **O nome** corresponde ao nome do recurso e pode ser qualquer coisa que você escolher.
    * **A região** é selecionada automaticamente se você escolher um grupo de recursos existente. Se você optou por criar um novo grupo de recursos, você também precisa escolher a região do Azure onde deseja que o recurso resida.

        > [!NOTE]
        > A região onde reside um grupo de recursos é independente do local onde você deseja criar peering com a Microsoft. Mas é uma prática recomendada organizar seus recursos de peering dentro de grupos de recursos que residem nas regiões mais próximas do Azure. Por exemplo, para peerings em Ashburn, você pode criar um grupo de recursos no Leste dos EUA ou leste dos EUA2.

    * Selecione seu ASN na caixa **Peer ASN.**

        > [!IMPORTANT]
        > * Você só pode escolher um ASN com ValidationState como aprovado antes de enviar uma solicitação de peering. Se você acabou de enviar sua solicitação peerasn, aguarde por 12 horas ou mais para que a associação ASN seja aprovada. Se o ASN selecionado estiver pendente de validação, você verá uma mensagem de erro. 
        > * Se você não ver o ASN que você precisa escolher, verifique se você selecionou a assinatura correta. Se assim for, verifique se você já criou o PeerAsn usando [o Associate Peer ASN para a assinatura do Azure](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Noções básicas de peering preenchidas](../media/setup-direct-basics-filled-tab.png)

    * Selecione **Next : Configuração >** continuar.
