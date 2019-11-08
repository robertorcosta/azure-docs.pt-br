---
title: O que é o Azure Synapse Analytics (antigo SQL DW)?
description: O Azure Synapse Analytics (antigo SQL DW) é um serviço de análise ilimitado que reúne data warehouse empresarial e análise de Big Data.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 07bf3070e42af8a03ce1fd7ea4445fc76557e8a3
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645510"
---
# <a name="what-is-azure-synapse-analytics-formerly-sql-dw"></a>O que é o Azure Synapse Analytics (antigo SQL DW)?

O Azure Synapse é um serviço de análise ilimitado que reúne data warehouse empresarial e análise de Big Data. Ele oferece a liberdade para consultar dados da forma que você quiser, usando recursos sob demanda sem servidor ou provisionados em escala. O Azure Synapse conecta esses dois mundos com uma experiência unificada para ingerir, preparar, gerenciar e fornecer dados para necessidades imediatas de BI e aprendizado de máquina

O Azure Synapse tem quatro componentes:
- Análise de SQL: Análise baseada em T-SQL completa, disponibilidade geral
    - Pool do SQL (pagamento por DWU provisionado) 
    - SQL sob demanda (pagamento por TB processado, versão prévia)
- Spark: Apache Spark profundamente integrado (versão prévia) 
- Integração de dados: Integração de dados híbridos (versão prévia)
- Estúdio: Experiência de usuário unificada.  (Visualização)

> [!NOTE]
> Para acessar os recursos de versão prévia do Azure Synapse, solicite o acesso [aqui](https://aka.ms/synapsepreview). A Microsoft fará a triagem de todas as solicitações e responderá assim que possível.

## <a name="sql-analytics-and-sql-pool-in-azure-synapse"></a>Análise de SQL e pool de SQL no Azure Synapse

A Análise de SQL refere-se aos recursos corporativos de data warehousing que estão disponíveis publicamente com o Azure Synapse. 

O pool do SQL representa uma coleção de recursos de análise que estão sendo provisionados ao usar a Análise de SQL. O tamanho do pool do SQL é determinado pelas DWU (unidades de data warehouse).

Importe big data com consultas T-SQL simples do [PolyBase](/sql/relational-databases/polybase/polybase-guide?view=sql-server-2017&viewFallbackFrom=azure-sqldw-latest) e, em seguida, use o poder do MPP para executar a análise de alto desempenho. À medida que você integrar e analisar, a Análise de SQL se tornará a única versão da verdade com que sua empresa poderá contar para obter informações mais rápidas e robustas.  

## <a name="key-component-of-a-big-data-solution"></a>Componente fundamental de uma solução de big data

Data warehouse é um componente fundamental de uma solução completa de big data baseada na nuvem.

![Solução de data warehouse](media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png) 

Em uma solução de dados de nuvem, os dados são incluídos nos repositórios de big data de uma variedade de fontes. Uma vez em um repositório de big data, os algoritmos do Hadoop, do Spark e de machine learning preparam e treinam os dados. Quando os dados estiverem prontos para análises complexas, a Análise de SQL usará o PolyBase para consultar os armazenamentos de big data. O PolyBase usa consultas T-SQL padrão para trazer os dados para as tabelas da Análise de SQL.
 
A Análise de SQL armazena dados em tabelas relacionais com armazenamento em colunas. Esse formato reduz consideravelmente os custos de armazenamento de dados e melhora o desempenho da consulta. Depois que os dados forem armazenados, você poderá executar análises em grande escala. Em comparação aos tradicionais sistemas de banco de dados, as consultas de análise são concluídas em segundos em vez de minutos, ou em horas em vez de dias. 

Os resultados da análise podem ir para aplicativos ou bancos de dados em todo o mundo. Os analistas de negócios podem então obter informações para tomar decisões de negócios bem informadas.

## <a name="next-steps"></a>Próximas etapas

- Explorar a [arquitetura do Azure Synapse](/azure/sql-data-warehouse/massively-parallel-processing-mpp-architecture)
- [Criar rapidamente um pool de SQL](create-data-warehouse-portal.md)
- [Carregue dados de exemplo][load sample data].
- Explore os [Vídeos](/azure/sql-data-warehouse/sql-data-warehouse-videos)

Ou, dê uma olhada em alguns desses outros recursos do Azure Synapse.  
* Pesquisar em [Blogs]
* Enviar [Solicitações de recursos]
* Pesquisar em [Blogs da Equipe Consultoria para Clientes]
* [Criar um tíquete de suporte]
* Pesquisar no [Fórum do MSDN]
* Pesquisar no [Fórum Stack Overflow]


<!--Image references-->
[1]: ./media/sql-data-warehouse-overview-what-is/dwarchitecture.png

<!--Article references-->
[Criar um tíquete de suporte]: ./sql-data-warehouse-get-started-create-support-ticket.md
[load sample data]: ./sql-data-warehouse-load-sample-databases.md
[create a data warehouse]: ./sql-data-warehouse-get-started-provision.md
[Migration documentation]: ./sql-data-warehouse-overview-migrate.md
[Azure Synapse Analytics solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[Integrated tools overview]: ./sql-data-warehouse-overview-integrate.md
[Backup and restore overview]: ./sql-data-warehouse-restore-database-overview.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md

<!--MSDN references-->

<!--Other Web references-->
[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN]: https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=AzureSQLDataWarehouse
[Fórum Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[SLA for Azure Synapse Analytics]: https://azure.microsoft.com/support/legal/sla/sql-data-warehouse/v1_0/
[Volume Licensing]: https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=37
[Service Level Agreements]: https://azure.microsoft.com/support/legal/sla/
