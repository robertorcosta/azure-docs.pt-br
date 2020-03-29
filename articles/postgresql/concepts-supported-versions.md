---
title: Versões suportadas - Banco de dados Azure para PostgreSQL - Servidor Único
description: Descreve as versões principais e menores do Postgres suportadas no Banco de Dados Azure para PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792227"
---
# <a name="supported-postgresql-major-versions"></a>Versões principais do PostgreSQL suportadas
A Microsoft pretende suportar versões n-2 do mecanismo PostgreSQL no Azure Database para PostgreSQL - Single Server. As versões seriam a versão principal atual no Azure (n) e as duas versões principais anteriores (-2).

O Banco de Dados Azure para PostgreSQL atualmente suporta as seguintes versões principais:

## <a name="postgresql-version-11"></a>PostgreSQL versão 11
A versão menor atual é 11.5. Consulte a [documentação postgreSQL](https://www.postgresql.org/docs/11/static/release-11-5.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-10"></a>PostgreSQL versão 10
A versão menor atual é 10.10. Consulte a [documentação postgreSQL](https://www.postgresql.org/docs/10/static/release-10-10.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-96"></a>PostgreSQL versão 9.6
A versão menor atual é 9.6.15. Consulte a [documentação postgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="postgresql-version-95"></a>PostgreSQL versão 9.5
A versão menor atual é 9.5.19. Consulte a [documentação postgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) para saber mais sobre melhorias e correções nesta versão menor.

## <a name="managing-upgrades"></a>Gerenciamento de upgrades
O projeto PostgreSQL emite regularmente pequenas versões para corrigir bugs relatados. O Banco de Dados Azure para PostgreSQL patches automaticamente servidores com pequenas versões durante as implantações mensais do serviço. 

A atualização automática da versão principal não tem suporte. Por exemplo, não há uma atualização automática do PostgreSQL 9.5 para PostgreSQL 9.6. Se você quiser atualizar para a próxima versão principal, crie um [banco de dados dump e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="version-syntax"></a>Sintaxe de versão
Antes do PostgreSQL versão 10, a [política de versão PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma _atualização de versão principal_ para ser um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9,6 foi considerado um _grande_ upgrade de versão. A partir da versão 10, apenas uma mudança no primeiro número é considerada uma atualização de versão principal. Por exemplo, 10.0 a 10.1 é uma _pequena_ atualização de versão. A versão 10 para a 11 é uma _grande_ atualização da versão.

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre extensões PostgreSQL suportadas, consulte [o documento de extensões](concepts-extensions.md).
