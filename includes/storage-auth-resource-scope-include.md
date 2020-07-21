---
title: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: c222869df561a9a36ebd69eb9ae09fa688ba0086
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86519193"
---
Antes de atribuir uma função de RBAC a uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso a recursos de BLOB e fila do Azure, começando com o escopo mais estreito:

- **Um contêiner individual.** Nesse escopo, uma atribuição de função se aplica a todos os BLOBs no contêiner, bem como propriedades e metadados do contêiner.
- **Uma fila individual.** Nesse escopo, uma atribuição de função se aplica a mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres e seus BLOBs, ou a todas as filas e suas mensagens.
- **O grupo de recursos.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento no grupo de recursos.
- **A assinatura.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos na assinatura.
- **Um grupo de gerenciamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos em todas as assinaturas no grupo de gerenciamento.

Para obter mais informações sobre atribuições de função RBAC e escopo, consulte [o que é o Azure RBAC (controle de acesso baseado em função)?](../articles/role-based-access-control/overview.md).
