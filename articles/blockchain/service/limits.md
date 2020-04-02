---
title: Limites do Azure Blockchain Service
description: Visão geral do serviço e dos limites funcionais no Azure Blockchain Service
ms.date: 03/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: c728e617ac37795988cd596c7cb0c5025aac4ccf
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529567"
---
# <a name="limits-in-azure-blockchain-service"></a>Limites no Serviço blockchain do Azure

O Azure Blockchain Service tem limites de serviço e funcionais, como o número de nódulos que um membro pode ter, restrições de consórcio e valores de armazenamento.

## <a name="pricing-tier"></a>Tipo de preço

Os limites máximos de transações e nós validadores dependem se você prover o Azure Blockchain Service em níveis básicos ou padrão de preços.

| Tipo de preço | Nó de transação máximo | Nó seletor máximo |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Sua rede de consórcio deve ter pelo menos dois nodes padrão do Azure Blockchain Service. Os nós de nível padrão incluem dois nós de validação. Quatro nós validadores são necessários para atender ao consenso de [Tolerância a Falhas Bizantinas de Istambul.](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus)

Use o nível básico é para desenvolvimento, teste e prova de conceitos. Use o nível padrão para implantações de grau de produção. Você também deve usar o nível *Standard* se estiver usando o Blockchain Data Manager ou enviando um alto volume de transações privadas.

A alteração do nível de preços entre o básico e o padrão após a criação do membro não é suportada.

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

    Em vez disso, eles devem ser reconvidados para se juntar ao consórcio e criar um novo membro. Seus recursos de membro existentes não são excluídos para preservar transações históricas.

* **Todos os membros de um consórcio devem estar usando a mesma versão contábil**

    Para obter mais informações sobre as versões de patches, atualizações e livros contábeis disponíveis no Azure Blockchain Service, consulte [Patches, atualizações e versões](ledger-versions.md).

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre políticas de patches e upgrades de sistemas - [Patches, atualizações e versões](ledger-versions.md).
