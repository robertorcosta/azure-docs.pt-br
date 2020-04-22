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
ms.openlocfilehash: fda22346a44388248e37473bc7891b8a130569c4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681044"
---
1. Na **página Criar um peering,** na guia **Configuração,** preencha as caixas como mostrado aqui.

    * Para o tipo Peering, selecione **'Direto ''''Em** **pares'.**
    * Para **rede Microsoft,** selecione **AS8075**. Não crie peering com ASN 8069. É reservado para aplicativos especiais e usado apenas pela [Microsoft peering](mailto:peering@microsoft.com).
    * Selecione **SKU** como **Basic Free**. Não selecione Premium Free porque ele é reservado para aplicações especiais.
    * Selecione o local do **metrô** onde deseja configurar o peering.

        > [!NOTE]
        > Se você já tiver conexões de peering com a Microsoft no local do **Metrô** selecionado e estiver usando o portal Azure pela primeira vez para configurar o peering nesse local, suas conexões de peering existentes serão listadas na seção **conexões peering** como mostrado. A Microsoft converterá automaticamente essas conexões de peering para um recurso do Azure para que você possa gerenciá-las todas, juntamente com as novas conexões, em um só lugar. Para obter mais informações, consulte [Converter um peering direct legado para um recurso do Azure usando o portal](../howto-legacy-direct-portal.md).
        >

1. Em **Conexões peering,** selecione **Criar uma nova** linha para cada nova conexão que você deseja configurar.

    * Para configurar ou modificar as configurações de conexão, selecione o botão editar para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-direct-conf-tab-edit.png)
    
    * Para excluir uma linha, selecione **...**  >  **Excluir**.

        > [!div class="mx-imgBorder"]
        > ![Botão Excluir](../media/setup-direct-conf-tab-delete.png)

    * Você é obrigado a fornecer todas as configurações para uma conexão, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Página de conexão de peering direta](../media/setup-direct-conf-tab-connection.png)

        1. Selecione a **instalação peering** onde a conexão precisa ser configurada.
        1. **O Provedor de Endereços de Sessão** é usado para determinar quem fornece a sub-rede necessária para configurar a sessão BGP entre sua rede e a Microsoft. Se você puder fornecer a sub-rede, selecione **Peer**. Caso contrário, selecione o [peering da](mailto:peering@microsoft.com) **Microsoft** e da Microsoft entrará em contato com você. A escolha dessa opção levará mais tempo para a Microsoft processar a solicitação de peering. Em alguns casos, a Microsoft pode não ser capaz de fornecer sub-redes, o que resultará em negação de solicitação.
        1. Se você selecionou a opção **Provedor de endereço de sessão** como **Peer,** digite os endereços IPv4 e IPv6 juntamente com as máscaras de prefixo nas caixas **de prefixo Session IPv4** e **Session IPv6,** respectivamente.
        1. Digite o número de prefixos IPv4 e IPv6 que você anunciará nos **endereços IPv4 anunciados máximos** e **caixas de endereços IPv6 anunciadas máximas,** respectivamente.
        1. Ajuste o controle deslizante **de largura de banda total** para refletir a largura de banda da conexão.
        1. Selecione **Salvar** para salvar suas configurações de conexão.

1. Repita a etapa anterior para adicionar mais conexões em qualquer instalação onde a Microsoft esteja conectada à sua rede, dentro do **Metrô** que você selecionou anteriormente.

1. Depois de adicionar todas as conexões necessárias, selecione **'Revisar + criar '''**

    > [!div class="mx-imgBorder"]
    > ![Peering Configuration tab final](../media/setup-direct-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Uma fita na parte superior exibe a mensagem *Executando a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia validação de peering](../media/setup-direct-review-tab-validation.png)

1. Depois que a mensagem passar para *Validação,* verifique suas informações. Envie a solicitação selecionando **Criar**. Para modificar sua solicitação, selecione **Previous** e repita as etapas.

    > [!div class="mx-imgBorder"]
    > ![Submissão peering](../media/setup-direct-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde o término da implantação. Se a implantação falhar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com) Uma implantação bem sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Sucesso peering](../media/setup-direct-success.png)
