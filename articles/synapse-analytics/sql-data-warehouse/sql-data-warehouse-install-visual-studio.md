---
title: Instalar o Visual Studio 2019
description: Instalar o Visual Studio e o SSDT (ferramentas de desenvolvimento de SQL Server) para Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 3ab0188fa44eeb09e3981b8e1809832948963d47
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511634"
---
# <a name="getting-started-with-visual-studio-2019"></a>Guia de Introdução ao Visual Studio

O SSDT (Visual Studio **2019** SQL Server Data Tools) é uma única ferramenta que permite que você faça o seguinte:

- Conectar, consultar e desenvolver aplicativos
- Aproveite um pesquisador de objetos para explorar visualmente todos os objetos em seu modelo de dados, incluindo tabelas, exibições, procedimentos armazenados e etc.
- Gerar scripts de linguagem de definição de dados (DDL) T-SQL para seus objetos
- Desenvolva seu data warehouse usando uma abordagem baseada em estado com projetos de banco de dados SSDT
- Integre seu projeto de banco de dados com sistemas de controle do código-fonte, como git com Azure Repos
- Configurar pipelines de implantação e integração contínua com servidores de automação, como o Azure DevOps

## <a name="install-visual-studio-2019"></a>Instalar o Visual Studio 2019

Consulte [baixar o visual studio 2019](https://visualstudio.microsoft.com/downloads/) para baixar e instalar o visual Studio **16,3 e superior**. Durante a instalação, selecione a carga de trabalho de armazenamento e processamento de dados. A instalação autônoma do SSDT não é mais necessária no Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Recursos sem suporte no SSDT

Há ocasiões em que as versões de recursos do Synapse SQL podem não incluir suporte para SSDT. Atualmente, não há suporte para os seguintes recursos:


- [Gerenciamento de carga](sql-data-warehouse-workload-management.md) de trabalho-grupos de carga de trabalho e classificadores
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (incluindo funções com valor de tabela)
  - Envie um [tíquete de suporte ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) para obter o recurso com suporte.
  - Envie um [tíquete de suporte ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) para obter o recurso com suporte.
- Tabelas com uma [coluna de identidade](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)
- Determinados recursos do T-SQL, como:
   - Na cláusula *Group* na função de cadeia de caracteres [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql?view=sqlallproducts-allversions) .

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar](sql-data-warehouse-query-visual-studio.md) ao seu pool do SQL.
