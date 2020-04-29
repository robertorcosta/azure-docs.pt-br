---
title: Perguntas frequentes sobre o Azure Synapse Analytics (anteriormente conhecido como SQL DW)
description: Este artigo lista as perguntas frequentes sobre o Azure Synapse Analytics (anteriormente conhecido como SQL DW) de clientes e desenvolvedores
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 260086d186f7e2b2d6f76c437057f5fbc8af39b5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81416082"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Synapse Analytics (anteriormente conhecido como SQL DW)

## <a name="general"></a>Geral

Q. O que é o Azure Synapse?

A. O Azure Synapse é um serviço de análise ilimitado que reúne data warehousing e análise de Big Data. Ele oferece a você a liberdade de consultar dados sobre seus termos, usando recursos sem servidor sob demanda ou provisionados em escala. O Azure Synapse conecta esses dois mundos com uma experiência unificada para ingerir, preparar, gerenciar e fornecer dados para necessidades imediatas de BI e de aprendizado de máquina. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. O que aconteceu com o Azure SQL Data Warehouse?

A. O Azure Synapse é um SQL Data Warehouse do Azure (SQL DW) evoluído. Nós tiramos o mesmo data warehouse líder do setor para um nível totalmente novo de desempenho e recursos. Você pode continuar executando suas cargas de trabalho de data warehouse existentes em produção com o Azure Synapse e se beneficiar automaticamente dos novos recursos, que estão em versão prévia. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. O que é o pool do SQL Synapse?

A. Synapse pool SQL refere-se aos recursos corporativos de data warehousing que estão geralmente disponíveis com o Azure Synapse. Para obter mais informações, consulte [o que é o Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Como fazer introdução ao Azure Synapse?

A. Você pode começar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/sql-data-warehouse/) ou [entre em contato com as vendas para obter mais informações](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

Q. O que o Azure Synapse oferece para segurança de dados?

A. O Azure Synapse oferece várias soluções para proteger dados, como TDE e auditoria. Para saber mais, consulte [Segurança](sql-data-warehouse-overview-manage-security.md).

Q. Onde posso descobrir quais padrões legais ou de negócios o Azure Synapse está em conformidade?

A. Visite a página [Conformidade da Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) para ver várias ofertas de conformidade por produto, como SOC e ISO.
Primeiro, escolha por título de conformidade. Em seguida, expanda Azure na seção serviços de nuvem no escopo da Microsoft no lado direito da página para ver quais serviços são compatíveis com o Azure Synapse.

Q. Posso conectar Power BI?

A. Sim. Embora Power BI ofereça suporte à consulta direta com o Azure Synapse, ele não se destina a um grande número de usuários ou dados em tempo real. Para otimizar ainda mais o desempenho do Power BI, considere usar Power BI sobre o Azure Analysis Services ou o IaaS do Analysis Service.

Q. Quais são os limites de capacidade do pool SQL Synapse?

A. Consulte nossa página de [limites de capacidade](sql-data-warehouse-service-capacity-limits.md) atuais.

Q. Por que Escalar/Pausar/Retomar está demorando tanto para mim?

A. Vários fatores podem influenciar o tempo para operações de gerenciamento de computação. Um caso comum para operações de longa execução é a reversão transacional. Quando uma operação de escala ou pausa é iniciada, todas as sessões de entrada são bloqueadas e as consultas são esvaziadas. Para deixar o sistema em um estado estável, as transações devem ser revertidas antes do início de uma operação. Quanto maior o número e maior o tamanho do log de transações, por mais tempo a operação ficará interrompida na restauração do sistema para um estado estável.

## <a name="user-support"></a>Suporte ao usuário

Q. Tenho uma solicitação de recurso. Para qual local devo enviá-la?

A. Caso tenha uma solicitação de recurso, envie-a para nossa página do [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

Q. Como posso fazer?

A. Para obter ajuda com o desenvolvimento com o Azure Synapse, você pode fazer perguntas em nossa página de [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw) .

Q. Como fazer para enviar um tíquete de suporte?

A. Os [Tíquetes de Suporte](sql-data-warehouse-get-started-create-support-ticket.md) podem ser apresentados por meio do portal do Azure.

## <a name="sql-languagefeature-support"></a>Suporte a linguagem/recurso do SQL

Q. Há suporte para quais tipos de dados?

A. Consulte [tipos de dados](sql-data-warehouse-tables-data-types.md).

Q. Para quais recursos de tabela há suporte?

A. Há suporte para muitos recursos. Recursos que não têm suporte podem ser encontrados em [recursos de tabela sem suporte](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Ferramentas e administração

Q. O pool SQL Synapse dá suporte a APIs REST?

A. Sim. A maioria das funcionalidades REST que podem ser usadas com o banco de dados SQL também está disponível com o pool SQL Synapse. Você pode encontrar informações de API em [bancos](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)de dados ou páginas de documentação REST.

## <a name="loading"></a>Carregando

Q. Para quais drivers de cliente há suporte?

A. O suporte de driver para o pool SQL Synapse pode ser encontrado na página [cadeias de conexão](../sql/connection-strings.md)

P: quais formatos de arquivo são compatíveis com o polybase?

R: Orc, RC, Parquet e texto simples delimitado

P: quais fontes de dados posso me conectar usando o polybase?

R: [Azure data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) e [blobs de armazenamento do Azure](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

P: a aplicação de computação é possível ao se conectar a blobs de armazenamento do Azure ou ADLS?

R: não, o polybase interage apenas com os componentes de armazenamento.

P: Posso me conectar ao HDI?

R: O HDI pode usar o ADLS ou o WASB como a camada HDFS. Se você tiver o como sua camada HDFS, poderá carregar esses dados em um pool SQL Synapse. No entanto, não é possível gerar o cálculo de aplicação para a instância HDI.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Synapse como um todo, consulte nossa página de [visão geral](sql-data-warehouse-overview-faq.md) .
