---
title: Extensões PostgreSQL no banco de dados do Azure para PostgreSQL – Citus (hiperescala)
description: Descreve a capacidade de estender a funcionalidade do banco de dados usando extensões no banco de dados do Azure para PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 41edcc40bae7915e321a278592dd2f73621aae05
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482408"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>Extensões PostgreSQL no banco de dados do Azure para PostgreSQL – Citus (hiperescala)

O PostgreSQL fornece a capacidade de estender a funcionalidade do seu banco de dados usando extensões. As extensões permitem o agrupamento de vários objetos SQL relacionados em um único pacote que pode ser carregado ou removido de seu banco de dados com um único comando. Depois de ser carregado no banco de dados, as extensões podem funcionar como recursos internos. Para obter mais informações sobre extensões PostgreSQL, consulte [empacotar objetos relacionados em uma extensão](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="use-postgresql-extensions"></a>Usar extensões do PostgreSQL

As extensões PostgreSQL devem ser instaladas no banco de dados para que você possa usá-las. Para instalar uma extensão específica, execute o comando criar de [extensão](https://www.postgresql.org/docs/9.6/static/sql-createextension.html) da ferramenta psql para carregar os objetos empacotados em seu banco de dados.

O banco de dados do Azure para PostgreSQL-Citus (hiperescala) atualmente dá suporte a um subconjunto de extensões de chave, conforme listado aqui. Não há suporte para extensões que não aquelas listadas. Você não pode criar sua própria extensão com o banco de dados do Azure para PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensões com suporte do Banco de Dados do Azure para PostgreSQL

As tabelas a seguir listam as extensões PostgreSQL padrão que têm suporte atualmente do Banco de Dados do Azure para PostgreSQL. Essas informações também estão disponíveis por meio da execução de `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensões de tipos de dados

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Fornece um tipo de cadeia de caracteres que não diferencia maiúsculas de minúsculas. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Fornece um tipo de dados para cubos multidimensionais. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Fornece um tipo de dados para armazenar conjuntos de pares chave-valor. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Fornece tipos de dados para padrões de numeração de produto internacionais. |
> | [lo](https://www.postgresql.org/docs/current/lo.html) | Manutenção de objeto grande. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Fornece um tipo de dados para estruturas semelhantes a árvores hierárquicas. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Tipo de dados para representar segmentos de linha ou intervalos de ponto flutuante. |
> | [TopN](https://github.com/citusdata/postgresql-topn/) | Tipo para Top-n JSONB. |

### <a name="full-text-search-extensions"></a>Extensões de pesquisa de texto completo

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Fornece um modelo de dicionário de pesquisa de texto para números inteiros. |
> | [xsyn de\_de dict](https://www.postgresql.org/docs/current/dict-xsyn.html) | Modelo de dicionário de pesquisa de texto para processamento estendido de sinônimo. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Um dicionário de pesquisa de texto que remove acentos (sinais diacríticos) dos lexemas. |

### <a name="functions-extensions"></a>Extensões de funções

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Funções para incrementar campos automaticamente. |
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Fornece um meio para calcular as distância ortodrômicas na superfície da Terra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Fornece várias funções para determinar semelhanças e a distância entre cadeias de caracteres. |
> | [inserir\_nome de usuário](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Funções para controlar quem alterou uma tabela. |
> | [intag](https://www.postgresql.org/docs/current/intagg.html) | Agregador inteiro e enumerador (obsoleto). |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Fornece funções e operadores para manipular matrizes de inteiros sem nulos. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Funções para controlar a hora da última modificação. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Fornece funções de criptografia. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Gerencia as tabelas de partição por hora ou ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Fornece funções e operadores para determinar a semelhança de texto alfanumérico, com base na correspondência de trigram. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Funções para implementar a integridade referencial (obsoleto). |
> | análise de\_de sessão | Funções para consultar matrizes hstore. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Fornece funções que manipulam tabelas inteiras, incluindo a tabela de referência cruzada. |
> | [TCN](https://www.postgresql.org/docs/current/tcn.html) | Notificações de alteração disparadas. |
> | [viagem de viagens](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Funções para implementação de viagem de tempo. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Gera UUIDs (identificadores exclusivos universais). |

### <a name="hyperscale-extensions"></a>Extensões de hiperescala

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Banco de dados distribuído Citus. |
> | rebalanceador de\_de fragmento | Equilibre dados com segurança em um grupo de servidores em caso de adição ou remoção de nós. |

### <a name="index-types-extensions"></a>Extensões de tipos de índice

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [cair](https://www.postgresql.org/docs/current/bloom.html) | Incair no método de acesso – índice baseado em arquivo de assinatura. |
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Fornece exemplos de classes de operadores iniciar que implementam comportamento de árvore B para determinados tipos de dados. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Fornece classes de operador de índice GiST que implementam a árvore B. |

### <a name="language-extensions"></a>Extensões de linguagem

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Linguagem de procedimento carregável PL/pgSQL. |

### <a name="miscellaneous-extensions"></a>Extensões diversas

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Funções administrativas para PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Funções para verificar a integridade da relação. |
> | [arquivo\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Wrapper de dados estrangeiros para acesso de arquivo simples. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Inspecione o conteúdo das páginas do banco de dados em um nível baixo. |
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Fornece um meio para examinar o que está acontecendo no cache do buffer compartilhado em tempo real. |
> | [PG\_cron](https://github.com/citusdata/pg_cron) | Agendador de trabalhos para PostgreSQL. |
> | [PG\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Examine o mapa de espaço livre (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Fornece uma maneira para carregar dados de relação no cache de buffer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Fornece um meio para rastrear as estatísticas de execução de todas as instruções SQL executadas por um servidor. Consulte a seção "pg_stat_statements" para obter informações sobre essa extensão. |
> | [visibilidade de\_PG](https://www.postgresql.org/docs/current/pgvisibility.html) | Examine a VM (mapa de visibilidade) e as informações de visibilidade no nível da página. |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Fornece um meio para mostrar informações de bloqueio de nível de linha. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Fornece um meio para mostrar estatísticas em nível de tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Wrapper de dados externos usado para acessar dados armazenados em servidores PostgreSQL externos. Consulte a seção "dblink e postgres_fdw" para obter informações sobre essa extensão.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informações sobre certificados SSL. |
> | [linhas de\_do sistema TSM\_](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Método TABLESAMPLE, que aceita o número de linhas como um limite. |
> | [tempo de\_do sistema TSM\_](https://www.postgresql.org/docs/current/tsm-system-time.html) | Método TABLESAMPLE, que aceita o tempo em milissegundos como um limite. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Fornece um meio de criação de índices hipotéticos que não gastam CPU ou disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Um módulo que suporta conexões com outros bancos de dados do PostgreSQL de dentro de uma sessão de banco de dados. Consulte a seção "dblink e postgres_fdw" para obter informações sobre essa extensão. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | Consulta XPath e XSLT. |


### <a name="postgis-extensions"></a>Extensões PostGIS

> [!div class="mx-tableFixed"]
> | **Extensão** | **Descrição** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciais e geográficos para PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Usado para analisar um endereço em elementos constituintes. Usado para oferecer suporte à etapa de normalização de endereços de geocodificação. |
> | PostGIS\_sfcgal | PostGIS SFCGAL functions. |
> | PostGIS\_Tiger\_geocodificador | Geocodificador PostGIS Tiger e reverso geocodificador. |
> | topologia de\_PostGIS | Tipos e funções espaciais de topologia PostGIS. |


## <a name="pg_stat_statements"></a>pg_stat_statements
A [extensão pg\_stat\_Statements](https://www.postgresql.org/docs/current/pgstatstatements.html) é pré-carregado em cada servidor de banco de dados do Azure para PostgreSQL para fornecer a você um meio de controlar estatísticas de execução de instruções SQL.

A configuração `pg_stat_statements.track` controla quais instruções são contadas pela extensão. O padrão é `top`, o que significa que todas as instruções emitidas diretamente pelos clientes são controladas. Dois outros níveis de rastreamento são `none` e `all`. Essa definição é configurável como um parâmetro de servidor por meio de [portal do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) ou da [CLI do Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Há uma compensação entre as informações de execução de consulta que o pg_stat_statements fornece e o efeito no desempenho do servidor enquanto registra cada instrução SQL. Se você não estiver usando ativamente a extensão pg_stat_statements, recomendamos que defina `pg_stat_statements.track` como `none`. Alguns serviços de monitoramento de terceiros podem depender do pg_stat_statements para fornecer informações de desempenho de consulta, portanto, confirme se esse é o caso para você ou não.

## <a name="dblink-and-postgres_fdw"></a>dblink e postgres_fdw
Você pode usar dblink e postgres_fdw para se conectar de um servidor PostgreSQL para outro ou a outro banco de dados no mesmo servidor. O servidor de recebimento precisa permitir conexões do servidor de envio por meio de seu firewall. Para usar essas extensões para se conectar entre os servidores do banco de dados do Azure para PostgreSQL, defina **permitir acesso aos serviços do Azure** como ativado. Você também precisará ativar essa configuração se quiser usar as extensões para fazer um loop de volta para o mesmo servidor. A configuração **permitir acesso aos serviços do Azure** pode ser encontrada na página portal do Azure para o servidor postgres em **segurança de conexão**. Desativando **permitir o acesso aos serviços do Azure** em listas de permissões todos os IPS do Azure.

Atualmente, não há suporte para conexões de saída do banco de dados do Azure para PostgreSQL, exceto para conexões com outros servidores do banco de dados do Azure para PostgreSQL.
