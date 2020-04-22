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
ms.openlocfilehash: 268703081a2a40e8bcc665889eaeaf8edd673bfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680933"
---
1. Na **página Criar um peering,** na guia **Configuração,** preencha as caixas como mostrado.

    > [!div class="mx-imgBorder"]
    > ![Criar um peering page Exchange peering type](../media/setup-exchange-conf-tab.png)

    * Para **o tipo Peering, selecione** **Exchange**.
    * Selecione **SKU** como **Basic Free**.
    * Selecione o local do **metrô** onde deseja configurar o peering.

        > [!NOTE]
        > Se você já tiver conexões de peering com a Microsoft no local de **Metrô** selecionado e estiver usando o portal pela primeira vez para configurar o peering nesse local, suas conexões de peering existentes serão listadas na seção **Conexões peering,** conforme mostrado. A Microsoft converterá automaticamente essas conexões de peering para um recurso do Azure para que você possa gerenciá-las todas, juntamente com as novas conexões em um só lugar. Para obter mais informações, consulte [Converter um correspondente legado de peering para um recurso do Azure usando o portal](../howto-legacy-exchange-portal.md).
        >

1. Em **Conexões peering,** selecione **Criar uma nova** linha para cada nova conexão que você deseja configurar.

    * Para configurar ou modificar as configurações de conexão, selecione o botão editar para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Botão Editar](../media/setup-exchange-conf-tab-edit.png)

    * Para excluir uma linha, selecione **...**  >  **Excluir**.

        > [!div class="mx-imgBorder"]
        > ![Botão Excluir](../media/setup-exchange-conf-tab-delete.png)

    * Você é obrigado a fornecer todas as configurações para uma conexão, como mostrado aqui.

         > [!div class="mx-imgBorder"]
         > ![Página de conexão de peering do exchange](../media/setup-exchange-conf-tab-connection.png)

        1. Selecione a **instalação peering** onde a conexão precisa ser configurada.
        1. Nos **endereços IPv4** e **IPv6,** digite os endereços IPv4 e IPv6, respectivamente, que seriam configurados em roteadores da Microsoft usando o comando neighbor.
        1. Digite o número de prefixos IPv4 e IPv6 que você anunciará nos **endereços IPv4 anunciados máximos** e **caixas de endereços IPv6 anunciadas máximas,** respectivamente.
        1. Selecione **OK** para salvar suas configurações de conexão.

1. Repita a etapa para adicionar mais conexões em qualquer instalação onde a Microsoft esteja situada com sua rede, dentro do **Metro** selecionado anteriormente.

1. Depois de adicionar todas as conexões necessárias, selecione **'Revisar + criar '''**

    > [!div class="mx-imgBorder"]
    > ![Guia de configuração de peering](../media/setup-exchange-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Uma fita na parte superior exibe a mensagem *Executando a validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia validação de peering](../media/setup-direct-review-tab-validation.png)

1. Depois que a mensagem passar para *Validação,* verifique suas informações. Envie a solicitação selecionando **Criar**. Para modificar sua solicitação, selecione **Previous** e repita as etapas.

    > [!div class="mx-imgBorder"]
    > ![Submissão peering](../media/setup-exchange-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde o término da implantação. Se a implantação falhar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com) Uma implantação bem sucedida aparece como mostrado aqui.

    > [!div class="mx-imgBorder"]
    > ![Sucesso peering](../media/setup-direct-success.png)
