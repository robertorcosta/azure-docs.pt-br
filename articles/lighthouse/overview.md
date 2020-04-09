---
title: O que é o Azure Lighthouse?
description: O Azure Lighthouse permite que os provedores de serviços forneçam serviços gerenciados para seus clientes com maior automação e eficiência em escala.
ms.date: 11/11/2019
ms.topic: overview
ms.openlocfilehash: 1d4d33238f30c2a579c6a0da6a39238207db4f4a
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982779"
---
# <a name="what-is-azure-lighthouse"></a>O que é o Azure Lighthouse?

O Azure Lighthouse oferece aos provedores de serviços um plano de controle único para exibir e gerenciar o Azure em todos os seus clientes com maior automação, escala e governança avançada. Com o Azure Lighthouse, os provedores de serviços podem entregar serviços gerenciados usando ferramentas de gerenciamento abrangentes e robustas integradas na plataforma Azure. Essa oferta também pode beneficiar as organizações de TI empresariais que gerenciam recursos em vários locatários.

![Diagrama de visão geral do Azure Lighthouse](media/azure-lighthouse-overview.jpg)

## <a name="benefits"></a>Benefícios

O Azure Lighthouse ajuda a criar e fornecer de forma lucrativa e eficiente serviços gerenciados para seus clientes. Os benefícios incluem:

- **Gerenciamento em escala**: a participação do cliente e as operações de ciclo de vida para gerenciar os recursos do cliente são mais fáceis e escaláveis.
- **Maior visibilidade e precisão para os clientes**: os clientes cujos recursos você está gerenciando terão maior visibilidade de suas ações e controle preciso sobre o escopo delegado para gerenciamento, enquanto o IP é preservado.
- **Ferramentas de plataforma unificadas e abrangentes**: nossa experiência de ferramentas aborda os principais cenários de provedor de serviços, incluindo vários modelos de licenciamento, como EA, CSP e pago conforme o uso. Os novos recursos funcionam com ferramentas e APIs existentes, modelos de licenciamento e programas parceiros, [como o CSP (programa Provedor de Soluções na Nuvem)](https://docs.microsoft.com/partner-center/csp-overview). As opções do Azure Lighthouse que você escolher poderão ser integradas em seus fluxos de trabalho e aplicativos existentes, e você poderá acompanhar seu impacto sobre as participações de clientes [vinculando sua ID de parceiro](../billing/billing-partner-admin-link-started.md).

Não há custos adicionais associados ao uso do Azure Lighthouse para gerenciar os recursos do Azure dos seus clientes.

## <a name="capabilities"></a>Funcionalidades

O Azure Lighthouse inclui várias maneiras de ajudar a simplificar a participação e o gerenciamento de clientes:

- **Gerenciamento de recursos delegados do Azure**: gerencie os recursos do Azure dos seus clientes com segurança de dentro do seu próprio locatário sem precisar alternar os planos de contexto e de controle. Para saber mais, confira [Gerenciamento de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- **Novas experiências do portal do Azure**: veja as informações entre locatários na nova página **Meus clientes** no [portal do Azure](https://portal.azure.com). Uma folha **Provedores de serviços** correspondente permite que seus clientes vejam e gerenciem o acesso do provedor de serviços. Para saber mais, confira [Exibir e gerenciar clientes](./how-to/view-manage-customers.md) e [Exibir e gerenciar provedores de serviços](how-to/view-manage-service-providers.md).
- **Modelos do Azure Resource Manager**: execute tarefas de gerenciamento com mais facilidade, incluindo a integração de clientes para o gerenciamento de recursos delegados do Azure. Para saber mais, confira nosso [repositório de exemplos](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates) e [Integrar um cliente ao gerenciamento de recursos delegados do Azure](how-to/onboard-customer.md).
- **Ofertas de serviços gerenciados no Azure Marketplace**: ofereça seus serviços aos clientes por meio de ofertas privadas ou públicas e faça com que eles sejam automaticamente integrados ao gerenciamento de recursos delegados do Azure como uma alternativa à integração usando modelos do Azure Resource Manager. Para saber mais, confira [Ofertas de serviços gerenciados no Azure Marketplace](concepts/managed-services-offers.md).
- **Aplicativos gerenciados do Azure**: empacote e envie aplicativos fáceis para seus clientes implantarem e usarem em suas próprias assinaturas. O aplicativo é implantado em um grupo de recursos que você acessa de seu locatário, permitindo que você gerencie o serviço como parte da experiência geral do Azure Lighthouse. Para saber mais, confira [Visão geral de aplicativos gerenciados do Azure](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](concepts/azure-delegated-resource-management.md).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](concepts/cross-tenant-management-experience.md).
