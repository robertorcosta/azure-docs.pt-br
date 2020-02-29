---
title: O que é a segurança em nível de coluna para o Azure Synapse?
description: A segurança em nível de coluna permite que os clientes controlem o acesso a colunas de tabela de banco de dados com base no contexto de execução do usuário ou na associação de grupo, simplificando o design e codificação de segurança em seu aplicativo e permitindo que você implemente restrições na coluna às.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 02/05/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: aa9791f019436cc5c7effc9bce197d89131a6557
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78199964"
---
# <a name="column-level-security"></a>Segurança de Nível da Coluna

A segurança em nível de coluna permite que os clientes controlem o acesso a colunas de tabela com base no contexto de execução do usuário ou na associação de grupo.


> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Como esse vídeo foi lançado, a [segurança em nível de linha](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) ficou disponível para o Azure Synapse. 

A segurança em nível de coluna simplifica o design e a codificação de segurança em seu aplicativo, permitindo que você restrinja o acesso a colunas para proteger dados confidenciais. Por exemplo, garantir que usuários específicos possam acessar apenas certas colunas de uma tabela pertinente ao seu departamento. A lógica de restrição de acesso é localizado na camada de banco de dados, em vez de longe dos dados em outra camada de aplicativo. O banco de dados aplica as restrições de acesso toda vez que o acesso a dados é tentado em qualquer camada. Essa restrição torna sua segurança mais confiável e robusta, reduzindo a área de superfície do seu sistema de segurança geral. Além disso, a segurança em nível de coluna também elimina a necessidade de introduzir exibições para filtrar colunas para impor restrições de acesso aos usuários.

Você pode implementar a segurança em nível de coluna com a instrução [Grant](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql) T-SQL. Com este mecanismo, há suporte para autenticação SQL e o Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sintaxe

```sql
GRANT <permission> [ ,...n ] ON
    [ OBJECT :: ][ schema_name ]. object_name [ ( column [ ,...n ] ) ]
    TO <database_principal> [ ,...n ]
    [ WITH GRANT OPTION ]
    [ AS <database_principal> ]
<permission> ::=
    SELECT
  | UPDATE
<database_principal> ::=
      Database_user
    | Database_role
    | Database_user_mapped_to_Windows_User
    | Database_user_mapped_to_Windows_Group
```

## <a name="example"></a>Exemplo
O exemplo a seguir mostra como restringir `TestUser` de acessar a coluna `SSN` da tabela `Membership`:

Crie `Membership` tabela com a coluna SSN usada para armazenar números de seguro social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permita que `TestUser` acesse todas as colunas, exceto a coluna SSN, que tem os dados confidenciais:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

As consultas executadas como `TestUser` falharão se incluírem a coluna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de uso

Alguns exemplos de como a segurança em nível de coluna está sendo usada hoje:

- Uma empresa de serviços financeiros permite que apenas gerentes de contas tenham acesso aos SSN (números do seguro social), números de telefone e outras PII (informações de identificação pessoal).
- Um provedor de assistência médica permite que apenas médicos e horas tenham acesso a registros médicos confidenciais, impedindo que os membros do departamento de cobrança exibam esses dados.
