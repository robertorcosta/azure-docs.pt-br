---
title: Limitações - Banco de Dados Azure para MariaDB
description: Este artigo descreve as limitações no Banco de Dados do Azure para o MariaDB, como o número de opções de mecanismo de conexão e armazenamento.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 18f227c1888e0565eebb640fa61ced56dc994865
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632335"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Limitações no Banco de Dados do Azure para o MariaDB
As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados.

## <a name="server-parameters"></a>Parâmetros do Servidor

Os valores mínimo e máximo de vários parâmetros de servidor populares são determinados pelo nível de preços e vCores. Consulte as tabelas abaixo para obter limites.

### <a name="max_connections"></a>Max_connections

|**Nível de preços**|**vCore(s)**|**Valor padrão**|**Valor min**|**Valor máximo**|
|---|---|---|---|---|
|Basic|1|50|10|50|
|Basic|2|100|10|100|
|Uso Geral|2|300|10|600|
|Uso Geral|4|625|10|1250|
|Uso Geral|8|1250|10|2500|
|Uso Geral|16|2500|10|5.000|
|Uso Geral|32|5.000|10|10000|
|Uso Geral|64|10000|10|20000|
|Otimizado para memória|2|600|10|800|
|Otimizado para memória|4|1250|10|2500|
|Otimizado para memória|8|2500|10|5.000|
|Otimizado para memória|16|5.000|10|10000|
|Otimizado para memória|32|10000|10|20000|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> ERRO 1040 (08004): número excessivo de conexões

> [!IMPORTANT]
> Para obter a melhor experiência, recomendamos que você use um pooler de conexão como proxySQL para gerenciar conexões de forma eficiente.

Criar novas conexões de clientes ao MariaDB leva tempo e, uma vez estabelecidas, essas conexões ocupam recursos de banco de dados, mesmo quando ociosas. A maioria dos aplicativos solicita muitas conexões de curta duração, o que agrava essa situação. O resultado é menos recursos disponíveis para sua carga de trabalho real, levando a uma diminuição do desempenho. Um pooler de conexões que diminui as conexões ociosas e reutiliza as conexões existentes ajudará a evitar isso. Para saber mais sobre a configuração do ProxySQL, visite nosso [post no blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042).

### <a name="query_cache_size"></a>query_cache_size

O cache de consulta é desligado por padrão. Para ativar o cache de `query_cache_type` consulta, configure o parâmetro. 

Reveja a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#query_cache_size) para saber mais sobre esse parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor padrão**|**Valor min**|**Valor máximo**|
|---|---|---|---|---|
|Basic|1|Não configurável no nível Básico|N/D|N/D|
|Basic|2|Não configurável no nível Básico|N/D|N/D|
|Uso Geral|2|0|0|16777216|
|Uso Geral|4|0|0|33554432|
|Uso Geral|8|0|0|67108864|
|Uso Geral|16|0|0|134217728|
|Uso Geral|32|0|0|134217728|
|Uso Geral|64|0|0|134217728|
|Otimizado para memória|2|0|0|33554432|
|Otimizado para memória|4|0|0|67108864|
|Otimizado para memória|8|0|0|134217728|
|Otimizado para memória|16|0|0|134217728|
|Otimizado para memória|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

Reveja a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size) para saber mais sobre esse parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor padrão**|**Valor min**|**Valor máximo**|
|---|---|---|---|---|
|Basic|1|Não configurável no nível Básico|N/D|N/D|
|Basic|2|Não configurável no nível Básico|N/D|N/D|
|Uso Geral|2|524288|32768|4194304|
|Uso Geral|4|524288|32768|8388608|
|Uso Geral|8|524288|32768|16777216|
|Uso Geral|16|524288|32768|33554432|
|Uso Geral|32|524288|32768|33554432|
|Uso Geral|64|524288|32768|33554432|
|Otimizado para memória|2|524288|32768|8388608|
|Otimizado para memória|4|524288|32768|16777216|
|Otimizado para memória|8|524288|32768|33554432|
|Otimizado para memória|16|524288|32768|33554432|
|Otimizado para memória|32|524288|32768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

Reveja a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size) para saber mais sobre esse parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor padrão**|**Valor min**|**Valor máximo**|
|---|---|---|---|---|
|Basic|1|Não configurável no nível Básico|N/D|N/D|
|Basic|2|Não configurável no nível Básico|N/D|N/D|
|Uso Geral|2|262144|128|268435455|
|Uso Geral|4|262144|128|536870912|
|Uso Geral|8|262144|128|1073741824|
|Uso Geral|16|262144|128|2147483648|
|Uso Geral|32|262144|128|4294967295|
|Uso Geral|64|262144|128|4294967295|
|Otimizado para memória|2|262144|128|536870912|
|Otimizado para memória|4|262144|128|1073741824|
|Otimizado para memória|8|262144|128|2147483648|
|Otimizado para memória|16|262144|128|4294967295|
|Otimizado para memória|32|262144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

