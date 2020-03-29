---
title: Configure seu ambiente para operador de projeto
description: Saiba como configurar seu ambiente Azure para uso com a função RBAC (Based Role-Based Access Control, controle de acesso baseado em função) do Operador de Projeto.
ms.date: 08/26/2019
ms.topic: how-to
ms.openlocfilehash: fba0dd3f2eeb69f768800d1d04640510462d3c86
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873209"
---
# <a name="configure-your-environment-for-a-blueprint-operator"></a>Configurar seu ambiente para um operador de Blueprint

O gerenciamento de suas definições de projeto e atribuições de projeto podem ser atribuídos a diferentes equipes. É comum que um arquiteto ou equipe de governança seja responsável pelo gerenciamento do ciclo de vida das definições de seu projeto, enquanto uma equipe de operações é responsável por gerenciar as atribuições dessas definições de projeto controladas centralmente.

O **RBAC (Built-based** Role-based Role-based Control, controle de acesso) incorporado do Operador de Projeto foi projetado especificamente para uso neste tipo de cenário. A função permite que as equipes do tipo de operações gerenciem a atribuição das definições de projeto das organizações, mas não a capacidade de modificá-las. Isso requer alguma configuração em seu ambiente Azure e este artigo explica as etapas necessárias.

## <a name="grant-permission-to-the-blueprint-operator"></a>Permissão de concessão ao Operador de Projeto

O primeiro passo é conceder a função **de Operador de Projeto** para a conta ou grupo de segurança (recomendado) que estará atribuindo projetos. Essa ação deve ser feita no mais alto nível na hierarquia do grupo de gestão que abrange todos os grupos de gerenciamento e assinaturas que a equipe de operações deve ter acesso à atribuição de plantas. Recomenda-se seguir o princípio do menor privilégio ao conceder essas permissões.

1. (Recomendado) [Crie um grupo de segurança e adicione membros](../../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

1. [Adicione uma atribuição](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) de função do Operador de **Projeto** à conta ou ao grupo de segurança

## <a name="user-assign-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Uma definição de projeto pode usar identidades gerenciadas atribuídas pelo sistema ou atribuídas pelo usuário. No entanto, ao usar a função **Operador de projeto,** a definição do projeto precisa ser configurada para usar uma identidade gerenciada atribuída pelo usuário. Além disso, a conta ou o grupo de segurança que está sendo concedido a função **Operador de Projeto** precisa ser concedido a função operador de identidade **gerenciado** na identidade gerenciada atribuída pelo usuário. Sem essa permissão, as atribuições do projeto falham por falta de permissões.

1. [Criar uma identidade gerenciada atribuída pelo usuário](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) para uso por um projeto atribuído

1. [Adicione uma atribuição](../../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) de função do **Gerenciado Identity Operator** à conta ou ao grupo de segurança. Escopo da atribuição de função para a nova identidade gerenciada atribuída pelo usuário.

1. Como **operador de projeto,** [atribua um projeto](../create-blueprint-portal.md#assign-a-blueprint) que usa a nova identidade gerenciada atribuída pelo usuário.

## <a name="next-steps"></a>Próximas etapas

- Conheça o [ciclo de vida](../concepts/lifecycle.md)do projeto .
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).