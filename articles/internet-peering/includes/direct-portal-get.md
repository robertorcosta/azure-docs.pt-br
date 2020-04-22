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
ms.openlocfilehash: e50f53c65c330265d3d9ec1b1804a7910b632b1d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678841"
---
1. Vá para **grupos de**recursos e selecione o grupo de recursos selecionado quando criou o recurso **Peering.** Use a **caixa Filtro** se você tiver muitos grupos de recursos.

    > [!div class="mx-imgBorder"]
    > ![Grupos de recursos](../media/setup-direct-get-resourcegroup.png)

1. Selecione o recurso **peering** criado por você.

    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-get-open.png)

1. A **página Visão Geral** mostra informações de alto nível, como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Painel de visão geral de recursos de peering](../media/setup-direct-get-overview.png)

1. À esquerda, selecione **as informações ASN** para visualizar as informações enviadas quando você criou o PeerAsn.

    > [!div class="mx-imgBorder"]
    > ![Informações ASN de recursos de peering](../media/setup-direct-get-asninfo.png)

1. À esquerda, selecione **Conexões**. Na parte superior da tela, você vê um resumo das conexões de peering entre sua ASN e microsoft, em diferentes instalações dentro do metrô. Você também pode acessar o resumo das conexões da página **Visão Geral** selecionando **Conexões** no centro do painel, conforme mostrado.

    > [!div class="mx-imgBorder"]
    > ![Conexões de recursos de peering](../media/setup-direct-get-connectionssummary.png)

    * **O estado de conexão** corresponde ao estado da configuração de conexão de peering. Os estados exibidos neste campo seguem o diagrama de estado mostrado no passo a passo do [peering direto](../walkthrough-direct-all.md).
    * **O Estado da Sessão IPv4** e o **Estado de Sessão IPv6** correspondem aos estados da sessão IPv4 e IPv6 BGP, respectivamente. 
    * Quando você seleciona uma linha na parte superior da tela, a seção **Conexão** na parte inferior mostra detalhes para cada conexão. Selecione as setas para expandir **configuração,** **endereço IPv4**e **endereço IPv6**.
