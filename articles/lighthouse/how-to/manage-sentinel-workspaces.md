---
title: Gerenciar espaços de trabalho do Azure Sentinel em escala
description: Saiba como gerenciar efetivamente o Azure Sentinel em recursos de clientes delegados.
ms.date: 06/17/2020
ms.topic: how-to
ms.openlocfilehash: bca5b6fdc84fa5a7a5553fe64c0218c5f0b44aa6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85268594"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Gerenciar espaços de trabalho do Azure Sentinel em escala

Como provedor de serviços, é provável que você tenha integrado vários locatários de clientes no gerenciamento de recursos delegados do Azure. O Azure Lighthouse permite que os provedores de serviços executem operações em escala em vários locatários Azure Active Directory (Azure AD) de uma vez, tornando as tarefas de gerenciamento mais eficientes.

O Azure Sentinel fornece análise de segurança e inteligência contra ameaças, fornecendo uma solução única para detecção de alertas, visibilidade de ameaças, busca proativa e resposta a ameaças. Com o Azure Lighthouse, você pode gerenciar vários espaços de trabalho do Azure Sentinel entre locatários em escala. Isso permite cenários como a execução de consultas em vários espaços de trabalho ou a criação de pastas de trabalho para visualizar e monitorar dados de suas fontes de dados conectadas para obter informações. O IP, como consultas e guias estratégicos, permanece no seu locatário de gerenciamento, mas pode ser usado para executar o gerenciamento de segurança nos locatários do cliente.

Este tópico fornece uma visão geral de como usar o [Azure Sentinel](../../sentinel/overview.md) de uma maneira escalonável para visibilidade entre locatários e serviços de segurança gerenciados.

## <a name="architectural-considerations"></a>Considerações sobre arquitetura

Para um MSSP (provedor de serviços de segurança gerenciado) que deseja criar uma oferta de segurança como serviço usando o Azure Sentinel, um único SOC (centro de operações de segurança) pode ser necessário para monitorar, gerenciar e configurar centralmente vários espaços de trabalho do Azure Sentinel implantados em locatários individuais do cliente. Da mesma forma, as empresas com vários locatários do Azure AD podem querer gerenciar centralmente vários espaços de trabalho do Azure Sentinel implantados em seus locatários.

Esse modelo centralizado de implantação tem as seguintes vantagens:

- A propriedade dos dados permanece com cada locatário gerenciado.
- Dá suporte a requisitos para armazenar dados em limites geográficos.
- Garante o isolamento de dados, já que os dados de vários clientes não são armazenados no mesmo espaço de trabalho. 
- Impede a vazamento de dados dos locatários gerenciados, ajudando a garantir a conformidade dos dados.
- Os custos relacionados são cobrados em cada locatário gerenciado, e não no locatário de gerenciamento.
- Os dados de todas as fontes de dados e conectores de dados que são integrados ao Azure Sentinel (como logs de atividades do Azure AD, logs do Office 365 ou alertas de proteção contra ameaças da Microsoft) permanecerão dentro de cada locatário do cliente.
- Reduz a latência de rede.
- Fácil de adicionar ou remover novas subsidiárias ou clientes.

## <a name="granular-role-based-access-control-rbac"></a>RBAC (controle de acesso baseado em função) granular

Cada assinatura de cliente que um MSSP gerenciará deve ser [integrada para o gerenciamento de recursos delegado do Azure](onboard-customer.md). Isso permite que usuários designados no locatário de gerenciamento acessem e realizem operações de gerenciamento em espaços de trabalho do Azure Sentinel implantados em locatários do cliente.

Ao criar suas autorizações, você pode atribuir as funções internas do Azure Sentinel a usuários, grupos ou entidades de serviço no seu locatário de gerenciamento:

