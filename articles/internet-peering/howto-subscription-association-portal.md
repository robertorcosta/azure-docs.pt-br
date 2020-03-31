---
title: Associar o ASN do par à assinatura do Azure usando o portal
titleSuffix: Azure
description: Associar o ASN do par à assinatura do Azure usando o portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: cee548aff49cd5e4a57eed994b8ade2d157c6313
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75912222"
---
# <a name="associate-peer-asn-to-azure-subscription-using-the-portal"></a>Associar o ASN do par à assinatura do Azure usando o portal

Antes de enviar uma solicitação de peering, você deve primeiro associar sua ASN com a assinatura do Azure usando as etapas abaixo.

Se preferir, você pode completar este guia usando o [PowerShell](howto-subscription-association-powershell.md).

## <a name="create-peerasn-to-associate-your-asn-with-azure-subscription"></a>Crie peerasn para associar seu ASN com a assinatura do Azure

### <a name="sign-in-to-the-portal"></a>Faça login no portal
[!INCLUDE [Account](./includes/account-portal.md)]

### <a name="register-for-peering-resource-provider"></a>Registre-se para provedor de recursos de peering
Registre-se para o provedor de recursos de peering em sua assinatura seguindo as etapas abaixo. Se você não executar isso, então os recursos do Azure necessários para configurar o peering não são acessíveis.

1. Clique em **Assinaturas** no canto superior esquerdo do portal. Se você não vê-lo, clique em **Mais serviços** e pesquise por ele.

    > [!div class="mx-imgBorder"]
    > ![Inscrições abertas](./media/rp-subscriptions-open.png)

1. Clique na assinatura que deseja usar para peering.

    > [!div class="mx-imgBorder"]
    > ![Assinatura de lançamento](./media/rp-subscriptions-launch.png)

1. Uma vez que a assinatura seja aberta, à esquerda, clique em **Provedores de recursos**. Em seguida, no painel direito, procure *observar* na janela de pesquisa ou use a barra de rolagem para encontrar **microsoft.Peering** e olhar para o **Status**. Se o status ***estiver registrado,*** pule as etapas abaixo e prossiga para a seção **Criar PeerAsn**. Se o status ***não for registrado, selecione*** **Microsoft.Peering** e clique em **Registrar**.

    > [!div class="mx-imgBorder"]
    > ![Início das inscrições](./media/rp-register-start.png)

1. Observe que o status muda para ***Registro***.

    > [!div class="mx-imgBorder"]
    > ![Inscrições em andamento](./media/rp-register-progress.png)

1. Aguarde um min ou mais para que ele complete o registro. Em seguida, clique em **Atualizar** e verifique se o status está ***registrado***.

    > [!div class="mx-imgBorder"]
    > ![Inscrições concluídas](./media/rp-register-completed.png)

### <a name="create-peerasn"></a>Criar PeerAsn
Você pode criar um novo recurso PeerAsn para associar um Número de Sistema Autônomo (ASN) com a assinatura do Azure. Você pode associar vários ASNs a uma assinatura criando um **PeerAsn** para cada ASN que você precisa associar.

1. Clique **em Criar um recurso** > **Ver tudo**.

    > [!div class="mx-imgBorder"]
    > ![Pesquisar PeerAsn](./media/peerasn-seeall.png)

1. Procure *peerAsn* na caixa de pesquisa e aperte *Enter* em seu teclado. A partir dos resultados, clique no recurso **PeerAsn.**

    > [!div class="mx-imgBorder"]
    > ![Iniciar PeerAsn](./media/peerasn-launch.png)

1. Assim que **o PeerAsn** for lançado, clique em **Criar**.

    > [!div class="mx-imgBorder"]
    > ![Criar PeerAsn](./media/peerasn-create.png)

1. Na página **Associar um Peer ASN,** na guia **Basics,** preencha os campos conforme mostrado abaixo.

    > [!div class="mx-imgBorder"]
    > ![Guia de noções básicas do PeerAsn](./media/peerasn-basics-tab.png)

    * **O nome** corresponde ao nome do recurso e pode ser qualquer coisa que você escolher.  
    * Escolha a **Assinatura** com a qual você precisa associar o ASN.
    * **O nome** do ponto corresponde ao nome da sua empresa e precisa estar o mais próximo possível do seu perfil PeeringDB. Note que o valor suporta apenas caracteres a-z, A-Z e espaço
    * Insira seu ASN no campo **Peer ASN.**
    * Clique em **Criar novo** e digite endereço **de e-mail** e **número de telefone** para o seu Centro de Operações de Rede (NOC)
1. Em seguida, clique em **Revisar + criar** e observar que o portal executa a validação básica das informações inseridas. Isso é exibido em uma fita na parte superior, como *Running validação final...*.

    > [!div class="mx-imgBorder"]
    > ![Guia de avaliação do PeerAsn](./media/peerasn-review-tab-validation.png)

1. Uma vez que a mensagem na fita se transforma em *Validação Passada,* verifique suas informações e envie a solicitação clicando **em Criar**. Se a validação não passar, clique em **Anterior** e repita as etapas acima para modificar sua solicitação e garantir que os valores digitados não tenham erros.

    > [!div class="mx-imgBorder"]
    > ![Guia de avaliação do PeerAsn](./media/peerasn-review-tab.png)

1. Depois de enviar a solicitação, aguarde que ela seja concluída. Se a implantação falhar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com) Uma implantação bem sucedida aparecerá como abaixo.

    > [!div class="mx-imgBorder"]
    > ![Sucesso peerasn](./media/peerasn-success.png)

### <a name="view-status-of-a-peerasn"></a>Exibir status de um PeerAsn
Uma vez que o recurso PeerAsn seja implantado com sucesso, você precisará esperar que a Microsoft aprove a solicitação de associação. Pode levar até 12 horas para aprovação. Uma vez aprovado, você receberá uma notificação para o endereço de e-mail inserido na seção acima.

> [!IMPORTANT]
> Aguarde que o Estado de Validação gire "Aprovado" antes de enviar uma solicitação de peering. Pode levar até 12 horas para essa aprovação.

## <a name="modify-peerasn"></a>Modificar peerasn
A modificação do PeerAsn não é suportada no momento. Se você precisar modificar, entre em contato com [a Microsoft .](mailto:peering@microsoft.com)

## <a name="delete-peerasn"></a>Excluir PeerAsn
A exclusão de um PeerAsn não é suportada no momento. Se você precisar excluir o PeerAsn, entre em contato com [a Microsoft .](mailto:peering@microsoft.com)

## <a name="next-steps"></a>Próximas etapas

* [Criar ou modificar um Emparelhamento direto usando o portal](howto-direct-portal.md)
* [Converter um Emparelhamento direto herdado para o recurso do Azure usando o portal](howto-legacy-direct-portal.md)
* [Criar ou modificar o peering do Exchange usando o portal](howto-exchange-portal.md)
* [Converter um emparelhamento do Exchange herdado para o recurso do Azure usando o portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Recursos adicionais

Para obter mais informações, visite [perguntas frequentes sobre peering na Internet](faqs.md)