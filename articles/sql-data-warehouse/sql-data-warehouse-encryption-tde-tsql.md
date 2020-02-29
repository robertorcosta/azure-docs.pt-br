---
title: Transparent Data Encryption (T-SQL)
description: TDE (Transparent Data Encryption) no Azure Synapse Analytics (T-SQL)
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/30/2019
ms.author: jrasnick
ms.reviewer: rortloff
ms.custom: seo-lt-2019
ms.openlocfilehash: bd6f40b8389284c1932a2f16a70060cd56e412fb
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78195798"
---
# <a name="get-started-with-transparent-data-encryption-tde"></a>Introdução ao Transparent Data Encryption (TDE)
> [!div class="op_single_selector"]
> * [Visão geral de segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

## <a name="required-permissions"></a>Permissões necessárias
Para habilitar a TDE (Transparent Data Encryption), você deve ser um administrador ou um membro da função dbmanager.

## <a name="enabling-encryption"></a>Habilitando a criptografia
Siga estas etapas para habilitar o TDE:

1. Conecte ao banco de dados *mestre* no servidor que está hospedando o banco de dados que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION ON;
```

## <a name="disabling-encryption"></a>Desabilitando a criptografia
Siga estas etapas para desabilitar o TDE:

1. Conecte-se ao banco de dados *mestre* que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
ALTER DATABASE [AdventureWorks] SET ENCRYPTION OFF;
```

> [!NOTE]
> Um pool SQL em pausa deve ser retomado antes de fazer alterações nas configurações de TDE.
> 
> 

## <a name="verifying-encryption"></a>Verificando a criptografia
Para verificar o status de criptografia, siga as etapas abaixo:

1. Conecte-se ao banco de dados *mestre* ou de instância que usa um logon de administrador ou de um membro da função **dbmanager** no banco de dados mestre
2. Execute a instrução a seguir para criptografar o banco de dados.

```sql
SELECT
    [name],
    [is_encrypted]
FROM
    sys.databases;
```

Um resultado de ```1``` indica um banco de dados criptografado, ```0``` indica um banco de dados não criptografado.

## <a name="encryption-dmvs"></a>DMVs de criptografia
* [sys.databases][sys.databases] 
* [sys.dm_pdw_nodes_database_encryption_keys][sys.dm_pdw_nodes_database_encryption_keys]

<!--Anchors-->
[Transparent Data Encryption (TDE)]: https://msdn.microsoft.com/library/bb934049.aspx
[sys.databases]: https://msdn.microsoft.com/library/ms178534.aspx  
[sys.dm_pdw_nodes_database_encryption_keys]: https://msdn.microsoft.com/library/mt203922.aspx  

<!--Image references-->

<!--Link references-->
