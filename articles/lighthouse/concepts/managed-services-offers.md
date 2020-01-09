---
title: Ofertas de Serviços Gerenciados no Azure Marketplace
description: As ofertas de serviços gerenciados permitem que os provedores de serviços vendam ofertas de gerenciamento de recursos a clientes no Azure Marketplace.
ms.date: 12/16/2019
ms.topic: conceptual
ms.openlocfilehash: 1b4f0d7457a74afe710a48f429cfe47535a9b122
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453580"
---
# <a name="managed-services-offers-in-azure-marketplace"></a>Ofertas de Serviços Gerenciados no Azure Marketplace

Este artigo descreve o novo tipo de oferta **Serviços Gerenciados** no [Azure Marketplace](https://azuremarketplace.microsoft.com). As ofertas de serviços gerenciados permitem que você ofereça serviços de gerenciamento de recursos a clientes com o gerenciamento de recursos delegados do Azure. É possível disponibilizar essas ofertas a todos os clientes potenciais ou a apenas um ou clientes mais específicos. Como você cobra os clientes diretamente por custos relacionados a esses serviços gerenciados, não há valores cobrados pela Microsoft.

## <a name="understand-managed-services-offers"></a>Noções básicas sobre ofertas de serviços gerenciados

As ofertas de serviços gerenciados simplificam o processo de integrar clientes para o gerenciamento de recursos delegados do Azure. Quando um cliente adquire uma oferta no Azure Marketplace, ele pode especificar quais assinaturas e/ou grupos de recursos devem ser integrados. Observe que a assinatura deve ser autorizada para integração ao ser registrada manualmente no provedor de recursos **Microsoft.ManagedServices**.

Depois disso, os usuários em sua organização poderão executar tarefas de administração para esses recursos de dentro do locatário da sua organização, de acordo com o acesso que você definiu ao criar a oferta no [Portal do Cloud Partner](https://cloudpartner.azure.com/). Isso ocorre por meio de um manifesto que especifica os usuários, grupos e entidades de serviço do Azure AD que terão acesso aos recursos do cliente usando o gerenciamento de recursos delegados do Azure, junto com funções que definem o nível de acesso. Ao atribuir permissões a um grupo do Azure AD em vez de uma série de contas individuais de usuário ou de aplicativo, é possível adicionar ou remover usuários individuais quando seus requisitos de acesso mudam.

## <a name="public-and-private-offers"></a>Ofertas públicas e privadas

Cada oferta de serviços gerenciados inclui um ou mais planos. Os planos podem ser privados ou públicos. 

Se desejar limitar sua oferta a clientes específicos, será possível publicar um plano privado. Quando fizer isso, o plano só poderá ser adquirido para IDs da assinatura específicas que você fornecer. Para saber mais, confira [Ofertas privadas](../../marketplace/private-offers.md).

Planos públicos permitem que você promova serviços para novos clientes. Normalmente, eles são mais adequados quando você só requer acesso limitado ao locatário do cliente. Depois de estabelecer uma relação com um cliente, se decidir conceder acesso adicional à sua organização, você poderá fazer isso publicando um novo plano privado somente para esse cliente ou [integrando-os para obter acesso adicional usando os modelos do Azure Resource Manager](../how-to/onboard-customer.md).

Se apropriado, você pode incluir planos públicos e privados na mesma oferta.

> [!IMPORTANT]
> Depois que um plano tiver sido publicado como público, você não poderá alterá-lo para privado. Para controlar quais clientes podem aceitar sua oferta e delegar recursos, use um plano privado. Com um plano público, não é possível restringir a disponibilidade para determinados clientes ou até mesmo para um determinado número de clientes (embora você possa deixar de vender completamente o plano se optar por fazê-lo). Atualmente, não há nenhum mecanismo para rejeitar ou remover delegações depois que um cliente aceita uma oferta, embora você sempre possa entrar em contato com um cliente e pedir que ele [remova seu acesso](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Publicar ofertas de serviços gerenciados

Para saber como publicar uma oferta de serviços gerenciados, confira [Publicar uma oferta de Serviços Gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md). Para obter informações gerais sobre como publicar no Azure Marketplace usando o Portal do Cloud Partner, confira [Guia de publicação do Azure Marketplace e AppSource](../../marketplace/marketplace-publishers-guide.md) e [Gerenciar ofertas do Azure e do AppSource Marketplace](../../marketplace/cloud-partner-portal/manage-offers/cpp-manage-offers.md).

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre o [gerenciamento de recursos delegados do Azure](azure-delegated-resource-management.md) e [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- [Publique ofertas de serviços gerenciados](../how-to/publish-managed-services-offers.md) no Azure Marketplace.
