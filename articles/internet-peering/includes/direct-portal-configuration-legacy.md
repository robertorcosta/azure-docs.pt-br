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
ms.openlocfilehash: 632490498b8dd13414657edb9518cd543ac07af6
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678856"
---
1. Na **página Criar um peering,** na guia **Configuração,** preencha as caixas como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Criar uma guia de configuração de página de peering](../media/setup-direct-conf-tab.png)

    * Para o tipo Peering, selecione **'Direto ''''Em** **pares'.**
    * Para **rede Microsoft,** selecione **AS8075**. Não selecione ASN 8069. É reservado para aplicativos especiais e usado apenas pela [Microsoft peering](mailto:peering@microsoft.com).
    * Selecione **SKU** como **Basic Free**. Não selecione Premium Free porque ele é reservado para aplicações especiais.
    * Selecione o local do **Metrô** onde deseja converter o peering para um recurso do Azure. Se você tiver conexões de peering com a Microsoft no local de **Metrô** selecionado que não forem convertidos em um recurso do Azure, essas conexões serão listadas na seção **Conexões peering** como mostrado. Agora você pode converter essas conexões de peering para um recurso do Azure.

        > [!div class="mx-imgBorder"]
        > ![Lista de conexões peering](../media/setup-directlegacy-conf-tab.png)

1. Se você precisar atualizar a largura de banda, selecione o botão editar para uma linha para modificar as configurações de conexão.

    > [!div class="mx-imgBorder"]
    > ![Botão Editar](../media/setup-directlegacy-conf-tab-edit.png)

    > [!NOTE]
    > Se você quiser adicionar conexões adicionais de peering com a Microsoft no local **do Metro** selecionado, selecione **Criar novo**. Para obter mais informações, consulte [Criar ou modificar um peering direto usando o portal](../howto-direct-portal.md).
    >

1. Selecione **Examinar + criar**. Observe que o portal executa a validação básica das informações inseridas. Uma fita na parte superior exibe a mensagem *Executando a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia validação de peering](../media/setup-direct-review-tab-validation.png)

1. Depois que a mensagem passar para *Validação,* verifique suas informações. Envie a solicitação selecionando **Criar**. Para modificar sua solicitação, selecione **Previous** e repita as etapas.

    > [!div class="mx-imgBorder"]
    > ![Submissão peering](../media/setup-direct-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde o término da implantação. Se a implantação falhar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com) Uma implantação bem sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Sucesso peering](../media/setup-direct-success.png)
