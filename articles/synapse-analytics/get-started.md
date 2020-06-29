---
title: 'Tutorial: Introdução ao Azure Synapse Analytics'
description: Neste tutorial, você aprenderá as etapas básicas para configurar e usar o Azure Synapse Analytics.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 05/19/2020
ms.openlocfilehash: daa8b594b06203c7de9a9b462be469dd71ed2e49
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84791853"
---
# <a name="get-started-with-azure-synapse-analytics"></a>Introdução ao Azure Synapse Analytics

Este tutorial guia você pelas etapas básicas para configurar e usar o Azure Synapse Analytics.

## <a name="prepare-a-storage-account"></a>Preparar uma conta de armazenamento

1. Abra o [Portal do Azure](https://portal.azure.com).
1. Crie uma conta de armazenamento que tenha as seguintes configurações:

    |Tab|Configuração | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da conta de armazenamento**| Você pode colocar qualquer nome.|Neste documento, a chamaremos de **contosolake**.|
    |Noções básicas|**Tipo de conta**|Precisa ser definido como **StorageV2**.||
    |Noções básicas|**Localidade**|Escolha qualquer localização.| É recomendável que seu workspace do Azure Synapse Analytics e a conta do Azure Data Lake Storage Gen2 estejam na mesma região.|
    |Avançado|**Data Lake Storage Gen2**|**Habilitado**| O Azure Synapse só funciona com contas de armazenamento em que essa configuração está habilitada.|
    |||||

1. Depois de criar a conta de armazenamento, selecione **Controle de acesso (IAM)** no painel esquerdo. Depois, atribua as seguintes funções ou verifique se elas já estão atribuídas:
    1. Atribua-se à função de **Proprietário**.
    1. Atribua-se à função de **Proprietário de Dados do Storage Blob**.
1. No painel esquerdo, selecione **Contêineres** e crie um contêiner.
1. Você pode dar qualquer nome ao contêiner. Neste documento, chamaremos o contêiner de **usuários**.
1. Aceite a configuração padrão **Nível de acesso público** e, em seguida, selecione **Criar**.

Na etapa a seguir, você vai configurar seu workspace do Azure Synapse para usar essa conta de armazenamento como a conta de armazenamento "primária" e o contêiner para armazenar dados do workspace. O workspace armazena dados em tabelas do Apache Spark. Ele armazena logs de aplicativo do Spark em uma pasta chamada **/synapse/nomedoworkspace**.

## <a name="create-a-synapse-workspace"></a>Criar um workspace do Synapse

1. Abra o [portal do Azure](https://portal.azure.com) e, na parte superior, procure por **Synapse**.
1. Nos resultados da pesquisa em **Serviços**, selecione **Azure Synapse Analytics (versão prévia de workspaces)** .
1. Selecione **Adicionar** para criar um workspace usando estas configurações:

    |Tab|Configuração | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome do workspace**|Você pode dar qualquer nome.| Neste documento, usaremos o **myworkspace**.|
    |Noções básicas|**Região**|Faça a correspondência com a região da conta de armazenamento.|

1. Em **Selecionar Data Lake Storage Gen 2**, selecione a conta e o contêiner que você criou anteriormente.
1. Selecione **Examinar + criar** > **Criar**. Seu workspace fica pronto em alguns minutos.

## <a name="verify-access-to-the-storage-account"></a>Verificar o acesso à conta de armazenamento

Identidades gerenciadas para seu workspace do Azure Synapse talvez já tenham acesso à conta de armazenamento. Siga estas etapas para se certificar:

1. Abra o [portal do Azure](https://portal.azure.com) e a conta de armazenamento primária escolhida para seu workspace.
1. Selecione **Controle de acesso (IAM)** no painel esquerdo.
1. Atribua as funções a seguir ou verifique se elas já estão atribuídas. Usamos o mesmo nome para a identidade do workspace e o nome do workspace.
    1. Para a função de **Colaborador de Dados do Storage Blob** na conta de armazenamento, atribua **myworkspace** como a identidade do workspace.
    1. Atribua **myworkspace** como o nome do workspace.

1. Clique em **Salvar**.

## <a name="open-synapse-studio"></a>Abrir o Synapse Studio

Após criar o workspace do Azure Synapse, você tem duas maneiras de abrir o Synapse Studio:

* Abra o workspace do Synapse no [portal do Azure](https://portal.azure.com). Na parte superior da seção **Visão Geral**, selecione **Iniciar o Synapse Studio**.
* Acesse https://web.azuresynapse.net e entre no seu workspace.

## <a name="create-a-sql-pool"></a>Criar um pool de SQL

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools de SQL**.
1. Selecione **Novo** e insira estas configurações:

    |Configuração | Valor sugerido | 
    |---|---|---|
    |**Nome do pool de SQL**| **SQLDB1**|
    |**Nível de desempenho**|**DW100C**|
    |||

1. Selecione **Examinar + criar** > **Criar**. Seu pool de SQL estará pronto em alguns minutos. O pool de SQL é associado a um banco de dados do pool de SQL também chamado **SQLDB1**.

Um pool de SQL consome recursos faturáveis desde que ele esteja ativo. Você pode pausar o pool posteriormente para reduzir custos.

## <a name="create-an-apache-spark-pool"></a>Criar um Pool do Apache Spark

1. No Synapse Studio, no painel do lado esquerdo, selecione **Gerenciar** > **Pools do Apache Spark**.
1. Selecione **Novo** e insira estas configurações:

    |Configuração | Valor sugerido | 
    |---|---|---|
    |**Nome do Pool do Apache Spark**|**Spark1**
    |**Tamanho do nó**| **Pequeno**|
    |**Número de nós**| Defina o mínimo como 3 e o máximo como 3|

1. Selecione **Examinar + criar** > **Criar**. Seu Pool do Apache Spark estará pronto em alguns segundos.

> [!NOTE]
> Apesar do nome, um Pool do Apache Spark não é como um pool de SQL. São apenas alguns metadados básicos que você usa para informar ao workspace do Azure Synapse como interagir com o Spark.

Como são metadados, os Pools do Spark não podem ser iniciados nem interrompidos.

Ao realizar qualquer atividade do Spark no Azure Synapse, você especifica um Pool do Spark a ser usado. O pool informa ao Azure Synapse quantos recursos do Spark usar. Você paga somente pelos recursos que usa. Quando você parar ativamente de usar o pool, os recursos atingirão o tempo limite automaticamente e serão reciclados.

> [!NOTE]
> Os bancos de dados do Spark são criados independentemente dos Pools do Spark. Um workspace sempre tem um banco de dados Spark chamado **padrão**. Você pode criar bancos de dados do Spark adicionais.

## <a name="the-sql-on-demand-pool"></a>O pool de SQL sob demanda

Todo workspace é fornecido com um pool pré-criado chamado **SQL sob demanda**. Esse pool não pode ser excluído. O pool do SQL sob demanda permite que você trabalhe com o SQL sem precisar criar nem pensar sobre gerenciar um pool de SQL no Azure Synapse.

Ao contrário dos outros tipos de pools, a cobrança pelo SQL sob demanda baseia-se na quantidade de dados examinados para executar a consulta e não no número de recursos usados para executar a consulta.

* O SQL sob demanda tem seus próprios bancos de dados sob demanda do SQL que existem independentemente de qualquer pool do SQL sob demanda.
* Um workspace sempre tem exatamente um pool do SQL sob demanda chamado **SQL sob demanda**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregar os dados de exemplo de táxi de Nova York no banco de dados SQLDB1

1. No Synapse Studio, no menu azul superior, selecione o ícone de **?** ícone.
1. Selecione **Introdução** > **Hub de introdução**.
1. No cartão com rótulo **Dados de consulta de exemplo**, selecione o pool de SQL chamado **SQLDB1**.
1. Selecione **Dados de consulta**. Uma notificação "Carregando dados de exemplo" é exibida brevemente. Uma barra de notificação azul-clara exibida perto da parte superior do Synapse Studio, indica que os dados estão sendo carregados no SQLDB1.
1. Depois que a barra de status ficar verde, descarte-a.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explorar os dados de táxi de Nova York no pool de SQL

1. No Synapse Studio, acesse o hub **Dados**.
1. Acesse **SQLDB1** > **Tabelas**. Você verá várias tabelas carregadas.
1. Clique com o botão direito do mouse na tabela **dbo.Trip** e selecione **Novo Script de SQL** > **Selecionar as Primeiras 100 Linhas**.
1. Aguarde enquanto um novo script SQL é criado e executado.
1. Observe na parte superior do script de SQL que **Conectar ao** é automaticamente definido como o pool de SQL chamado **SQLDB1**.
1. Substitua o texto do script de SQL por esse código e execute-o.

    ```sql
    SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
    FROM  dbo.Trip
    WHERE TripDistanceMiles > 0 AND PassengerCount > 0
    GROUP BY PassengerCount
    ORDER BY PassengerCount
    ```

    Essa consulta mostra como as distâncias totais de viagem e a distância média da viagem estão relacionadas ao número de passageiros.
1. Na janela de resultados do script de SQL, altere a opção **Exibição** para **Gráfico** para uma visualização dos resultados como um gráfico de linhas.

## <a name="load-the-nyc-taxi-data-into-the-spark-nyctaxi-database"></a>Carregar os dados de táxi de Nova York no banco de dados nyctaxi do Spark

Temos dados disponíveis em uma tabela em **SQLDB1**. Carregue-os em um banco de dados do Spark chamado **nyctaxi**.

1. No Synapse Studio, acesse o hub **Desenvolver**.
1. Selecione **+**  > **Notebook**.
1. Na parte superior do notebook, defina o valor **Anexar a** como **Spark1**.
1. Selecione **Adicionar código** para adicionar uma célula de código do notebook e depois cole o seguinte texto:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Acesse o hub **Dados**, clique com o botão direito do mouse em **Bancos de dados** e selecione **Atualizar**. Agora você deve ver estes bancos de dados:

    - **SQLDB1** (pool de SQL)
    - **nyctaxi** (Spark)

## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analisar os dados de táxi de NYC usando Spark e notebooks

1. Retorne ao notebook.
1. Crie uma célula de código e insira o texto a seguir. Em seguida, execute a célula para mostrar os dados de táxi de Nova York que carregamos no banco de dados **nyctaxi** do Spark.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute o código a seguir para realizar a mesma análise que fizemos anteriormente com o pool de SQL **SQLDB1**. Esse código salva os resultados da análise em uma tabela chamada **nyctaxi.passengercountstats** e exibe os resultados.

   ```py
   %%pyspark
   df = spark.sql("""
      SELECT PassengerCount,
          SUM(TripDistanceMiles) as SumTripDistance,
          AVG(TripDistanceMiles) as AvgTripDistance
      FROM nyctaxi.trip
      WHERE TripDistanceMiles > 0 AND PassengerCount > 0
      GROUP BY PassengerCount
      ORDER BY PassengerCount
   """) 
   display(df)
   df.write.saveAsTable("nyctaxi.passengercountstats")
   ```

1. Nos resultados da célula, selecione **Gráfico** para visualizar os dados exibidos.

## <a name="customize-data-visualization-with-spark-and-notebooks"></a>Personalizar visualização de dados com Spark e notebooks

Você pode controlar o modo como os gráficos são renderizados usando notebooks. O código a seguir mostra um exemplo simples. Ele usa as bibliotecas populares **matplotlib** e **manascido**. O código renderiza o mesmo tipo de gráfico de linhas que as consultas SQL que executamos anteriormente.

```py
%%pyspark
import matplotlib.pyplot
import seaborn

seaborn.set(style = "whitegrid")
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.toPandas()
seaborn.lineplot(x="PassengerCount", y="SumTripDistance" , data = df)
seaborn.lineplot(x="PassengerCount", y="AvgTripDistance" , data = df)
matplotlib.pyplot.show()
```

## <a name="load-data-from-a-spark-table-into-a-sql-pool-table"></a>Carregar dados de uma tabela do Spark em uma tabela do pool de SQL

Anteriormente, copiamos dados da tabela do pool de SQL **SQLDB1.dbo.Trip** na tabela do Spark **nyctaxi.trip**. Em seguida, usando o Spark, agregamos os dados na tabela do Spark **nyctaxi.passengercountstats**. Agora, copiaremos os dados de **nyctaxi.passengercountstats** em uma tabela de pools de SQL chamada **SQLDB1.dbo.PassengerCountStats**.

Execute a célula a seguir no seu notebook. Ela copia a tabela do Spark agregada de volta na tabela do pool de SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analisar dados de táxi de NYC em bancos de dados do Spark usando SQL sob demanda

As tabelas em bancos de dados do Spark são automaticamente visíveis e podem ser consultadas pelo SQL sob demanda.

1. No Synapse Studio, acesse o hub **Desenvolver** e crie um script de SQL.
1. Defina **Conectar-se a** como **SQL sob demanda**.
1. Cole o texto a seguir no script e execute-o.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```

    > [!NOTE]
    > Na primeira vez que você executar uma consulta que usa SQL sob demanda, levará cerca de dez segundos para que o SQL sob demanda reúna os recursos de SQL necessários para executar suas consultas. As consultas subsequentes serão muito mais rápidas.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orquestrar atividades com pipelines

Você pode orquestrar uma ampla variedade de tarefas no Azure Synapse.

1. No Synapse Studio, navegue até o hub **Orquestrar**.
1. Selecione **+**  > **Pipeline** para criar um pipeline.
1. Navegue até o hub **Desenvolver** e localize o notebook que você criou anteriormente.
1. Arraste esse notebook para o pipeline.
1. No pipeline, selecione **Adicionar gatilho** > **Novo/editar**.
1. Em **Escolher gatilho**, selecione **Novo** e, em seguida, em **recorrência**, defina o gatilho para ser executado a cada hora.
1. Selecione **OK**.
1. Selecione **Publicar Tudo**. O pipeline é executado a cada hora.
1. Para fazer com que o pipeline seja executado agora sem esperar pela próxima hora, selecione **Adicionar gatilho** > **Novo/editar**.

## <a name="work-with-data-in-a-storage-account"></a>Trabalhar com os dados em uma conta de armazenamento

Até agora nós abordamos cenários em que os dados residem em bancos de dados no workspace. Agora, mostraremos a você como trabalhar com arquivos em contas de armazenamento. Nesse cenário, usaremos a conta de armazenamento primária do workspace e do contêiner que especificamos ao criar o workspace.

* O nome da conta de armazenamento: **contosolake**
* O nome do contêiner na conta de armazenamento: **usuários**

### <a name="create-csv-and-parquet-files-in-your-storage-account"></a>Criar arquivos CSV e Parquet na conta de armazenamento

Execute o código a seguir em um notebook. Ele cria um arquivo CSV e um arquivo Parquet na conta de armazenamento.

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyze-data-in-a-storage-account"></a>Analisar dados em uma conta de armazenamento

1. No Synapse Studio, acesse o hub **Dados** e, em seguida, selecione **Vinculado**.
1. Navegue até **Contas de armazenamento** > **myworkspace (Primário – contosolake)** .
1. Selecione **usuários (Primário)"** . Você deve ver a pasta **NYCTaxi**. Dentro dela, você verá duas pastas: **PassengerCountStats.csv** e **PassengerCountStats.parquet**.
1. Abra a pasta **PassengerCountStats.parquet**. Dentro dela, você verá um arquivo Parquet com um nome como *part-00000-2638e00c-0790-496b-a523-578da9a15019-c000.snappy.parquet*.
1. Clique com o botão direito do mouse em **.parquet** e selecione **novo notebook**. Ele cria um notebook que tem uma célula como esta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Execute a célula.
1. Clique com o botão direito do mouse no arquivo Parquet contido nele e selecione **Novo script SQL** > **SELECIONAR AS 100 PRIMEIRAS LINHAS**. Ele cria um script SQL como este:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```

     No script, o campo **Anexar a** é definido como **SQL sob demanda**.

1. Executar o script.

## <a name="visualize-data-with-power-bi"></a>Visualizar os dados com o Power BI

Com base nos dados de táxi de Nova York, criamos conjuntos de dados agregados em duas tabelas:
- **nyctaxi.passengercountstats**
- **SQLDB1.dbo.PassengerCountStats**

Você pode vincular um workspace do Power BI ao seu workspace do Azure Synapse. Isso permite que você obtenha dados com facilidade em seu workspace do Power BI. Você pode editar os relatórios de Power BI diretamente no seu workspace do Azure Synapse.

### <a name="create-a-power-bi-workspace"></a>Criar um workspace do Power BI

1. Entre no [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Crie um workspace do Power BI chamado **NYCTaxiWorkspace1**.

### <a name="link-your-azure-synapse-workspace-to-your-new-power-bi-workspace"></a>Vincular seu workspace do Azure Synapse ao novo workspace do Power BI

1. No Synapse Studio, vá para **Gerenciar** > **Serviços Vinculados**.
1. Selecione **Novo** > **Conectar ao Power BI** e defina estes campos:

    |Configuração | Valor sugerido | 
    |---|---|
    |**Nome**|**NYCTaxiWorkspace1**|
    |**Nome do workspace**|**NYCTaxiWorkspace1**|

1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-azure-synapse-workspace"></a>Criar um conjunto de dados do Power BI que use dados em seu workspace do Azure Synapse

1. No Synapse Studio, acesse **Desenvolver** > **Power BI**.
1. Navegue até **NYCTaxiWorkspace1** > **Conjuntos de dados do Power BI** e selecione **Novo conjunto de dados do Power BI**.
1. Passe o mouse sobre o banco de dados **SQLDB1** e selecione **Baixar arquivo .pbids**.
1. Abra o arquivo **.pbids** baixado. O Power BI Desktop abre e se conecta automaticamente ao **SQLDB1** no workspace do Azure Synapse.
1. Se você vir uma caixa de diálogo chamada **Banco de dados do SQL Server**:
    1. Selecione **Conta Microsoft**.
    1. Selecione **Entrar** e entre na conta.
    1. Selecione **Conectar**.
1. Depois que a caixa de diálogo **Navegador** for aberta, verifique a tabela **PassengerCountStats** e selecione **Carregar**.
1. Depois que a caixa de diálogo **Configurações de conexão** for exibida, selecione **DirectQuery** > **OK**.
1. Selecione o botão **Relatório** à esquerda.
1. Adicione um **Gráfico de linha** ao relatório.
    1. Arraste a coluna **PassengerCount** para **Visualizações** > **Eixo**.
    1. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Visualizações** > **Valores**.
1. Na guia **Página Inicial**, selecione **Publicar**.
1. Selecione **Salvar** para salvar as alterações.
1. Escolha o nome do arquivo **PassengerAnalysis.pbix** e, em seguida, selecione **Salvar**.
1. Em **Selecionar um destino**, escolha **NYCTaxiWorkspace1** e clique em **Selecionar**.
1. Aguarde a conclusão da publicação.

### <a name="configure-authentication-for-your-dataset"></a>Configurar autenticação para o conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e escolha **Entrar**.
1. No lado esquerdo, em **Workspaces**, selecione o workspace **NYCTaxiWorkspace1**.
1. Dentro desse workspace, localize um conjunto de dados chamado de **Análise de Passageiros** e um relatório chamado **Análise de Passageiros**.
1. Passe o mouse sobre o conjunto de dados **PassengerAnalysis**, selecione o botão de reticências (...) e, em seguida, selecione **Configurações**.
1. Em **Credenciais da fonte de dados**, defina o **Método de autenticação** como **OAuth2** e selecione **Entrar**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar um relatório no Synapse Studio

1. Volte para o Synapse Studio e selecione **Fechar e atualizar**.
1. Acesse o hub **Desenvolver**.
1. Passe o mouse sobre **Power BI** e selecione para atualizar o nó **Relatórios do Power BI**.
1. Agora, em **Power BI**, você deve ver:
    1. Em **NYCTaxiWorkspace1** > **Conjunto de dados do Power BI**, um novo conjunto de dados chamado **PassengerAnalysis**.
    1. Em **NYCTaxiWorkspace1** > **Relatórios do Power BI**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis**. O relatório é aberto e você pode editá-lo diretamente no Synapse Studio.

## <a name="monitor-activities"></a>Monitorar atividades

1. No Synapse Studio, navegue até o hub **monitorar**.
1. Nesse local, você poderá ver um histórico de todas as atividades que estão ocorrendo no workspace e quais estão ativas no momento.
1. Explore as **Execuções de pipeline**, **Aplicativos do Apache Spark** e **Solicitações de SQL** e você poderá ver o que já fez no workspace.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Azure Synapse Analytics (versão prévia dos workspaces)](overview-what-is.md).

