---
title: Instalar o Visual Studio 2019
description: Instale o Visual Studio e o SQL Server Development Tools (SSDT) para synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: aa2f903611f52f8f7a8ede0040b592a7dddd0e89
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584461"
---
# <a name="getting-started-with-visual-studio-2019"></a>Começando com o Visual Studio 2019
Visual Studio **2019** SQL Server Data Tools (SSDT) é uma única ferramenta que permite que você faça o seguinte:

- Conecte, consulte e desenvolva aplicativos
- Aproveite um explorador de objetos para explorar visualmente todos os objetos do seu modelo de dados, incluindo tabelas, visualizações, procedimentos armazenados e etc.
- Gerar scripts de definição de dados T-SQL (DDL) para seus objetos
- Desenvolva seu data warehouse usando uma abordagem baseada no estado com projetos de banco de dados SSDT
- Integre seu projeto de banco de dados com sistemas de controle de origem, como o Git com o Azure Repos
- Configure pipelines de integração e implantação contínuas com servidores de automação, como o Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalar o Visual Studio 2019
Consulte [Baixar Visual Studio 2019][] para baixar e instalar o Visual Studio **16.3 e acima**. Durante a instalação, selecione a carga de trabalho de armazenamento e processamento de dados. A instalação autônoma do SSDT não é mais necessária no Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Recursos não suportados no SSDT

Há momentos em que os lançamentos de recursos para synapse SQL podem não incluir suporte para SSDT. Os seguintes recursos não são suportados no momento:

- [Pontos de vista materializados](https://docs.microsoft.com/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?view=azure-sqldw-latest) (em andamento)
- [Índices de columnstore agrupados ordenados](https://docs.microsoft.com/sql/t-sql/statements/create-columnstore-index-transact-sql?view=azure-sqldw-latest#examples--and-) (em andamento)
- [Declaração COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (em andamento)
- [Gerenciamento da carga de trabalho](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-management) - grupos de carga de trabalho e classificadores (em andamento)
- [Segurança em nível de linha](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?view=sql-server-ver15)
  - Envie um bilhete de apoio ou vote [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) para ter o recurso suportado.
- [Mascaramento de dados dinâmicos](https://docs.microsoft.com/sql/relational-databases/security/dynamic-data-masking?toc=%2Fazure%2Fsql-data-warehouse%2Ftoc.json&view=sql-server-2017#defining-a-dynamic-data-mask)
  - Envie um bilhete de apoio ou vote [aqui](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) para ter o recurso suportado. 
- [PREDITOR função](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=sql-server-ver15&viewFallbackFrom=azure-sqldw-latest) 
- Não são suportadas [tabelas com restrições.](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-table-constraints#table-constraints) Para esses objetos de tabela, defina a ação de construção como "Nenhum".

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar](sql-data-warehouse-query-visual-studio.md) ao seu pool SQL.




<!--Other-->

[Baixar o Visual Studio 2019]: https://visualstudio.microsoft.com/downloads/
[Installing Visual Studio]: https://msdn.microsoft.com/library/e2h7fzkw.aspx
[SSDT Download]: https://msdn.microsoft.com/library/mt204009.aspx
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
