---
title: Diferenças de recursos do T-SQL no Azure Synapse SQL
description: Lista de recursos do Transact-SQL que estão disponíveis no Synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 6b4c44f0097b2896a063f3f3922760860e1d4a22
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84118305"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Recursos do Transact-SQL compatíveis com o Azure Synapse SQL

O Azure Synapse SQL é um serviço analítico de Big Data que permite consultar e analisar dados usando a linguagem T-SQL. Use o dialeto padrão em conformidade com ANSI da linguagem SQL usado no SQL Server e no Banco de Dados SQL do Azure para análise de dados. 

A linguagem Transact-SQL é usada no Synapse SQL sem servidor e o modelo provisionado pode referenciar diferentes objetos e tem algumas diferenças no conjunto de recursos compatíveis. Nesta página, você poderá encontrar diferenças de alto nível da linguagem Transact-SQL entre os modelos de consumo do Synapse SQL.

## <a name="database-objects"></a>Objetos de banco de dados

Os modelos de consumo do SQL do Synapse permitem que você use diferentes objetos de banco de dados. A comparação dos tipos de objeto compatíveis é mostrada na seguinte tabela:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Tabelas** | [Sim](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não, o modelo sem servidor pode consultar somente dados externos colocados no [Armazenamento do Azure](#storage-options) |
| **Exibições** | [Sim](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). As exibições podem usar [elementos de linguagem de consulta](#query-language) que estão disponíveis no modelo provisionado. | [Sim](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). As exibições podem usar [elementos de linguagem de consulta](#query-language) que estão disponíveis no modelo sem servidor. |
| **Esquemas** | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Tabelas temporárias** | [Sim](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Procedimentos** | [Sim](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Funções** | [Sim](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Gatilhos** | Não | Não |
| **Tabelas externas** | [Sim](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Confira os [formatos de dados](#data-formats) compatíveis. | [Sim](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Confira os [formatos de dados](#data-formats) compatíveis. |
| **Consultas de cache** | Sim, vários formatos (cache baseado em SSD, em memória, cache do conjunto de resultados). Além disso, há suporte para a exibição materializada | Não |
| **Variáveis de tabela** | [Não](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), use tabelas temporárias | Não |
| **[Distribuição de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Sim | Não |
| **[Índices da tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Sim | Não |
| **[Partições de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Sim | Não |
| **[Estatísticas](develop-tables-statistics.md)**            | Sim | Sim |
| **[Gerenciamento de carga de trabalho, classes de recursos e controle de simultaneidade](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim    | Não |

## <a name="query-language"></a>Idioma de consulta

As linguagens de consulta usadas no Synapse SQL podem ter diferentes recursos compatíveis, dependendo do modelo de consumo. A seguinte tabela descreve as diferenças mais importantes de linguagem de consulta nos dialetos Transact-SQL:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Instrução SELECT** | Sim. Não há suporte para as cláusulas de consulta Transact-SQL [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Sim. Não há suporte para as cláusulas de consulta Transact-SQL [FOR XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e para as dicas de consulta. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) e [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) podem ser usados para consultar objetos do sistema (não dados externos). |
| **Instrução INSERT** | Sim | Não |
| **Instrução UPDATE** | Sim | Não |
| **Instrução DELETE** | Sim | Não |
| **Instrução MERGE** | Sim | Não |
| **[Transações](develop-transactions.md)** | Sim | Não |
| **[Rótulos](develop-label.md)** | Sim | Não |
| **Carregamento de dados** | Sim. O utilitário preferencial é a instrução [COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), mas o sistema dá suporte ao carregamento BULK (BCP) e à instrução [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) para o carregamento de dados. | Não |
| **Exportação de dados** | Sim. Usando a instrução [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Sim. Usando a instrução [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Types** | Sim, todos os tipos Transact-SQL, exceto [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text e image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todos os tipos Transact-SQL, exceto os [cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text e image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Spatial Types](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [sql\_variant](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e Table |
| **Consultas entre bancos de dados** | Não | Sim, incluindo a instrução [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Funções internas (análise)** | Sim, todas as funções [analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), de conversão, de [data e hora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), lógicas e [matemáticas](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do Transact-SQL, exceto [CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todas as funções [analíticas](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), de conversão, de [data e hora](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), lógicas e [matemáticas](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do Transact-SQL. |
| **Funções internas (texto)** | Sim. Todas as funções Transact-SQL de [cadeia de caracteres](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e de ordenação, exceto [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim. Todas as funções Transact-SQL de [cadeia de caracteres](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e de ordenação. |
| **Funções internas com valor de tabela** | Sim, as [funções de conjunto de linhas do Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), exceto [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, as [funções de conjunto de linhas do Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), exceto [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Agregações** |  Agregações internas do Transact-SQL, exceto [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Agregações internas do Transact-SQL, exceto [STRING\_AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Operadores** | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), exceto [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, todos os [operadores Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Controle do fluxo** | Sim. Todas as [instruções Transact-SQL de controle de fluxo](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), exceto [CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim. Todas as [instruções Transact-SQL de controle de fluxo](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), exceto [RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) e a consulta SELECT na condição `WHILE (...)` |
| **Instruções DDL (CREATE, ALTER e DROP)** | Sim. Todas as instruções DDL Transact-SQL aplicáveis aos tipos de objeto compatíveis | Sim. Todas as instruções DDL Transact-SQL aplicáveis aos tipos de objeto compatíveis |

## <a name="security"></a>Segurança

O Synapse SQL permite que você use recursos de segurança internos para proteger seus dados e controlar o acesso. A tabela a seguir compara as diferenças de alto nível entre os modelos de consumo do Synapse SQL.

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Logons** | N/D (há suporte apenas a usuários independentes em bancos de dados) | Sim |
| **Usuários** |  N/D (há suporte apenas a usuários independentes em bancos de dados) | Sim |
| **[Usuários independentes](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Sim. **Observação:** apenas um usuário do Azure AD pode ser um administrador irrestrito | Sim |
| **Autenticação de nome de usuário/senha do SQL**| Sim | Sim |
| **Autenticação do AAD (Azure Active Directory)**| Sim, usuários do Azure AD | Sim, logons e usuários do Azure AD |
| **Autenticação de passagem do AAD (Azure Active Directory) de armazenamento** | Sim | Sim |
| **Autenticação de token SAS de armazenamento** | Não | Sim, usando [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) em [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ou [CREDENTIAL](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) no nível da instância. |
| **Autenticação da chave de acesso de armazenamento** | Sim, usando [DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) em [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Autenticação da [Identidade Gerenciada](../security/synapse-workspace-managed-identity.md) de armazenamento** | Sim, usando a [credencial da identidade de serviço gerenciado](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Sim, usando a credencial da `Managed Identity`. |
| **Autenticação da identidade de aplicativo de armazenamento** | [Sim](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Permissões – nível do objeto** | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários/aos logons nos objetos do sistema que são compatíveis |
| **Permissões – nível do esquema** | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários/aos logons no esquema | Sim, incluindo a capacidade de CONCEDER, NEGAR e REVOGAR permissões aos usuários/aos logons no esquema |
| **Permissões – [nível do banco de dados](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Sim | Sim |
| **Permissão – [nível do servidor](/sql/relational-databases/security/authentication-access/server-level-roles)** | Não | Sim, há suporte para sysadmin e outras funções de servidor |
| **Permissões: [Segurança no Nível da Coluna](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Sim | Sim |
| **Funções/grupos** | Sim (no escopo do banco de dados) | Sim (no escopo do servidor e do banco de dados) |
| **Funções de &amp; identidade e segurança** | Algumas funções e alguns operadores de segurança Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` e `OPEN/CLOSE MASTER KEY` | Algumas funções e alguns operadores de segurança Transact-SQL: `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS` e `REVERT`. As funções de segurança não podem ser usadas para consultar dados externos (armazene o resultado em uma variável que possa ser usada na consulta).  |
| **DATABASE SCOPED CREDENTIAL** | Sim | Sim |
| **SERVER SCOPED CREDENTIAL** | Não | Sim |
| **Segurança em nível de linha** | [Sim](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Não |
| **TDE (Transparent Data Encryption)** | [Sim](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Não | 
| **Descoberta e classificação de dados** | [Sim](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Avaliação de Vulnerabilidade** | [Sim](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **Proteção Avançada contra Ameaças** | [Sim](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Auditoria** | [Sim](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Não |
| **[Regras de firewall](../security/synapse-workspace-ip-firewall.md)**| Sim | Sim |
| **[Ponto de extremidade privado](../security/synapse-workspace-managed-private-endpoints.md)**| Sim | Sim |

O pool de SQL e o SQL sob demanda usam a linguagem Transact-SQL padrão para consultar dados. Para obter as diferenças detalhadas, confira a [Referência da linguagem Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Ferramentas

Use várias ferramentas para se conectar ao Synapse SQL e consultar dados.

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Synapse Studio** | Sim, scripts SQL | Sim, scripts SQL |
| **Power BI** | Sim | [Sim](tutorial-connect-power-bi-desktop.md) |
| **Azure Analysis Services** | Sim | Sim |
| **Azure Data Studio** | Sim | Sim, versão 1.18.0 ou superior. Há suporte para scripts SQL e SQL Notebooks. |
| **SQL Server Management Studio** | Sim | Sim, versão 18.5 ou superior |

> [!NOTE]
> Você pode usar o SSMS para se conectar ao SQL sob demanda (versão prévia) e consultar. Ele tem compatibilidade parcial da versão 18.5 em diante. Você pode usá-lo somente para se conectar e consultar.

A maioria dos aplicativos usa a linguagem Transact-SQL padrão pode consultar os modelos de consumo provisionado e sem servidor do Synapse SQL.

## <a name="storage-options"></a>Opções de armazenamento

Os dados analisados podem ser armazenados em vários tipos de armazenamento. A seguinte tabela lista todas as opções de armazenamento disponíveis:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Armazenamento interno** | Sim | Não |
| **Azure Data Lake v2** | Sim | Sim |
| **Armazenamento de Blobs do Azure** | Sim | Sim |
| **Armazenamento analítico do Azure Cosmos DB** | Não | Sim, usando o [Link do Synapse](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (na [versão prévia restrita](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |

## <a name="data-formats"></a>Formatos de dados

Os dados analisados podem ser armazenados em vários formatos de armazenamento. A seguinte tabela lista todos os formatos de dados disponíveis que podem ser analisados:

|   | Provisionado | Sem servidor |
| --- | --- | --- |
| **Delimitado** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim](query-single-csv-file.md) |
| **CSV** | Sim (não há suporte a delimitadores de vários caracteres) | [Sim](query-single-csv-file.md) |
| **Parquet** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Sim](query-parquet-files.md), incluindo arquivos com [tipos aninhados](query-parquet-nested-types.md) |
| **Hive ORC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **Hive RC** | [Sim](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Não |
| **JSON** | Sim | [Sim](query-json-files.md) |
| **Avro** | Não | Não |
| **[Delta-lake](https://delta.io/)** | Não | Não |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Não | Não |

## <a name="next-steps"></a>Próximas etapas
Encontre mais informações sobre as melhores práticas do pool de SQL e do SQL sob demanda nos seguintes artigos:

- [Melhores práticas do pool de SQL](best-practices-sql-pool.md)
- [Melhores práticas do SQL sob demanda](best-practices-sql-on-demand.md)
