---
title: 'Problemas conhecidos: Migrar do Oracle para o Banco de Dados Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações on-line do Oracle para o Azure Database para servidor PostgreSQL-Single usando o Serviço de Migração de Banco de Dados do Azure.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: fcebc7eb170239e5d7efd8a32599a6e782f630bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235253"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Problemas/limitações de migração conhecidas com migrações on-line do Oracle para o Azure DB para servidor PostgreSQL-Single

Problemas e limitações conhecidas associados a migrações on-line do Oracle para o Azure Database para servidor PostgreSQL-Single são descritos nas seguintes seções.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versões Oracle suportadas como um banco de dados de origem

O Azure Database Migration Service suporta a conexão com:

- Versão Oracle 10g, 11g e 12c.
- Oracle Enterprise, Standard, Express e Personal Edition.

O Azure Database Migration Service não suporta a conexão a CDBs (Multi-Tenant Databases, bancos de dados de contêineres de vários locatários).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versões postgreSQL suportadas como um banco de dados de destino

O Azure Database Migration Service suporta migrações para o Banco de Dados Azure para o servidor PostgreSQL-Single versão 9.5, 9.6, 10 e 11. Consulte o artigo [Versões de banco de dados PostgreSQL suportadas](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) para obter informações atuais sobre suporte à versão no Banco de Dados Azure para servidor PostgreSQL-Single.

## <a name="datatype-limitations"></a>Limitações de tipo de dados

Os seguintes tipos de **dados não** serão migrados:

- BFILE
- ROWID
- REF
- UROWID
- Anydata
- SDO_GEOMETRY
- Tabelas aninhadas
- Tipos de dados definidos pelo usuário
- Observações
- Colunas virtuais
- Visualizações materializadas com base na coluna ROWID

Além disso, as colunas BLOB/CLOB vazias são mapeadas para NULL no destino.

## <a name="lob-limitations"></a>Limitações de LOB

- Quando o modo LOB de tamanho limitado estiver ativado, LOBs vazios na fonte Oracle são replicados como valores NULL.
- Nomes de objetos longos (mais de 30 bytes) não são suportados.
- Os dados na coluna LONG e LONG RAW não podem exceder 64k. Qualquer dado além de 64k será truncado.
- Somente no Oracle 12, não são suportadas alterações nas colunas LOB (migradas).
- UPDATEs para colunas XMLTYPE e LOB não são suportados (migrados).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

- Os clientes devem usar o SYSDBA para se conectar à Oracle.
- As alterações de dados resultantes de operações de partição/subpartição (ADD, DROP, EXCHANGE e TRUNCATE) não serão migradas e podem causar os seguintes erros:
  - Para operações de ADD, atualizações e exclusões nos dados adicionados podem retornar um aviso de "0 linhas afetadas".
  - Para as operações DROP e TRUNCATE, novas inserções podem resultar em erros de "duplicatas".
  - Para as operações EXCHANGE, podem ocorrer erros de "0 linhas afetadas" e "duplicatas".
- Tabelas cujos nomes contêm apóstrofes não podem ser replicadas.
