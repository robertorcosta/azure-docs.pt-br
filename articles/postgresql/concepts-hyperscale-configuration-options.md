---
title: Opções de desempenho – Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Opções para um grupo de servidores Hyperscale (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462404"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Banco de dados Azure para opções de desempenho PostgreSQL – Hyperscale (Citus)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Você pode selecionar as configurações de computação e armazenamento independentemente para nós de trabalhador e o nó coordenador em um grupo de servidor Hyperscale (Citus).  Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. O tamanho de armazenamento para provisionamento refere-se à capacidade disponível para o coordenador e os nós do trabalhador no grupo de servidores Hyperscale (Citus). O armazenamento inclui arquivos de banco de dados, arquivos temporários, logs de transações e registros do servidor Postgres. O montante total de armazenamento que você provisão também define a capacidade de I/O disponível para cada nó de trabalho e coordenador.
 
|                       | Nó de trabalho           | Nó de coordenador      |
|-----------------------|-----------------------|-----------------------|
| Computação, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho de armazenamento, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo de armazenamento          | Propósito geral (SSD) | Propósito geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |


## <a name="regions"></a>Regiões
Grupos de servidores hyperscale (Citus) estão disponíveis nas seguintes regiões do Azure:

* Américas:
    * Canadá Central
    * Centro dos EUA
    * Leste dos EUA
    * Leste dos EUA 2
    * Centro-Norte dos EUA
    * Oeste dos EUA 2
* Ásia-Pacífico:
    * Leste da Austrália
    * Leste do Japão
    * Coreia Central
    * Sudeste Asiático
* Europa:
    * Norte da Europa
    * Sul do Reino Unido
    * Europa Ocidental

Algumas dessas regiões podem não ser inicialmente ativadas em todas as assinaturas do Azure. Se você quiser usar uma região da lista acima e não vê-la em sua assinatura, ou se você quiser usar uma região que não esteja nesta lista, abra uma solicitação de [suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Preços
Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) do serviço.
Para ver o custo da configuração desejada, o [portal Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia **Configurar** com base nas opções selecionadas. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [calculadora de preços do Azure,](https://azure.microsoft.com/pricing/calculator/) selecione Adicionar **itens,** expandir a categoria **Bancos de Dados** e escolher O Banco de Dados **Azure para PostgreSQL – Hyperscale (Citus)** para personalizar as opções.
 
## <a name="next-steps"></a>Próximas etapas
Aprenda a [criar um grupo de servidor Hyperscale (Citus) no portal](quickstart-create-hyperscale-portal.md).
