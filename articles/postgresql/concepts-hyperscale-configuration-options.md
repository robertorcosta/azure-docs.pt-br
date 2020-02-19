---
title: Opções de desempenho – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Opções para um grupo de servidores de hiperescala (Citus), incluindo computação de nó, armazenamento e regiões.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462404"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Banco de dados do Azure para PostgreSQL – opções de desempenho de Citus (hiperescala)

## <a name="compute-and-storage"></a>Computação e armazenamento
 
Você pode selecionar as configurações de computação e armazenamento independentemente para nós de trabalho e o nó de coordenador em um grupo de servidores de hiperescala (Citus).  Os recursos de computação são fornecidos como vCores, que representam a CPU lógica do hardware subjacente. O tamanho do armazenamento para provisionamento refere-se à capacidade disponível para os nós de coordenador e de trabalho em seu grupo de servidores de hiperescala (Citus). O armazenamento inclui arquivos de banco de dados, arquivos temporários, logs de transações e logs do servidor Postgres. A quantidade total de armazenamento que você provisiona também define a capacidade de e/s disponível para cada nó de trabalho e coordenador.
 
|                       | Nó de trabalho           | Nó de coordenador      |
|-----------------------|-----------------------|-----------------------|
| Computação, vCores       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória por vCore, GiB | 8                     | 4                     |
| Tamanho do armazenamento, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Tipo de armazenamento          | Uso geral (SSD) | Uso geral (SSD) |
| IOPS                  | Até 3 IOPS/GiB      | Até 3 IOPS/GiB      |


## <a name="regions"></a>Regiões
Os grupos de servidores de hiperescala (Citus) estão disponíveis nas seguintes regiões do Azure:

* Américas:
    * Canadá Central
    * Centro dos EUA
    * Leste dos EUA
    * Leste dos EUA 2
    * Centro-Norte dos EUA
    * Oeste dos EUA 2
* Pacífico Asiático:
    * Leste da Austrália
    * Leste do Japão
    * Coreia Central
    * Sudeste Asiático
* Européia
    * Norte da Europa
    * Sul do Reino Unido
    * Europa Ocidental

Algumas dessas regiões podem não ser ativadas inicialmente em todas as assinaturas do Azure. Se você quiser usar uma região da lista acima e não a vir na sua assinatura, ou se quiser usar uma região que não esteja nessa lista, abra uma [solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Preços
Para as informações mais recentes sobre preços, consulte a [página de preços](https://azure.microsoft.com/pricing/details/postgresql/) do serviço.
Para ver o custo da configuração desejada, o [portal do Azure](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) mostra o custo mensal na guia **Configurar** com base nas opções selecionadas. Se você não tiver uma assinatura do Azure, poderá usar a calculadora de preços do Azure para obter um preço estimado. No site da [calculadora de preços do Azure](https://azure.microsoft.com/pricing/calculator/) , selecione **Adicionar itens**, expanda a categoria **bancos** de dados e escolha **banco de dados do Azure para PostgreSQL – Citus (hiperescala)** para personalizar as opções.
 
## <a name="next-steps"></a>Próximas etapas
Saiba como [criar um grupo de servidores do Citus (hiperescala) no portal](quickstart-create-hyperscale-portal.md).
