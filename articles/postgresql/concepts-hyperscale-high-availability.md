---
title: Alta disponibilidade – Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Conceitos de alta disponibilidade e recuperação de desastres
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 10679ab02826fb606af65c72621f2afb609bc81b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975526"
---
# <a name="high-availability-in-azure-database-for-postgresql--hyperscale-citus"></a>Alta disponibilidade no Banco de Dados Azure para PostgreSQL – Hyperscale (Citus)

A alta disponibilidade (HA) evita o tempo de inatividade do banco de dados mantendo réplicas de espera de cada nó em um grupo de servidores. Se um nó cair, o Hyperscale muda as conexões recebidas do nó com falha para o seu standby. O failover acontece em poucos minutos, e os nódulos promovidos sempre têm dados novos através da replicação síncrona de streaming PostgreSQL.

Para aproveitar o HA no nó coordenador, os aplicativos de banco de dados precisam detectar e tentar novamente conexões descartadas e transações fracassadas. O coordenador recém-promovido estará acessível com a mesma seqüência de conexão.

A recuperação pode ser dividida em três estágios: detecção, failover e recuperação completa.  A hyperscale faz verificações periódicas de saúde em cada nó, e após quatro verificações fracassadas, determina que um nó está baixo. A hyperscale, então, promove uma espera para o status de nó primário (failover) e provê um novo standby-to-be.
A replicação por streaming começa, trazendo o novo nó atualizado.  Quando todos os dados foram replicados, o nó atingiu a recuperação total.

### <a name="next-steps"></a>Próximas etapas

- Saiba como [ativar a alta disponibilidade](howto-hyperscale-high-availability.md) em um grupo de servidores Hyperscale.
