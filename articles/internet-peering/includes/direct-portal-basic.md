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
ms.openlocfilehash: 79cf4b2edd1a25df427cf15f9ee7f2f331ebd2df
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774634"
---
1. Clique **em Criar um recurso** > **Ver tudo**.

    > [!div class="mx-imgBorder"]
    > ![Peering de pesquisa](../media/setup-seeall.png)

1. Procure *peering* na caixa de pesquisa e aperte *Enter* em seu teclado. A partir dos resultados, clique no recurso **Peering.**

    > [!div class="mx-imgBorder"]
    > ![Peering de lançamento](../media/setup-launch.png)

1. Assim **que o Peering** for lançado, revise a página para entender os detalhes. Quando estiver pronto, clique **em Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar peering](../media/setup-create.png)

1. Na **página Criar um peering,** na guia **Básico,** preencha os campos conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Noções básicas de peering](../media/setup-basics-tab.png)

    * Escolha sua **assinatura**do Azure .
    * Para **o grupo Resource,** você pode escolher um grupo de recursos existente a partir do drop-down ou criar um novo grupo clicando **em Criar novo**. Criaremos um novo grupo de recursos para este exemplo.
    * **O nome** corresponde ao nome do recurso e pode ser qualquer coisa que você escolher.
    * **A região** será selecionada automaticamente se você escolher um grupo de recursos existente na etapa acima. Se você optou por criar um novo grupo de recursos, então você também precisa escolher a região do Azure onde você deseja que o recurso resida. Leste dos EUA

        > [!NOTE]
        > A região onde reside o grupo de recursos é independente do local onde você deseja criar peering com a Microsoft. Mas é melhor organizar seus recursos de peering dentro de grupos de recursos que residem nas regiões mais próximas do Azure. Por exemplo: para peerings em Ashburn, você pode criar um grupo de recursos no *Leste dos EUA* ou leste dos *EUA2*

    * Escolha seu ASN no campo **Peer ASN.**

        > [!IMPORTANT]
        > * Você só pode escolher um ASN com ValidationState como "Aprovado" antes de enviar uma solicitação de peering. Se você acabou de enviar sua solicitação peerasn, aguarde por 12 horas ou mais para que a associação ASN seja "aprovada". Se o ASN selecionado estiver pendente de validação, você verá uma mensagem de erro. 
        > * Se você não ver o ASN que você precisa escolher, então verifique se você selecionou a assinatura correta. Se assim for, verifique se você já criou o PeerAsn usando [o Associate Peer ASN to Azure Subscription](../howto-subscription-association-portal.md).

        > [!div class="mx-imgBorder"]
        > ![Noções básicas de peering preenchidas](../media/setup-direct-basics-filled-tab.png)

    * Clique em **Next : Configuração >** para continuar.
