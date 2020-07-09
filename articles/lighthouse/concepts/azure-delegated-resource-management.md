---
title: Gerenciamento de recursos delegados do Azure
description: O gerenciamento de recursos delegado do Azure é uma parte fundamental do Azure Lighthouse, permitindo que os provedores de serviços gerenciem recursos delegados em escala com agilidade e precisão.
ms.date: 05/28/2020
ms.topic: conceptual
ms.openlocfilehash: bbe3c28cdcd252755b8350eaa5d2e72044981174
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86120884"
---
# <a name="azure-delegated-resource-management"></a>Gerenciamento de recursos delegados do Azure

O gerenciamento de recursos delegado do Azure é um dos principais componentes do [Azure Lighthouse](../overview.md). Com o gerenciamento de recursos delegados do Azure, os provedores de serviços podem simplificar as experiências de envolvimento e de integração do cliente e gerenciar recursos delegados em escala com agilidade e precisão.

## <a name="what-is-azure-delegated-resource-management"></a>O que é gerenciamento de recursos delegados do Azure?

O gerenciamento de recursos delegados do Azure permite a projeção lógica de recursos de um locatário para outro. Isso permite que usuários autorizados em um locatário do Azure AD (Active Directory) realizem operações de gerenciamento em diferentes locatários do Azure AD que pertencem a seus clientes. Os provedores de serviços podem entrar em seu próprio locatário do Azure AD e ter autorização para trabalhar em grupos de recursos e assinaturas de clientes delegados. Isso permite que eles realizem operações de gerenciamento em nome de seus clientes, sem precisar entrar em cada locatário individual do cliente.

> [!NOTE]
> O gerenciamento de recursos delegado do Azure também pode ser usado [em uma empresa que tem vários locatários do Azure ad próprios](enterprise.md) para simplificar o gerenciamento entre locatários.

Com o gerenciamento de recursos delegados do Azure, os usuários autorizados podem trabalhar diretamente no contexto de uma assinatura de cliente sem ter uma conta no locatário do cliente ou ser um coproprietário do locatário do cliente. Eles também podem [exibir e gerenciar todas as assinaturas de clientes delegados na nova página **Meus clientes**](../how-to/view-manage-customers.md) no portal do Azure.

A [experiência de gerenciamento entre locatários](cross-tenant-management-experience.md) ajuda você a trabalhar de maneira mais eficiente com os serviços de gerenciamento do Azure, como o Azure Policy, a Central de Segurança do Azure e muito mais. Todas as atividades do provedor de serviços são controladas no log de atividades, que é armazenado no locatário do cliente (e pode ser exibido por usuários no locatário de gerenciamento). Isso significa que o cliente e o provedor de serviços podem identificar facilmente o usuário associado a alterações.

Quando você integra um cliente ao gerenciamento de recursos delegado do Azure, ele terá acesso à página **provedores de serviços** na portal do Azure, onde poderá [confirmar e gerenciar suas ofertas, provedores de serviços e recursos delegados](../how-to/view-manage-service-providers.md). Se o cliente desejar revogar o acesso para um provedor de serviços, ele poderá fazer isso aqui a qualquer momento.

Você pode [publicar o novo tipo de oferta de serviço gerenciado no Azure Marketplace](../how-to/publish-managed-services-offers.md) para integrar facilmente os clientes ao gerenciamento de recursos delegado do Azure. Ou será possível [concluir o processo de integração implantando modelos do Azure Resource Manager](../how-to/onboard-customer.md).

## <a name="how-azure-delegated-resource-management-works"></a>Como o gerenciamento de recursos delegados do Azure funciona

Em um alto nível, veja como o gerenciamento de recursos delegados do Azure funciona:

1. Como um provedor de serviços, identifique o acesso (funções) em que seus grupos, entidades de serviço ou usuários precisarão gerenciar os recursos do Azure do cliente. A definição de acesso contém a ID de locatário do provedor de serviços junto com o acesso necessário da oferta, definido usando as identidades **principalId** do seu locatário mapeadas para valores [**roleDefinition** internos](../../role-based-access-control/built-in-roles.md) (Colaborador, Colaborador de VM, Leitor, etc.).
2. Você especifica esse acesso e integra o cliente ao gerenciamento de recursos delegados do Azure em uma de duas maneiras:
   - [Publicar uma oferta de serviço gerenciado do Azure Marketplace](../how-to/publish-managed-services-offers.md) (privada ou pública) que o cliente aceitará
   - [Implantar um modelo de Azure Resource Manager no locatário do cliente](../how-to/onboard-customer.md) para uma ou mais assinaturas ou grupos de recursos específicos
3. Depois que o cliente tiver sido integrado, os usuários autorizados poderão entrar no seu locatário do provedor de serviços e executar tarefas de gerenciamento no escopo determinado do cliente, com base no acesso que você definiu.

> [!NOTE]
> Você pode gerenciar recursos delegados que estão localizados em [regiões](../../availability-zones/az-overview.md#regions)diferentes. No entanto, a delegação de assinaturas em uma [nuvem nacional](../../active-directory/develop/authentication-national-cloud.md) e na nuvem pública do Azure ou em duas nuvens nacionais separadas não tem suporte.

## <a name="support-for-azure-delegated-resource-management"></a>Suporte para gerenciamento de recursos delegados do Azure

Se precisar de ajuda com relação ao gerenciamento de recursos delegados do Azure, abra uma solicitação de suporte no portal do Azure. Para **Tipo de problema**, escolha **Técnico**. Selecione uma assinatura e, em seguida, selecione **Lighthouse** (em **monitoramento & gerenciamento**).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba mais sobre [ofertas de serviços gerenciados no Azure Marketplace](managed-services-offers.md).