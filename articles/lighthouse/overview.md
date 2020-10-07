---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os provedores de serviços forneçam serviços gerenciados para seus clientes com maior automação e eficiência em escala.
ms.date: 08/19/2020
ms.topic: overview
ms.openlocfilehash: 22bec7ec8944a11ce0cfdf51776f1f193a1aedaa
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2020
ms.locfileid: "89488806"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse permite o gerenciamento entre vários locatários, o que gera maior automação, escalabilidade e governança aprimorada entre recursos e locatários. Com o Azure Lighthouse, os provedores de serviços podem entregar serviços gerenciados usando ferramentas de gerenciamento abrangentes e robustas integradas na plataforma Azure. Essa oferta também pode beneficiar as organizações de TI empresariais que gerenciam recursos em vários locatários.

![Diagrama de visão geral do Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda a criar e fornecer serviços gerenciados de maneira lucrativa e eficiente. Os benefícios incluem:

- **Gerenciamento em escala**: a participação do cliente e as operações de ciclo de vida para gerenciar os recursos do cliente são mais fáceis e escaláveis. APIs, ferramentas de gerenciamento e fluxos de trabalho existentes podem ser usados com recursos delegados, independentemente das regiões onde estão localizados.
- **Maior visibilidade e precisão para os clientes**: os clientes terão maior visibilidade de suas ações e controle preciso sobre o escopo delegado para gerenciamento, incluindo a capacidade de remover totalmente o acesso, enquanto o IP é preservado.
- **Ferramentas de plataforma unificadas e abrangentes**: nossa experiência de ferramentas aborda os principais cenários de provedor de serviços, incluindo vários modelos de licenciamento, como EA, CSP e pago conforme o uso. Os novos recursos funcionam com ferramentas e APIs existentes, modelos de licenciamento e programas parceiros, [como o CSP (programa Provedor de Soluções na Nuvem)](/partner-center/csp-overview). O Azure Lighthouse pode ser integrado a seus fluxos de trabalho e aplicativos existentes. Você pode acompanhar seu impacto sobre as participações de clientes e receber crédito ganho pelo parceiro [vinculando sua ID de parceiro](./how-to/partner-earned-credit.md).

Não há custos adicionais associados ao uso do Azure Lighthouse para gerenciar os recursos do Azure. Qualquer cliente ou parceiro do Azure pode usar o Azure Lighthouse.

## <a name="capabilities"></a>Funcionalidades

O Azure Lighthouse inclui várias maneiras de ajudar a simplificar a participação e o gerenciamento:

- **Gerenciamento de recursos delegados do Azure**: gerencie os recursos do Azure dos seus clientes com segurança de dentro do seu próprio locatário sem precisar alternar os planos de contexto e de controle. As assinaturas e os grupos de recursos podem ser delegados a usuários e funções especificados no locatário de gerenciamento, com a capacidade de remover o acesso conforme necessário. Para saber mais, confira [Gerenciamento de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- **Novas experiências do portal do Azure**: veja as informações entre locatários na [página **Meus clientes**](./how-to/view-manage-customers.md) no portal do Azure. Uma [página **Provedores de serviços**](how-to/view-manage-service-providers.md) correspondente permite que os clientes vejam e gerenciem o acesso do provedor de serviços.
- **Modelos do Azure Resource Manager**: Nossos modelos ajudam você a executar tarefas de gerenciamento entre locatários e integrar recursos de clientes delegados. Para obter mais informações, consulte nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) e [Integrar um cliente ao Azure Lighthouse](how-to/onboard-customer.md).
- **Ofertas de serviço gerenciado no Azure Marketplace**: Ofereça seus serviços aos clientes por meio de ofertas privadas ou públicas e integre-os automaticamente ao Azure Lighthouse. Para saber mais, confira [Ofertas de serviço gerenciado no Azure Marketplace](concepts/managed-services-offers.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Explore [experiências de gerenciamento entre locatários](concepts/cross-tenant-management-experience.md).
- Veja como [usar o Azure Lighthouse em uma empresa](concepts/enterprise.md).
