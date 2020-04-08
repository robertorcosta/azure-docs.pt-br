---
title: Opções de configuração - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Opções para um grupo de servidores Hyperscale (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804581"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Banco de dados Azure para opções de configuração PostgreSQL – Hyperscale (Citus)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Você pode selecionar as configurações de computação e armazenamento independentemente para nós de trabalhador e o nó coordenador em um grupo de servidor Hyperscale (Citus).  Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. O tamanho de armazenamento para provisionamento refere-se à capacidade disponível para o coordenador e os nós do trabalhador no grupo de servidores Hyperscale (Citus). O armazenamento inclui arquivos de banco de dados, arquivos temporários, logs de transações e registros do servidor Postgres.
 
|                       | Nó de trabalho           | Nó de coordenador      |
|-----------------------|-----------------------|-----------------------|
| Computação, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho de armazenamento, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tipo de armazenamento          | Propósito geral (SSD) | Propósito geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |

A quantidade total de RAM em um único nó Hyperscale (Citus) é baseada no número selecionado de vCores.

| vCores | Um nó de trabalhador, GiB RAM | Nó coordenador, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

O montante total de armazenamento que você provisão também define a capacidade de I/O disponível para cada nó de trabalho e coordenador.

| Tamanho de armazenamento, TiB | IOPS máximo |
|-------------------|--------------|
| 0.5               | 1.536        |
| 1                 | 3.072        |
| 2                 | 6,148        |

Para todo o cluster Hyperscale (Citus), o IOPS agregado funciona para os seguintes valores:

| Nós de trabalho | 0.5 TiB, IOPS total | 1 TiB, IOPS total | 2 TiB, IOPS total |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3.072               | 6.144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6.144               | 12,288            | 24,592            |
| 5            | 7,680               | 15.360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15.360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

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
