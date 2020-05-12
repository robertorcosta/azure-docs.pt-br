---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os provedores de serviços forneçam serviços gerenciados para seus clientes com maior automação e eficiência em escala.
ms.date: 05/05/2020
ms.topic: overview
ms.openlocfilehash: bc54a6f67ed3d0ac194793baba4270d524edf5b1
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857139"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse oferece aos provedores de serviços um plano de controle único para exibir e gerenciar o Azure em todos os seus clientes com maior automação, escala e governança avançada. Com o Azure Lighthouse, os provedores de serviços podem entregar serviços gerenciados usando ferramentas de gerenciamento abrangentes e robustas integradas na plataforma Azure. Essa oferta também pode beneficiar as organizações de TI empresariais que gerenciam recursos em vários locatários. 

![Diagrama de visão geral do Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda a criar e fornecer de forma lucrativa e eficiente serviços gerenciados para seus clientes. Os benefícios incluem:

- **Gerenciamento em escala**: a participação do cliente e as operações de ciclo de vida para gerenciar os recursos do cliente são mais fáceis e escaláveis. APIs, ferramentas de gerenciamento e fluxos de trabalho existentes podem ser usados com recursos de cliente delegados.
- **Maior visibilidade e precisão para os clientes**: os clientes terão maior visibilidade de suas ações e controle preciso sobre o escopo delegado para gerenciamento, incluindo a capacidade de remover totalmente o acesso, enquanto o IP é preservado.
- **Ferramentas de plataforma unificadas e abrangentes**: nossa experiência de ferramentas aborda os principais cenários de provedor de serviços, incluindo vários modelos de licenciamento, como EA, CSP e pago conforme o uso. Os novos recursos funcionam com ferramentas e APIs existentes, modelos de licenciamento e programas parceiros, [como o CSP (programa Provedor de Soluções na Nuvem)](https://docs.microsoft.com/partner-center/csp-overview). O Azure Lighthouse pode ser integrado a seus fluxos de trabalho e aplicativos existentes. Você pode acompanhar seu impacto sobre as participações de clientes [vinculando sua ID de parceiro](../billing/billing-partner-admin-link-started.md).

Não há custos adicionais associados ao uso do Azure Lighthouse para gerenciar os recursos do Azure dos seus clientes. Qualquer cliente ou parceiro do Azure pode usar o Azure Lighthouse.

## <a name="capabilities"></a>Funcionalidades

O Azure Lighthouse inclui várias maneiras de ajudar a simplificar a participação e o gerenciamento de clientes:

- **Gerenciamento de recursos delegados do Azure**: gerencie os recursos do Azure dos seus clientes com segurança de dentro do seu próprio locatário sem precisar alternar os planos de contexto e de controle. As assinaturas e os grupos de recursos podem ser delegados a usuários e funções especificados no locatário de gerenciamento, com a capacidade de remover o acesso conforme necessário. Para saber mais, confira [Gerenciamento de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- **Novas experiências do portal do Azure**: veja as informações entre locatários na nova página **Meus clientes** no [portal do Azure](https://portal.azure.com). Uma folha **Provedores de serviços** correspondente permite que seus clientes vejam e gerenciem o acesso do provedor de serviços. Para saber mais, confira [Exibir e gerenciar clientes](./how-to/view-manage-customers.md) e [Exibir e gerenciar provedores de serviços](how-to/view-manage-service-providers.md).
- **Modelos do Azure Resource Manager**: execute tarefas de gerenciamento com mais facilidade, incluindo a integração de clientes para o gerenciamento de recursos delegados do Azure. Para saber mais, confira nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) e [Integrar um cliente ao gerenciamento de recursos delegados do Azure](how-to/onboard-customer.md).
- **Ofertas de serviço gerenciado no Azure Marketplace**: ofereça seus serviços aos clientes por meio de ofertas privadas ou públicas e faça com que eles sejam automaticamente integrados ao gerenciamento de recursos delegados do Azure como uma alternativa à integração usando modelos do Azure Resource Manager. Para saber mais, confira [Ofertas de serviço gerenciado no Azure Marketplace](concepts/managed-services-offers.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](concepts/cross-tenant-management-experience.md).
