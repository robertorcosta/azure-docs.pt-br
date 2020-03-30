---
title: incluir arquivo
description: incluir arquivo
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/17/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 74ef8270b5efcd3b7cdf756c103dcc2e1c935508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "75460512"
---
Antes de atribuir uma função RBAC a um diretor de segurança, determine o escopo de acesso que o diretor de segurança deve ter. As melhores práticas ditam que é sempre melhor conceder apenas o escopo mais estreito possível.

A lista a seguir descreve os níveis nos quais você pode escopo de acesso aos recursos de blob e fila do Azure, começando pelo escopo mais estreito:

- **Um recipiente individual.** Neste escopo, uma atribuição de função se aplica a todas as bolhas no contêiner, bem como propriedades do contêiner e metadados.
- **Uma fila individual.** Neste escopo, uma atribuição de função se aplica às mensagens na fila, bem como propriedades de fila e metadados.
- **A conta de armazenamento.** Neste escopo, uma atribuição de função se aplica a todos os contêineres e suas bolhas, ou a todas as filas e suas mensagens.
- **O grupo de recursos.** Neste escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento no grupo de recursos.
- **A assinatura.** Neste escopo, uma atribuição de função se aplica a todos os contêineres ou filas em todas as contas de armazenamento em todos os grupos de recursos da assinatura.

> [!IMPORTANT]
> Se sua assinatura incluir um namespace do Azure DataBricks, as funções que estão escopo da assinatura não concederão acesso a dados de blob e fila. Funções de escopo para o grupo de recursos, conta de armazenamento ou contêiner ou fila.     
