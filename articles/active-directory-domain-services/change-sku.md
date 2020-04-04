---
title: Alterar o SKU para um Azure AD Domain Services | Microsoft Docs
description: Saiba como fazer o nível SKU para um domínio gerenciado do Azure AD Domain Services se os requisitos dos negócios mudarem
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655595"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Alterar o SKU para um domínio gerenciado azure AD Domain Services existente

No Azure Active Directory Domain Services (Azure AD DS), o desempenho e os recursos disponíveis são baseados no tipo SKU. Essas diferenças de recursos incluem a freqüência de backup ou o número máximo de fundos florestais de saída de uma via (atualmente em visualização). Você seleciona um SKU quando cria o domínio gerenciado e pode alternar SKUs para cima ou para baixo à medida que seus negócios precisam ser trocados após a implantação do domínio gerenciado. Mudanças nos requisitos de negócios podem incluir a necessidade de backups mais freqüentes ou de criar fundos florestais adicionais. Para obter mais informações sobre os limites e preços das diferentes SKUs, consulte os [conceitos do Azure AD DS SKU][concepts-sku] e as páginas [de preços do Azure AD DS.][pricing]

Este artigo mostra como alterar o SKU para um domínio gerenciado pelo Azure AD DS existente usando o portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Para concluir este artigo, você precisa dos seguintes recursos e privilégios:

* Uma assinatura ativa do Azure.
    * Se você não tiver uma assinatura do Azure, [crie uma conta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Um locatário do Azure Active Directory associado com a assinatura, sincronizado com um diretório local ou somente em nuvem.
    * Se necessário, [crie um locatário do Azure Active Directory][create-azure-ad-tenant] ou [associe uma assinatura do Azure à sua conta][associate-azure-ad-tenant].
* Um domínio gerenciado do Azure Active Directory Domain Services habilitado e configurado no locatário do Azure AD.
    * Se necessário, complete o tutorial para [criar e configurar uma instância de Serviços de Domínio do Diretório Ativo do Azure][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Limitações de alteração do SKU

Você pode alterar skus para cima ou para baixo depois que o domínio gerenciado do Azure AD DS foi implantado. No entanto, se você usar uma floresta de recursos (atualmente em pré-visualização) e tiver criado fundos florestais de saída única do Azure AD DS para um ambiente AD DS no local, existem algumas limitações para a operação de alteração do SKU. As SKUs *Premium* e *Enterprise* definem um limite no número de trusts que você pode criar. Você não pode mudar para um SKU com um limite máximo menor do que você configurou atualmente.

Por exemplo:

* Se você criou dois fundos florestais no *SKU Premium,* você não pode mudar para o *SKU padrão.* O *Standard* SKU não suporta fundos florestais.
* Ou, se você criou sete trusts no *Premium* SKU, você não pode mudar para o *Enterprise* SKU. O *Enterprise* SKU suporta um máximo de cinco trusts.

Para obter mais informações sobre esses limites, consulte [os recursos e limites do Azure AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Selecione um novo SKU

Para alterar o SKU para um domínio gerenciado pelo Azure AD DS usando o portal Azure, complete as seguintes etapas:

1. No topo do portal Azure, procure e selecione **Azure AD Domain Services**. Escolha seu domínio gerenciado na lista, como *aaddscontoso.com*.
1. No menu no lado esquerdo da página Azure AD DS, selecione **Configurações > SKU**.

    ![Selecione a opção de menu SKU para o domínio gerenciado do Azure AD DS no portal Azure](media/change-sku/overview-change-sku.png)

1. No menu suspenso, selecione o SKU desejado para o domínio gerenciado pelo Azure AD DS. Se você tem uma floresta de recursos, você não pode selecionar *o Standard* SKU, pois os fundos florestais só estão disponíveis no *Enterprise* SKU ou superior.

    Escolha o SKU que deseja no menu suspenso e selecione **Salvar**.

    ![Escolha o SKU necessário no menu suspenso no portal Azure](media/change-sku/change-sku-selection.png)

Pode levar um minuto ou dois para mudar o tipo SKU.

## <a name="next-steps"></a>Próximas etapas

Se você tiver uma floresta de recursos e quiser criar fundos adicionais após a alteração do SKU, consulte [Criar um fundo florestal de saída para um domínio local no Azure AD DS (preview)][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
