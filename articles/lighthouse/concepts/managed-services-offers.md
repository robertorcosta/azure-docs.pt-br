---
title: Ofertas de serviço gerenciado no Azure Marketplace
description: As ofertas de serviço gerenciado permitem que os provedores de serviços vendam ofertas de gerenciamento de recursos para clientes no Azure Marketplace.
ms.date: 05/04/2020
ms.topic: conceptual
ms.openlocfilehash: 119ecc8d15ef93a265cb5419404840496aaa1572
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121581"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Ofertas de serviço gerenciado no Azure Marketplace

Este artigo descreve o tipo de oferta de **serviço gerenciado** no [Azure Marketplace](https://azuremarketplace.microsoft.com). As ofertas de serviço gerenciado permitem que você ofereça serviços de gerenciamento de recursos aos clientes por meio [do Azure Lighthouse](../overview.md). Você pode disponibilizar essas ofertas para todos os clientes potenciais ou apenas para um ou mais clientes específicos. Como você cobra os clientes diretamente por custos relacionados a esses serviços gerenciados, não há valores cobrados pela Microsoft.

## <a name="understand-managed-service-offers"></a>Entender as ofertas de serviço gerenciado

O serviço gerenciado oferece simplificar o processo de integração de clientes com o Azure Lighthouse. Quando um cliente adquire uma oferta no Azure Marketplace, ele pode especificar quais assinaturas e/ou grupos de recursos devem ser integrados.

Depois disso, os usuários em sua organização poderão trabalhar nesses recursos de dentro do locatário da sua organização por meio do [Gerenciamento de recursos delegado do Azure](azure-delegated-resource-management.md), de acordo com o acesso que você definiu ao criar a oferta. Isso é feito por meio de um manifesto que especifica os usuários do Azure Active Directory (Azure AD), os grupos e as entidades de serviço que terão acesso aos recursos do cliente, juntamente com as funções que definem seu nível de acesso. Ao atribuir permissões a um grupo do Azure AD em vez de uma série de contas individuais de usuário ou de aplicativo, é possível adicionar ou remover usuários individuais quando seus requisitos de acesso mudam.

## <a name="public-and-private-offers"></a>Ofertas públicas e privadas

Cada oferta de serviços gerenciados inclui um ou mais planos. Os planos podem ser privados ou públicos.

Se desejar limitar sua oferta a clientes específicos, será possível publicar um plano privado. Quando fizer isso, o plano só poderá ser adquirido para IDs da assinatura específicas que você fornecer. Para saber mais, confira [Ofertas privadas](../../marketplace/private-offers.md).

Planos públicos permitem que você promova serviços para novos clientes. Normalmente, eles são mais adequados quando você só requer acesso limitado ao locatário do cliente. Depois de estabelecer uma relação com um cliente, se decidir conceder acesso adicional à sua organização, você poderá fazer isso publicando um novo plano privado somente para esse cliente ou [integrando-os para obter acesso adicional usando os modelos do Azure Resource Manager](../how-to/onboard-customer.md).

Se apropriado, você pode incluir planos públicos e privados na mesma oferta.

> [!IMPORTANT]
> Depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, não é possível restringir a disponibilidade para determinados clientes ou até mesmo para um determinado número de clientes (embora você possa deixar de vender completamente o plano se optar por fazê-lo). Você pode [remover o acesso a uma delegação](../how-to/remove-delegation.md) depois que um cliente aceitar uma oferta somente se você tiver incluído uma **Autorização** com a **Definição de função** definida como [Função de exclusão de registro de serviços gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) ao publicar a oferta. Você também pode entrar em contato com o cliente e pedir a eles para [remover seu acesso](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicar ofertas de serviço gerenciado

Para saber como publicar uma oferta de serviços gerenciados, consulte [publicar uma oferta de serviços gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [gerenciamento de recursos delegados do Azure](azure-delegated-resource-management.md) e [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- [Publicar ofertas de serviços gerenciados](../how-to/publish-managed-services-offers.md) no Azure Marketplace.
