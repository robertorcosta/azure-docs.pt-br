---
title: Versões com suporte-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve as versões principais e secundárias postgres com suporte no banco de dados do Azure para PostgreSQL-servidor único.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: cfe4b92dbed69440ee2c07cff758faad7e01293f
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707907"
---
# <a name="supported-postgresql-major-versions"></a>Versões principais do PostgreSQL com suporte
A Microsoft pretende oferecer suporte a versões n-2 do mecanismo PostgreSQL no banco de dados do Azure para PostgreSQL-servidor único. As versões seriam a versão principal atual no Azure (n) e as duas versões principais anteriores (-2).

O banco de dados do Azure para PostgreSQL atualmente dá suporte às seguintes versões principais:

## <a name="postgresql-version-11"></a>PostgreSQL versão 11
A versão secundária atual é 11,6. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-10"></a>PostgreSQL versão 10
A versão secundária atual é 10,11. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-96"></a>PostgreSQL versão 9,6
A versão secundária atual é 9.6.16. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="postgresql-version-95"></a>PostgreSQL versão 9,5
A versão secundária atual é 9.5.20. Consulte a [documentação do PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) para saber mais sobre melhorias e correções nesta versão secundária.

## <a name="managing-upgrades"></a>Gerenciando atualizações
O projeto PostgreSQL emite regularmente versões secundárias para corrigir bugs relatados. O banco de dados do Azure para PostgreSQL corrige automaticamente os servidores com versões secundárias durante as implantações mensais do serviço. 

Não há suporte para atualizações automáticas in-loco para versões principais. Para atualizar para a próxima versão principal, você pode 
   * Use [pg_dump e pg_restore](./howto-migrate-using-dump-and-restore.md) para mover um banco de dados para um servidor criado com a nova versão do mecanismo
   * Como alternativa, você pode atualizar do PostgreSQL 10 para 11 usando o [serviço de migração de banco de dados do Azure](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)

### <a name="version-syntax"></a>Sintaxe da versão
Antes do PostgreSQL versão 10, a [política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ . A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal. Por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ . A versão 10 a 11 é uma atualização de versão _principal_ .

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre extensões PostgreSQL com suporte, consulte [o documento extensões](concepts-extensions.md).