- [Leitor do Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Respondente do Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Colaborador do Azure Sentinel](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Você também pode querer atribuir funções internas adicionais para executar funções adicionais. Para obter informações sobre funções específicas que podem ser usadas com o Azure Sentinel, consulte [permissões no Azure Sentinel](../../sentinel/roles.md).

Depois de integrar seus clientes, os usuários designados podem fazer logon em seu locatário de gerenciamento e [acessar diretamente o espaço de trabalho do Azure Sentinel do cliente](../../sentinel/multiple-tenants-service-providers.md) com as funções que foram atribuídas.

## <a name="view-and-manage-incidents-across-workspaces"></a>Exibir e gerenciar incidentes em espaços de trabalho

Se você estiver gerenciando recursos do Azure Sentinel para vários clientes, poderá exibir e gerenciar incidentes em vários espaços de trabalho em vários locatários de uma vez. Para obter mais informações, consulte [trabalhar com incidentes em muitos espaços de trabalho ao mesmo tempo](../../sentinel/multiple-workspace-view.md) e [estender o Azure Sentinel entre espaços de trabalho e locatários](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Certifique-se de que os usuários em seu locatário de gerenciamento tenham recebido permissões de leitura e gravação em todos os espaços de trabalho gerenciados. Se um usuário só tiver permissões de leitura em alguns espaços de trabalho, mensagens de aviso poderão ser exibidas ao selecionar incidentes nesses espaços de trabalho, e o usuário não poderá modificar esses incidentes ou quaisquer outros que você tenha selecionado com eles (mesmo que você tenha permissões para os outros).

## <a name="configure-playbooks-for-mitigation"></a>Configurar guias estratégicos para mitigação

Os [Guias estratégicos](../../sentinel/tutorial-respond-threats-playbook.md) podem ser usados para mitigação automática quando um alerta é disparado. Esses guias estratégicos podem ser executados manualmente ou podem ser executados automaticamente quando alertas específicos são disparados. Os guias estratégicos podem ser implantados no locatário de gerenciamento ou locatário do cliente, com os procedimentos de resposta configurados com base nos usuários de locatários que precisarão tomar medidas em resposta a uma ameaça de segurança.

## <a name="create-cross-tenant-workbooks"></a>Criar pastas de trabalho de locatário cruzado

[Azure monitor pastas de trabalho no Azure Sentinel](../../sentinel/overview.md#workbooks) o ajudam a visualizar e monitorar dados de suas fontes de dados conectadas para obter informações. Você pode usar os modelos de pasta de trabalho internos no Azure Sentinel ou criar pastas de trabalho personalizadas para seus cenários.

Você pode implantar pastas de trabalho no seu locatário de gerenciamento e criar painéis em escala para monitorar e consultar dados entre locatários do cliente. Para obter mais informações, consulte [monitoramento entre espaços de trabalho](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). Observe que alguns recursos [não têm suporte em vários espaços de trabalho](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

Você também pode implantar pastas de trabalho diretamente em um locatário individual que você gerencia para cenários específicos para esse cliente.

## <a name="run-queries-across-azure-sentinel-workspaces"></a>Executar consultas em espaços de trabalho do Azure Sentinel

Você pode criar e salvar Log Analytics consultas para detecção de ameaças centralmente no locatário de gerenciamento. Essas consultas podem ser executadas em todos os espaços de trabalho do Azure Sentinel de seus clientes usando o operador Union e a expressão Workspace (). Para obter mais informações, consulte [consulta entre espaços de trabalho](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Usar a automação para gerenciamento entre espaços de trabalho

Você pode usar a automação para gerenciar vários espaços de trabalho do Azure Sentinel e configurar [consultas de busca](../../sentinel/hunting.md), guias estratégicos e pastas de trabalho. Para obter mais informações, consulte [gerenciamento entre espaços de trabalho usando automação](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

Observe que alguns recursos [não têm suporte no momento em vários espaços de trabalho](../../sentinel/extend-sentinel-across-workspaces-tenants.md#whats-not-supported-across-workspaces).

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre o [Azure Sentinel](../../sentinel/overview.md).
- Examine a [página de preços do Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Saiba mais sobre as [experiências de gerenciamento entre locatários](../concepts/cross-tenant-management-experience.md).

