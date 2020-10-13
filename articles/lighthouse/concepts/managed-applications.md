---
title: Azure Lighthouse e aplicativos gerenciados do Azure
description: Entenda como o Azure Lighthouse e os aplicativos gerenciados do Azure podem ajudar a habilitar cenários diferentes e como eles podem ser usados juntos.
ms.date: 08/12/2020
ms.topic: conceptual
ms.openlocfilehash: 078361ba3cff80a5fff03e3d9ca767afcc3ca029
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91970751"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse e aplicativos gerenciados do Azure

Os aplicativos gerenciados do Azure e o Azure Lighthouse funcionam habilitando um provedor de serviços para acessar os recursos que residem no locatário do cliente. Pode ser útil entender as diferenças no modo como funcionam e os cenários que eles ajudam a habilitar, além de como elas podem ser usadas juntas.

> [!TIP]
> Embora possamos nos referimos a provedores de serviços e clientes neste tópico, as [empresas que gerenciam vários locatários](enterprise.md) podem usar os mesmos processos e ferramentas.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Comparando o Azure Lighthouse e os aplicativos gerenciados do Azure

### <a name="azure-lighthouse"></a>Azure Lighthouse

Com o [Azure Lighthouse](../overview.md), um provedor de serviços pode executar uma ampla gama de tarefas de gerenciamento diretamente na assinatura de um cliente (ou grupo de recursos). Esse acesso é obtido por meio de uma projeção lógica, permitindo que os provedores de serviço entrem em seus próprios locatários e acessem os recursos que pertencem ao locatário do cliente. O cliente pode determinar quais assinaturas ou grupos de recursos delegar ao provedor de serviços, e o cliente mantém acesso completo a esses recursos. Eles também podem remover o acesso do provedor de serviços a qualquer momento.

Para usar o Azure Lighthouse, os clientes são integrados ao [Gerenciamento de recursos delegado do Azure](azure-delegated-resource-management.md) [implantando modelos de ARM](../how-to/onboard-customer.md) ou por meio de uma [oferta de serviço gerenciado no Azure Marketplace](managed-services-offers.md). Você pode acompanhar seu impacto nos compromissos do cliente e receber o crédito ganho [do parceiro vinculando sua ID de parceiro](../how-to/partner-earned-credit.md).

O Azure Lighthouse normalmente é usado quando um provedor de serviços executa tarefas de gerenciamento para um cliente de forma contínua.

### <a name="azure-managed-applications"></a>Aplicativos gerenciados do Azure

[Os aplicativos gerenciados do Azure](../../azure-resource-manager/managed-applications/overview.md) permitem que um provedor de serviços ou ISV ofereça soluções de nuvem que são fáceis para os clientes de implantar e usar em suas próprias assinaturas.

Em um aplicativo gerenciado, os recursos usados pelo aplicativo são agrupados e implantados em um grupo de recursos gerenciado pelo Publicador. Esse grupo de recursos está presente na assinatura do cliente, mas uma identidade no locatário do editor tem acesso a ele. O ISV continua a gerenciar e manter o aplicativo gerenciado, enquanto o cliente não tem acesso direto para trabalhar em seu grupo de recursos ou qualquer acesso a seus recursos.

Os aplicativos gerenciados dão suporte a [experiências personalizadas de portal do Azure](../../azure-resource-manager/managed-applications/concepts-view-definition.md) e [integração com provedores personalizados](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md). Essas opções podem ser usadas para fornecer uma experiência mais personalizada e integrada, facilitando para os clientes a execução de algumas tarefas de gerenciamento.

Os aplicativos gerenciados podem ser [publicados no Azure Marketplace](../../marketplace/partner-center-portal/create-new-azure-apps-offer.md), seja como uma oferta privada para uso específico do cliente ou como ofertas públicas que vários clientes podem comprar. Eles também podem ser entregues aos usuários em sua organização [Publicando aplicativos gerenciados em seu catálogo de serviços](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md). Você pode implantar as instâncias do catálogo de serviços e do Marketplace usando modelos do ARM, que podem incluir um identificador exclusivo do parceiro do Marketplace comercial para controlar a [atribuição de uso do cliente](../../marketplace/azure-partner-customer-usage-attribution.md).

Os aplicativos gerenciados do Azure normalmente são usados para uma necessidade específica do cliente que pode ser obtida por meio de uma solução prontamente gerenciada pelo provedor de serviços.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Usando o Azure Lighthouse e aplicativos gerenciados do Azure juntos

Embora o Azure Lighthouse e os aplicativos gerenciados do Azure usem diferentes mecanismos de acesso para atingir metas diferentes, pode haver situações em que faz sentido para um provedor de serviços usar ambos com o mesmo cliente.

Por exemplo, um cliente pode querer serviços gerenciados entregues por um provedor de serviços por meio do Azure Lighthouse, para que eles tenham visibilidade das ações do parceiro junto com o controle contínuo de sua assinatura delegada. No entanto, o provedor de serviços pode não querer que o cliente acesse determinados recursos que serão armazenados no locatário do cliente ou permita qualquer ação personalizada nesses recursos. Para atender a essas metas, o provedor de serviços pode publicar uma oferta privada como um aplicativo gerenciado. O aplicativo gerenciado pode incluir um grupo de recursos que é implantado no locatário do cliente, mas que não pode ser acessado diretamente pelo cliente.

Os clientes também podem estar interessados em aplicativos gerenciados de vários provedores de serviços, quer eles também usem serviços gerenciados por meio do Azure Lighthouse de qualquer um desses provedores de serviços. Além disso, os parceiros no programa CSP (provedor de soluções de nuvem) podem revender determinados aplicativos gerenciados publicados por outros ISVs para clientes que eles dão suporte por meio do Azure Lighthouse. Com uma ampla variedade de opções, os provedores de serviços podem escolher o equilíbrio certo para atender às necessidades dos clientes e restringir o acesso aos recursos quando apropriado.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [os aplicativos gerenciados do Azure](../../azure-resource-manager/managed-applications/overview.md).
- Saiba como [carregar uma assinatura no Azure Lighthouse](../how-to/onboard-customer.md).