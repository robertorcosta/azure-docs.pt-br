---
title: Key differences for Machine Learning Services (preview)
description: Este tópico descreve as principais diferenças entre os serviços do Machine Learning do Banco de Dados SQL do Azure (com R) e os serviços do Machine Learning do SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ms.openlocfilehash: 533e2b9e50a92cce1419da521d8cebc4955e4df6
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462124"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Key differences between Machine Learning Services in Azure SQL Database (preview) and SQL Server

The functionality of Azure SQL Database Machine Learning Services (with R) in  (preview) is similar to [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Below are some key differences.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Suporte de idiomas

O SQL Server tem suporte para R e Python por meio da [estrutura de extensibilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). O Banco de Dados SQL não dá suporte às duas linguagens. As principais diferenças são:

- O R é a única linguagem com suporte no Banco de Dados SQL. No momento, não há suporte para Python.
- A versão do R é 3.4.4.
- Não é necessário configurar `external scripts enabled` por meio de `sp_configure`. Quando você estiver [inscrito](sql-database-machine-learning-services-overview.md#signup), o machine learning estará habilitado para o banco de dados SQL.

## <a name="package-management"></a>Gerenciamento de pacotes

O gerenciamento e a instalação de pacotes R funcionam de maneira diferente entre o Banco de Dados SQL e o SQL Server. Essas diferenças são:

- Os pacotes R são instalados por meio de [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Os pacotes não podem realizar chamadas de rede de saída. This limitation is similar to the [default firewall rules for Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) in SQL Server, but can't be changed in SQL Database.
- Não há suporte para pacotes que dependem de runtimes externos (como Java) ou que precisam acessar APIs de OS para instalação ou uso.

## <a name="writing-to-a-temporary-table"></a>Writing to a temporary table

If you're using RODBC in Azure SQL Database, then you can't write to a temporary table, whether it's created inside or outside of the `sp_execute_external_script` session. The workaround is to use [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) and [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (with overwrite=FALSE and append="rows") to write to a global temporary table created before the `sp_execute_external_script` query.

## <a name="resource-governance"></a>Governança de recursos

Não é possível limitar os recursos de R por meio do [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e de pools de recursos externos.

During the public preview, R resources are set to a maximum of 20% of the SQL Database resources, and depend on which service tier you choose. Para saber mais, confira [Modelos de compra do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Insufficient memory error

If there is insufficient memory available for R, you will get an error message. Common error messages are:

- Unable to communicate with the runtime for 'R' script for request id: *******. Please check the requirements of 'R' runtime
- 'R' script error occurred during execution of 'sp_execute_external_script' with HRESULT 0x80004004. ...an external script error occurred: "..could not allocate memory (0 Mb) in C function 'R_AllocStringBuffer'"
- An external script error occurred: Error: cannot allocate vector of size.

Memory usage depends on how much is used in your R scripts and the number of parallel queries being executed. If you receive the errors above, you can scale your database to a higher service tier to resolve this.

## <a name="next-steps"></a>Próximos passos

- See the overview, [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md).
- To learn how to use R to query Azure SQL Database Machine Learning Services (preview), see the [Quickstart guide](sql-database-connect-query-r.md).
- To get started with some simple R scripts, see [Create and run simple R scripts in Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
