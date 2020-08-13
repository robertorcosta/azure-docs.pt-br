---
title: Publicar uma oferta de serviço gerenciado no Azure Marketplace
description: Saiba como publicar uma oferta de serviço gerenciado que integra os clientes ao Azure Lighthouse.
ms.date: 07/28/2020
ms.topic: how-to
ms.openlocfilehash: ab12238bac7e479766b3e328c02f73521d08ad96
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88167208"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Publicar uma oferta de serviço gerenciado no Azure Marketplace

Neste artigo, você aprenderá a publicar uma oferta de serviço gerenciado pública ou privada para o [Azure Marketplace](https://azuremarketplace.microsoft.com) usando o programa do [Marketplace comercial](../../marketplace/partner-center-portal/commercial-marketplace-overview.md) no Partner Center. Os clientes que comprarem a oferta delegarão assinaturas ou grupos de recursos, permitindo que você os gerencie por meio [do Azure Lighthouse](../overview.md).

## <a name="publishing-requirements"></a>Publicação de requisitos

Você precisa ter uma conta válida [no Partner Center](../../marketplace/partner-center-portal/create-account.md) para criar e publicar ofertas. Se você ainda não tiver uma conta, o [processo de inscrição](https://aka.ms/joinmarketplace) o conduzirá pelas etapas de criação de uma conta no Partner Center e registro no programa do Marketplace comercial.

De acordo com os [requisitos de certificação da oferta de serviço gerenciado](/legal/marketplace/certification-policies#7004-business-requirements), você deve ter um [nível de competência de plataforma de nuvem prata ou ouro](/partner-center/learn-about-competencies) ou ser um [Azure expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) para publicar uma oferta de serviço gerenciado.

Sua ID do MPN (Microsoft Partner Network) será [associada automaticamente](../../cost-management-billing/manage/link-partner-id.md) às ofertas que você publicar para controlar seu impacto na participação dos clientes.

> [!NOTE]
> Se não quiser publicar uma oferta no Azure Marketplace, você poderá integrar os clientes manualmente usando modelos do Azure Resource Manager. Para obter mais informações, consulte integrar [um cliente ao Azure Lighthouse](onboard-customer.md).

## <a name="create-your-offer"></a>Criar sua oferta

Para obter instruções detalhadas sobre como criar sua oferta, incluindo todas as informações e ativos que você precisará fornecer, consulte [criar uma oferta de serviço gerenciado](../../marketplace/partner-center-portal/create-new-managed-service-offer.md).

Para saber mais sobre o processo de publicação geral, consulte o [Guia de publicação do Azure Marketplace e do AppSource](../../marketplace/marketplace-publishers-guide.md). Você também deve examinar as [políticas de certificação do mercado comercial](/legal/marketplace/certification-policies), especialmente a seção [Serviços Gerenciados](/legal/marketplace/certification-policies#700-managed-services).

Depois que um cliente adiciona sua oferta, ele poderá delegar uma ou mais assinaturas ou grupos de recursos, que serão [integrados ao Azure Lighthouse](#the-customer-onboarding-process).

> [!IMPORTANT]
> Cada plano em uma oferta de serviço gerenciado inclui uma seção **detalhes do manifesto** , em que você define as entidades Azure Active Directory (Azure AD) em seu locatário que terão acesso aos grupos de recursos delegados e/ou assinaturas para clientes que compram esse plano. É importante estar ciente de que qualquer grupo (ou entidade de serviço ou usuário) que você incluir terá as mesmas permissões para cada cliente que comprar o plano. Para atribuir grupos diferentes para trabalhar com cada cliente, você pode publicar um [plano privado](../../marketplace/private-offers.md) separado que seja exclusivo para cada cliente. Tenha em mente que não há suporte para planos privados com assinaturas estabelecidas por meio de um revendedor do programa CSP (provedor de soluções na nuvem).

## <a name="publish-your-offer"></a>Publicar sua oferta

Depois de concluir todas as seções, a próxima etapa é publicar a oferta no Azure Marketplace. Selecione o botão **Publicar** para iniciar o processo de ativação da oferta. Mais informações sobre esse processo podem ser encontradas [aqui](../../marketplace/partner-center-portal/create-new-managed-service-offer.md#publish). 

Você pode [publicar uma versão atualizada de sua oferta](../..//marketplace/partner-center-portal/update-existing-offer.md) a qualquer momento. Por exemplo, talvez você queira adicionar uma nova definição de função a uma oferta publicada anteriormente. Quando você fizer isso, os clientes que já tiverem adicionado a oferta verão um ícone na página [**Provedores de serviço**](view-manage-service-providers.md) no portal do Azure avisando que há uma atualização disponível. Cada cliente poderá [revisar as alterações](view-manage-service-providers.md#update-service-provider-offers) e decidir se deseja atualizar para a nova versão. 

## <a name="the-customer-onboarding-process"></a>O processo de integração do cliente

Depois que um cliente adiciona sua oferta, ele poderá [delegar uma ou mais assinaturas ou grupos de recursos específicos](view-manage-service-providers.md#delegate-resources), que serão integrados ao Azure Lighthouse. Se um cliente tiver aceitado uma oferta, mas ainda não tiver delegado nenhum recurso, ele verá uma observação na parte superior da seção **Ofertas de provedores** da página [**Provedores de serviço**](view-manage-service-providers.md) no portal do Azure.

> [!IMPORTANT]
> A delegação deve ser feita por uma conta que não seja de convidado no locatário do cliente que tem a [função interna de proprietário](../../role-based-access-control/built-in-roles.md#owner) para a assinatura que está sendo integrada (ou que contém os grupos de recursos que estão sendo integrados). Para ver todos os usuários que podem delegar a assinatura, um usuário do locatário do cliente poderá selecionar a assinatura no portal do Azure, abrir o **IAM (Controle de acesso)** e [exibir todos os usuários com a função Proprietário](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Depois que o cliente delega uma assinatura (ou um ou mais grupos de recursos em uma assinatura), o provedor de recursos **Microsoft. managedservices** será registrado para essa assinatura e os usuários em seu locatário poderão acessar os recursos delegados de acordo com as autorizações em sua oferta.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Marketplace comercial](../../marketplace/partner-center-portal/commercial-marketplace-overview.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).
- [Exiba e gerencie clientes](view-manage-customers.md) acessando **Meus clientes** no portal do Azure.
