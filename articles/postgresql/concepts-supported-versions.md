---
title: Versões com suporte-banco de dados do Azure para PostgreSQL-servidor único
description: Descreve as versões principais e secundárias postgres com suporte no banco de dados do Azure para PostgreSQL-servidor único.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/17/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 61d9712026daab63a002793adf2e17b9db943f94
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976890"
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

A atualização automática da versão principal não tem suporte. Por exemplo, não há uma atualização automática do PostgreSQL 9.5 para PostgreSQL 9.6. Se você quiser atualizar para a próxima versão principal, crie um [banco de dados dump e restaure](./howto-migrate-using-dump-and-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

### <a name="version-syntax"></a>Sintaxe da versão
Antes do PostgreSQL versão 10, a [política de controle de versão do PostgreSQL](https://www.postgresql.org/support/versioning/) considerou uma atualização de _versão principal_ para ser um aumento no primeiro _ou_ segundo número. Por exemplo, 9,5 a 9,6 foi considerado uma atualização de versão _principal_ . A partir da versão 10, apenas uma alteração no primeiro número é considerada uma atualização de versão principal. Por exemplo, 10,0 a 10,1 é uma atualização de versão _secundária_ . A versão 10 a 11 é uma atualização de versão _principal_ .

## <a name="next-steps"></a>Próximas etapas
Para obter informações sobre extensões PostgreSQL com suporte, consulte [o documento extensões](concepts-extensions.md).
