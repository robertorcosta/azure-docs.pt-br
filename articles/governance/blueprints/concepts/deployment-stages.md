---
title: Etapas de uma implantação de blueprint
description: Aprenda as etapas relacionadas à segurança e ao artefato que os serviços do Azure Blueprints passam ao criar uma atribuição de projeto.
ms.date: 11/13/2019
ms.topic: conceptual
ms.openlocfilehash: 61d19c84cd659b9df3a272c5c2743944e51df06e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80677321"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapas de uma implantação de blueprint

Quando um projeto é implantado, uma série de ações é tomada pelo serviço Azure Blueprints para implantar os recursos definidos no projeto. Este artigo fornece detalhes sobre o que cada etapa envolve.

A implantação do projeto é acionada atribuindo um projeto a uma assinatura ou [atualizando uma atribuição existente](../how-to/update-existing-assignments.md). Durante a implantação, o Azure Blueprints toma as seguintes etapas de alto nível:

> [!div class="checklist"]
> - Azure Blueprints concedeu os direitos do proprietário
> - O objeto de atribuição do projeto é criado
> - Opcional - Azure Blueprints cria identidade **gerenciada atribuída ao sistema**
> - A identidade gerenciada implanta artefatos de projeto
> - O serviço azure Blueprints e os direitos de identidade **gerenciados atribuídos pelo sistema** são revogados

## <a name="azure-blueprints-granted-owner-rights"></a>Azure Blueprints concedeu os direitos do proprietário

O principal do serviço Azure Blueprints recebe os direitos do proprietário da assinatura ou assinaturas atribuídas quando uma identidade [gerenciada gerenciada atribuída pelo sistema](../../../active-directory/managed-identities-azure-resources/overview.md) é usada. A função concedida permite que o Azure Blueprints crie e, posteriormente, revogue a identidade **gerenciada atribuída pelo sistema.** Se usar uma identidade **gerenciada atribuída pelo usuário,** o diretor do serviço Azure Blueprints não recebe e não precisa de direitos de proprietário na assinatura.

Os direitos são concedidos automaticamente se a cessão for feita através do portal. No entanto, se a atribuição for feita através da API REST, a concessão dos direitos precisa ser feita com uma chamada de API separada. O Azure Blueprints AppId é `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, mas o principal de serviço varia de acordo com o inquilino. Use [a API do Gráfico de Diretório Ativo do Azure](../../../active-directory/develop/active-directory-graph-api.md) e o serviço de ponto final [RESTPrincipais](/graph/api/resources/serviceprincipal) para obter o principal de serviço. Em seguida, conceda aos Projetos do Azure a função _proprietário_ através do [Portal,](../../../role-based-access-control/role-assignments-portal.md) [Azure CLI,](../../../role-based-access-control/role-assignments-cli.md) [Azure PowerShell,](../../../role-based-access-control/role-assignments-powershell.md) [REST API](../../../role-based-access-control/role-assignments-rest.md)ou um [modelo de Gerenciador de Recursos.](../../../role-based-access-control/role-assignments-template.md)

O serviço Azure Blueprints não implanta diretamente os recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>O objeto de atribuição do projeto é criado

Um usuário, grupo ou diretor de serviço atribui um projeto a uma assinatura. O objeto de atribuição existe no nível de assinatura onde o projeto foi atribuído. Os recursos criados pela implantação não são feitos no contexto da entidade de implantação.

Ao criar a atribuição do projeto, o tipo de [identidade gerenciada](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionado. O padrão é uma identidade **gerenciada atribuída ao sistema.** Uma identidade **gerenciada atribuída pelo usuário** pode ser escolhida. Ao usar uma identidade **gerenciada atribuída pelo usuário,** ela deve ser definida e concedida permissões antes que a atribuição do projeto seja criada. Tanto as funções [incorporadas do](../../../role-based-access-control/built-in-roles.md#owner) Proprietário `blueprintAssignment/write` quanto do Operador de [Projeto](../../../role-based-access-control/built-in-roles.md#blueprint-operator) têm a permissão necessária para criar uma atribuição que use uma identidade **gerenciada atribuída pelo usuário.**

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Opcional - Azure Blueprints cria identidade gerenciada atribuída ao sistema

Quando a [identidade gerenciada atribuída ao sistema](../../../active-directory/managed-identities-azure-resources/overview.md) é selecionada durante a atribuição, o Azure Blueprints cria a identidade e concede a identidade gerenciada à função de [proprietário.](../../../role-based-access-control/built-in-roles.md#owner) Se uma [atribuição existente for atualizada,](../how-to/update-existing-assignments.md)o Azure Blueprints usará a identidade gerenciada criada anteriormente.

A identidade gerenciada relacionada à atribuição de projeto é usada para implantar ou reimplantar os recursos definidos no projeto. Este projeto evita que atribuições interfiem inadvertidamente entre si.
Este design também suporta o recurso [de bloqueio de recursos](./resource-locking.md) controlando a segurança de cada recurso implantado a partir do projeto.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A identidade gerenciada implanta artefatos de projeto

A identidade gerenciada então aciona as implantações do Gerenciador de recursos dos artefatos dentro do projeto na [ordem de seqüenciamento](./sequencing-order.md)definida . A ordem pode ser ajustada para garantir que artefatos dependentes de outros artefatos sejam implantados na ordem correta.

Uma falha de acesso por uma implantação é muitas vezes o resultado do nível de acesso concedido à identidade gerenciada. O serviço Azure Blueprints gerencia o ciclo de vida de segurança da identidade **gerenciada atribuída ao sistema.** No entanto, o usuário é responsável pelo gerenciamento dos direitos e do ciclo de vida de uma identidade **gerenciada atribuída pelo usuário.**

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>O serviço de projeto e os direitos de identidade gerenciados atribuídos pelo sistema são revogados

Uma vez concluídas as implantações, o Azure Blueprints revoga os direitos da identidade **gerenciada atribuída pelo sistema** a partir da assinatura. Em seguida, o serviço Azure Blueprints revoga seus direitos da assinatura. A remoção de direitos impede que o Azure Blueprints se torne um proprietário permanente em uma assinatura.

## <a name="next-steps"></a>Próximas etapas

- Saiba como usar [parâmetros estáticos e dinâmicos](parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).
