---
title: Azure Synapse Analytics (anteriormente SQL DW) Perguntas frequentes
description: Este artigo lista perguntas freqüentes sobre o Azure Synapse Analytics (anteriormente SQL DW) de clientes e desenvolvedores
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: dcefe6a6144c9a8f9c6ab7daf5b34b2d81a73c3f
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743010"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Azure Synapse Analytics (anteriormente SQL DW) Perguntas frequentes

## <a name="general"></a>Geral

Q. O que é o Azure Synapse?

a. O Azure Synapse é um serviço de análise ilimitado que reúne armazenamento de dados e análises de Big Data. Ele lhe dá a liberdade de consultar dados em seus termos, usando recursos sob demanda ou sem servidor - em escala. O Azure Synapse une esses dois mundos com uma experiência unificada para ingerir, preparar, gerenciar e servir dados para necessidades imediatas de BI e aprendizado de máquina. Para obter mais informações, consulte [O que é a Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. O que aconteceu com o Azure SQL Data Warehouse?

a. Azure Synapse é o Azure SQL Data Warehouse (SQL DW) evoluído. Nós tomamos o mesmo data warehouse líder do setor para um novo nível de desempenho e capacidades. Você pode continuar executando suas cargas de trabalho existentes do data warehouse em produção com o Azure Synapse e se beneficiar automaticamente dos novos recursos, que estão em visualização. Para obter mais informações, consulte [O que é Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. O que é pool Synapse SQL?

a. O pool Synapse SQL refere-se aos recursos de armazenamento de dados corporativos que geralmente estão disponíveis com o Azure Synapse. Para obter mais informações, consulte [O que é a Azure Synapse Analytics](sql-data-warehouse-overview-what-is.md).

Q. Como eu começar com o Azure Synapse?

a. Você pode começar com uma [conta gratuita do Azure](https://azure.microsoft.com/free/sql-data-warehouse/) ou [entrar em contato com as vendas para obter mais informações](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html).

Q. O que o Azure Synapse oferece para segurança de dados?

a. O Azure Synapse oferece várias soluções para proteger dados como TDE e auditoria. Para saber mais, consulte [Segurança](sql-data-warehouse-overview-manage-security.md).

Q. Onde posso descobrir quais padrões legais ou comerciais o Azure Synapse está em conformidade?

a. Visite a página [Conformidade da Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) para ver várias ofertas de conformidade por produto, como SOC e ISO.
Primeiro, escolha pelo título de Conformidade. Em seguida, expanda o Azure na seção de serviços de nuvem no escopo da Microsoft no lado direito da página para ver quais serviços são compatíveis com o Azure Synapse.

Q. Posso conectar o Power BI?

a.  Sim! Embora o Power BI suporte consulta direta com o Azure Synapse, ele não é destinado a um grande número de usuários ou dados em tempo real. Para otimizar ainda mais o desempenho do Power BI, considere usar o Power BI em cima dos Serviços de Análise do Azure ou do Serviço de Análise IaaS.

Q. Quais são os limites de capacidade do pool Synapse SQL?

a. Consulte nossa página de [limites de capacidade](sql-data-warehouse-service-capacity-limits.md) atuais.

Q. Por que Escalar/Pausar/Retomar está demorando tanto para mim?

a. Vários fatores podem influenciar o tempo para operações de gerenciamento de computação. Um caso comum para operações de longa duração é a reversão transacional. Quando uma operação de escala ou pausa é iniciada, todas as sessões de entrada são bloqueadas e as consultas são esvaziadas. Para deixar o sistema em um estado estável, as transações devem ser revertidas antes do início de uma operação. Quanto maior o número e maior o tamanho do log de transações, por mais tempo a operação ficará interrompida na restauração do sistema para um estado estável.

## <a name="user-support"></a>Suporte ao usuário

Q. Tenho uma solicitação de recurso. Para qual local devo enviá-la?

a. Caso tenha uma solicitação de recurso, envie-a para nossa página do [UserVoice](https://feedback.azure.com/forums/307516-sql-data-warehouse)

Q. Como posso fazer?

a. Para obter ajuda no desenvolvimento com o Azure Synapse, você pode fazer perguntas em nossa página [Stack Overflow.](https://stackoverflow.com/questions/tagged/azure-sqldw)

Q. Como fazer para enviar um tíquete de suporte?

a. Os [Tíquetes de Suporte](sql-data-warehouse-get-started-create-support-ticket.md) podem ser apresentados por meio do portal do Azure.

## <a name="sql-languagefeature-support"></a>Suporte a linguagem/recurso do SQL

Q. Há suporte para quais tipos de dados?

a. Ver [tipos de dados](sql-data-warehouse-tables-data-types.md).

Q. Para quais recursos de tabela há suporte?

a. Muitos recursos são suportados. Os recursos que não são suportados podem ser encontrados em [Recursos de tabela não suportados](sql-data-warehouse-tables-data-types.md).

## <a name="tooling-and-administration"></a>Ferramentas e administração

Q. O pool Synapse SQL suporta APIs REST?

a. Sim. A maioria das funcionalidades REST que podem ser usadas com o SQL Database também está disponível com o pool Synapse SQL. Você pode encontrar informações da API em páginas de documentação REST ou [bancos de dados](/rest/api/sql/databases?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="loading"></a>Carregando

Q. Para quais drivers de cliente há suporte?

a. O suporte ao driver para pool Synapse SQL pode ser encontrado na página [Strings de conexão](sql-data-warehouse-connection-strings.md)

P: Quais formatos de arquivo são suportados pelo PolyBase?

R: Orc, RC, Parquet e texto simples delimitado

P: Quais fontes de dados posso me conectar usando o PolyBase?

A: [Azure Data Lake Storage](sql-data-warehouse-load-from-azure-data-lake-store.md) e [Azure Storage Blobs](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

P: A flexão de computação é possível ao se conectar a Blobs de armazenamento Azure ou ADLS?

A: Não, o PolyBase interage apenas com os componentes de armazenamento.

P: Posso me conectar ao HDI?

R: O HDI pode usar o ADLS ou o WASB como a camada HDFS. Se você tiver qualquer camada de HDFS, você pode carregar esses dados em um pool Synapse SqL. No entanto, não é possível gerar o cálculo de aplicação para a instância HDI.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure Synapse como um todo, consulte nossa página [Visão Geral.](sql-data-warehouse-overview-faq.md)
