---
title: Limitações-banco de dados do Azure para MySQL
description: Este artigo descreve limitações no Banco de Dados do Azure para MySQL, como número de opções de mecanismo de armazenamento e conexão.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 8b3d6ea46c4a88187b70b520457ad34f7e7f36ba
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975135"
---
# <a name="limitations-in-azure-database-for-mysql"></a>Limitações no Banco de Dados do Azure para MySQL
As seções a seguir descrevem a capacidade, suporte do mecanismo de armazenamento, suporte de privilégio, suporte à instrução de manipulação de dados e limites funcionais no serviço do banco de dados. Consulte também as [limitações gerais](https://dev.mysql.com/doc/mysql-reslimits-excerpt/5.6/en/limits.html) aplicáveis ao mecanismo de banco de dados MySQL.

## <a name="maximum-connections"></a>Número máximo de conexões
O número máximo de conexões por tipo de preço e vCores é o seguinte: 

|**Tipo de preço**|**vCore(s)**| **Máximo de conexões**|
|---|---|---|
|Basic| 1| 50|
|Basic| 2| 100|
|Propósito geral| 2| 600|
|Propósito geral| 4| 1250|
|Propósito geral| 8| 2500|
|Propósito geral| 16| 5\.000|
|Propósito geral| 32| 10000|
|Propósito geral| 64| 20000|
|Memória Otimizada| 2| 1250|
|Memória Otimizada| 4| 2500|
|Memória Otimizada| 8| 5\.000|
|Memória Otimizada| 16| 10000|
|Memória Otimizada| 32| 20000|

Quando as conexões excederem o limite, você poderá receber o seguinte erro:
> ERRO 1040 (08004): número excessivo de conexões

## <a name="storage-engine-support"></a>Suporte do mecanismo de armazenamento

### <a name="supported"></a>Com suporte
- [InnoDB](https://dev.mysql.com/doc/refman/5.7/en/innodb-introduction.html)
- [MEMORY](https://dev.mysql.com/doc/refman/5.7/en/memory-storage-engine.html)

### <a name="unsupported"></a>Sem suporte
- [MyISAM](https://dev.mysql.com/doc/refman/5.7/en/myisam-storage-engine.html)
- [BLACKHOLE](https://dev.mysql.com/doc/refman/5.7/en/blackhole-storage-engine.html)
- [ARCHIVE](https://dev.mysql.com/doc/refman/5.7/en/archive-storage-engine.html)
- [FEDERATED](https://dev.mysql.com/doc/refman/5.7/en/federated-storage-engine.html)

## <a name="privilege-support"></a>Suporte de privilégio

### <a name="unsupported"></a>Sem suporte
- Função DBA: muitas configurações e parâmetros do servidor podem inadvertidamente prejudicar o desempenho do servidor ou negar as propriedades ACID do DBMS. Desa forma, para manter a SLA e integridade do serviço em um nível de produto, esse serviço não expõe a função DBA. A conta de usuário padrão, que é construída quando uma nova instância do banco de dados é criada, permite que o usuário execute a maioria das instruções DDL e DML na instância do banco de dados gerenciado. 
- Privilégio SUPER: semelhante a privilégio [SUPER](https://dev.mysql.com/doc/refman/5.7/en/privileges-provided.html#priv_super) também é restrito.
- O desfinamento: requer a criação de privilégios e é restrito. Se estiver importando dados usando um backup, remova os comandos `CREATE DEFINER`manualmente ou usando o comando `--skip-definer` ao executar um mysqldump.

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
- Não há suporte para a migração automatizada entre versões de mecanismo de banco de dados principal. Se você quiser atualizar para a próxima versão principal, faça um [despejo e restaure](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="point-in-time-restore"></a>Restauração pontual
- Ao usar o recurso PITR, o novo servidor é criado com as mesmas configurações nas quais o servidor está baseado.
- Não há suporte para restaurar um servidor eliminado.

### <a name="vnet-service-endpoints"></a>Ponto de extremidade de serviço VNet
- O suporte para ponto de extremidade de serviço de VNet é apenas para servidores de Uso Geral e Otimizado para memória.

### <a name="storage-size"></a>Tamanho do armazenamento
- Veja os [tipos de preço](concepts-pricing-tiers.md) para os limites de tamanho de armazenamento por tipo de preço.

## <a name="current-known-issues"></a>Problemas frequentes conhecidos
- A Instância do MySQL Server exibe a versão de servidor incorreta após a conexão ser estabelecida. Para obter a versão correta do mecanismo de instância de servidor, use o comando `select version();`.

## <a name="next-steps"></a>Próximos passos
- [O que está disponível em cada camada de serviço](concepts-pricing-tiers.md)
- [Versões de banco de dados MySQL com suporte](concepts-supported-versions.md)
