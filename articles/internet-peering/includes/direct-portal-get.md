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
ms.openlocfilehash: 2e8938f270ed175c687d975b0b248275ad92f8e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75775076"
---
1. Vá para **grupos de recursos** e clique no grupo de recursos selecionado ao criar recurso **peering.** Você pode usar o campo *Filtro* se tiver muitos grupos de recursos.

    > [!div class="mx-imgBorder"]
    > ![Grupo de recursos de peering](../media/setup-direct-get-resourcegroup.png)

1. Clique no recurso **peering** que você criou.

    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-get-open.png)

1. A **página Visão Geral** mostra informações de alto nível. Observe as informações destacadas abaixo.

    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-get-overview.png)

1. À esquerda, clique em **informações ASN** para visualizar as informações enviadas enquanto cria o PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-get-asninfo.png)

1. À esquerda, clique em **Conexões**. Observe no topo um resumo das conexões de peering entre sua ASN e microsoft, em diferentes instalações dentro do metrô. Você também pode chegar ao resumo de conexões da página **Visão Geral,** clicando em **Conexões** no painel central, conforme destacado acima.

    > [!div class="mx-imgBorder"]
    > ![Exibição de recursos de peering](../media/setup-direct-get-connectionssummary.png)

    * **O Estado de Conexão** corresponde ao estado da conexão de peering configurada. Os estados exibidos neste campo seguem o diagrama de estado mostrado no [passo a passo de peering direto](../walkthrough-direct-all.md)
    * **O Estado da Sessão IPv4** e o **Estado de Sessão IPv6** correspondem aos estados da sessão IPv4 e IPv6 BGP, respectivamente.  
    * Quando você seleciona uma linha na parte superior, a seção ***Conexão*** na parte inferior mostra detalhes para cada conexão. Você pode clicar em marcas de seta para expandir a ***configuração***das subseções, ***endereço IPv4*** e ***endereço IPv6***
