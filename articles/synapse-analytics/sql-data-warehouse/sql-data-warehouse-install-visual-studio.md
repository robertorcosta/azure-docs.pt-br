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
ms.openlocfilehash: f83ae9c8290a52381c8087b46da959d4723d7f4e
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745156"
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

Consulte [Baixar Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) para baixar e instalar o Visual Studio **16.3 e acima**. Durante a instalação, selecione a carga de trabalho de armazenamento e processamento de dados. A instalação autônoma do SSDT não é mais necessária no Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Recursos não suportados no SSDT

Há momentos em que os lançamentos de recursos para synapse SQL podem não incluir suporte para SSDT. Os seguintes recursos não são suportados no momento:

- [Pontos de vista materializados](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Índices de columnstore agrupados ordenados](/sql/t-sql/statements/create-columnstore-index-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#examples--and-)
- [Declaração COPY](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Gerenciamento de carga de trabalho](sql-data-warehouse-workload-management.md) - grupos de carga de trabalho e classificadores
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Envie um [bilhete de apoio ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) para obter o recurso suportado.
- [Mascaramento de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Envie um [bilhete de apoio ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) para obter o recurso suportado.
- Não são suportadas [tabelas com restrições.](sql-data-warehouse-table-constraints.md#table-constraints) Para esses objetos de tabela, defina a ação de construção como "Nenhum".

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar](sql-data-warehouse-query-visual-studio.md) ao seu pool SQL.
