---
title: Segurança ao nível da coluna
description: Segurança de Nível de Coluna (CLS) permite aos clientes controlar o acesso às colunas da tabela de banco de dados com base no contexto de execução do usuário ou sua participação no grupo. A CLS simplifica o design e codificação de segurança em seu aplicativo. A CLS permite implementar restrições de acesso à linha de dados.
services: sql-data-warehouse
author: julieMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: security
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
ms.openlocfilehash: 85f705022a0ff5970d30c61206d4f2631254b7ce
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077097"
---
# <a name="column-level-security"></a>Segurança de Nível da Coluna
Segurança de Nível de Coluna (CLS) permite aos clientes controlar o acesso às colunas da tabela de banco de dados com base no contexto de execução do usuário ou sua participação no grupo.
Atualizar para o vídeo abaixo-como esse vídeo foi lançado, a [segurança em nível de linha](/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc&view=sql-server-2017) também está disponível em SQL data warehouse. 
> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]

A CLS simplifica o design e codificação de segurança em seu aplicativo. A CLS permite implementar restrições no acesso da coluna para proteger dados confidenciais. Por exemplo, garantir que usuários específicos possam acessar apenas certas colunas de uma tabela pertinente ao seu departamento. A lógica de restrição de acesso está localizada na camada do banco de dados, em vez de estar longe dos dados em outra camada de aplicativo. O banco de dados aplica as restrições de acesso sempre que houver uma tentativa de acessar esses dados em qualquer camada. Essa restrição torna o sistema de segurança mais robusto e confiável, reduzindo a área de superfície do sistema de segurança. Além disso, o CLS também elimina a necessidade de introduzir modos de exibição para filtrar colunas para impor restrições de acesso em usuários.

Você pode implementar CLS com a instrução T-SQL [GRANT](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql). Com este mecanismo, há suporte para autenticação SQL e o Azure Active Directory (AAD).

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
O exemplo a seguir mostra como restringir 'TestUser' de acessar a coluna 'SSN' da tabela 'Membership':

Crie tabela de 'Membership' com coluna SSN usada para armazenar números de previdência social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permitir 'TestUser' para acessar todas as colunas, exceto a coluna SSN com dados confidenciais:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

Consultas executadas como 'TestUser' irão falhar se elas incluírem a coluna SSN:

```sql
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de uso
Alguns exemplos de como CLS está sendo usado atualmente:
- Uma empresa de serviços financeiros permite que apenas gerentes de contas tenham acesso aos SSN (números do seguro social), números de telefone e outras PII (informações de identificação pessoal).
- Um provedor de saúde permite apenas médicos e enfermeiros terem acesso a registros médicos confidenciais enquanto não permite membros do departamento de cobrança ver estes dados.
