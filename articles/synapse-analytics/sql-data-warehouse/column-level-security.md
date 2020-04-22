---
title: O que é segurança em nível de coluna para o Azure Synapse?
description: O Column-Level Security permite que os clientes controlem o acesso às colunas de tabela do banco de dados com base no contexto de execução do usuário ou membros do grupo, simplificando o design e a codificação da segurança em seu aplicativo e permitindo que você implemente restrições no acesso à coluna.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/19/2020
ms.author: jrasnick
ms.reviewer: igorstan, carlrab
ms.custom: seo-lt-2019
tags: azure-synapse
ms.openlocfilehash: b0a783ad5db86ca783ff1cebceec8d77ab528047
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687933"
---
# <a name="column-level-security"></a>Segurança de Nível da Coluna

O Column-Level Security permite que os clientes controlem o acesso às colunas de tabela com base no contexto de execução do usuário ou na associação de grupo.

> [!VIDEO https://www.youtube.com/embed/OU_ESg0g8r8]
Desde que este vídeo foi [postado, a Segurança de Nível de Linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) ficou disponível para o Azure Synapse.

A segurança em nível de coluna simplifica o design e a codificação da segurança em seu aplicativo, permitindo restringir o acesso à coluna para proteger dados confidenciais. Por exemplo, garantir que usuários específicos possam acessar apenas certas colunas de uma tabela pertinente ao seu departamento. A lógica de restrição de acesso é localizado na camada de banco de dados, em vez de longe dos dados em outra camada de aplicativo. O banco de dados aplica as restrições de acesso toda vez que o acesso aos dados é tentado de qualquer nível. Essa restrição torna sua segurança mais confiável e robusta, reduzindo a área de superfície do seu sistema de segurança geral. Além disso, a segurança em nível de coluna também elimina a necessidade de introduzir visualizações para filtrar colunas para impor restrições de acesso aos usuários.

Você pode implementar a segurança em nível de coluna com a declaração [GRANT](/sql/t-sql/statements/grant-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL. Com este mecanismo, há suporte para autenticação SQL e o Azure Active Directory (AAD).

![cls](./media/column-level-security/cls.png)

## <a name="syntax"></a>Sintaxe

```syntaxsql
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

O exemplo a seguir `TestUser` mostra como `SSN` restringir `Membership` o acesso à coluna da tabela:

Criar `Membership` tabela com a coluna SSN usada para armazenar números de segurança social:

```sql
CREATE TABLE Membership
  (MemberID int IDENTITY,
   FirstName varchar(100) NULL,
   SSN char(9) NOT NULL,
   LastName varchar(100) NOT NULL,
   Phone varchar(12) NULL,
   Email varchar(100) NULL);
```

Permitir `TestUser` acessar todas as colunas, exceto a coluna SSN, que possui os dados confidenciais:

```sql
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO TestUser;
```

As consultas executadas como `TestUser` falharão se incluirem a coluna SSN:

```sql
SELECT * FROM Membership;

-- Msg 230, Level 14, State 1, Line 12
-- The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="use-cases"></a>Casos de uso

Alguns exemplos de como a segurança em nível de coluna está sendo usada hoje:

- Uma empresa de serviços financeiros permite que apenas gerentes de contas tenham acesso aos SSN (números do seguro social), números de telefone e outras PII (informações de identificação pessoal).
- Um provedor de saúde permite que apenas médicos e enfermeiros tenham acesso a prontuários sensíveis, impedindo que os membros do departamento de cobrança obtemsiem esses dados.
