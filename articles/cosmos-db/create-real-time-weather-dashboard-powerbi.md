---
title: Crie um painel em tempo real usando o Azure Cosmos DB, a azure Analysis Services e o Power BI
description: Aprenda a criar um painel meteorológico ao vivo no Power BI usando o Azure Cosmos DB e o Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376587"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Crie um painel em tempo real usando o Azure Cosmos DB e o Power BI

Este artigo descreve as etapas necessárias para criar um painel meteorológico ao vivo no Power BI usando o Azure Cosmos DB e o Azure Analysis Services. O painel Power BI exibirá gráficos para mostrar informações em tempo real sobre temperatura e chuva em uma região.

## <a name="reporting-scenarios"></a>Relatórios de cenários

Existem várias maneiras de configurar painéis de relatórios sobre dados armazenados no Azure Cosmos DB. Dependendo dos requisitos de obsoleto e do tamanho dos dados, a tabela a seguir descreve a configuração de relatórios para cada cenário:


|Cenário |Instalação |
|---------|---------|
|1. Gerando relatórios ad-hoc (sem atualização)    |  [Conector Power BI Azure Cosmos DB com modo de importação](powerbi-visualize.md)       |
|2. Geração de relatórios ad-hoc com atualização periódica   |  [Conector Power BI Azure Cosmos DB com modo de importação (atualização periódica programada)](powerbi-visualize.md)       |
|3. Relatórios sobre grandes conjuntos de dados (< 10 GB)     |  Conector POWER BI Azure Cosmos DB com atualização incremental       |
|4. Reportando em tempo real em grandes conjuntos de dados    |  Conector Power BI Azure Analysis Services com consulta direta + Serviços de Análise Do Azure (conector Azure Cosmos DB)       |
|5. Relatórios sobre dados ao vivo com agregados     |  [Conector Power BI Spark com consulta direta + Conector Azure Databricks + Cosmos DB Spark.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Relatórios sobre dados ao vivo com agregados em grandes conjuntos de dados   |  Conector Power BI Azure Analysis Services com consulta direta + Serviços de Análise do Azure + Conector Azure Databricks + Cosmos DB Spark conector.       |

Os cenários 1 e 2 podem ser facilmente configurados usando o conector Azure Cosmos DB Power BI. Este artigo descreve as configurações para os cenários 3 e 4.

### <a name="power-bi-with-incremental-refresh"></a>Power BI com atualização incremental

O POWER BI tem um modo onde a atualização incremental pode ser configurada. Esse modo elimina a necessidade de criar e gerenciar partições do Azure Analysis Services. A atualização incremental pode ser configurada para filtrar apenas as atualizações mais recentes em grandes conjuntos de dados. No entanto, este modo funciona apenas com o serviço Power BI Premium que tem uma limitação de conjunto de dados de 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector de análise Power BI Azure + Serviços de Análise do Azure

O Azure Analysis Services fornece uma plataforma totalmente gerenciada como um serviço que hospeda modelos de dados de nível corporativo na nuvem. Conjuntos de dados maciços podem ser carregados do Azure Cosmos DB para os Serviços de Análise do Azure. Para evitar consultar todo o conjunto de dados o tempo todo, os conjuntos de dados podem ser subdivididos em partições do Azure Analysis Services, que podem ser atualizadas independentemente em diferentes frequências.

## <a name="power-bi-incremental-refresh"></a>Atualização incremental do POWER BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos no Azure Cosmos DB

Configure um oleoduto de ingestão para carregar [dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive) para o Azure Cosmos DB. Você pode configurar um trabalho [da Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db.md) para carregar periodicamente os dados meteorológicos mais recentes no Azure Cosmos DB usando o dissipador HTTP Source e Cosmos DB.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Conecte o POWER BI ao Azure Cosmos DB

1. **Conecte a conta do Azure Cosmos ao Power BI** - Abra o Power BI Desktop e use o conector Azure Cosmos DB para selecionar o banco de dados e o contêiner certos.

   ![Conector do Power BI do Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Configure a atualização incremental** - Siga as etapas na [atualização incremental com](/power-bi/service-premium-incremental-refresh) o artigo Power BI para configurar a atualização incremental para o conjunto de dados. Adicione os parâmetros **RangeStart** e **RangeEnd,** conforme mostrado na captura de tela a seguir:

   ![Configurar parâmetros de alcance](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Uma vez que o conjunto de dados tem uma coluna Data que está em forma de texto, os parâmetros **RangeStart** e **RangeEnd** devem ser transformados para usar o seguinte filtro. No painel **Do Editor Avançado,** modifique o texto da consulta a seguir para filtrar as linhas com base nos parâmetros RangeStart e RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Dependendo de qual coluna e tipo de dados está presente no conjunto de dados de origem, você pode alterar os campos RangeStart e RangeEnd de acordo com

   
   |Propriedade  |Tipo de dados  |Filtrar  |
   |---------|---------|---------|
   |_ts     |   Numérico      |  [_ts] > Duração.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) e [_ts] < Duração.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0)))       |
   |Data (por exemplo:- 2019-08-19)     |   String      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") e [Document.date] < DateTime.ToText (RangeEnd,"yyyy-MM-dd")        |
   |Data (por exemplo:- 2019-08-11 12:00:00)   |  String       |  [Document.date]> DateTime.ToText(RangeStart", yyyy-mm-dd HH:mm:ss") e [Document.date] < DateTime.ToText(RangeEnd,","yyyy-mm-dd HH:mm:ss")       |


1. **Defina a política de atualização** - Defina a política de atualização navegando até a guia de atualização **incremental** no menu **de contexto** da tabela. Defina a política de atualização para atualizar **todos os dias** e armazenar os dados do último mês.

   ![Definir política de atualização](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Ignore o aviso que diz que *a consulta M não pode ser confirmada como dobrada*. O conector Azure Cosmos DB dobra as consultas do filtro.

1. **Carregue os dados e gere os relatórios** - Usando os dados que você carregou anteriormente, crie os gráficos para informar sobre temperatura e chuva.

   ![Carregar dados e gerar relatórios](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Publique o relatório para o Prêmio Power BI** - Como a atualização incremental é apenas um recurso Premium, a caixa de diálogo de publicação só permite a seleção de um espaço de trabalho na capacidade Premium. A primeira atualização pode demorar mais para importar os dados históricos. As atualizações de dados subseqüentes são muito mais rápidas porque usam atualização incremental.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Conector de análise Power BI Azure + Serviços de Análise do Azure 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Ingerir dados meteorológicos no Azure Cosmos DB 

Configure um oleoduto de ingestão para carregar [dados meteorológicos](https://catalog.data.gov/dataset/local-weather-archive) para o Azure Cosmos DB. Você pode configurar um trabalho aazure Data Factory (ADF) para carregar periodicamente os dados meteorológicos mais recentes no Azure Cosmos DB usando a Fonte HTTP e o Bios DB Sink.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Conecte os serviços de análise do Azure à conta do Azure Cosmos

1. **Crie um novo cluster** - de Serviços de Análise do Azure[Crie uma instância dos serviços de análise do Azure](../analysis-services/analysis-services-create-server.md) na mesma região que a conta do Azure Cosmos e o cluster Databricks.

1. **Crie um novo Projeto Tabular de Serviços de Análise no Visual Studio** -  [Instale o SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) e crie um projeto tabular de serviços de análise no Visual Studio.

   ![Criar projeto de Serviços de Análise do Azure](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Escolha a instância **de espaço de trabalho integrado** e o conjunto do Nível de Compatibilidade para **SQL Server 2017 / Azure Analysis Services (1400)**

   ![Azure Analysis Services designer de modelos tabular](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Adicione a fonte de dados Azure Cosmos DB** - Navegue **para modelos**> **fontes de** > dados**Nova fonte de dados** e adicione a fonte de dados Azure Cosmos DB, conforme mostrado na captura de tela a seguir:

   ![Adicionar fonte de dados Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Conecte-se ao Azure Cosmos DB fornecendo a **conta URI,** nome do **banco de dados**e o nome do **contêiner**. Agora você pode ver que os dados do contêiner Azure Cosmos são importados para o Power BI.

   ![Visualizar dados do Azure Cosmos DB](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Construa o modelo Serviços de Análise** - Abra o editor de consulta, execute as operações necessárias para otimizar o conjunto de dados carregados:

   * Extrair apenas as colunas relacionadas ao clima (temperatura e chuva)

   * Extrair as informações do mês da tabela. Esses dados são úteis na criação de partições conforme descrito na próxima seção.

   * Converta as colunas de temperatura em número

   A expressão M resultante é a seguinte:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Além disso, altere o tipo de dados das colunas de temperatura para Decimal para garantir que esses valores possam ser plotados no Power BI.

1. **Criar partições de análise do Azure** - Crie partições nos Serviços de Análise do Azure para dividir o conjunto de dados em partições lógicas que podem ser atualizadas independentemente e em diferentes frequências. Neste exemplo, você cria duas partições que dividiriam o conjunto de dados nos dados do mês mais recente e tudo mais.

   ![Criar partições de serviços de análise](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Crie as duas partições a seguir nos Serviços de Análise do Azure:

   * **Último Mês** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Histórico** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Implantar o modelo no Servidor de Análise do Azure** - Clique com o botão direito do mouse no projeto Azure Analysis Services e escolha **Implantar**. Adicione o nome do servidor no painel **de propriedades Deployment Server.**

   ![Implantar modelo de Serviços de Análise do Azure](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Configure atualizações e mesclagem de partição** - O Azure Analysis Services permite o processamento independente de partições. Uma vez que queremos que a partição **do último mês** seja constantemente atualizada com os dados mais recentes, defina o intervalo de atualização para 5 minutos. Não é necessário atualizar os dados na partição histórica. Além disso, você precisa escrever algum código para consolidar a partição do último mês para a partição histórica e criar uma nova partição do mês mais recente.


## <a name="connect-power-bi-to-analysis-services"></a>Conecte o BI de energia aos serviços de análise

1. **Conecte-se ao Servidor de Análise do Azure usando o conector de banco de dados azure Analysis Services** - Escolha o **modo Live** e conecte-se à instância dos Serviços de Análise do Azure, conforme mostrado na captura de tela a seguir:

   ![Obter dados do Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Carregue os dados e gere relatórios** - Usando os dados que você carregou anteriormente, crie gráficos para relatar a temperatura e as chuvas. Uma vez que você está criando uma conexão ao vivo, as consultas devem ser executadas nos dados do modelo Azure Analysis Services que você implantou na etapa anterior. Os gráficos de temperatura serão atualizados dentro de cinco minutos após o carregamento dos novos dados no Azure Cosmos DB.

   ![Carregue os dados e gere relatórios](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Power BI, confira [Introdução ao Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).

* [Conectar o Qlik Sense ao Azure Cosmos DB e visualizar seus dados](visualize-qlik-sense.md)