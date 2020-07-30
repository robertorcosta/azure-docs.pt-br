---
title: Nós – Citus (hiperescala) – banco de dados do Azure para PostgreSQL
description: Saiba mais sobre os dois tipos de nós, coordenador e trabalhadores, em um grupo de servidores no banco de dados do Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: af743ca56572f507091db01f11d3283294a9e3d5
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87382790"
---
# <a name="nodes-in-azure-database-for-postgresql--hyperscale-citus"></a>Nós no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

O tipo de Hospedagem de hiperescala (Citus) permite que os servidores do banco de dados do Azure para PostgreSQL (chamados de nós) se coordenem entre si em uma arquitetura "nada compartilhado". Os nós em um grupo de servidores contêm, coletivamente, mais dados e usam mais núcleos de CPU do que seria possível em um único servidor. A arquitetura também permite que o banco de dados seja dimensionado adicionando mais nós ao grupo de servidores.

## <a name="coordinator-and-workers"></a>Coordenador e trabalhadores

Cada grupo de servidores tem um nó de coordenador e vários trabalhadores. Os aplicativos enviam suas consultas para o nó de coordenador, que os retransmite para os trabalhadores relevantes e acumula seus resultados. Os aplicativos não podem se conectar diretamente aos trabalhadores.

O Citus (hiperescala) permite que o administrador de banco de dados *distribua* tabelas, armazenando linhas diferentes em nós de trabalho diferentes. As tabelas distribuídas são a chave para o desempenho de hiperescala. A incapacidade de distribuir tabelas as deixa totalmente no nó de coordenador e não pode tirar proveito do paralelismo entre máquinas.

Para cada consulta em tabelas distribuídas, o coordenador a roteia para um único nó de trabalho ou a paralelize em vários dependendo se os dados necessários residem em um único nó ou vários. O coordenador decide o que fazer por meio das tabelas de metadados de consultoria. Essas tabelas acompanham os nomes DNS e a integridade dos nós de trabalho e a distribuição de dados entre nós.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [tabelas distribuídas](concepts-hyperscale-distributed-data.md)
