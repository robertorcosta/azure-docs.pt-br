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
ms.subservice: ''
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9f36fb952b21b058fb50dc567f714e8bdb665d6c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200322"
---
# <a name="getting-started-with-visual-studio-2019"></a>Introdução ao Visual Studio 2019

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
- [Segurança em nível de linha](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
  - Envie um [tíquete de suporte ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) para obter o recurso com suporte.
- [Mascaramento de dados dinâmicos](/sql/relational-databases/security/dynamic-data-masking?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest#defining-a-dynamic-data-mask)
   - Envie um [tíquete de suporte ou vote](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) para obter o recurso com suporte.

## <a name="next-steps"></a>Próximas etapas

Agora que você tem a versão mais recente do SSDT, você está pronto para [se conectar](sql-data-warehouse-query-visual-studio.md) ao seu pool do SQL.
