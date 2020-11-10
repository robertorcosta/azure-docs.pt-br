---
title: Perguntas frequentes – Azure Synapse Analytics (versão prévia dos workspaces)
description: Perguntas frequentes do Azure Synapse Analytics (versão prévia dos workspaces)
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: d7e8ea231dd6db9eab6cf9115f6ce2e62893371a
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305402"
---
# <a name="azure-synapse-analytics-workspaces-preview-frequently-asked-questions"></a>Perguntas frequentes sobre o Azure Synapse Analytics (versão prévia dos workspaces)

Neste guia, você encontrará as perguntas mais frequentes sobre o Synapse Analytics.

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="general"></a>Geral

### <a name="q-what-is-azure-synapse-analytics"></a>P: O que é o Azure Synapse Analytics?

A: o Azure Synapse é uma plataforma de dados integrada para BI, IA e inteligência contínua. Ele conecta vários runtimes de análise, como SQL e Apache Spark, por meio de uma só plataforma que fornece um modo unificado de:

- Proteger seus recursos de análise, incluindo rede e gerenciamento de acesso de logon único a artefatos do pool, de dados e de desenvolvimento.
- Monitorar e otimizar rapidamente, depurar e responder a eventos que ocorrem em suas atividades do workspace em qualquer camada.
- Gerenciar seus metadados nos mecanismos. Crie uma tabela do Pool do Apache Spark sem servidor. Ela ficará disponível automaticamente nos seus bancos de dados do Azure Synapse.
- Interagir com os dados por meio de uma experiência do usuário unificada. O Synapse Studio reúne desenvolvedores de Big Data, engenheiros de dados, DBAs, analistas de dados e cientistas de dados na mesma plataforma.

Para saber mais, confira [O que é o Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics/overview-what-is).

### <a name="q-what-are-the-main-components-of-azure-synapse-analytics"></a>P: Quais são os principais componentes do Azure Synapse Analytics?

A: O Azure Synapse traz as seguintes funcionalidades:

- As funcionalidades de análise são oferecidas por meio do pool de SQL dedicado ou do pool de SQL sem servidor (versão prévia).
- Pool do Apache Spark sem servidor (versão prévia) com suporte completo para Scala, Python, Spark, SQL e C#
- Fluxo de dados que oferece uma experiência de transformação de Big Data sem código
- Integração de dados e orquestração para integrar seus dados e operacionalizar todo o desenvolvimento de código
- Studio para acessar todas essas funcionalidades por meio de uma só interface do usuário da Web

### <a name="q-how-does-azure-synapse-analytics-relate-to-azure-sql-data-warehouse"></a>P: Qual é a relação entre o Azure Synapse Analytics e o SQL Data Warehouse do Azure?

A: O Azure Synapse Analytics é uma evolução do SQL Data Warehouse do Azure em uma plataforma de análise, que inclui o pool de SQL dedicado como a solução de data warehouse. Essa plataforma combina exploração de dados, ingestão, transformação, preparação e uma camada de análise de serviço.

## <a name="use-cases"></a>Casos de uso

### <a name="q-how-do-i-rename-a-published-artifact-dataset-notebook-sql-script-and-so-on-in-azure-synapse"></a>P: Como renomear um artefato publicado (conjunto de dados, notebook, script sql e assim por diante) no Azure Synapse?

A: Para renomear um arquivo de artefato publicado, primeiro clone o arquivo e renomeie o novo arquivo como você preferir. Será necessário atualizar manualmente todas as referências do artefato para o novo nome de arquivo e excluir o antigo.

### <a name="q-what-is-a-good-use-case-for-dedicated-sql-pool"></a>P: Qual é um caso de uso ideal para o pool de SQL dedicado?

