---
title: Colocalização de mesa - Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Como armazenar informações relacionadas em conjunto para consultas mais rápidas
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967330"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Colocalização de tabela no Banco de Dados Azure para PostgreSQL – Hyperscale (Citus)

Colocation significa armazenar informações relacionadas nos mesmos nódulos. As consultas podem ser rápidas quando todos os dados necessários estão disponíveis sem qualquer tráfego de rede. A colocação de dados relacionados em diferentes linfonodos permite que as consultas sejam executadas de forma eficiente em paralelo em cada nó.

## <a name="data-colocation-for-hash-distributed-tables"></a>Colocalização de dados para tabelas distribuídas por hash

No Banco de Dados Azure para PostgreSQL – Hyperscale (Citus), uma linha é armazenada em um fragmento se o hash do valor na coluna de distribuição estiver dentro do intervalo de hash do fragmento. Fragmentos com o mesmo alcance de hash são sempre colocados no mesmo nó. Linhas com valores iguais de coluna de distribuição estão sempre no mesmo nó entre as mesas.

![Fragmentos](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Um exemplo prático de colocation

Considere as tabelas a seguir que podem fazer parte de uma análise web de vários inquilinos SaaS:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Agora queremos responder a consultas que podem ser emitidas por um painel voltado para o cliente. Uma pergunta de exemplo é "Retornar o número de visitas na semana passada para todas as páginas começando com '/blog' no inquilino seis."

Se nossos dados estavam na opção de implantação do Single-Server, poderíamos facilmente expressar nossa consulta usando o rico conjunto de operações relacionais oferecidas pelo SQL:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Enquanto o [conjunto de trabalho](https://en.wikipedia.org/wiki/Working_set) para essa consulta se encaixar na memória, uma tabela de um servidor é uma solução apropriada. Vamos considerar as oportunidades de dimensionar o modelo de dados com a opção de implantação Hyperscale (Citus).

### <a name="distribute-tables-by-id"></a>Distribuir tabelas por ID

As consultas de servidor único começam a diminuir à medida que o número de inquilinos e os dados armazenados para cada inquilino cresce. O conjunto de trabalho pára de encaixar na memória e a CPU se torna um gargalo.

Neste caso, podemos fragmentos de dados em muitos nós usando Hyperscale (Citus). A primeira e mais importante escolha que precisamos fazer quando decidirmos fragmentos é a coluna de distribuição. Vamos começar com uma escolha ingênua de `event_id` usar `page_id` para `page` a mesa de eventos e para a mesa:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Quando os dados são dispersos em diferentes trabalhadores, não podemos realizar uma adesão como faríamos em um único nó PostgreSQL. Em vez disso, precisamos emitir duas consultas:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Posteriormente, os resultados das duas etapas precisam ser combinados pelo aplicativo.

A execução das consultas deve consultar os dados em fragmentos espalhados por nós.

![Consultas ineficientes](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

Neste caso, a distribuição de dados cria desvantagens substanciais:

-   Sobrecarga de consulta de cada fragmento e execução de várias consultas.
-   Sobrecarga do Q1 retornando muitas linhas para o cliente.
-   O q2 torna-se grande.
-   A necessidade de escrever consultas em várias etapas requer alterações no aplicativo.

Os dados são dispersos, para que as consultas possam ser paraleleterizadas. Só é benéfico se a quantidade de trabalho que a consulta faz for substancialmente maior do que a sobrecarga de consultar muitos fragmentos.

### <a name="distribute-tables-by-tenant"></a>Distribuir mesas por inquilino

Em Hyperscale (Citus), as linhas com o mesmo valor da coluna de distribuição são garantidas no mesmo nó. Começando de mais, podemos `tenant_id` criar nossas tabelas com a coluna de distribuição.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Agora, o Hyperscale (Citus) pode responder à consulta original de servidor único sem modificação (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Por causa do filtro e da adesão ao tenant_id, a Hyperscale (Citus) sabe que toda a consulta pode ser respondida usando o conjunto de fragmentos colocados que contêm os dados para esse inquilino em particular. Um único nó PostgreSQL pode responder à consulta em um único passo.

![Melhor consulta](media/concepts-hyperscale-colocation/colocation-better-query.png)

Em alguns casos, consultas e esquemas de tabela devem ser alterados para incluir o ID do inquilino em restrições únicas e juntar condições. Essa mudança geralmente é simples.

## <a name="next-steps"></a>Próximas etapas

- Veja como os dados do inquilino são colocados no [tutorial de vários inquilinos](tutorial-design-database-hyperscale-multi-tenant.md).
