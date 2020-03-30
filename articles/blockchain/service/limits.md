---
title: Limites do Azure Blockchain Service
description: Visão geral do serviço e dos limites funcionais no Azure Blockchain Service
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455650"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites no Serviço blockchain do Azure

O Azure Blockchain Service tem limites de serviço e funcionais, como o número de nódulos que um membro pode ter, restrições de consórcio e valores de armazenamento.

## <a name="pricing-tier"></a>Tipo de preço

Os limites máximos de transações e nós validadores dependem se você prover o Azure Blockchain Service em níveis de preços básicos ou padrão.

| Tipo de preço | Nó de transação máximo | Nó seletor máximo |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

A alteração do nível de preços entre Básico e Padrão após a criação do membro não é suportada.

## <a name="storage-capacity"></a>Capacidade de armazenamento

A quantidade máxima de armazenamento que pode ser usada por nó para dados contábeis e logs é de 1,8 terabytes.

A diminuição do tamanho do armazenamento de livros e troncos não é suportada.

## <a name="consortium-limits"></a>Limites do consórcio

* **Os nomes de consórcios e membros devem ser exclusivos** de outros nomes de consórcios e membros do Azure Blockchain Service.

* **Os nomes de membros e consórcios não podem ser alterados**

* **Todos os membros de um consórcio devem estar no mesmo nível de preços**

* **Todos os membros que participam de um consórcio devem residir na mesma região**

    O primeiro membro criado em um consórcio dita a região. Os membros convidados do consórcio devem residir na mesma região que o primeiro membro. Limitar todos os membros à mesma região ajuda a garantir que o consenso da rede não seja impactado negativamente.

* **Um consórcio deve ter pelo menos um administrador**

    Se houver apenas um administrador em um consórcio, eles não podem se retirar do consórcio ou excluir seu membro até que outro administrador seja adicionado ou promovido no consórcio.

* **Os membros removidos do consórcio não podem ser adicionados novamente**

    Em vez disso, eles devem ser reconvidados para se juntar ao consórcio e criar um novo membro. Seu recurso de membro existente não é excluído para preservar transações históricas.

* **Todos os membros de um consórcio devem estar usando a mesma versão contábil**

    Para obter mais informações sobre as versões de patches, atualizações e livros contábeis disponíveis no Azure Blockchain Service, consulte [Patches, atualizações e versões](ledger-versions.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre políticas de patches e upgrades de sistemas - [Patches, atualizações e versões](ledger-versions.md).