A: O pool de SQL dedicado é o centro das suas necessidades de data warehouse. É a solução de data warehouse líder em [preço/desempenho](https://azure.microsoft.com/services/sql-data-warehouse/compare/). O pool de SQL dedicado é a solução de data warehouse na nuvem líder do setor porque nela você pode:

- fornecer uma variedade grande e mista de cargas de trabalho sem impacto no desempenho, graças à alta simultaneidade e ao isolamento da carga de trabalho
- proteger seus dados com facilidade por meio de recursos avançados, que vão da segurança de rede ao acesso refinado
- beneficiar-se de uma ampla gama de ecossistemas

### <a name="q-what-is-a-good-use-case-for-serverless-apache-spark-pool-in-azure-synapse"></a>P: Qual é um bom caso de uso para o Pool do Apache Spark sem servidor no Azure Synapse?

A: Nossa primeira meta é fornecer uma ótima experiência de Engenharia de Dados para transformar os dados do lake em lote ou fluxo. A integração rígida e simples à nossa orquestração de dados simplifica a operacionalização do seu trabalho de desenvolvimento.

Outro caso de uso do Apache Spark permite que um Cientista de Dados:

- extraia um recurso
- explore os dados
- treine um modelo

### <a name="q-what-is-a-good-use-case-for-serverless-sql-pool-in-azure-synapse"></a>P: Qual é um bom caso de uso para o pool de SQL sem servidor no Azure Synapse?

A: O pool de SQL sem servidor é um serviço de consulta dos dados no data lake. Ele permite que você democratize o acesso a todos os seus dados fornecendo uma sintaxe T-SQL conhecida para consultar os dados in-loco, sem a necessidade de copiar ou carregar dados em um repositório especializado.

Alguns exemplos de caso de uso incluem os seguintes:

- descoberta e exploração básica: fornece a analistas de dados, cientistas de dados emergentes e engenheiros de dados um roteiro fácil para os primeiros insights sobre os dados que residem no data lake com esquema nas consultas T-SQL lidas
- data warehouse lógico: os analistas de dados podem executar a expressividade completa da linguagem T-SQL para consultar e analisar diretamente os dados que residem no Armazenamento do Azure e usar ferramentas de BI conhecidas (por exemplo, Azure Analysis Services, Power BI Premium etc.) para atualizar os dashboards executando novamente consultas do Starlight Query
- ETL de "consulta única": permite que os engenheiros de dados transformem dados baseados no Armazenamento do Azure de um formato para outro, filtrem esses dados, façam agregações etc. no modo de processamento altamente paralelo, persistam os resultados da consulta no Armazenamento do Azure e disponibilize-os imediatamente para processamento adicional no Starlight Query ou em outros serviços de interesse

### <a name="q-what-is-a-good-use-case-for-data-flow-in-azure-synapse"></a>P: Qual é um caso de uso ideal para o fluxo de dados no Azure Synapse?

A: O fluxo de dados permite que os engenheiros de dados desenvolvam a lógica de transformação de dados gráficos sem codificação. Os fluxos de dados resultantes são executados como atividades na integração de dados e na orquestração. As atividades de fluxo de dados podem ser operacionalizadas por meio das funcionalidades existentes de agendamento, controle, fluxo e monitoramento.

## <a name="security-and-access"></a>Segurança e acesso

A: A experiência de logon único de ponta a ponta é um processo de autenticação importante no Synapse Analytics. São obrigatórios o gerenciamento e a aprovação da identidade por meio de uma integração completa do Azure AD.

### <a name="q-how-do-i-get-access-to-files-and-folders-in-the-adls-gen2"></a>P: Como fazer para obter acesso a arquivos e pastas no ADLS Gen2?

A: O acesso a arquivos e pastas é atualmente gerenciado por meio do ADLS Gen2. Para obter mais informações, confira [Controle de acesso no Data Lake Storage](../storage/blobs/data-lake-storage-access-control.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="q-can-i-use-third-party-business-intelligence-tools-to-access-azure-synapse-analytics"></a>P: Posso usar ferramentas de business intelligence de terceiros para acessar o Azure Synapse Analytics?

A: Sim, você pode usar os seus aplicativos de negócios de terceiros, como Tableau e Power BI, para se conectar ao pool de SQL dedicado e ao pool de SQL sem servidor. O Spark dá suporte ao IntelliJ.

## <a name="next-steps"></a>Próximas etapas

- [Criar um workspace](quickstart-create-workspace.md)
- [Usar o Synapse Studio](quickstart-synapse-studio.md)
- [Criar um pool de SQL dedicado](quickstart-create-sql-pool-portal.md)
- [Usar o pool de SQL sem servidor](quickstart-sql-on-demand.md)
- [Criar um Pool do Apache Spark sem servidor](quickstart-create-apache-spark-pool-portal.md) 
