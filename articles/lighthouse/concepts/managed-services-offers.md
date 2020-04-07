---
title: Ofertas de serviços gerenciados no Azure Marketplace
description: As ofertas de serviços gerenciados permitem que os provedores de serviços vendam ofertas de gerenciamento de recursos aos clientes no Azure Marketplace.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: a0670bd74971132dcf243736bdf882a00154a942
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672434"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Ofertas de serviços gerenciados no Azure Marketplace

Este artigo descreve o tipo de oferta **de serviço gerenciado** no [Azure Marketplace](https://azuremarketplace.microsoft.com). As ofertas gerenciadas de serviços permitem que você ofereça serviços de gerenciamento de recursos aos clientes usando [o gerenciamento de recursos delegado do Azure.](azure-delegated-resource-management.md) Você pode disponibilizar essas ofertas para todos os clientes em potencial, ou apenas para um ou mais clientes específicos. Como você cobra os clientes diretamente por custos relacionados a esses serviços gerenciados, não há valores cobrados pela Microsoft.

## <a name="understand-managed-service-offers"></a>Entenda as ofertas de serviços gerenciados

Os serviços gerenciados simplificam o processo de onboarding de clientes para o gerenciamento de recursos delegados do Azure. Quando um cliente adquire uma oferta no Azure Marketplace, ele pode especificar quais assinaturas e/ou grupos de recursos devem ser integrados.

Depois disso, os usuários da sua organização poderão trabalhar esses recursos de dentro do inquilino da sua organização, de acordo com o acesso que você definiu ao criar a oferta no [Portal de Parceiros na Nuvem.](https://cloudpartner.azure.com/) Isso ocorre por meio de um manifesto que especifica os usuários, grupos e entidades de serviço do Azure AD que terão acesso aos recursos do cliente usando o gerenciamento de recursos delegados do Azure, junto com funções que definem o nível de acesso. Ao atribuir permissões a um grupo do Azure AD em vez de uma série de contas individuais de usuário ou de aplicativo, é possível adicionar ou remover usuários individuais quando seus requisitos de acesso mudam.

## <a name="public-and-private-offers"></a>Ofertas públicas e privadas

Cada oferta de serviços gerenciados inclui um ou mais planos. Os planos podem ser privados ou públicos.

Se desejar limitar sua oferta a clientes específicos, será possível publicar um plano privado. Quando fizer isso, o plano só poderá ser adquirido para IDs da assinatura específicas que você fornecer. Para saber mais, confira [Ofertas privadas](../../marketplace/private-offers.md).

Planos públicos permitem que você promova serviços para novos clientes. Normalmente, eles são mais adequados quando você só requer acesso limitado ao locatário do cliente. Depois de estabelecer uma relação com um cliente, se decidir conceder acesso adicional à sua organização, você poderá fazer isso publicando um novo plano privado somente para esse cliente ou [integrando-os para obter acesso adicional usando os modelos do Azure Resource Manager](../how-to/onboard-customer.md).

Se apropriado, você pode incluir planos públicos e privados na mesma oferta.

> [!IMPORTANT]
> Uma vez que um plano tenha sido publicado como público, você não pode mudá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, você não pode restringir a disponibilidade a certos clientes ou mesmo a um certo número de clientes (embora você possa parar de vender o plano completamente se você optar por fazê-lo). Você pode [remover o acesso a uma delegação](../how-to/onboard-customer.md#remove-access-to-a-delegation) depois que um cliente aceitar uma oferta somente se você incluiu uma **autorização** com a definição **de função** definida como Função [de Exclusão de Atribuição de Registro de Serviços Gerenciados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) quando você publicou a oferta. Você também pode entrar em contato com o cliente e pedir-lhe para [remover seu acesso](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicar ofertas de serviços gerenciados

Para saber como publicar uma oferta de serviços gerenciados, confira [Publicar uma oferta de Serviços Gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md). Para obter informações gerais sobre a publicação no Azure Marketplace usando o Portal de Parceiros em Nuvem, consulte [o Azure Marketplace e o AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [gerenciamento de recursos delegados do Azure](azure-delegated-resource-management.md) e [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- [Publique ofertas de serviços gerenciados](../how-to/publish-managed-services-offers.md) ao Azure Marketplace.
