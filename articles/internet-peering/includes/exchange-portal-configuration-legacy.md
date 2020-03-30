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
ms.openlocfilehash: 7d7b9f847cdcc4ab4b1ff065425eebe07fb4d888
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775206"
---
1. Na **página Criar um peering,** na guia **Configuração,** preencha os campos conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Configuração peering - Troca](../media/setup-exchange-conf-tab.png)

    * Para **o tipo Peering, selecione** *Exchange*.
    * Selecione **SKU** como *Basic Free*.
    * Escolha o local do **Metrô** para onde você deseja converter o peering para o recurso do Azure. Se você tiver conexões de peering com a Microsoft no local de **Metrô** selecionado que não são convertidos em recurso do Azure, essas conexões serão listadas na seção **conexões peering** como mostrado abaixo. Agora você pode converter essas conexões de peering para o recurso Do Zure.

        > [!div class="mx-imgBorder"]
        > ![Configuração peering - Exchange - Conexões Legados](../media/setup-exchange-legacy-conf-tab.png)

        > [!NOTE]
        > Não é possível modificar as configurações para conexões de peering legados. Se você quiser adicionar conexões adicionais de peering com a Microsoft no local de **Metro** selecionado, você pode fazê-lo clicando em **Criar novo** botão. Consulte [Criar ou modificar um peering do Exchange usando o portal](../howto-exchange-portal.md) para obter mais informações.
        >

1. Clique em **Revisar + criar**. Observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma fita na parte superior, como *Running validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia de validação de peering](../media/setup-direct-review-tab-validation.png)

1. Depois de se transformar em *Validação Passada,* verifique suas informações e envie a solicitação clicando **em Criar**. Se você precisar modificar sua solicitação, clique em **Anterior** e repita as etapas acima.

    > [!div class="mx-imgBorder"]
    > ![Envio de peering](../media/setup-exchange-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde que ela seja concluída. Se a implantação falhar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com) Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![Sucesso peering](../media/setup-direct-success.png)
