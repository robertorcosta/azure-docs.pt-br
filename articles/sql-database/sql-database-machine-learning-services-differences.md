---
title: Principais diferenças para serviços de aprendizagem de máquina (visualização)
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
ROBOTS: NOINDEX
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453142"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Principais diferenças entre os Serviços de Aprendizado de Máquina no Banco de Dados SQL (preview) do Azure e no SQL Server

A funcionalidade do Azure SQL Database Machine Learning Services (com R) in (visualização) é semelhante aos Serviços de Aprendizagem de [Máquina do Servidor SQL](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Abaixo estão algumas das principais diferenças.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Suporte ao idioma

O SQL Server tem suporte para R e Python por meio da [estrutura de extensibilidade](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). O Banco de Dados SQL não dá suporte às duas linguagens. As principais diferenças são:

- O R é a única linguagem com suporte no Banco de Dados SQL. No momento, não há suporte para Python.
- A versão do R é 3.4.4.
- Não é necessário configurar `external scripts enabled` por meio de `sp_configure`.

## <a name="package-management"></a>Gerenciamento de pacotes

O gerenciamento e a instalação de pacotes R funcionam de maneira diferente entre o Banco de Dados SQL e o SQL Server. Essas diferenças são:

- Os pacotes R são instalados por meio de [sqlmlutils](https://github.com/Microsoft/sqlmlutils) ou [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Os pacotes não podem realizar chamadas de rede de saída. Essa limitação é semelhante às [regras de firewall padrão para Serviços de Aprendizado de Máquina](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) no SQL Server, mas não pode ser alterada no Banco de Dados SQL.
- Não há suporte para pacotes que dependem de runtimes externos (como Java) ou que precisam acessar APIs de OS para instalação ou uso.

## <a name="writing-to-a-temporary-table"></a>Escrevendo para uma mesa temporária

Se você estiver usando RODBC no Banco de Dados SQL do Azure, então você não pode `sp_execute_external_script` escrever para uma tabela temporária, seja ela criada dentro ou fora da sessão. A solução alternativa é usar [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) e [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (com overwrite=FALSE e append="rows") `sp_execute_external_script` para escrever em uma tabela temporária global criada antes da consulta.

## <a name="resource-governance"></a>Governança de recursos

Não é possível limitar os recursos de R por meio do [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) e de pools de recursos externos.

Durante a visualização pública, os recursos R são definidos para um máximo de 20% dos recursos do Banco de Dados SQL e dependem de qual nível de serviço você escolher. Para saber mais, confira [Modelos de compra do Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Erro de memória insuficiente

Se houver memória insuficiente disponível para R, você receberá uma mensagem de erro. As mensagens de erro comuns são:

- Não é possível comunicar com o tempo de execução do script 'R' para id de solicitação: *******. Verifique os requisitos do tempo de execução 'R'
- O erro de script 'R' ocorreu durante a execução de 'sp_execute_external_script' com HRESULT 0x80004004. ... ocorreu um erro de script externo: ".. não foi possível alocar memória (0 Mb) na função C 'R_AllocStringBuffer'"
- Ocorreu um erro de script externo: Erro: não é possível alocar vetor de tamanho.

O uso da memória depende de quanto é usado em seus scripts R e do número de consultas paralelas sendo executadas. Se você receber os erros acima, você pode dimensionar seu banco de dados para um nível de serviço mais alto para resolver isso.

## <a name="next-steps"></a>Próximas etapas

- Veja a visão geral, [Azure SQL Database Machine Learning Services with R (preview)](sql-database-machine-learning-services-overview.md).
- Para saber como usar o R para consultar o Azure SQL Database Machine Learning Services (preview), consulte o [guia Quickstart](sql-database-connect-query-r.md).
- Para começar com alguns scripts R simples, consulte [Criar e executar scripts R simples no Azure SQL Database Machine Learning Services (visualização)](sql-database-quickstart-r-create-script.md).
