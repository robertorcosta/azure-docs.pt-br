---
title: Dados distribuídos - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Saiba mais sobre tabelas distribuídas, tabelas de referência, tabelas locais e fragmentos no Banco de Dados Azure para PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243647"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Dados distribuídos no Banco de Dados Azure para PostgreSQL – Hyperscale (Citus)

Este artigo descreve os três tipos de tabela no Banco de Dados Azure para PostgreSQL – Hyperscale (Citus).
Ele mostra como as tabelas distribuídas são armazenadas como fragmentos, e a maneira como os fragmentos são colocados em nós.

## <a name="table-types"></a>Tipos de tabela

Existem três tipos de tabelas em um grupo de servidor Hyperscale (Citus), cada uma usada para diferentes propósitos.

### <a name="type-1-distributed-tables"></a>Tipo 1: Tabelas distribuídas

O primeiro tipo, e mais comum, são as tabelas distribuídas. Parecem ser tabelas normais para instruções SQL, mas são horizontalmente particionadas entre os nódulos do trabalhador. O que isso significa é que as linhas da tabela são armazenadas em diferentes nós, em tabelas fragmentadas chamadas fragmentos.

Hyperscale (Citus) executa não apenas instruções SQL, mas DDL em um cluster.
Alterando o esquema de uma tabela distribuída em cascatas para atualizar todos os fragmentos da tabela entre os trabalhadores.

#### <a name="distribution-column"></a>Coluna de distribuição

Hyperscale (Citus) usa fragmento sumirítmico para atribuir linhas a fragmentos. A atribuição é feita determinicamente com base no valor de uma coluna de tabela chamada coluna de distribuição. O administrador de cluster deve designar esta coluna ao distribuir uma tabela.
Fazer a escolha certa é importante para o desempenho e funcionalidade.

### <a name="type-2-reference-tables"></a>Tipo 2: Tabelas de referência

Uma tabela de referência é um tipo de tabela distribuída cujo conteúdo inteiro está concentrado em um único fragmento. O fragmento é replicado em todos os trabalhadores. Consultas em qualquer trabalhador podem acessar as informações de referência localmente, sem a sobrecarga da rede de solicitações de linhas de outro nó. As tabelas de referência não têm coluna de distribuição porque não há necessidade de distinguir fragmentos separados por linha.

As tabelas de referência são tipicamente pequenas e são usadas para armazenar dados relevantes para consultas em execução em qualquer nó do trabalhador. Um exemplo são valores enumerados, como status do pedido ou categorias de produtos.

### <a name="type-3-local-tables"></a>Tipo 3: Tabelas locais

Quando você usa Hyperscale (Citus), o nó coordenador ao que você se conecta é um banco de dados PostgreSQL regular. Você pode criar tabelas comuns no coordenador e optar por não usá-las.

Um bom candidato para mesas locais seriam pequenas mesas administrativas que não participam de consultas de adesão. Um exemplo é uma tabela de usuários para login e autenticação de aplicativos.

## <a name="shards"></a>Fragmentos

A seção anterior descreveu como as tabelas distribuídas são armazenadas como fragmentos nos nós do trabalhador. Esta seção discute mais detalhes técnicos.

A `pg_dist_shard` tabela de metadados no coordenador contém uma linha para cada fragmento de cada tabela distribuída no sistema. A linha combina com um ID de fragmento com uma gama de inteiros em um espaço hash (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Se o nó coordenador quiser determinar qual fragmento `github_events`contém uma linha de , ele hashes o valor da coluna de distribuição na linha. Em seguida, o nó\'verifica qual faixa de fragmento s contém o valor hashed. As faixas são definidas de modo que a imagem da função hash é sua união desarticulada.

### <a name="shard-placements"></a>Colocações de fragmentos

Suponha que o fragmento 102027 esteja associado à linha em questão. A linha é lida ou `github_events_102027` escrita em uma tabela chamada em um dos trabalhadores. Qual trabalhador? Isso é determinado inteiramente pelas tabelas de metadados. O mapeamento do fragmento para o trabalhador é conhecido como a colocação de fragmentos.

O nó coordenador reescreve consultas em fragmentos que `github_events_102027` se referem às tabelas específicas como e executa esses fragmentos nos trabalhadores apropriados. Aqui está um exemplo de uma consulta nos bastidores para encontrar o nó segurando o fragmento iD 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Próximas etapas
- Saiba como [escolher uma coluna de distribuição](concepts-hyperscale-choose-distribution-column.md) para tabelas distribuídas.
