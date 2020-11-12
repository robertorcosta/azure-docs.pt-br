---
title: Versões com suporte – banco de dados do Azure para MySQL
description: Saiba quais versões do MySQL Server têm suporte no banco de dados do Azure para o serviço MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: ed767055c1227d39fc29ac77a44c96b19030ad28
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535853"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versões com suporte do servidor de Banco de Dados do Azure para MySQL

O Banco de Dados do Azure para MySQL foi desenvolvido a partir do [MySQL Community Edition](https://www.mysql.com/products/community/), usando o mecanismo InnoDB.

O MySQL usa o esquema de nomenclatura X.Y.Z. X é a versão principal, Y é a versão secundária e Z é a versão de correção de bug. Para obter mais informações sobre o esquema, confira a [documentação do MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> No serviço, um gateway é usado para redirecionar as conexões para as instâncias de servidor. Depois que a conexão for estabelecida, o cliente MySQL exibirá a versão do MySQL definida no gateway, não a versão real em execução na instância do servidor MySQL. Para determinar a versão da instância do servidor MySQL, use o `SELECT VERSION();` comando no prompt do MySQL.

No momento, o Banco de Dados do Azure para MySQL dá suporte às seguintes versões:

## <a name="mysql-version-56"></a>MySQL Versão 5.6

Versão de correção de bug: 5.6.47

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-57"></a>MySQL Versão 5.7

Versão de correção de bug: 5.7.29

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="mysql-version-80"></a>MySQL versão 8,0

Versão de correção de bug: 8.0.15

Consulte as notas de [versão](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) do MySQL para saber mais sobre melhorias e correções nesta versão.

## <a name="managing-updates-and-upgrades"></a>Gerenciar atualizações e upgrades
O serviço gerencia automaticamente a aplicação de patch para atualizações de versão de correção de bug. Por exemplo, 5.7.20 a 5.7.21.  

Atualmente, não há suporte para atualizações de versão principal e secundária. Por exemplo, não há suporte para a atualização do MySQL 5.6 para o MySQL 5.7. Caso deseje atualizar da versão 5.6 para a 5.7, faça um [despejo e restaure-a](./concepts-migrate-dump-restore.md) para um servidor que foi criado com a nova versão do mecanismo.

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre a política de controle de versão do banco de dados do Azure para MySQL, consulte [este documento](concepts-version-policy.md).
- Para obter informações sobre cotas e limitações de recursos específicas com base em sua **camada de serviço** , consulte [camadas de serviço](./concepts-pricing-tiers.md)
