---
title: Versões do livro-razão do Azure Blockchain Service, patches, & upgrade
description: Visão geral das versões de livros contábeis suportadas no Azure Blockchain Service, incluindo políticas relativas a patches de sistemas e atualizações gerenciadas pelo sistema e gerenciadas pelo usuário.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325181"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versões de livro razão do Azure Blockchain Service suportadas

O Azure Blockchain Service usa o livro-razão [Quorum](https://www.goquorum.com/developers) baseado em Ethereum projetado para o processamento de transações privadas dentro de um grupo de participantes conhecidos, identificados como um consórcio no Azure Blockchain Service.

Atualmente, o Azure Blockchain Service suporta [quorum versão 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) e [gerenciador de transações Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades

A versão em Quorum é feita através de um grande, menor e lançamento de patch. Por exemplo, se a versão quorum for 2.0.1, o tipo de versão será categorizado da seguinte forma:

|Principal | Secundária  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

O Azure Blockchain Service atualiza automaticamente as versões de patch do Quorum para os membros em execução existentes dentro de 30 dias após serem disponibilizados no Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilidade de novas versões contábeis

O Azure Blockchain Service fornece as versões principais e menores mais recentes do livro-razão Quorum dentro de 60 dias após estar disponível no fabricante do Quorum. Um máximo de quatro liberações menores são previstas para os consórcios escolherem ao fornecer um novo membro e consórcio. A atualização de uma versão maior ou menor não é suportada no momento. Por exemplo, se você estiver executando a versão 2.x, uma atualização para a versão 3.x não é suportada no momento. Da mesma forma, se você estiver executando a versão 2.2, uma atualização para a versão 2.3 não é suportada no momento.

## <a name="next-steps"></a>Próximas etapas

[Limites no Serviço blockchain do Azure](limits.md)
