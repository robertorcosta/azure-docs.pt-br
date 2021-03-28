---
title: 'Azure Synapse Analytics: guia de migração'
description: Siga este guia para migrar seus bancos de dados para o pool SQL dedicado do Azure Synapse Analytics.
ms.service: synapse-analytics
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: julieMSFT
ms.author: jrasnick
ms.reviewer: jrasnick
ms.date: 03/10/2021
ms.openlocfilehash: e27e7e0bb4616508c8f137260bde68de9420f9cf
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644969"
---
# <a name="migrating-a-data-warehouse-to-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Migrando um data warehouse para um pool SQL dedicado no Azure Synapse Analytics 
As seções a seguir fornecem uma visão geral do que está envolvido na migração de uma solução de data warehouse existente para o pool SQL dedicado do Azure Synapse Analytics.

## <a name="overview"></a>Visão geral
Antes de migrar, você deve verificar se o Azure Synapse Analytics é a melhor solução para sua carga de trabalho. O Azure Synapse Analytics é um sistema distribuído projetado para executar análises em grandes volumes de dados. Migrar para o Azure Synapse Analytics requer algumas alterações de design que não são difíceis de entender, mas que podem levar algum tempo para implementar. Se sua empresa exige um data warehouse corporativo, os benefícios valem a pena. No entanto, se você não precisar do poder da análise de Synapse do Azure, será mais econômico usar [SQL Server](https://docs.microsoft.com/sql/sql-server/) ou o [banco de dados SQL do Azure](https://docs.microsoft.com/azure/azure-sql/).

Considere o uso do Azure Synapse Analytics quando você:
- Ter um ou mais terabytes de dados.
- Planeje executar análises em quantidades substanciais de dados.
- Precisa da capacidade de dimensionar a computação e o armazenamento.
- Quiser economizar nos custos pausando os recursos de computação quando não precisar deles.

Em vez da análise de Synapse do Azure, considere outras opções para cargas de trabalho operacionais (OLTP) que têm:
- Leituras e gravações de alta frequência.
- Um grande número de seleções singleton.
- Grandes volumes de inserções de linha única.
- Necessidades de processamento linha a linha.
- Formatos incompatíveis (JSON, XML).

## <a name="azure-synapse-pathway"></a>Azure Synapse Pathway
Um dos clientes de bloqueadores críticos enfrentam a tradução de seus objetos de banco de dados ao migrar de um sistema para outro. O [caminho de Synapse do Azure](https://docs.microsoft.com/sql/tools/synapse-pathway/azure-synapse-pathway-overview) ajuda você a atualizar para uma plataforma moderna data warehouse automatizando a tradução de objetos do data warehouse existente. Ele é uma ferramenta gratuita, intuitiva e fácil de usar que automatiza a conversão de código, permitindo executar uma migração mais rápida para o Azure Synapse Analytics.

## <a name="prerequisites"></a>Pré-requisitos
# <a name="migrate-from-sql-server"></a>[Migrar do SQL Server](#tab/migratefromSQLServer)
Para migrar seu SQL Server data warehouse para o Azure Synapse Analytics, verifique se você tem os seguintes pré-requisitos: 

- Uma carga de trabalho de data warehouse ou análise 
- Baixe a ferramenta de [caminho de Synapse do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) mais recente para migrar objetos SQL Server para objetos Synapse do Azure.
- Um [pool SQL dedicado](../get-started-create-workspace.md) no espaço de trabalho Synapse do Azure. 

# <a name="migrate-from-netezza"></a>[Migrar do Netezza](#tab/migratefromNetezza)
Para migrar sua data warehouse do Netezza para o Azure Synapse Analytics, verifique se você tem os seguintes pré-requisitos: 

- Baixe a ferramenta de [caminho de Synapse do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) mais recente para migrar objetos SQL Server para objetos Synapse do Azure.
- Um [pool SQL dedicado](../get-started-create-workspace.md) no espaço de trabalho Synapse do Azure.

Para obter mais informações [, visite soluções do Azure Synapse Analytics e migração para Netezza](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-netezza).

# <a name="migrate-from-snowflake"></a>[Migrar do floco de neve](#tab/migratefromSnowflake)
Para migrar seu data warehouse de floco de neve para o Azure Synapse Analytics, verifique se você tem os seguintes pré-requisitos: 

- Baixe a ferramenta de [caminho de Synapse do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=102787) mais recente para migrar objetos floco de neve para objetos Synapse do Azure.
- Um [pool SQL dedicado](../get-started-create-workspace.md) no espaço de trabalho Synapse do Azure. 

# <a name="migrate-from-oracle"></a>[Migrar do Oracle](#tab/migratefromOracle)
Para migrar seu data warehouse Oracle para o Azure Synapse Analytics, verifique se você tem os seguintes pré-requisitos: 

- Uma carga de trabalho de data warehouse ou análise 
- Baixe o SSMA para Oracle para converter objetos Oracle em SQL Server. Consulte [migrando bancos de dados Oracle para SQL Server (OracleToSQL)](https://docs.microsoft.com/sql/ssma/oracle/migrating-oracle-databases-to-sql-server-oracletosql) para obter mais informações. 
- Baixe a versão mais recente da ferramenta de [caminho Synapse do Azure](https://www.microsoft.com/download/details.aspx?id=102787) para migrar objetos SQL Server para objetos Synapse do Azure.
- Um [pool SQL dedicado](../get-started-create-workspace.md) no espaço de trabalho Synapse do Azure. 

Para obter mais informações [, visite soluções do Azure Synapse Analytics e migração para um data warehouse Oracle](https://docs.microsoft.com/azure/cloud-adoption-framework/migrate/azure-best-practices/analytics/analytics-solutions-exadata).

---

## <a name="pre-migration"></a>Pré-migração
Depois de tomar a decisão de migrar uma solução existente para o Azure Synapse Analytics, é importante planejar a migração antes de começar. Um objetivo principal do planejamento é garantir que os dados, os esquemas de tabela e o código sejam compatíveis com a análise de Synapse do Azure. Há algumas diferenças de compatibilidade entre o sistema atual e o Azure Synapse Analytics, que você precisará resolver. Além disso, a migração de grandes quantidades de dados para o Azure leva tempo. O planejamento cuidadoso acelerará o processo de obtenção de seus dados para o Azure. Outro objetivo importante do planejamento é ajustar seu design para garantir que sua solução Aproveite ao máximo o alto desempenho de consulta que o Azure Synapse Analytics foi projetado para fornecer. A criação de data warehouses para escala apresenta padrões de design exclusivos, portanto, as abordagens tradicionais nem sempre são as melhores. Embora alguns ajustes de design possam ser feitos após a migração, fazer alterações no início do processo irá poupar tempo mais tarde.

## <a name="migrate"></a>Migrar
A execução de uma migração bem-sucedida exige que você migre os esquemas, o código e os dados da tabela. Para obter diretrizes mais detalhadas sobre esses tópicos, consulte:
- O artigo [considerar o design da tabela](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-overview).
- O artigo [considera a alteração do código](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-develop#development-recommendations-and-coding-techniques).
- O artigo [migra seus dados](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/design-elt-data-loading).
- O artigo [considerar o gerenciamento de carga de trabalho](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-workload-management).

## <a name="additional-resources"></a>Recursos adicionais 
- O gato (equipe de consultoria para clientes) tem algumas diretrizes excelentes do Azure Synapse Analytics (anteriormente conhecido como SQL DW) publicadas como postagens no blog. Certifique-se de dar uma olhada em seu artigo, [migrando dados para o Azure SQL data warehouse na prática](https://docs.microsoft.com/archive/blogs/sqlcat/migrating-data-to-azure-sql-data-warehouse-in-practice), para obter diretrizes adicionais sobre a migração.

## <a name="migration-assets-from-real-world-engagements"></a>Ativos de migração de compromissos do mundo real
Para obter assistência adicional com a conclusão desse cenário de migração, confira os recursos a seguir, que foram desenvolvidos para dar suporte a um projeto de migração do mundo real.

| Título/link                              | Descrição                                                                                                                       |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| [Modelo e Ferramenta de Avaliação de Carga de Trabalho de Dados](https://github.com/Microsoft/DataMigrationTeam/tree/master/Data%20Workload%20Assessment%20Model%20and%20Tool) | Essa ferramenta dá sugestão das plataformas de destino de "melhor ajuste", da preparação para a nuvem e do nível de correção de aplicativo/banco de dados para uma determinada carga de trabalho. Ela oferece um cálculo simples, com um único clique, e oferece a geração de relatórios que ajudam muito a acelerar avaliações de grandes volumes fornecendo um processo de decisão de plataforma de destino uniforme e automatizado. |
| [Tratamento de problemas de codificação de dados ao carregar dados no Azure Synapse Analytics](https://azure.microsoft.com/en-us/blog/handling-data-encoding-issues-while-loading-data-to-sql-data-warehouse/) | Este blog destina-se a fornecer informações sobre alguns dos problemas de codificação de dados que você pode encontrar ao usar o polybase para carregar dados para SQL Data Warehouse. Este artigo também fornece algumas opções que você pode usar para superar esses problemas e carregar os dados com êxito. |
| [Obtendo tamanhos de tabela no pool SQL dedicado do Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Getting%20table%20sizes%20in%20SQL%20DW.pdf) | Uma das principais tarefas que um arquiteto deve executar é obter métricas sobre um novo ambiente após a migração: coletando tempos de carregamento do local para a nuvem, coletando tempos de carregamento do polybase, etc. Dessas tarefas, uma das mais importantes é determinar o tamanho do armazenamento em SQL Data Warehouse em comparação com a plataforma atual do cliente. |
| [Utilitário para mover os logons SQL Server locais para o Azure Synapse Analytics](https://github.com/Microsoft/DataMigrationTeam/tree/master/IP%20and%20Scripts/MoveLogins) | Um script do PowerShell que cria um script de comando T-SQL para recriar logons e Selecionar usuários de banco de dados de um SQL Server "local" para um serviço de PaaS do SQL do Azure. A ferramenta permite o mapeamento automático de contas do Windows AD para contas do Azure AD ou pode fazer pesquisas de UPN para cada logon no Windows Active Directory local. A ferramenta também move SQL Server logons nativos. As funções de servidor e banco de dados personalizadas são inseridas em script, bem como associação de função e permissões de usuário e função de banco de dados. Os bancos de dados independentes ainda não têm suporte e apenas um subconjunto de possíveis SQL Server permissões são incluídos no script; ou seja, as permissões Grant com Grant não têm suporte (árvores de permissão complexas). Mais detalhes estão disponíveis no documento de suporte e o script tem comentários para facilitar a compreensão. |

A equipe de engenharia de SQL de dados desenvolveu esses recursos. O principal compromisso desta equipe é desbloquear e acelerar a modernização complexa para projetos de migração da plataforma de dados para a plataforma de dados do Azure da Microsoft.

## <a name="videos"></a>Vídeos
- Observe como o [Walgreens migrou seu sistema de inventário de varejo](https://www.youtube.com/watch?v=86dhd8N1lH4) com cerca de 100 TB de dados de Netezza para a análise de Synapse do Azure (anteriormente conhecido como SQL DW) em tempo recorde. 