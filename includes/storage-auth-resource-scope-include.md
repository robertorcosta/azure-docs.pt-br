---
title: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 644d58c3d1c60611b0d22d2757da089313fa12b6
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87423688"
---
Antes de atribuir uma função RBAC a uma entidade de segurança, determine o escopo do acesso que essa entidade de segurança deve ter. De acordo com as melhores práticas, sempre é melhor conceder o escopo mais estreito possível.

A seguinte lista descreve os níveis de escopo de acesso que você pode definir para os recursos de blobs e de filas do Azure, começando pelo escopo mais estreito:

- **Um contêiner individual.** Nesse escopo, uma atribuição de função se aplica a todos os blobs no contêiner, bem como às propriedades e aos metadados do contêiner.
- **Uma fila individual.** Nesse escopo, uma atribuição de função se aplica às mensagens na fila, bem como às propriedades e aos metadados da fila.
- **A conta de armazenamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres e seus blobs ou a todas as filas e suas mensagens.
- **O grupo de recursos.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento do grupo de recursos.
- **A assinatura.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento de todos os grupos de recursos da assinatura.
- **Um grupo de gerenciamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento de todos os grupos de recursos em todas as assinaturas do grupo de gerenciamento.

Para saber mais sobre as atribuições de função do Azure e seu escopo, confira [O que é o RBAC (controle de acesso baseado em função) do Azure?](../articles/role-based-access-control/overview.md).
