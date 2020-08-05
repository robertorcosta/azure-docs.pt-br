---
title: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/16/2020
ms.author: tamram
ms.openlocfilehash: 96d8100f2cffcfb001a693575128ce19e742225d
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87534118"
---
Antes de atribuir uma função do Azure a uma entidade de segurança, determine o escopo do acesso que essa entidade de segurança deve ter. De acordo com as melhores práticas, sempre é melhor conceder o escopo mais estreito possível.

A seguinte lista descreve os níveis de escopo de acesso que você pode definir para os recursos de blobs e de filas do Azure, começando pelo escopo mais estreito:

- **Um contêiner individual.** Nesse escopo, uma atribuição de função se aplica a todos os blobs no contêiner, bem como às propriedades e aos metadados do contêiner.
- **Uma fila individual.** Nesse escopo, uma atribuição de função se aplica às mensagens na fila, bem como às propriedades e aos metadados da fila.
- **A conta de armazenamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres e seus blobs ou a todas as filas e suas mensagens.
- **O grupo de recursos.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento do grupo de recursos.
- **A assinatura.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento de todos os grupos de recursos da assinatura.
- **Um grupo de gerenciamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento de todos os grupos de recursos em todas as assinaturas do grupo de gerenciamento.

Para saber mais sobre as atribuições de função do Azure e seu escopo, confira [O que é o RBAC (controle de acesso baseado em função) do Azure?](../articles/role-based-access-control/overview.md).
