---
title: Extensões - Banco de Dados Azure para PostgreSQL - Servidor Único
description: Conheça as extensões postgres disponíveis no Banco de Dados Azure para PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201264"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Extensões PostgreSQL no Banco de Dados Azure para PostgreSQL - Servidor Único
O PostgreSQL fornece a capacidade de estender a funcionalidade de seu banco de dados usando as extensões. As extensões agrupam vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido do seu banco de dados com um único comando. Depois de serem carregados no banco de dados, as extensões funcionam como recursos incorporados.

## <a name="how-to-use-postgresql-extensions"></a>Como usar as extensões PostgreSQL
As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute o comando [CREATE EXTENSION](https://www.postgresql.org/docs/current/sql-createextension.html) da ferramenta psql para carregar os objetos embalados em seu banco de dados.

O Banco de Dados Azure para PostgreSQL suporta um subconjunto de extensões-chave listadas abaixo. Essas informações também estão disponíveis por meio da execução de `SELECT * FROM pg_available_extensions;`. Extensões além das listadas não são suportadas. Não é possível criar sua própria extensão no Banco de Dados Azure para PostgreSQL.

## <a name="postgres-11-extensions"></a>Postgres 11 extensões

As seguintes extensões estão disponíveis no Banco de Dados Azure para servidores PostgreSQL que têm postgres versão 11. 

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de dados do padronizador de endereços dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1,3             | suporte para indexação de tipos de dados comuns no GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | suporte para indexação de tipos de dados comuns no GiST|
> |[citext](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | tipo de dados para seqüências de caracteres insensíveis a casos|
> |[Cubo](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um banco de dados|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/11/earthdistance.html)                | 1,1             | calcular distâncias de grande círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1,1             | determinar semelhanças e distância entre as cordas|
> |[hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | funções, operadores e suporte a índices para matrizes 1-D de inteiros|
> |[isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | tipos de dados para padrões internacionais de numeração de produtos|
> |[ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1,1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1,3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1,3             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Extensão para gerenciar tabelas particionadas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | dados de relação pré-enxame|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | acompanhar as estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/JavaScript (v8) linguagem processual confiável|
> |[Postgis](https://www.postgis.net/)                      | 2.5.1           | Geometria postGIS, geografia e tipos e funções espaciais rasterizadas|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | Geocodificador de tigre postGIS e geocodificador reverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Tipos e funções espaciais de topologia pós-gis|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | Habilita inserções escaláveis e consultas complexas para dados de séries tempológicas|
> |[unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1,1             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1,1             | gerar identificadores universalmente únicos (UUIDs)|

## <a name="postgres-10-extensions"></a>Postgres 10 extensões 

As seguintes extensões estão disponíveis no Banco de Dados Azure para servidores PostgreSQL que têm postgres versão 10.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Exemplo de conjunto de dados do padronizador de endereços dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1,3             | suporte para indexação de tipos de dados comuns no GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | suporte para indexação de tipos de dados comuns no GiST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | tipo de dados para senhas criptografadas automáticas|
> |[citext](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | tipo de dados para seqüências de caracteres insensíveis a casos|
> |[Cubo](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um banco de dados|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/10/earthdistance.html)                | 1,1             | calcular distâncias de grande círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1,1             | determinar semelhanças e distância entre as cordas|
> |[hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | funções, operadores e suporte a índices para matrizes 1-D de inteiros|
> |[isn](https://www.postgresql.org/docs/10/isn.html)                          | 1,1             | tipos de dados para padrões internacionais de numeração de produtos|
> |[ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1,1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1,3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1,3             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerenciar tabelas particionadas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1,1             | dados de relação pré-enxame|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | acompanhar as estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1,3             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) linguagem processual confiável|
> |[Postgis](https://www.postgis.net/)                      | 2.4.3           | Geometria postGIS, geografia e tipos e funções espaciais rasterizadas|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | Geocodificador de tigre postGIS e geocodificador reverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | Tipos e funções espaciais de topologia pós-gis|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Habilita inserções escaláveis e consultas complexas para dados de séries tempológicas|
> |[unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1,1             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1,1             | gerar identificadores universalmente únicos (UUIDs)|

## <a name="postgres-96-extensions"></a>Extensões postgres 9.6 

As seguintes extensões estão disponíveis no Banco de Dados Azure para servidores PostgreSQL que têm a versão 9.6 do Postgres.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Exemplo de conjunto de dados do padronizador de endereços dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | suporte para indexação de tipos de dados comuns no GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | suporte para indexação de tipos de dados comuns no GiST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | tipo de dados para senhas criptografadas automáticas|
> |[citext](https://www.postgresql.org/docs/9.6/citext.html)                       | 1,3             | tipo de dados para seqüências de caracteres insensíveis a casos|
> |[Cubo](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | conectar-se a outros bancos de dados PostgreSQL de dentro de um banco de dados|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1,1             | calcular distâncias de grande círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1,1             | determinar semelhanças e distância entre as cordas|
> |[hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | funções, operadores e suporte a índices para matrizes 1-D de inteiros|
> |[isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1,1             | tipos de dados para padrões internacionais de numeração de produtos|
> |[ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1,1             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1,3             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerenciar tabelas particionadas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1,1             | dados de relação pré-enxame|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | acompanhar as estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1,3             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[plv8](https://plv8.github.io/)                         | 2.1.0          | PL/JavaScript (v8) linguagem processual confiável|
> |[Postgis](https://www.postgis.net/)                      | 2.3.2           | Geometria postGIS, geografia e tipos e funções espaciais rasterizadas|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | Geocodificador de tigre postGIS e geocodificador reverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | Tipos e funções espaciais de topologia pós-gis|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Habilita inserções escaláveis e consultas complexas para dados de séries tempológicas|
> |[unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1,1             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1,1             | gerar identificadores universalmente únicos (UUIDs)|

## <a name="postgres-95-extensions"></a>Extensões postgres 9.5 

As seguintes extensões estão disponíveis no Banco de Dados Azure para servidores PostgreSQL que têm postgres versão 9.5.

> [!div class="mx-tableFixed"]
> | **Extensão**| **Versão da extensão** | **Descrição** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Usado para analisar um endereço em elementos constituintes. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Exemplo de conjunto de dados do padronizador de endereços dos EUA|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | suporte para indexação de tipos de dados comuns no GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1,1             | suporte para indexação de tipos de dados comuns no GiST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | tipo de dados para senhas criptografadas automáticas|
> |[citext](https://www.postgresql.org/docs/9.5/citext.html)                       | 1,1             | tipo de dados para seqüências de caracteres insensíveis a casos|
> |[Cubo](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | tipo de dados para cubos multidimensionais|
> |[dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1,1             | conectar-se a outros bancos de dados PostgreSQL de dentro de um banco de dados|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | modelo de dicionário de pesquisa de texto para inteiros|
> |[earthdistance](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | calcular distâncias de grande círculo na superfície da Terra|
> |[fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | determinar semelhanças e distância entre as cordas|
> |[hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1,3             | tipo de dados para armazenar conjuntos de pares (chave, valor)|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Índices hipotéticos para PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | funções, operadores e suporte a índices para matrizes 1-D de inteiros|
> |[isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | tipos de dados para padrões internacionais de numeração de produtos|
> |[ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | tipo de dados para estruturas hierárquicas semelhantes a árvores|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Funções e operadores que emular um subconjunto de funções e pacotes de RDBMS comerciais|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | fornece funcionalidade de auditoria|
> |[pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | funções criptográficas|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | extensão pgRouting|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1,1             | mostrar informações de bloqueio em nível de linha|
> |[pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1,3             | mostrar estatísticas de nível tuple|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1,1             | examinar o cache de buffer compartilhado|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensão para gerenciar tabelas particionadas por tempo ou ID|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | dados de relação pré-enxame|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1,3             | acompanhar as estatísticas de execução de todas as declarações SQL executadas|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1,1             | medição de similaridade de texto e pesquisa de índice com base em trigramas|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | Linguagem processual PL/pgSQL|
> |[Postgis](https://www.postgis.net/)                      | 2.3.0           | Geometria postGIS, geografia e tipos e funções espaciais rasterizadas|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | Funções PostGIS SFCGAL|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | Geocodificador de tigre postGIS e geocodificador reverso|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | Tipos e funções espaciais de topologia pós-gis|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | invólucro de dados estrangeiros para servidores PostgreSQL remotos|
> |[tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | funções que manipulam tabelas inteiras, incluindo crosstab|
> |[unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | dicionário de pesquisa de texto que remove sotaques|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | gerar identificadores universalmente únicos (UUIDs)|


## <a name="pg_stat_statements"></a>pg_stat_statements
A [extensão pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregada em todos os bancos de dados Do Azure para servidor PostgreSQL para fornecer um meio de rastrear estatísticas de execução de declarações SQL.
A configuração `pg_stat_statements.track`, que controla quais instruções são contadas por extensão, tem `top` como padrão, que significa que todas as instruções emitidas diretamente por clientes são rastreadas. Dois outros níveis de rastreamento são `none` e `all`. Essa definição é configurável como um parâmetro de servidor por meio de [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou da [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há um equilíbrio entre as informações de execução de consulta fornecida por pg_stat_statements e o impacto no desempenho do servidor, que registra cada instrução SQL. Se você não está usando ativamente a extensão pg_stat_statements, recomendamos que você defina `pg_stat_statements.track` como `none`. Observe que alguns serviços de monitoramento de terceiros podem depender de pg_stat_statements para fornecer informações de desempenho de consulta, para confirmar se este é o caso para você ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
[dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) e [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) permitem que você se conecte de um servidor PostgreSQL a outro, ou a outro banco de dados no mesmo servidor. O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall. Ao usar essas extensões para conectar-se entre os servidores do Banco de Dados do Azure para PostgreSQL, isso pode ser feito definindo "Permitir acesso aos serviços do Azure" como LIGADO. Isso também será necessário se você quiser usar as extensões para executar um loop no mesmo servidor. A configuração "Permitir acesso aos serviços do Azure" pode ser encontrada na página do portal do Azure para o servidor Postgres em Segurança de Conexão. A virada "Permitir acesso aos serviços do Azure" ON coloca todos os IPs do Azure na lista de depermitir.

Atualmente, as conexões de saída do Banco de Dados Azure para PostgreSQL não são suportadas, exceto para conexões com outros bancos de dados Do Zure para servidores PostgreSQL.

## <a name="uuid"></a>uuid
Se você está `uuid_generate_v4()` planejando usar a partir da [extensão uuid-ossp,](https://www.postgresql.org/docs/current/uuid-ossp.html)considere comparar com `gen_random_uuid()` a [extensão pgcrypto](https://www.postgresql.org/docs/current/pgcrypto.html) para benefícios de desempenho.

## <a name="pgaudit"></a>pgAudit
A [extensão pgAudit](https://github.com/pgaudit/pgaudit/blob/master/README.md) fornece registro de auditoria de sessão e objeto. Para saber como usar essa extensão no Banco de Dados Azure para PostgreSQL, visite o [artigo de conceitos de auditoria](concepts-audit.md). 

## <a name="pg_prewarm"></a>pg_prewarm
A extensão pg_prewarm carrega dados relacionais em cache. Pré-aquecimento de seus caches significa que suas consultas têm melhores tempos de resposta em sua primeira execução após uma reinicialização. Em Postgres 10 e abaixo, o pré-aquecimento é feito manualmente usando a [função pré-enxame](https://www.postgresql.org/docs/10/pgprewarm.html).

No Postgres 11 ou superior, você pode configurar o pré-aquecimento para acontecer [automaticamente](https://www.postgresql.org/docs/current/pgprewarm.html). Você precisa incluir pg_prewarm `shared_preload_libraries` na lista do seu parâmetro e reiniciar o servidor para aplicar a alteração. Os parâmetros podem ser definidos a partir do [portal Azure,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)Rest API ou modelo ARM. 

## <a name="timescaledb"></a>TimescaleDB
TimescaleDB é um banco de dados de séries tempois que é embalado como uma extensão para PostgreSQL. O TimescaleDB fornece funções analíticas orientadas ao tempo, otimizações e escalas postgres para cargas de trabalho em séries tempo..

[Saiba mais sobre timescaleDB](https://docs.timescale.com/latest), uma marca registrada da [Timescale, Inc.](https://www.timescale.com/). O Banco de Dados Azure para PostgreSQL fornece a versão de código aberto do Timescale. Para saber quais recursos de escala de tempo estão disponíveis nesta versão, consulte [a comparação de produtos timescale](https://www.timescale.com/products/).

### <a name="installing-timescaledb"></a>Instalação de TimescaleDB
Para instalar o TimescaleDB, você precisa incluí-lo nas bibliotecas de pré-carga compartilhadas do servidor. Uma alteração no parâmetro `shared_preload_libraries` do Postgres requer uma **reinicialização do servidor** para fazer efeito. Você pode alterar parâmetros usando o [portal Azure](howto-configure-server-parameters-using-portal.md) ou o [Azure CLI](howto-configure-server-parameters-using-cli.md).

Usando o [portal Azure:](https://portal.azure.com/)

1. Selecione seu servidor de Banco de Dados do Azure para PostgreSQL.

2. Na barra lateral, selecione **Parâmetros do servidor**.

3. Pesquise o parâmetro `shared_preload_libraries`.

4. Selecione **TimescaleDB**.

5. Selecione **Salvar** para preservar suas alterações. Você recebe uma notificação assim que a alteração for salva. 

6. Após a notificação, **reinicie** o servidor para aplicar essas alterações. Para saber como reiniciar um servidor, confira [Reiniciar um servidor de Banco de Dados do Azure para PostgreSQL](howto-restart-server-portal.md).


Agora você pode ativar o TimescaleDB no seu banco de dados Postgres. Conecte-se ao banco de dados e emita o seguinte comando:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Se você vir um erro, confirme que [reiniciou seu servidor](howto-restart-server-portal.md) depois de salvar shared_preload_libraries. 

Agora você pode criar uma hipertabela TimescaleDB [do zero](https://docs.timescale.com/getting-started/creating-hypertables) ou migrar dados [de séries tempontos existentes no PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).

### <a name="restoring-a-timescale-database"></a>Restaurando um banco de dados de escala de tempo
Para restaurar um banco de dados de escala de tempo usando pg_dump `timescaledb_pre_restore()` e `timescaledb_post restore()`pg_restore, você precisa executar dois procedimentos auxiliares no banco de dados de destino: e .

Primeiro prepare o banco de dados de destino:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Agora você pode executar pg_dump no banco de dados original e, em seguida, fazer pg_restore. Após a restauração, certifique-se de executar o seguinte comando no banco de dados restaurado:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Próximas etapas
Se você não vir uma extensão que gostaria de usar, fale conosco. Vote nas solicitações existentes ou crie novas solicitações de feedback em nosso [fórum de feedback.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)