Reveja a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size) para saber mais sobre esse parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor padrão**|**Valor min**|**Valor máximo**|
|---|---|---|---|---|
|Basic|1|Não configurável no nível Básico|N/D|N/D|
|Basic|2|Não configurável no nível Básico|N/D|N/D|
|Uso Geral|2|16777216|16384|268435455|
|Uso Geral|4|16777216|16384|536870912|
|Uso Geral|8|16777216|16384|1073741824|
|Uso Geral|16|16777216|16384|2147483648|
|Uso Geral|32|16777216|16384|4294967295|
|Uso Geral|64|16777216|16384|4294967295|
|Otimizado para memória|2|16777216|16384|536870912|
|Otimizado para memória|4|16777216|16384|1073741824|
|Otimizado para memória|8|16777216|16384|2147483648|
|Otimizado para memória|16|16777216|16384|4294967295|
|Otimizado para memória|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

Reveja a [documentação do MariaDB](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size) para saber mais sobre esse parâmetro.

|**Nível de preços**|**vCore(s)**|**Valor padrão**|**Valor min**|**Valor máximo**|
|---|---|---|---|---|
|Basic|1|Não configurável no nível Básico|N/D|N/D|
|Basic|2|Não configurável no nível Básico|N/D|N/D|
|Uso Geral|2|16777216|1024|67108864|
|Uso Geral|4|16777216|1024|134217728|
|Uso Geral|8|16777216|1024|268435456|
|Uso Geral|16|16777216|1024|536870912|
|Uso Geral|32|16777216|1024|1073741824|
|Uso Geral|64|16777216|1024|1073741824|
|Otimizado para memória|2|16777216|1024|134217728|
|Otimizado para memória|4|16777216|1024|268435456|
|Otimizado para memória|8|16777216|1024|536870912|
|Otimizado para memória|16|16777216|1024|1073741824|
|Otimizado para memória|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

As tabelas de fuso horário `mysql.az_load_timezone` podem ser preenchidas chamando o procedimento armazenado de uma ferramenta como a linha de comando MySQL ou MySQL Workbench. Consulte os artigos do [portal Azure](howto-server-parameters.md#working-with-the-time-zone-parameter) ou [do Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter) sobre como chamar o procedimento armazenado e definir os fusos horários globais ou em nível de sessão.

## <a name="storage-engine-support"></a>Suporte do mecanismo de armazenamento

### <a name="supported"></a>Com suporte
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [Memória](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>Sem suporte
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [Arquivo](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Sem suporte
- Função DBA: muitas configurações e parâmetros do servidor podem inadvertidamente prejudicar o desempenho do servidor ou negar as propriedades ACID do DBMS. Desa forma, para manter a SLA e integridade do serviço em um nível de produto, esse serviço não expõe a função DBA. A conta de usuário padrão, que é construída quando uma nova instância do banco de dados é criada, permite que o usuário execute a maioria das instruções DDL e DML na instância do banco de dados gerenciado.
- SUPER privilégio: Da mesma [forma, o privilégio SUPER](https://mariadb.com/kb/en/library/grant/#global-privileges) também é restrito.
- DEFINER: Requer super privilégios para criar e é restrito. Se estiver importando dados usando um backup, remova os comandos `CREATE DEFINER`manualmente ou usando o comando `--skip-definer` ao executar um mysqldump.

## <a name="data-manipulation-statement-support"></a>Suporte à instrução de manipulação de dados

### <a name="supported"></a>Com suporte
- `LOAD DATA INFILE` tem suporte, mas o parâmetro `[LOCAL]` deve ser especificado e direcionado para um caminho UNC (armazenamento do Azure montado por meio do SMB).

### <a name="unsupported"></a>Sem suporte
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>Limitações funcionais

### <a name="scale-operations"></a>Operações de dimensionamento
- O dimensionamento dinâmico de e para as camadas de preços básicas não tem suporte no momento.
- Não há suporte para diminuir o tamanho de armazenamento do servidor.

### <a name="server-version-upgrades"></a>Upgrade da versão do servidor
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal.

### <a name="point-in-time-restore"></a>Restauração pontual
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações nas quais o servidor está baseado.
- Não há suporte para restaurar um servidor eliminado.

### <a name="subscription-management"></a>Gerenciamento de assinaturas
- Não há suporte para mover dinamicamente servidores criados previamente entre a assinatura e o grupo de recursos.

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizados para Memória.

### <a name="storage-size"></a>Tamanho de armazenamento
- Consulte os [níveis de preços](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por nível de preço.

## <a name="current-known-issues"></a>Problemas frequentes conhecidos
- Instância de servidor MariaDB exibe a versão de servidor incorreto após o estabelecimento de conexão. Para obter a versão correta do mecanismo de instância de servidor, use o comando `select version();`.

## <a name="next-steps"></a>Próximas etapas
- [O que está disponível em cada nível de serviço](concepts-pricing-tiers.md)
- [Versões com suporte de banco de dados MariaDB](concepts-supported-versions.md)
