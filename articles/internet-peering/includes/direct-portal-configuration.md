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
ms.openlocfilehash: 2a42ba8809e6895c9eea9f8762513b7fcaa9eb3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775271"
---
1. Na **página Criar um peering,** na guia **Configuração,** preencha os campos conforme mostrado abaixo.

    * Para o tipo Peering, selecione *'Direto ''''Em* **pares'.**
    * Para **a rede Microsoft,** escolha *AS8075*. Não crie peering com ASN 8069. É reservado para aplicativos especiais e usado apenas pela [Microsoft peering](mailto:peering@microsoft.com).
    * Selecione **SKU** como *Basic Free*. Não selecione *Premium Free,* pois ele é reservado para aplicações especiais.
    * Escolha o local do **metrô** para onde você deseja configurar o peering.

        > [!NOTE]
        > Se você já tiver conexões de peering com a Microsoft no local de **Metrô** selecionado e estiver usando o portal pela primeira vez para configurar o peering nesse local, então suas conexões de peering existentes serão listadas na seção **Conexões peering,** conforme mostrado abaixo. A Microsoft converterá automaticamente essas conexões de peering para o recurso Do Zure para que você possa gerenciá-las todas, juntamente com as novas conexões, em um só lugar. Consulte [Converter um recurso direct peering legado para o recurso do Azure usando o portal](../howto-legacy-direct-portal.md) para obter mais informações.
        >

1. Em **Conexões peering,** clique **em Criar uma nova** linha para cada nova conexão que você deseja configurar.

    * Para configurar/modificar as configurações de conexão, clique no botão editar para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição direta](../media/setup-direct-conf-tab-edit.png)
    
    * Para excluir uma linha, clique em **...** botão > **Delete**.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição direta](../media/setup-direct-conf-tab-delete.png)

    * Você é obrigado a fornecer todas as configurações para uma conexão, conforme mostrado abaixo.

         > [!div class="mx-imgBorder"]
         > ![Configuração de peering - Conexão direta](../media/setup-direct-conf-tab-connection.png)

        1. Selecione a **instalação peering** onde a conexão precisa ser configurada.
        1. **O Provedor de Endereços de Sessão** é usado para determinar quem fornece a sub-rede necessária para configurar a sessão BGP entre sua rede e a Microsoft. Se você for capaz de fornecer sub-rede, então escolha *Peer*. Em caso de **escolha, a Microsoft** e [a Microsoft, entre em](mailto:peering@microsoft.com) contato com você. Note que, ao escolher assim, levaria mais tempo para a Microsoft processar a solicitação de peering. Em alguns casos, a Microsoft pode não ser capaz de fornecer sub-redes que resultarão em negação de solicitação.
        1. Se você escolheu **o Provedor de Endereço de Sessão** como *Peer,* digite o endereço IPv4 e IPv6 juntamente com a máscara de prefixo nos campos **Session IPv4 Prefix** e Session **IPv6 Prefix,** respectivamente.
        1. Digite o número de prefixos IPv4 e IPv6 que você anunciará nos campos **Endereços IPv4 anunciados máximos** e **endereços IPv6 anunciados máximos,** respectivamente.
        1. Ajuste o controle deslizante **de largura de banda total** para refletir a largura de banda da conexão.
        1. Clique em **OK** para salvar suas configurações de conexão.

1. Repita a etapa acima para adicionar mais conexões em qualquer instalação onde a Microsoft esteja situada com sua rede, dentro do **Metro** selecionado anteriormente.

1. Depois de adicionar todas as conexões necessárias, clique em **'Revisar + criar**.

    > [!div class="mx-imgBorder"]
    > ![Guia de configuração de peering Final](../media/setup-direct-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma fita na parte superior, como *Running validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia de validação de peering](../media/setup-direct-review-tab-validation.png)

1. Depois de se transformar em *Validação Passada,* verifique suas informações e envie a solicitação clicando **em Criar**. Se você precisar modificar sua solicitação, clique em **Anterior** e repita as etapas acima.

    > [!div class="mx-imgBorder"]
    > ![Envio de peering](../media/setup-direct-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde que ela seja concluída. Se a implantação falhar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com) Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![Sucesso peering](../media/setup-direct-success.png)
