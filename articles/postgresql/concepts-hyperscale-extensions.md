---
title: Extensões – Hyperscale (Citus) - Banco de dados Azure para PostgreSQL
description: Descreve a capacidade de estender a funcionalidade do seu banco de dados usando extensões no Banco de Dados Azure para PostgreSQL - Hyperscale (Citus)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485396"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensões PostgreSQL no Banco de Dados Azure para PostgreSQL – Hyperscale (Citus)

O PostgreSQL oferece a capacidade de estender a funcionalidade do seu banco de dados usando extensões. As extensões permitem o agrupamento de vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido de seu banco de dados com um único comando. Depois de serem carregados no banco de dados, as extensões podem funcionar como recursos incorporados. Para obter mais informações sobre extensões PostgreSQL, consulte [Objetos relacionados ao pacote em uma extensão](https://www.postgresql.org/docs/current/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Use extensões PostgreSQL

As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute o comando [CREATE EXTENSION](https://www.postgresql.org/docs/current/static/sql-createextension.html) da ferramenta psql para carregar os objetos embalados em seu banco de dados.

O Banco de Dados Azure para PostgreSQL - Hyperscale (Citus) atualmente suporta um subconjunto de extensões-chave listadas aqui. Extensões diferentes das listadas não são suportadas. Você não pode criar sua própria extensão com o Azure Database para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões com suporte do Banco de Dados do Azure para PostgreSQL

As tabelas a seguir listam as extensões PostgreSQL padrão que têm suporte atualmente do Banco de Dados do Azure para PostgreSQL. Essas informações também estão disponíveis por meio da execução de `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Fornece um tipo de cadeia de caracteres que não diferencia maiúsculas de minúsculas. |
> | [Cubo](https://www.postgresql.org/docs/current/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares de valor-chave. |
> | [Hll](https://github.com/citusdata/postgresql-hll) | Fornece uma estrutura de dados HyperLogLog. |
> | [isn](https://www.postgresql.org/docs/current/static/isn.html) | Fornece tipos de dados para padrões de numeração de produto internacionais. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objetos grandes. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Fornece um tipo de dados para estruturas semelhantes a árvores hierárquicas. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar segmentos de linha ou intervalos de ponto flutuante. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Digite para top-n JSONB. |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para números inteiros. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento prolongado de sinônimos. |
> | [unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) dos lexemas. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para campos de autoincrementação. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Fornece um meio para calcular as distância ortodrômicas na superfície da Terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e a distância entre cadeias de caracteres. |
> | [inserir\_nome de usuário](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para rastrear quem mudou uma tabela. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Agregador inteiro e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Fornece funções e operadores para manipular matrizes de inteiros sem nulos. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para acompanhar o tempo de última modificação. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Fornece funções de criptografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gerencia as tabelas de partição por hora ou ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança de texto alfanumérico, com base na correspondência de trigram. |
> | [refino](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções para implementação de integridade referencial (obsoleta). |
> | análise\_de sessão | Funções para consultar matrizes hstore. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Fornece funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Notificações de alteração acionadas. |
> | [viagem no tempo](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para implementar viagens no tempo. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Gera UUIDs (identificadores exclusivos universais). |

### <a name="hyperscale-extensions"></a>Extensões de hiperescala

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Banco de dados distribuído citus. |
> | rebalanceador de fragmentos\_ | Reequilibrar os dados com segurança em um grupo de servidores em caso de adição ou remoção de nó. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [Flor](https://www.postgresql.org/docs/current/bloom.html) | Método de acesso Bloom - índice baseado em arquivo de assinatura. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Fornece classes de operadores de GIN de amostra que implementam comportamento semelhante a árvore B para certos tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Linguagem de procedimento carregável PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade da relação. |
> | [arquivo\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Invólucro de dados estrangeiros para acesso a arquivos planos. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas do banco de dados em um nível baixo. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo no cache do buffer compartilhado em tempo real. |
> | [companheiro\_de pg](https://github.com/citusdata/pg_cron) | Agendador de empregos para PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa espacial livre (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Fornece uma maneira para carregar dados de relação no cache de buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. Consulte a seção "pg_stat_statements" para obter informações sobre esta extensão. |
> | [visibilidade\_pg](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine o mapa de visibilidade (VM) e as informações de visibilidade em nível de página. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio de nível de linha. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas em nível de tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Wrapper de dados externos usado para acessar dados armazenados em servidores PostgreSQL externos. Consulte a seção "dblink and postgres_fdw" para obter informações sobre esta extensão.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informações sobre certificados SSL. |
> | [linhas do\_\_sistema tsm](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE, que aceita o número de linhas como um limite. |
> | [tempo do\_\_sistema tsm](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE, que aceita o tempo em milissegundos como um limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fornece um meio de criação de índices hipotéticos que não gastam CPU ou disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta conexões com outros bancos de dados do PostgreSQL de dentro de uma sessão de banco de dados. Consulte a seção "dblink and postgres_fdw" para obter informações sobre esta extensão. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões PostGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Usado para analisar um endereço em elementos constituintes. Usado para oferecer suporte à etapa de normalização de endereços de geocodificação. |
> | postgis\_sfcgal | Funções PostGIS SFCGAL. |
> | postgis\_\_tigre geocoder | Geocodificador de tigre PostGIS e geocodificador reverso. |
> | topologia\_postgis | Tipos e funções espaciais de topologia PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [\_extensão de declarações\_de estatística pg](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregada em todos os bancos de dados do Azure para servidor PostgreSQL para fornecer-lhe um meio de rastrear estatísticas de execução de declarações SQL.

A `pg_stat_statements.track` configuração controla quais instruções são contadas pela extensão. Ele é `top`padrão, o que significa que todas as declarações emitidas diretamente pelos clientes são rastreadas. Dois outros níveis de rastreamento são `none` e `all`. Essa definição é configurável como um parâmetro de servidor por meio de [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou da [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há uma troca entre as informações de execução de consulta pg_stat_statements fornece e o efeito no desempenho do servidor à medida que registra cada declaração SQL. Se você não estiver usando ativamente a extensão `pg_stat_statements.track` pg_stat_statements, recomendamos que você defina para `none`. Alguns serviços de monitoramento de terceiros podem contar com pg_stat_statements para fornecer insights de desempenho de consulta, então confirme se este é o caso para você ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
Você pode usar dblink e postgres_fdw para conectar de um servidor PostgreSQL a outro, ou a outro banco de dados no mesmo servidor. O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall. Para usar essas extensões para conectar entre o Banco de Dados Azure para servidores PostgreSQL, defina **permitir o acesso aos serviços do Azure** para ON. Você também precisa ativar essa configuração se quiser usar as extensões para voltar ao mesmo servidor. O Permitir acesso à configuração **de serviços do Azure** pode ser encontrado na página do portal Azure para o servidor Postgres em **Segurança de Conexão**. Transformando **Permitir o acesso aos serviços do Azure** ON whitelists todos os IPs do Azure.

Atualmente, as conexões de saída do Banco de Dados Azure para PostgreSQL não são suportadas, exceto para conexões com outros bancos de dados Do Zure para servidores PostgreSQL.
