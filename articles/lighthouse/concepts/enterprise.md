---
title: Azure Lighthouse em cenários empresariais
description: As funcionalidades do Azure Lighthouse podem ser usadas para simplificar o gerenciamento entre locatários dentro de uma empresa que usa vários locatários do Azure AD.
ms.date: 09/25/2019
ms.topic: conceptual
ms.openlocfilehash: 91089a6fb1a965191489e87027ef508c7ebe2aa2
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749216"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>Azure Lighthouse em cenários empresariais

O cenário mais comum para o [Azure Lighthouse](../overview.md) é um provedor de serviços que gerencia recursos nos locatários do Azure AD (Azure Active Directory) de seus clientes. No entanto, as funcionalidades do Azure Lighthouse também podem ser usadas para simplificar o gerenciamento entre locatários dentro de uma empresa que usa vários locatários do Azure AD.

## <a name="single-vs-multiple-tenants"></a>Locatários únicos versus múltiplos

Para a maioria das organizações, o gerenciamento é mais fácil com um único locatário do Azure AD. Ter todos os recursos em um locatário permite a centralização de tarefas de gerenciamento por usuários, grupos de usuários ou entidades de serviço designados dentro desse locatário. É recomendável usar um locatário para sua organização sempre que possível.

Ao mesmo tempo, há situações que podem exigir que uma organização mantenha vários locatários do Azure AD. Em alguns casos, isso pode ser uma situação temporária, como quando as aquisições ocorreram e uma estratégia de fusão de locatário de longo prazo levará algum tempo para ser definida. Uma organização também pode precisar manter vários locatários continuamente (devido às subsidiárias totalmente independentes, requisitos geográficos e legais e assim por diante). Nos casos em que é necessária uma arquitetura multilocatário, o gerenciamento de recursos delegados do Azure pode ser usado para centralizar e simplificar as operações de gerenciamento. As assinaturas de vários locatários podem ser integradas para o [gerenciamento de recursos delegados do Azure](azure-delegated-resource-management.md), permitindo que usuários designados em um locatário gerenciador realizem [funções de gerenciamento entre locatários](cross-tenant-management-experience.md) de maneira centralizada e escalonável.

## <a name="tenant-management-architecture"></a>Arquitetura de gerenciamento de locatários

Ao centralizar as operações de gerenciamento em vários locatários, será necessário determinar qual locatário incluirá os usuários que executam operações de gerenciamento para os outros locatários. Em outras palavras, será necessário determinar qual locatário será o locatário de gerenciamento para outros locatários.

Por exemplo, digamos que sua organização tenha um único locatário que chamarei *de locatário a*. Em seguida, sua organização adquire dois locatários adicionais, o *locatário B* e o *locatário C*, e você tem motivos comerciais que exigem que você os mantenha como locatários separados.

Sua organização deseja usar as mesmas definições de política, práticas de backup e processos de segurança entre todos os locatários. Como você já tem usuários (incluindo grupos de usuários e entidades de serviço) responsáveis por realizar essas tarefas no Locatário A, é possível integrar todas as assinaturas dentro do Locatário B e do Locatário C para que esses mesmos usuários no Locatário A possam executar essas tarefas.

![Usuários no Locatário A gerenciando recursos no Locatário B e no Locatário C](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>Considerações sobre segurança e acesso

Na maioria dos cenários empresariais, convém delegar uma assinatura completa para o gerenciamento de recursos delegados do Azure, embora também seja possível apenas especificar grupos de recursos dentro de uma assinatura.

De qualquer forma, [siga o princípio do menor privilégio ao definir quais usuários terão acesso aos recursos](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). Fazer isso ajuda a verificar se os usuários só têm as permissões necessárias para executar as tarefas necessárias e reduz a chance de erros inadvertidos.

O Azure Lighthouse e o gerenciamento de recursos delegados do Azure só fornecem links lógicos entre um locatário gerenciador e locatários gerenciados, em vez de mover fisicamente dados ou recursos. Além disso, o acesso sempre vai em apenas uma direção, do locatário gerenciador para os locatários gerenciados.  Os usuários e grupos no locatário gerenciador devem continuar usando a autenticação multifator ao executar operações de gerenciamento em recursos de locatários gerenciados.

As empresas com proteções de governança e conformidade internas e externas podem usar os [Logs de atividade do Azure](../../azure-monitor/platform/platform-logs-overview.md) para atender aos seus requisitos de transparência. Quando os locatários empresariais tiverem estabelecido relações de locatário gerenciador e gerenciado, os usuários em cada locatário podem monitorar e obter visibilidade para ações executadas pelos usuários no outro locatário exibindo a atividade registrada em log.

## <a name="onboarding-process-considerations"></a>Considerações sobre a integração de processos

As assinaturas (ou grupos de recursos dentro de uma assinatura) podem ser integradas ao gerenciamento de recursos delegados do Azure por meio da implantação de modelos do Azure Resource Manager ou de ofertas dos Serviços Gerenciados publicadas no Azure Marketplace, de maneira privada ou pública.

Como os usuários empresariais normalmente poderão obter acesso direto aos locatários da empresa e não há necessidade de comercializar ou promover uma oferta de gerenciamento, geralmente é mais rápido e simples implantar diretamente com modelos do Azure Resource Manager. Embora nos refiramos aos provedores de serviços e clientes nas [diretrizes de integração](../how-to/onboard-customer.md), as empresas podem usar os mesmos processos.

Se preferir, os locatários em uma empresa poderão ser integrados [publicando uma oferta de Serviços Gerenciados no Azure Marketplace](../how-to/publish-managed-services-offers.md). Para verificar se a oferta só está disponível para os locatários adequados, certifique-se de que seus planos estão marcados como privados. Com um plano privado, é possível fornecer IDs de assinatura para cada locatário que você planeja integrar e ninguém mais poderá obter sua oferta.

## <a name="terminology-notes"></a>Observações sobre terminologia

Para o gerenciamento entre locatários da empresa, as referências aos provedores de serviço na documentação do Azure Lighthouse podem ser compreendidas para serem aplicadas ao locatário gerenciador dentro de uma empresa – ou seja, o locatário que inclui os usuários que gerenciarão recursos em outros locatários por meio do gerenciamento de recursos delegados do Azure. De maneira semelhante, as referências aos clientes podem ser entendidas para serem aplicadas aos locatários que estão delegando recursos para serem gerenciados por meio de usuários no locatário gerenciador.

Por exemplo, no exemplo descrito acima, o Locatário A pode ser pensado como um locatário de provedor de serviços (o locatário gerenciador) e o Locatário B e o Locatário C podem ser pensados como os locatários do cliente.

Nesse exemplo, os usuários do Locatário A com as permissões adequadas podem [exibir e gerenciar recursos delegados](../how-to/view-manage-customers.md) na página **Meus clientes** do portal do Azure. Da mesma forma, os usuários dos Locatários B e C com as permissões adequadas podem [exibir e gerenciar os recursos que foram delegados](../how-to/view-manage-service-providers.md) ao Locatário A na página **Provedores de serviço** do portal do Azure.

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre as [experiências de gerenciamento entre locatários](cross-tenant-management-experience.md).
- Saiba mais sobre o [Gerenciamento de recursos delegados do Azure](azure-delegated-resource-management.md).