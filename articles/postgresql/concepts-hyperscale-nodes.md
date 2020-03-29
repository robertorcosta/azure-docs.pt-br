---
title: Nós - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Conheça os dois tipos de nódulos, coordenador e trabalhadores, em um grupo de servidores no Banco de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 04ebb4298f8a5398b0aa9921d740e3eaacfd8e11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973995"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nós no Banco de Dados Azure para PostgreSQL – Hyperscale (Citus)

O tipo de hospedagem Hyperscale (Citus) permite que o Banco de Dados Azure para servidores PostgreSQL (chamados nós) coordene entre si em uma arquitetura "compartilhada nada". Os nós em um grupo de servidor contêm coletivamente mais dados e usam mais núcleos de CPU do que seria possível em um único servidor. A arquitetura também permite que o banco de dados seja dimensionado adicionando mais nós ao grupo de servidores.

## <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidores tem um nó de coordenador e vários trabalhadores. Os aplicativos enviam suas consultas para o nó coordenador, que retransmite para os trabalhadores relevantes e acumula seus resultados. Os aplicativos não podem se conectar diretamente aos trabalhadores.

Para cada consulta, o coordenador ou o encaminha para um único nó de trabalhador, ou paraparapara qualquer um, dependendo se os dados necessários vivem em um único nó ou múltiplo. O coordenador decide o que fazer consultando tabelas de metadados. Essas tabelas acompanham os nomes do DNS e a saúde dos nódulos do trabalhador e a distribuição de dados entre os latrocinos.

## <a name="next-steps"></a>Próximas etapas
- Saiba como os nodes [armazenam dados distribuídos](concepts-hyperscale-distributed-data.md)
