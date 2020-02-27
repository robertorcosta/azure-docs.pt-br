---
title: 'Problemas conhecidos: migrar do Oracle para o banco de dados do Azure para PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Saiba mais sobre problemas conhecidos e limitações de migração com migrações online do Oracle para o banco de dados do Azure para PostgreSQL-servidor único usando o serviço de migração de banco de dados do Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 6ab1050290119a53ee0fb674e6939938a3b855e0
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648591"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-oracle-to-azure-db-for-postgresql-single-server"></a>Problemas conhecidos/limitações de migração com migrações online do Oracle para o BD do Azure para PostgreSQL-servidor único

Problemas conhecidos e limitações associadas a migrações online do Oracle para o banco de dados do Azure para PostgreSQL-um único servidor são descritos nas seções a seguir.

## <a name="oracle-versions-supported-as-a-source-database"></a>Versões do Oracle com suporte como um banco de dados de origem

O serviço de migração de banco de dados do Azure dá suporte à conexão com

- Oracle versão 10g, 11g e 12c.
- Oracle Enterprise, Standard, Express e Personal Edition.

O serviço de migração de banco de dados do Azure não dá suporte à conexão com bancos de dados de contêiner multilocatário (CDBs).

## <a name="postgresql-versions-supported-as-a-target-database"></a>Versões do PostgreSQL com suporte como um banco de dados de destino

O serviço de migração de banco de dados do Azure dá suporte a migrações para o banco de dados do Azure para PostgreSQL, versão 9,5, 9,6, 10 e 11 do servidor. Consulte o artigo [versões de banco de dados PostgreSQL com suporte](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) para obter informações atuais sobre o suporte de versão no banco de dados do Azure para PostgreSQL-servidor único.

## <a name="datatype-limitations"></a>Limitações de tipo de dados

Os seguintes tipos de texto **não** serão migrados:

- BFILE
- ROWID
- REF
- UROWID
- ANYDATA
- SDO_GEOMETRY
- Tabelas aninhadas
- Tipos de dados definidos pelo usuário
- Observações
- Colunas virtuais
- Exibições materializadas com base na coluna de ROWID

Além disso, colunas BLOB/CLOB vazias são mapeadas para NULL no destino.

## <a name="lob-limitations"></a>Limitações de LOB

- Quando o modo LOB de tamanho limitado estiver habilitado, os LOBs vazios na origem do Oracle serão replicados como valores nulos.
- Não há suporte para nomes de objeto longos (mais de 30 bytes).
- Os dados na coluna bruta longa e longa não podem exceder 64K. Todos os dados além de 64K serão truncados.
- Somente no Oracle 12, não há suporte para alterações nas colunas LOB (migradas).
- Não há suporte para atualizações em colunas XMLTYPE e LOB (migradas).

## <a name="known-issues-and-limitations"></a>Limitações e problemas conhecidos

- Os clientes devem usar o SYSDBA para se conectar ao Oracle.
- As alterações de dados resultantes de operações de partição/subpartição (adicionar, descartar, trocar e TRUNCAr) não serão migradas e poderão causar os seguintes erros:
  - Para adicionar operações, as atualizações e exclusões nos dados adicionados podem retornar um aviso "0 linhas afetadas".
  - Para operações DROP e TRUNCATE, novas inserções podem resultar em erros de "duplicatas".
  - Para operações do EXCHANGE, um aviso "0 linhas afetadas" e erros "duplicatas" podem ocorrer.
- Tabelas cujos nomes contêm apóstrofos não podem ser replicados.
