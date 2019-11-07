---
title: Perguntas frequentes sobre o Azure Synapse Analytics (anteriormente conhecido como SQL DW)
description: Este artigo lista as perguntas frequentes sobre o Azure Synapse Analytics (anteriormente conhecido como SQL DW) de clientes e desenvolvedores
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: f7b4f926bb9611d87c67276f754a6b596850b59d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645601"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Synapse Analytics (anteriormente conhecido como SQL DW)

## <a name="general"></a>Geral

P. O que é o Azure Synapse?

a. O Azure Synapse é um serviço de análise ilimitado que reúne data warehousing e análise de Big Data. Ele oferece a você a liberdade de consultar dados sobre seus termos, usando recursos sem servidor sob demanda ou provisionados em escala. O Azure Synapse traz esses dois mundos junto com uma experiência unificada para ingerir, preparar, gerenciar e fornecer dados para necessidades imediatas de BI e aprendizado de máquina. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. O que aconteceu com o Azure SQL Data Warehouse?

a. O Azure Synapse é um SQL Data Warehouse do Azure (SQL DW) evoluído. Nós tiramos a mesma data warehouse líder do setor em um nível totalmente novo de desempenho e recursos. Você pode continuar executando suas cargas de trabalho data warehouse existentes em produção hoje mesmo com o Azure Synapse e se beneficiar automaticamente dos novos recursos que estão em versão prévia. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. O que é o SQL Analytics?

a. A análise de SQL refere-se aos recursos corporativos de data warehousing que estão geralmente disponíveis com o Azure Synapse. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

P. Como fazer introdução ao Azure Synapse?

a. Você pode começar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/sql-data-warehouse/) ou [entre em contato com as vendas para obter mais informações](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

P. O que o Azure Synapse oferece para segurança de dados?

a. O Azure Synapse oferece várias soluções para proteger dados, como TDE e auditoria. Para saber mais, consulte [Segurança].

P. Onde posso descobrir quais padrões legais ou de negócios o Azure Synapse está em conformidade?

a. Visite a página [Conformidade da Microsoft] para ver várias ofertas de conformidade por produto, como SOC e ISO. Primeiro escolha por título de conformidade e, em seguida, expanda Azure na seção serviços de nuvem no escopo da Microsoft no lado direito da página para ver quais serviços estão em conformidade com o Azure Synapse.

P. Posso conectar Power BI?

a. Sim! Embora Power BI ofereça suporte à consulta direta com o Azure Synapse, ele não se destina a um grande número de usuários ou dados em tempo real. Para otimizar ainda mais o desempenho do Power BI, considere usar Power BI sobre o Azure Analysis Services ou o IaaS do Analysis Service.

P. Quais são os limites de capacidade da análise de SQL?

a. Consulte nossa página de [limites de capacidade] atuais. 

P. Por que Escalar/Pausar/Retomar está demorando tanto para mim?

a. Uma variedade de fatores pode influenciar o tempo de computação das operações de gerenciamento. Um caso comum para operações de execução longa é a reversão transacional. Quando uma operação de escala ou pausa é iniciada, todas as sessões de entrada são bloqueadas e as consultas são esvaziadas. Para deixar o sistema em um estado estável, as transações devem ser revertidas antes do início de uma operação. Quanto maior o número e maior o tamanho do log de transações, por mais tempo a operação ficará interrompida na restauração do sistema para um estado estável.

## <a name="user-support"></a>Suporte ao usuário

P. Tenho uma solicitação de recurso. Para qual local devo enviá-la?

a. Caso tenha uma solicitação de recurso, envie-a para nossa página do [UserVoice]

P. Como posso fazer?

a. Para obter ajuda no desenvolvimento com o Azure Synapse, você pode fazer perguntas em nossa página de [Stack Overflow] . 

P. Como fazer para enviar um tíquete de suporte?

a. Os [Tíquetes de Suporte] podem ser apresentados por meio do portal do Azure.

## <a name="sql-languagefeature-support"></a>Suporte a linguagem/recurso do SQL 

P. Quais tipos de DataTipo são suportados?

a. Consulte [tipos de dados].

P. Para quais recursos de tabela há suporte?

a. Muitos recursos têm suporte, aqueles que não têm suporte e estão documentados em [recursos de tabela sem suporte].

## <a name="tooling-and-administration"></a>Ferramentas e administração

P. Há suporte para projetos de Banco de Dados no Visual Studio?

a. Atualmente, não há suporte para projetos de banco de dados no Visual Studio. Se desejar votar para obter esse recurso, visite nossa [solicitação de recursos de projetos de Banco de Dados] do User Voice.

P. A análise do SQL dá suporte a APIs REST?

a. Sim. A maioria das funcionalidades REST que podem ser usadas com o banco de dados SQL também está disponível com a análise de SQL. É possível encontrar informações sobre API nas páginas de documentação do REST ou no [MSDN].


## <a name="loading"></a>Carregando

P. Para quais drivers de cliente há suporte?

a. O suporte ao driver para o DW pode ser encontrado na página [Cadeias de conexão]

P: quais formatos de arquivo são compatíveis com o polybase?

R: Orc, RC, Parquet e texto simples delimitado

P: quais fontes de dados posso me conectar usando o polybase? 

R: O [Repositório Azure Data Lake] e o [Blobs de armazenamento do Azure]

P: a aplicação de computação é possível ao se conectar a blobs de armazenamento do Azure ou ADLS? 

R: não, o polybase interage apenas com os componentes de armazenamento. 

P: Posso me conectar ao HDI?

R: O HDI pode usar o ADLS ou o WASB como a camada HDFS. Se você tiver como a camada HDFS, será possível carregar esse dados no SQL DW. No entanto, não é possível gerar o cálculo de aplicação para a instância HDI. 

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o Azure Synapse como um todo, consulte nossa página de [visão geral] .


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Cadeias de conexão]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Tíquetes de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Segurança]: ./sql-data-warehouse-overview-manage-security.md
[Conformidade da Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[limites de capacidade]: ./sql-data-warehouse-service-capacity-limits.md
[tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Recursos de tabela sem suporte]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Repositório Azure Data Lake]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Blobs de armazenamento do Azure]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Solicitação de recursos de projetos de Banco de Dados]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Visão geral]: ./sql-data-warehouse-overview-faq.md
