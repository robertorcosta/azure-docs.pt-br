---
title: Dimensionar a taxa de transferência no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB dimensiona o throughput em diferentes regiões onde a conta do Azure Cosmos é provisionada.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873498"
---
# <a name="globally-scale-provisioned-throughput"></a>Taxa de transferência provisionada para dimensionamento global 

No Azure Cosmos DB, o throughput provisionado é representado como unidades de solicitação/segundo (RU/s ou RUs de forma plural). As RUs medem o custo das operações de leitura e de gravação em seu contêiner do Cosmos como mostrado nesta imagem:

![Unidades de solicitação](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

Você pode provisionar RUs em um contêiner ou em um banco de dados do Cosmos. As RUs provisionadas em um contêiner estão exclusivamente disponíveis para as operações realizadas naquele contêiner. RUs provisionadas em um banco de dados são compartilhadas entre todos os contêineres do banco de dados (exceto por contêineres com RUs atribuídas de maneira exclusiva).

Para o throughput provisionado de dimensionamento elástico, você pode aumentar ou diminuir o RU/s provisionado a qualquer momento. Para obter mais informações, consulte [como provisionar o throughput](set-throughput.md) e dimensione elásticamente os contêineres e bancos de dados cosmos. Para o throughput de dimensionamento global, você pode adicionar ou remover regiões da sua conta Cosmos a qualquer momento. Para obter mais informações, consulte [Adicionar ou remover regiões de sua conta de banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account). Associar várias regiões a uma conta cosmos é importante em muitos cenários - para alcançar baixa latência e [alta disponibilidade](high-availability.md) em todo o mundo.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Como a taxa de transferência provisionada é distribuída entre regiões

Se você disponibilizar *RUs 'R'* em um contêiner (ou banco de dados cosmos), o Cosmos DB garante que *'R'* RUs estão disponíveis em *cada* região associada à sua conta Cosmos. Cada vez que você adiciona uma nova região à sua conta, o Cosmos DB disponibiliza automaticamente *RUs 'R'* na região recém-adicionada. As operações realizadas contra o contêiner Cosmos são garantidas para obter *RUs 'R'* em cada região. Você não pode atribuir seletivamente RUs a uma região específica. As RUs provisionadas em um contêiner Cosmos (ou banco de dados) são provisionadas em todas as regiões associadas à sua conta Cosmos.

Supondo que um contêiner Cosmos esteja configurado com *RUs 'R'* e existam regiões *'N'* associadas à conta Cosmos, então:

- Se a conta Cosmos estiver configurada com uma única região de gravação, as RUs totais disponíveis globalmente no contêiner = *R* x *N*.

- Se a conta Cosmos estiver configurada com várias regiões de gravação, as RUs totais disponíveis globalmente no contêiner = *R* x (*N*+1). As *RR* adicionais são automaticamente provisionadas para processar conflitos de atualização e tráfego anti-entropia em todas as regiões.

Sua escolha de modelo de [consistência](consistency-levels.md) também afeta o throughput. Você pode obter aproximadamente 2x de throughput de leitura para os níveis de consistência mais relaxados (por exemplo, *sessão,* *prefixo consistente* e *consistência eventual)* em comparação com níveis de consistência mais fortes (por exemplo, *obsoleto ou* consistência *forte).*

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender como configurar o throughput em um contêiner ou banco de dados:

* [Obter e definir a taxa de transferência para contêineres e bancos de dados](set-throughput.md) 

