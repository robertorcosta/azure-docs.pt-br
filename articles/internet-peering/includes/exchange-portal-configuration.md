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
ms.openlocfilehash: cd51eca0ea4563e1b56f74677df0829669d9e177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774491"
---
1. Na **página Criar um peering,** na guia **Configuração,** preencha os campos conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Configuração peering - Troca](../media/setup-exchange-conf-tab.png)

    * Para **o tipo Peering, selecione** *Exchange*.
    * Selecione **SKU** como *Basic Free*.
    * Escolha o local do **metrô** para onde você deseja configurar o peering.

        > [!NOTE]
        > Se você já tiver conexões de peering com a Microsoft no local de **Metrô** selecionado e estiver usando o portal pela primeira vez para configurar o peering nesse local, então suas conexões de peering existentes serão listadas na seção **Conexões peering,** conforme mostrado abaixo. A Microsoft converterá automaticamente essas conexões de peering para o recurso Do Zure para que você possa gerenciá-las todas, juntamente com as novas conexões, em um só lugar. Consulte [Converter um recurso do Exchange legado para o recurso do Azure usando o portal](../howto-legacy-exchange-portal.md) para obter mais informações.
        >

1. Em **Conexões peering,** clique **em Criar uma nova** linha para cada nova conexão que você deseja configurar.

    * Para configurar/modificar as configurações de conexão, clique no botão editar para uma linha.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição de troca](../media/setup-exchange-conf-tab-edit.png)

    * Para excluir uma linha, clique em **...** botão > **Delete**.

        > [!div class="mx-imgBorder"]
        > ![Configuração de peering - Edição de troca](../media/setup-exchange-conf-tab-delete.png)

    * Você é obrigado a fornecer todas as configurações para uma conexão, conforme mostrado abaixo.

         > [!div class="mx-imgBorder"]
         > ![Configuração de peering - Conexão de troca](../media/setup-exchange-conf-tab-connection.png)

        1. Selecione a **instalação peering** onde a conexão precisa ser configurada.
        1. Nos campos **endereço IPv4** e **endereço IPv6,** digite o endereço IPv4 e IPv6, respectivamente, que seria configurado em roteadores da Microsoft usando o comando neighbor.
        1. Digite o número de prefixos IPv4 e IPv6 que você anunciará nos campos **Endereços IPv4 anunciados máximos** e **endereços IPv6 anunciados máximos,** respectivamente.
        1. Clique em **OK** para salvar suas configurações de conexão.

1. Repita a etapa acima para adicionar mais conexões em qualquer instalação onde a Microsoft esteja situada com sua rede, dentro do **Metro** selecionado anteriormente.

1. Depois de adicionar todas as conexões necessárias, clique em **'Revisar + criar**.

    > [!div class="mx-imgBorder"]
    > ![Guia de configuração de peering Final](../media/setup-exchange-conf-tab-final.png)

1. Observe que o portal executa a validação básica das informações inseridas. Isso é exibido em uma fita na parte superior, como *Running validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia de validação de peering](../media/setup-direct-review-tab-validation.png)

1. Depois de se transformar em *Validação Passada,* verifique suas informações e envie a solicitação clicando **em Criar**. Se você precisar modificar sua solicitação, clique em **Anterior** e repita as etapas acima.

    > [!div class="mx-imgBorder"]
    > ![Envio de peering](../media/setup-exchange-review-tab-submit.png)

1. Depois de enviar a solicitação, aguarde que ela seja concluída. Se a implantação falhar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com) Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![Sucesso peering](../media/setup-direct-success.png)
