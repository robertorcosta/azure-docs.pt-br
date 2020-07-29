---
title: Alta disponibilidade – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Conceitos de alta disponibilidade e recuperação de desastre
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74975526"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Alta disponibilidade no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

A HA (alta disponibilidade) evita o tempo de inatividade do banco de dados mantendo réplicas em espera de cada nó em um grupo de servidores. Se um nó ficar inativo, o hiperescala alternará as conexões de entrada do nó com falha para o seu estado de espera. O failover ocorre em alguns minutos e os nós promovidos sempre têm dados atualizados por meio da replicação de streaming síncrona do PostgreSQL.

Para aproveitar a HA no nó de coordenador, os aplicativos de banco de dados precisam detectar e repetir conexões descartadas e transações com falha. O coordenador recém-promovido será acessível com a mesma cadeia de conexão.

A recuperação pode ser dividida em três estágios: detecção, failover e recuperação completa.  O hiperscale executa verificações de integridade periódicas em cada nó e, após quatro verificações com falha, ele determina que um nó está inoperante. O hiperescala promove uma espera para o status do nó primário (failover) e provisiona uma nova em espera.
A replicação de streaming começa, colocando o novo nó atualizado.  Quando todos os dados tiverem sido replicados, o nó terá atingido a recuperação completa.

### <a name="next-steps"></a>Próximas etapas

- Saiba como [habilitar a alta disponibilidade](howto-hyperscale-high-availability.md) em um grupo de servidores de hiperescala.
