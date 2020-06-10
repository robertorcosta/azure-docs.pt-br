---
title: 'Tutorial: Introdução ao Azure Synapse Analytics'
description: Passo a passo para entender rapidamente os conceitos básicos no Azure Synapse
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: quickstart
ms.date: 05/19/2020
ms.openlocfilehash: 24a34ae6f00eca7154021162184f5e71503da06b
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84248321"
---
# <a name="getting-started-with-azure-synapse-analytics"></a>Introdução ao Azure Synapse Analytics

Este documento guia você por todas as etapas básicas necessárias para configurar e usar o Azure Synapse Analytics.

## <a name="prepare-a-storage-account-for-use-with-a-synapse-workspace"></a>Preparar uma conta de armazenamento para usar com um workspace do Synapse

* Abra o [portal do Azure](https://portal.azure.com)
* Crie uma conta de armazenamento com as seguintes configurações:

    |Tab|Configuração | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome da conta de armazenamento**| Você pode colocar qualquer nome.|Neste documento, vamos nos referir a ela como `contosolake`.|
    |Noções básicas|**Tipo de conta**|Precisa ser definida como `StorageV2`||
    |Noções básicas|**Localidade**|Você pode escolher qualquer local| É recomendável que seu workspace do Synapse e a conta do ADLS (Azure Data Lake Storage) Gen2 estejam na mesma região.|
    |Avançado|**Data Lake Storage Gen2**|`Enabled`| O Azure Synapse só funciona com contas de armazenamento em que essa configuração está habilitada.|

1. Depois que a conta de armazenamento for criada, selecione **Controle de acesso (IAM)** na navegação à esquerda. Em seguida, atribua as seguintes funções ou verifique se elas já estão atribuídas. 

    a. * Atribua-se à função de **Proprietário** na conta de armazenamento b. * Atribua-se à função de **Proprietário de Dados do Blob de Armazenamento** na Conta de Armazenamento

1. No painel de navegação esquerdo, selecione **Contêineres** e crie um contêiner. Você pode colocar qualquer nome. Aceite o **Nível de acesso público** padrão. Neste documento, chamaremos o contêiner de `users`. Selecione **Criar**. 

Na etapa a seguir, você vai configurar seu workspace do Synapse para usar essa conta de armazenamento como a conta de armazenamento "primária" e o contêiner para armazenar dados do workspace. O workspace armazenará dados em tabelas do Apache Spark e logs do aplicativo Spark nessa conta em uma pasta chamada `/synapse/workspacename`.

## <a name="create-a-synapse-workspace"></a>Criar um workspace do Synapse

* Abra o [portal do Azure](https://portal.azure.com) e, na parte superior, procure `Synapse`.
* Nos resultados da pesquisa em **Serviços**, selecione **Azure Synapse Analytics (versão prévia de workspaces)**
* Selecione **+ Adicionar** para criar um workspace usando essas configurações

    |Tab|Configuração | Valor sugerido | Descrição |
    |---|---|---|---|
    |Noções básicas|**Nome do workspace**|Você pode dar qualquer nome.| Neste documento, usaremos `myworkspace`|
    |Noções básicas|**Região**|Corresponder à região da conta de armazenamento|

1. Em **Selecionar Data Lake Storage Gen 2**, selecione a conta e o contêiner que você criou anteriormente.

1. Selecione **Examinar + criar**. Selecione **Criar**. Seu workspace estará pronto em alguns minutos.

## <a name="verify-the-synapse-workspace-msi-has-access-to-the-storage-account"></a>Verifique se o MSI do workspace do Synapse tem acesso à conta de armazenamento

Isso pode já ter sido feito para você. Em todo caso, você deve verificar.

1. Abra o [portal do Azure](https://portal.azure.com) e abra a conta de armazenamento primária escolhida para seu workspace.
1. Selecione **Controle de acesso (IAM)** na navegação à esquerda. Em seguida, atribua as seguintes funções ou verifique se elas já estão atribuídas. 
    a. Atribua a identidade do workspace à função **Colaborador de Dados do Blob de Armazenamento** na conta de armazenamento. A identidade do workspace tem o mesmo nome que o workspace. Neste documento, o nome do workspace é `myworkspace`, portanto, a identidade do workspace é `myworkspaced`
1. Clique em **Salvar**.
    
## <a name="launch-synapse-studio"></a>Iniciar o Synapse Studio

Depois de criar o workspace do Synapse, você tem duas maneiras de abrir o Synapse Studio:
* Abra o workspace do Synapse no [portal do Azure](https://portal.azure.com) e, na parte superior da seção **Visão geral**, selecione **Iniciar o Synapse Studio**
* Acesse diretamente https://web.azuresynapse.net e entre no seu workspace.

## <a name="create-a-sql-pool"></a>Criar um pool de SQL

1. No Synapse Studio, na navegação do lado esquerdo, selecione **Gerenciar > Pools de SQL**
1. Selecione **+Novo** e insira estas configurações:

    |Configuração | Valor sugerido | 
    |---|---|
    |**Nome do pool de SQL**| `SQLDB1`|
    |**Nível de desempenho**|`DW100C`|

1. Selecione **Examinar + Criar** e, em seguida, selecione **Criar**.
1. Seu pool de SQL estará pronto em alguns minutos. Quando o pool de SQL for criado, ele será associado a um banco de dados do pool de SQL também chamado **SQLDB1**.

Um pool de SQL consome recursos faturáveis desde que ele esteja ativo. Você pode pausar o pool posteriormente para reduzir custos.

## <a name="create-an-apache-spark-pool"></a>Criar um Pool do Apache Spark

1. No Synapse Studio, no lado esquerdo, selecione **Gerenciar > Pools do Apache Spark**
1. Selecione **+Novo** e insira estas configurações:

    |Configuração | Valor sugerido | 
    |---|---|
    |**Nome do Pool do Apache Spark**|`Spark1`
    |**Tamanho do nó**| `Small`|
    |**Número de nós**| Defina o mínimo como 3 e o máximo como 3|

1. Selecione **Examinar + Criar** e, em seguida, selecione **Criar**.
1. Seu Pool do Apache Spark estará pronto em alguns segundos.

> [!NOTE]
> Apesar do nome, um Pool do Apache Spark não é como um pool de SQL. São apenas alguns metadados básicos que você usa para informar ao workspace do Synapse como interagir com o Spark. 

Como são metadados, os Pools do Spark não podem ser iniciados nem interrompidos. 

Ao fazer qualquer atividade do Spark no Synapse, você especifica um Pool do Spark a ser usado. O pool informa ao Synapse quantos recursos do Spark usar. Você paga somente pelos recursos usados. Quando você parar ativamente de usar o pool, os recursos atingirão o tempo limite automaticamente e serão reciclados.

> [!NOTE]
> Os bancos de dados do Spark são criados independentemente dos Pools do Spark. Um workspace sempre tem um banco de dados do Spark chamado **padrão** e você pode criar bancos de dados adicionais do Spark.

## <a name="the-sql-on-demand-pool"></a>O pool de SQL sob demanda

Todo workspace é fornecido com um pool pré-criado e impossível de excluir chamado **SQL sob demanda**. O pool de SQL sob demanda permite que você trabalhe com o SQL sem precisar criar nem pensar no gerenciamento de um pool de SQL do Synapse. Ao contrário dos outros tipos de pools, a cobrança pelo SQL sob demanda baseia-se na quantidade de dados examinados para executar a consulta e não no número de recursos usados para executar a consulta.

* O SQL sob demanda também tem seus próprios bancos de dados sob demanda do SQL que existem independentemente de qualquer pool do SQL sob demanda.
* Atualmente, um workspace sempre tem exatamente um pool do SQL sob demanda chamado **SQL sob demanda**.

## <a name="load-the-nyc-taxi-sample-data-into-the-sqldb1-database"></a>Carregar os dados de exemplo de táxi de NYC no banco de dados SQLDB1

1. No Synapse Studio, no menu azul superior, selecione o ícone de **?** ícone.
1. Selecione **Introdução > Hub de introdução**
1. No cartão com rótulo **Dados de consulta de exemplo**, selecione o pool de SQL chamado `SQLDB1`
1. Selecione **Dados de consulta**. Você verá uma notificação informando "Carregando dados de exemplo" que aparecerá e desaparecerá.
1. Você verá uma barra de notificação azul-clara perto da parte superior do Synapse Studio, indicando que os dados estão sendo carregados no SQLDB1. Aguarde até que ela fique verde e descarte-a.

## <a name="explore-the-nyc-taxi-data-in-the-sql-pool"></a>Explorar os dados de táxi de NYC no pool de SQL

1. No Synapse Studio, navegue até o hub **Dados**
1. Navegue até **SQLDB1 > Tabelas**. Você verá várias tabelas que foram carregadas.
1. Clique com o botão direito do mouse na tabela **dbo.Trip** e selecione **Novo Script de SQL > Selecionar as primeiras 100 linhas**
1. Um novo script de SQL será criado e executado automaticamente.
1. Observe na parte superior do script de SQL que **Conectar a** é automaticamente definido como o pool de SQL chamado `SQLDB1`.
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

1. Essa consulta mostra como as distâncias totais de viagem e a distância média da viagem estão relacionadas ao número de passageiros
1. Na janela de resultados do script de SQL, altere a opção **Exibição** para **Gráfico** para uma visualização dos resultados como um gráfico de linhas

## <a name="load-the-nyc-taxi-sample-data-into-the-spark-nyctaxi-database"></a>Carregar os dados de exemplo de táxi de NYC no banco de dados nyctaxi do Spark

Temos dados disponíveis em uma tabela em `SQLDB1`. Agora, carregamos esses dados em um banco de dados do Spark nomeado `nyctaxi`.

1. No Synapse Studio, navegue até o hub **Desenvolver**
1. Selecione **+** e selecione **Notebook**
1. Na parte superior do notebook, defina o valor **Anexar ao** como `Spark1`
1. Selecione **Adicionar código** para adicionar uma célula de código do notebook e cole o texto abaixo:

    ```scala
    %%spark
    spark.sql("CREATE DATABASE IF NOT EXISTS nyctaxi")
    val df = spark.read.sqlanalytics("SQLDB1.dbo.Trip") 
    df.write.mode("overwrite").saveAsTable("nyctaxi.trip")
    ```

1. Navegue até o hub **Dados**, clique com o botão direito do mouse em **Bancos de dados** e selecione **Atualizar**.
1. Agora você deve ver estes bancos de dados:
    - SQLDB (pool de SQL)
    - nyctaxi (Spark)
      
## <a name="analyze-the-nyc-taxi-data-using-spark-and-notebooks"></a>Analisar os dados de táxi de NYC usando Spark e notebooks

1. Retornar ao notebook
1. Crie uma célula de código, insira o texto abaixo e execute a célula para obter exemplos dos dados de táxi de NYC que carregamos no banco de dados do Spark `nyctaxi`.

   ```py
   %%pyspark
   df = spark.sql("SELECT * FROM nyctaxi.trip") 
   display(df)
   ```

1. Execute o código a seguir para realizar a mesma análise que fizemos anteriormente com o pool de SQL `SQLDB1`. Esse código também salva os resultados da análise em uma tabela chamada `nyctaxi.passengercountstats` e visualiza os resultados.

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

1. Nos resultados da célula, selecione **Gráfico** para observar os dados visualizados
 
## <a name="customize-data-visualization-data-with-spark-and-notebooks"></a>Personalizar visualização de dados com Spark e notebooks

Com notebooks, você pode controlar como renderizar gráficos. O código a seguir mostra um exemplo simples usando as bibliotecas populares `matplotlib` e `seaborn`. Ele renderizará o mesmo tipo de gráfico de linhas que você viu ao executar as consultas SQL anteriormente.

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

Anteriormente, copiamos dados de uma tabela do pool de SQL `SQLDB1.dbo.Trip` em uma tabela do Spark `nyctaxi.trip`. Em seguida, usando o Spark, agregamos os dados na tabela do Spark `nyctaxi.passengercountstats`. Agora, copiaremos os dados de `nyctaxi.passengercountstats` em uma tabela de pool de SQL chamada `SQLDB1.dbo.PassengerCountStats`. 

Execute a célula abaixo no notebook. Ele copiará a tabela do Spark agregada de volta na tabela do pool de SQL.

```scala
%%spark
val df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df.write.sqlanalytics("SQLDB1.dbo.PassengerCountStats", Constants.INTERNAL )
```

## <a name="analyze-nyc-taxi-data-in-spark-databases-using-sql-on-demand"></a>Analisar dados de táxi de NYC em bancos de dado do Spark usando SQL sob demanda 

As tabelas em bancos de dados do Spark são automaticamente visíveis e consultáveis pelo SQL sob demanda.

1. No Synapse Studio, navegue até o hub **Desenvolver** e crie um script de SQL
1. Defina **Conectar-se ao** como **SQL sob demanda** 
1. Cole o texto a seguir no script e execute-o.

    ```sql
    SELECT *
    FROM nyctaxi.dbo.passengercountstats
    ```
    > [!NOTE]
    > Na primeira vez que você executar uma consulta que usa SQL sob demanda, levará cerca de 10 segundos para que o SQL sob demanda reúna os recursos de SQL necessários para executar suas consultas. As consultas subsequentes não exigirão esse tempo e serão muito mais rápidas.
  
## <a name="orchestrate-activities-with-pipelines"></a>Orquestrar atividades com pipelines

Você pode orquestrar uma ampla variedade de tarefas no Azure Synapse. Nesta seção, você verá como isso é fácil.

1. No Synapse Studio, navegue até o hub **Orquestrar**.
1. Selecione **+** e, em seguida, selecione **Pipeline**. Um novo pipeline será criado.
1. Navegue até o hub Desenvolver e localize o notebook que você criou anteriormente.
1. Arraste esse notebook para o pipeline.
1. No pipeline, selecione **Adicionar gatilho > Novo/editar**.
1. Em **Escolher gatilho**, selecione **Novo** e, em seguida, em recorrência, defina o gatilho para ser executado a cada uma hora.
1. Selecione **OK**.
1. Selecione **Publicar Todos** e o pipeline será executado a cada hora.
1. Se você quiser fazer com que o pipeline seja executado agora sem esperar pela próxima hora, selecione **Adicionar gatilho > Novo/editar**.

## <a name="working-with-data-in-a-storage-account"></a>Trabalhando com os dados em uma conta de armazenamento

Até agora nós abordamos cenários em que os dados residiam em bancos de dados no workspace. Agora, mostraremos como trabalhar com arquivos em contas de armazenamento. Nesse cenário, usaremos a conta de armazenamento primária do workspace e do contêiner que especificamos ao criar o workspace.

* O nome da conta de armazenamento: `contosolake`
* O nome do contêiner na conta de armazenamento: `users`

### <a name="creating-csv-and-parquet-files-in-your-storage-account"></a>Criando arquivos CSV e Parquet em sua Conta de armazenamento

Execute o código a seguir em um notebook. Ele cria um arquivo CSV e um arquivo Parquet na conta de armazenamento

```py
%%pyspark
df = spark.sql("SELECT * FROM nyctaxi.passengercountstats")
df = df.repartition(1) # This ensure we'll get a single file during write()
df.write.mode("overwrite").csv("/NYCTaxi/PassengerCountStats.csv")
df.write.mode("overwrite").parquet("/NYCTaxi/PassengerCountStats.parquet")
```

### <a name="analyzing-data-in-a-storage-account"></a>Analisando dados em uma conta de armazenamento

1. No Synapse Studio, navegue até o hub **Dados**
1. Selecionar **Vinculado**
1. Navegue até **Contas de armazenamento > myworkspace (primário – contosolake)**
1. Selecione **usuários (Primário)"**
1. Você verá uma pasta chamada `NYCTaxi`. Dentro de você deve ver duas pastas, `PassengerCountStats.csv` e `PassengerCountStats.parquet`.
1. Navegue até a pasta `PassengerCountStats.parquet`.
1. Clique com o botão direito do mouse no arquivo parquet que está dentro e selecione **novo notebook**; isso criará um notebook com uma célula como esta:

    ```py
    %%pyspark
    data_path = spark.read.load('abfss://users@contosolake.dfs.core.windows.net/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet', format='parquet')
    data_path.show(100)
    ```

1. Execute a célula.
1. Clique com o botão direito do mouse no arquivo parquet que está dentro e selecione **Novo script de SQL > Selecionar as primeiras 100 linhas**; isso criará um script de SQL como este:

    ```sql
    SELECT TOP 100 *
    FROM OPENROWSET(
        BULK 'https://contosolake.dfs.core.windows.net/users/NYCTaxi/PassengerCountStats.parquet/part-00000-1f251a58-d8ac-4972-9215-8d528d490690-c000.snappy.parquet',
        FORMAT='PARQUET'
    ) AS [r];
    ```
    
1. No script, o campo **Anexar ao** será definido como **SQL sob demanda**.
1. Execute o script.

## <a name="visualize-data-with-power-bi"></a>Visualizar os dados com o Power BI

Com base nos dados de táxi de NYX, criamos conjuntos de dados agregados em duas tabelas:
* `nyctaxi.passengercountstats`
* `SQLDB1.dbo.PassengerCountStats`

Você pode vincular um workspace do Power BI ao seu workspace do Synapse. Isso permite que você leve dados facilmente para seu workspace do Power BI e você pode editar os relatórios do Power BI diretamente no workspace do Synapse.

### <a name="create-a-power-bi-workspace"></a>Criar um Workspace do Power BI

1. Entre no [powerbi.microsoft.com](https://powerbi.microsoft.com/).
1. Crie um workspace do Power BI chamado `NYCTaxiWorkspace1`.

### <a name="link-your-synapse-workspace-to-your-new-power-bi-workspace"></a>Vincular seu workspace do Synapse ao novo workspace do Power BI

1. No Synapse Studio, navegue até **Gerenciar > Serviços Vinculados**.
1. Selecione **+ Novo**, selecione **Conectar ao Power BI** e defina esses campos:

    |Configuração | Valor sugerido | 
    |---|---|
    |**Nome**|`NYCTaxiWorkspace1`|
    |**Nome do workspace**|`NYCTaxiWorkspace1`|
        
1. Selecione **Criar**.

### <a name="create-a-power-bi-dataset-that-uses-data-in-your-synapse-workspace"></a>Criar um conjunto de dados do Power BI que use dados em seu workspace do Synapse

1. No Synapse Studio, navegue até **Desenvolver > Power BI**.
1. Navegue até **NYCTaxiWorkspace1 > Conjuntos de dados do Power BI** e selecione **Novo conjunto de dados do Power BI**.
1. Passe o mouse sobre o banco de dados `SQLDB1` e selecione **Baixar arquivo .pbids**.
1. Abra o arquivo `.pbids` baixado. 
1. Isso iniciará o Power BI Desktop e o conectará automaticamente ao `SQLDB1` em seu workspace do Synapse.
1. Se você vir uma caixa de diálogo chamada **Banco de dados do SQL Server**: a. Selecione **Conta Microsoft**. 
    b. Selecione **Entrar** e entre.
    c. Selecione **Conectar**.
1. A caixa de diálogo **Navegador** será aberta. Quando isso ocorrer, verifique a tabela **PassengerCountStats** e selecione **Carregar**.
1. Uma caixa de diálogo **Configurações da conexão** será exibida. Selecione **DirectQuery** e selecione **OK**
1. Selecione o botão **Relatório** à esquerda.
1. Adicione um **Gráfico de linha** ao relatório.
    a. Arraste a coluna **PasssengerCount** para **Visualizações > Eixo** b. Arraste as colunas **SumTripDistance** e **AvgTripDistance** para **Visualizações > Valores**.
1. Na guia **Página Inicial**, selecione **Publicar**.
1. Você será perguntado se deseja salvar as alterações. Clique em **Salvar**.
1. Será solicitado que você escolha um nome de arquivo. Escolha `PassengerAnalysis.pbix` e selecione **Salvar**.
1. Será solicitado que você **Selecione um destino**; selecione `NYCTaxiWorkspace1` e escolha **Selecionar**.
1. Aguarde a conclusão da publicação.

### <a name="configure-authentication-for-your-dataset"></a>Configurar autenticação para o conjunto de dados

1. Abra [powerbi.microsoft.com](https://powerbi.microsoft.com/) e **Entre**
1. À esquerda, em **Workspaces**, selecione o workspace `NYCTaxiWorkspace1`.
1. Dentro desse workspace, você verá um conjunto de dados chamado `Passenger Analysis` e um relatório chamado `Passenger Analysis`.
1. Passe o mouse sobre o conjunto de dados `PassengerAnalysis` e selecione o ícone com os três pontos e selecione **Configurações**.
1. Em **Credenciais da fonte de dados**, defina o **Método de autenticação** como **OAuth2** e selecione **Entrar**.

### <a name="edit-a-report-in-synapse-studio"></a>Editar um relatório no Synapse Studio

1. Volte para o Synapse Studio e selecione **Fechar e atualizar** 
1. Navegue até o hub **Desenvolver** 
1. Passe o mouse sobre **Power BI** e clique no nó atualizar **Relatórios do Power BI**.
1. Agora, em **Power BI** você deve ver: a. * Em **NYCTaxiWorkspace1 > Conjunto de dados do Power BI**, um novo conjunto de dados chamado **PassengerAnalysis**.
    b. * Em **NYCTaxiWorkspace1 > Relatórios do Power BI**, um novo relatório chamado **PassengerAnalysis**.
1. Selecione o relatório **PassengerAnalysis**. 
1. O relatório será aberto e agora você poderá editar o relatório diretamente no Synapse Studio.

## <a name="monitor-activities"></a>Monitorar atividades

1. No Synapse Studio, navegue até o hub de monitoramento.
1. Nesse local, você poderá ver um histórico de todas as atividades que estão ocorrendo no workspace e quais estão ativas no momento.
1. Explore as **execuções de pipeline**, **Aplicativos Apache Spark** e **Solicitações de SQL** e você poderá ver o que já fez no workspace.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [Azure Synapse Analytics (versão prévia)](overview-what-is.md)

