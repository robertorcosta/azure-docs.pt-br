---
title: Publique uma oferta de serviço gerenciado para o Azure Marketplace
description: Saiba como publicar uma oferta de serviço gerenciado que integre os clientes ao gerenciamento de recursos delegados do Azure.
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 922e78a0715ce5c758ca7d068a38af43e214a524
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673943"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publique uma oferta de serviço gerenciado para o Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta de serviço gerenciado público ou privado ao [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o programa [Commercial Marketplace](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) no Partner [Center](https://partner.microsoft.com/). Os clientes que comprarem a oferta podem então embarcar em assinaturas e grupos de recursos para [o gerenciamento de recursos delegados do Azure.](../concepts/azure-delegated-resource-management.md)

## <a name="publishing-requirements"></a>Publicação de requisitos

Você precisa ter uma conta válida [no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar ofertas. Se você ainda não tiver uma conta, o [processo de inscrição](https://aka.ms/joinmarketplace) o levará através das etapas de criação de uma conta no Partner Center e inscrição no programa Comercial.

De acordo com os [requisitos de certificação](https://docs.microsoft.com/legal/marketplace/certification-policies#7004-business-requirements)do Serviço Gerenciado, você deve ter um nível de competência da [Plataforma Silver ou Gold Cloud](https://docs.microsoft.com/partner-center/learn-about-competencies) ou ser um MSP especialista do [Azure](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de Serviço Gerenciado.

Sua ID do MPN (Microsoft Partner Network) será [associada automaticamente](../../billing/billing-partner-admin-link-started.md) às ofertas que você publicar para controlar seu impacto na participação dos clientes.

> [!NOTE]
> Se não quiser publicar uma oferta no Azure Marketplace, você poderá integrar os clientes manualmente usando modelos do Azure Resource Manager. Para saber mais, confira [Integrar um cliente no gerenciamento de recursos delegados do Azure](onboard-customer.md).

## <a name="create-your-offer"></a>Criar sua oferta

Para obter instruções detalhadas sobre como criar sua oferta, incluindo todas as informações e ativos que você precisará fornecer, consulte [Criar uma nova oferta de Serviços Gerenciados](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Para saber mais sobre o processo de publicação geral, consulte [O Azure Marketplace e o AppSource Publishing Guide](../../marketplace/marketplace-publishers-guide.md). Você também deve examinar as [políticas de certificação do mercado comercial](https://docs.microsoft.com/legal/marketplace/certification-policies), especialmente a seção [Serviços Gerenciados](https://docs.microsoft.com/legal/marketplace/certification-policies#700-managed-services).

Uma vez que um cliente adicione sua oferta, ele poderá delegar uma ou mais assinaturas ou grupos de recursos, que serão então [a bordo para o gerenciamento de recursos delegados do Azure.](#the-customer-onboarding-process)

> [!IMPORTANT]
> Cada plano em uma oferta de serviço gerenciado inclui uma seção **Detalhes do Manifesto,** onde você define as entidades do Azure Active Directory (Azure AD) em seu inquilino que terão acesso aos grupos de recursos delegados e/ou assinaturas para clientes que comprarem esse plano. É importante estar ciente de que qualquer grupo (ou usuário ou diretor de serviço) que você incluir terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir diferentes grupos para trabalhar com cada cliente, você precisará publicar um [plano privado](../../marketplace/private-offers.md) separado que seja exclusivo de cada cliente.

## <a name="publish-your-offer"></a>Publicar sua oferta

Depois de concluir todas as seções, a próxima etapa é publicar a oferta no Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de ativação da oferta. Mais informações sobre esse processo podem ser encontradas [aqui.](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish)

Você pode [publicar uma versão atualizada de sua oferta](../../marketplace/partner-center-portal/update-existing-offer.md) a qualquer momento. Por exemplo, talvez você queira adicionar uma nova definição de função a uma oferta publicada anteriormente. Quando você fizer isso, os clientes que já tiverem adicionado a oferta verão um ícone na página [**Provedores de serviço**](view-manage-service-providers.md) no portal do Azure avisando que há uma atualização disponível. Cada cliente poderá [examinar as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se querem atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de integração do cliente

Após o cliente adicionar a oferta, ele poderá [delegar uma ou mais assinaturas ou grupos de recursos específicos](view-manage-service-providers.md#delegate-resources), que serão integrados para gerenciamento de recursos delegados do Azure. Se um cliente tiver aceitado uma oferta, mas ainda não tiver delegado nenhum recurso, ele verá uma observação na parte superior da seção **Ofertas de provedores** da página [**Provedores de serviço**](view-manage-service-providers.md) no portal do Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta não-convidada no inquilino do cliente que tenha a [função de Proprietário incorporada](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) para a assinatura que está sendo a bordo (ou que contém os grupos de recursos que estão sendo abordados). Para ver todos os usuários que podem delegar a assinatura, um usuário do locatário do cliente poderá selecionar a assinatura no portal do Azure, abrir o **IAM (Controle de acesso)** e [exibir todos os usuários com a função Proprietário](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Uma vez que o cliente delegue uma assinatura (ou um ou mais grupos de recursos dentro de uma assinatura), o provedor de recursos **Microsoft.ManagedServices** será registrado para essa assinatura, e os usuários em seu inquilino poderão acessar os recursos delegados de acordo com as autorizações em sua oferta.

## <a name="next-steps"></a>Próximas etapas

- Conheça o [Mercado Comercial](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
