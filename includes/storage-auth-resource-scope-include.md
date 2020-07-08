---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8b97a62626666fa39a5b0622852d9eec47c2410a
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024870"
---
Antes de atribuir uma função de RBAC a uma entidade de segurança, determine o escopo de acesso que a entidade de segurança deve ter. As práticas recomendadas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode fazer o escopo de acesso a recursos de BLOB e fila do Azure, começando com o escopo mais estreito:

- **Um contêiner individual.** Nesse escopo, uma atribuição de função se aplica a todos os BLOBs no contêiner, bem como propriedades e metadados do contêiner.
- **Uma fila individual.** Nesse escopo, uma atribuição de função se aplica a mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres e seus BLOBs, ou a todas as filas e suas mensagens.
- **O grupo de recursos.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento no grupo de recursos.
- **A assinatura.** Nesse escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos na assinatura.
