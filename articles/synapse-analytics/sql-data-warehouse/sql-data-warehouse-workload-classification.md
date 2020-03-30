---
title: Classificação de carga de trabalho
description: Orientação para usar a classificação para gerenciar recursos de concorrência, importância e computação para consultas no Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7661981f07799592f9fdfcab3fb402336d48b4d4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349968"
---
# <a name="azure-synapse-analytics-workload-classification"></a>Classificação da carga de trabalho do Azure Synapse Analytics

Este artigo explica o processo de classificação da carga de trabalho de atribuir um grupo de carga de trabalho e importância às solicitações recebidas com o SQL Analytics no Azure Synapse.

## <a name="classification"></a>classificação

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

A classificação de gerenciamento da carga de trabalho permite que as políticas de carga de trabalho sejam aplicadas às solicitações por meio da atribuição de classes de [recursos](resource-classes-for-workload-management.md#what-are-resource-classes) e [importância](sql-data-warehouse-workload-importance.md).

Embora existam muitas maneiras de classificar as cargas de trabalho de armazenamento de dados, a classificação mais simples e comum é a carga e a consulta. Você carrega dados com instruções de inserção, atualização e exclusão.  Você consulta os dados usando seleções. Uma solução de armazenamento de dados geralmente terá uma política de carga de trabalho para atividade de carga, como atribuir uma classe de recursos mais alta com mais recursos. Uma política de carga de trabalho diferente poderia ser aplicada a consultas, como menor importância em comparação com atividades de carga.

Você também pode subclassificar suas cargas de carga e consultar cargas de trabalho. A subclassificação lhe dá mais controle de suas cargas de trabalho. Por exemplo, as cargas de trabalho de consulta podem consistir em atualizações de cubos, consultas de painel ou consultas ad-hoc. Você pode classificar cada uma dessas cargas de trabalho de consulta com diferentes classes de recursos ou configurações de importância. A carga também pode se beneficiar da subclassificação. Grandes transformações podem ser atribuídas a classes de recursos maiores. Maior importância pode ser usada para garantir que os principais dados de vendas sejam carregadores antes de dados meteorológicos ou um feed de dados sociais.

Nem todas as declarações são classificadas, pois não exigem recursos ou precisam de importância para influenciar a execução.  Os comandos DBCC, BEGIN, COMMIT e ROLLBACK TRANSACTION não são classificados.

## <a name="classification-process"></a>Processo de classificação

A classificação para SQL Analytics no Azure Synapse é alcançada hoje atribuindo os usuários a uma função que tem uma classe de recursos correspondente atribuída a ele usando [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). A capacidade de caracterizar solicitações além de um login em uma classe de recursos é limitada com esse recurso. Um método mais rico para classificação está agora disponível com a sintaxe [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql)  Com essa sintaxe, os usuários do SQL Analytics podem atribuir importância `workload_group` e quanto recursos do sistema são atribuídos a uma solicitação através do parâmetro. 

> [!NOTE]
> A classificação é avaliada por solicitação. Várias solicitações em uma única sessão podem ser classificadas de forma diferente.

## <a name="classification-weighting"></a>Ponderação de classificação

Como parte do processo de classificação, a ponderação está em vigor para determinar qual grupo de carga de trabalho é atribuído.  O peso é o seguinte:

|Parâmetro do classificador |Peso   |
|---------------------|---------|
|NOME DO MEMBRO:USUÁRIO      |64       |
|NOME DO MEMBRO:FUNÇÃO      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

O `membername` parâmetro é obrigatório.  No entanto, se o nome do membro especificado for um usuário de banco de dados em vez de uma função de banco de dados, a ponderação para o usuário é maior e, portanto, esse classificador é escolhido.

Se um usuário for um membro de várias funções com classes de recursos diferentes atribuídos ou correspondentes em vários classificadores, o usuário receberá a atribuição de classe de recurso mais alta.  Esse comportamento é consistente com o comportamento de atribuição de classe de recursos existente.

## <a name="system-classifiers"></a>Classificadores do sistema

A classificação da carga de trabalho tem classificadores de carga de trabalho do sistema. Os classificadores do sistema mapeiam as adesões de atribuição de função de classe de recursos existentes para alocações de recursos de classe de recursos com importância normal. Os classificadores do sistema não podem ser descartados. Para visualizar os classificadores do sistema, você pode executar a consulta abaixo:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Misturando atribuições de classe de recursos com classificadores

Os classificadores de sistema criados em seu nome fornecem um caminho fácil para migrar para a classificação da carga de trabalho. O uso de mapeamentos de função de classe de recursos com precedência de classificação pode levar a uma classificação indevido à medida que você começa a criar novos classificadores com importância.

Considere o cenário a seguir.

- Um data warehouse existente tem um usuário de banco de dados DBAUser atribuído à função de classe de recursos largerc. A atribuição da classe de recursos foi feita com sp_addrolemember.
- O data warehouse agora é atualizado com o gerenciamento da carga de trabalho.
- Para testar a nova sintaxe de classificação, a função de banco de dados DBARole (da qual o DBAUser é membro), possui um classificador criado para eles mapeá-los para mediumrc e alta importância.
- Quando o DBAUser fizer login e executá-lo uma consulta, a consulta será atribuída ao biggerc. Porque um usuário tem prioridade sobre uma associação de papéis.

Para simplificar a má classificação, recomendamos que você remova mapeamentos de função de classe de recursos à medida que cria classificadores de carga de trabalho.  O código abaixo retorna as adesões de função de classe de recursos existentes.  Execute [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql) para cada nome de membro retornado da classe de recursos correspondente.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember ‘[Resource Class]’, membername
```

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a criação de um classificador, consulte o [CLASSIFIER DE CARGA DE TRABALHO (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-workload-classifier-transact-sql).  
- Consulte o Quickstart sobre como criar um classificador de carga de trabalho [Criar um classificador de carga de trabalho](quickstart-create-a-workload-classifier-tsql.md).
- Consulte os artigos de como configurar a [importância da carga de trabalho](sql-data-warehouse-how-to-configure-workload-importance.md) e como gerenciar e monitorar o gerenciamento da carga de [trabalho](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Confira [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) para exibir consultas e a importância atribuída.