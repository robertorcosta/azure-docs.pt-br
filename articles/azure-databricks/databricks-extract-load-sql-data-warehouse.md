---
title: Tutorial – Executar operações de ETL usando o Azure Databricks
description: Neste tutorial, saiba como extrair dados do Data Lake Storage Gen2 para o Azure Databricks, transformar os dados e, em seguida, carregá-los no Azure Synapse Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.custom: mvc
ms.topic: tutorial
ms.date: 01/29/2020
ms.openlocfilehash: d97fae777b06625488234eb6d48a74a60a83f4d6
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018711"
---
# <a name="tutorial-extract-transform-and-load-data-by-using-azure-databricks"></a>Tutorial: Extrair, transformar e carregar dados usando o Azure Databricks

Neste tutorial, você executa uma operação de ETL (extração, transformação e carregamento de dados) usando o Azure Databricks. Extraia dados do Azure Data Lake Storage Gen2 no Azure Databricks, execute transformações nos dados no Azure Databricks e carregue os dados transformados no Azure Synapse Analytics.

As etapas neste tutorial usam o conector do Azure Synapse para Azure Databricks a fim de transferir dados para o Azure Databricks. Esse conector, por sua vez, usa o Armazenamento de Blobs do Azure como armazenamento temporário para os dados que estão sendo transferidos entre um cluster do Azure Databricks e o Azure Synapse.

A ilustração a seguir mostra o fluxo do aplicativo:

![Azure Databricks com Data Lake Storage e Azure Synapse](./media/databricks-extract-load-sql-data-warehouse/databricks-extract-transform-load-sql-datawarehouse.png "Azure Databricks com Data Lake Storage e Azure Synapse")

Este tutorial cobre as seguintes tarefas:

> [!div class="checklist"]
> * Criar um serviço do Azure Databricks.
> * Criar um cluster Spark no Azure Databricks.
> * Criar um sistema de arquivos na conta do Data Lake Storage Gen2.
> * Carregar os exemplos de dados na conta do Azure Data Lake Storage Gen2.
> * Crie uma entidade de serviço.
> * Extrair dados da conta do Azure Data Lake Storage Gen2.
> * Transformar dados no Azure Databricks.
> * Carregar dados no Azure Synapse.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

> [!Note]
> Este tutorial não pode ser realizado usando a **Assinatura de avaliação gratuita do Azure**.
> Se você tiver uma conta gratuita, acesse seu perfil e altere para uma assinatura **pré-paga**. Para saber mais, confira [Conta gratuita do Azure](https://azure.microsoft.com/free/). Em seguida, [remova o limite de gastos](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) e [solicite um aumento de cota](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) para as vCPUs da sua região. Quando você cria seu espaço de trabalho do Azure Databricks, pode selecionar o tipo de preço **Versão de avaliação (Premium - DBUs gratuitas por 14 dias)** para conceder ao espaço de trabalho acesso gratuito aos DBUs do Premium Azure Databricks por 14 dias.
     
## <a name="prerequisites"></a>Pré-requisitos

Conclua estas tarefas antes de iniciar este tutorial:

* Crie um Azure Synapse, crie uma regra de firewall no nível do servidor e conecte-se ao servidor como um administrador do servidor. Confira [Início Rápido: Criar e consultar um pool de SQL do Synapse usando o portal do Azure](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* Crie uma chave mestra para o Azure Synapse. Confira [Criar uma chave mestra de banco de dados](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-a-database-master-key).

* Crie uma conta do Armazenamento de Blobs do Azure e um contêiner dentro dela. Além disso, recupere a chave de acesso para acessar a conta de armazenamento. Confira [Início Rápido: Carregar, baixar e listar blobs com o portal do Azure](../storage/blobs/storage-quickstart-blobs-portal.md).

* Criar uma conta de armazenamento do Azure Data Lake Storage Gen2. Confira [Início Rápido: Criar uma conta de armazenamento do Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

* Crie uma entidade de serviço. Veja [Como Usar o portal para criar um aplicativo e uma entidade de serviço do Azure AD que possa acessar recursos](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   Há algumas tarefas específicas que você precisará realizar conforme executar as etapas deste artigo.

   * Ao executar as etapas da seção [Atribuir o aplicativo a uma função](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) do artigo, atribua a função **Colaborador dos Dados do Storage Blob** à entidade de serviço no escopo da conta do Data Lake Storage Gen2. Se você atribuir uma função ao grupo de recursos pai ou à assinatura, receberá erros relacionados a permissões até que essas atribuições de função sejam propagadas para a conta de armazenamento.

      Se você preferir usar uma ACL (lista de controle de acesso) para associar a entidade de serviço a um arquivo ou diretório específico, confira [Controle de acesso no Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

   * Ao executar as etapas da seção [Obter valores para conexão](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) do artigo, cole a ID do locatário, a ID do aplicativo e os valores secretos em um arquivo de texto.

* Entre no [portal do Azure](https://portal.azure.com/).

## <a name="gather-the-information-that-you-need"></a>Reúna as informações que você precisa

Conclua os pré-requisitos deste tutorial.

   Antes de começar, você deve ter estes itens de informação:

   :heavy_check_mark:  O nome do banco de dados, nome do servidor de banco de dados, nome de usuário e a senha do Azure Synapse.

   :heavy_check_mark:  A chave de acesso da sua conta de armazenamento de blobs.

   :heavy_check_mark:  O nome de sua conta de armazenamento do Azure Data Lake Storage Gen2.

   :heavy_check_mark:  A ID de locatário da sua assinatura.

   :heavy_check_mark:  A ID de aplicativo do aplicativo que você registrou com o Azure AD (Azure Active Directory).

   :heavy_check_mark:  A chave de autenticação para o aplicativo que você registrou com o Azure AD.

## <a name="create-an-azure-databricks-service"></a>Criar um serviço do Azure Databricks

Nesta seção, você criará um serviço do Azure Databricks usando o portal do Azure.

1. No menu do portal do Azure, selecione **Criar um recurso**.

    ![Criar um recurso no portal do Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-on-portal.png)

    Em seguida, selecione **Análise** > **Azure Databricks**.

    ![Criar um Azure Databricks no portal do Azure](./media/databricks-extract-load-sql-data-warehouse/azure-databricks-resource-create.png)



2. Em **Serviço do Azure Databricks**, forneça os seguintes valores para criar um serviço do Databricks:

    |Propriedade  |Descrição  |
    |---------|---------|
    |**Nome do workspace**     | Forneça um nome para o seu workspace do Databricks.        |
    |**Assinatura**     | Na lista suspensa, selecione sua assinatura do Azure.        |
    |**Grupo de recursos**     | Especifique se deseja criar um novo grupo de recursos ou usar um existente. Um grupo de recursos é um contêiner que mantém os recursos relacionados a uma solução do Azure. Para obter mais informações, consulte [Visão geral do Grupo de Recursos do Azure](../azure-resource-manager/management/overview.md). |
    |**Localidade**     | Selecione **Oeste dos EUA 2**.  Para outras regiões disponíveis, consulte [serviços do Azure por região](https://azure.microsoft.com/regions/services/).      |
    |**Tipo de preço**     |  Selecione **Padrão**.     |

3. A criação da conta leva alguns minutos. Para monitorar o status da operação, veja a barra de progresso na parte superior.

4. Selecione **Fixar no painel** e depois **Criar**.

## <a name="create-a-spark-cluster-in-azure-databricks"></a>Criar um cluster Spark no Azure Databricks

1. No portal do Azure, acesse o serviço do Databricks criado e selecione **Iniciar Workspace**.

2. Você será redirecionado ao portal do Azure Databricks. No portal, selecione **Cluster**.

    ![Databricks no Azure](./media/databricks-extract-load-sql-data-warehouse/databricks-on-azure.png "Databricks no Azure")

3. Na página **Novo cluster**, forneça os valores para criar um cluster.

    ![Criar um cluster Spark do Databricks no Azure](./media/databricks-extract-load-sql-data-warehouse/create-databricks-spark-cluster.png "Criar um cluster Spark do Databricks no Azure")

4. Preencha os valores para os seguintes campos e aceite os valores padrão para os outros campos:

    * Insira um nome para o cluster.

    * Verifique se você marcou a caixa de seleção **Terminar depois de \_\_ minutos de inatividade**. Forneça uma duração (em minutos) para terminar o cluster quando ele não estiver sendo usado.

    * Selecione **Criar cluster**. Quando o cluster está em execução, você pode anexar notebooks a ele e executar trabalhos do Spark.

## <a name="create-a-file-system-in-the-azure-data-lake-storage-gen2-account"></a>Criar um sistema de arquivos na conta do Azure Data Lake Storage Gen2

Nesta seção, você cria um notebook no workspace do Azure Databricks e executa snippets de código para configurar a conta de armazenamento

1. No [portal do Azure](https://portal.azure.com), acesse o serviço do Azure Databricks criado e selecione **Iniciar Workspace**.

2. À esquerda, selecione **Workspace**. Na lista suspensa **Workspace**, selecione **Criar** > **Notebook**.

    ![Criar um notebook no Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-create-notebook.png "Criar notebook no Databricks")

3. Na caixa de diálogo **Criar Bloco de Anotações**, digite um nome para o bloco de anotações. Selecione **Scala** como linguagem e selecione o cluster Spark criado anteriormente.

    ![Fornecer detalhes para um notebook no Databricks](./media/databricks-extract-load-sql-data-warehouse/databricks-notebook-details.png "Fornecer detalhes para um notebook no Databricks")

4. Selecione **Criar**.

5. O bloco de código a seguir define as credenciais de entidade de serviço padrão para qualquer conta ADLS Gen 2 acessada na sessão do Spark. O segundo bloco de código anexa o nome de conta à configuração para especificar credenciais para uma conta específica do ADLS Gen 2.  Copie e cole qualquer bloco de código na primeira célula do notebook do Azure Databricks.

   **Configuração de sessão**

   ```scala
   val appID = "<appID>"
   val secret = "<secret>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id", "<appID>")
   spark.conf.set("fs.azure.account.oauth2.client.secret", "<secret>")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint", "https://login.microsoftonline.com/<tenant-id>/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   ```

   **Configuração da conta**

   ```scala
   val storageAccountName = "<storage-account-name>"
   val appID = "<app-id>"
   val secret = "<secret>"
   val fileSystemName = "<file-system-name>"
   val tenantID = "<tenant-id>"

   spark.conf.set("fs.azure.account.auth.type." + storageAccountName + ".dfs.core.windows.net", "OAuth")
   spark.conf.set("fs.azure.account.oauth.provider.type." + storageAccountName + ".dfs.core.windows.net", "org.apache.hadoop.fs.azurebfs.oauth2.ClientCredsTokenProvider")
   spark.conf.set("fs.azure.account.oauth2.client.id." + storageAccountName + ".dfs.core.windows.net", "" + appID + "")
   spark.conf.set("fs.azure.account.oauth2.client.secret." + storageAccountName + ".dfs.core.windows.net", "" + secret + "")
   spark.conf.set("fs.azure.account.oauth2.client.endpoint." + storageAccountName + ".dfs.core.windows.net", "https://login.microsoftonline.com/" + tenantID + "/oauth2/token")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "true")
   dbutils.fs.ls("abfss://" + fileSystemName  + "@" + storageAccountName + ".dfs.core.windows.net/")
   spark.conf.set("fs.azure.createRemoteFileSystemDuringInitialization", "false")
   ```

6. Neste bloco de código, substitua os valores de espaço reservado `<app-id>`, `<secret>`, `<tenant-id>` e `<storage-account-name>` pelos valores coletados ao concluir os pré-requisitos deste tutorial. Substitua o valor de espaço reservado `<file-system-name>` por qualquer nome que deseje fornecer ao sistema de arquivos.

   * A `<app-id>` e a `<secret>` são provenientes do aplicativo que você registrou no Active Directory como parte da criação de uma entidade de serviço.

   * A `<tenant-id>` é proveniente de sua assinatura.

   * O `<storage-account-name>` é o nome de sua conta de armazenamento do Azure Data Lake Storage Gen2.

7. Pressione as teclas **SHIFT+ENTER** para executar o código nesse bloco.

## <a name="ingest-sample-data-into-the-azure-data-lake-storage-gen2-account"></a>Ingestão de dados de exemplo na conta de armazenamento do Azure Data Lake Storage Gen2

Antes de iniciar esta seção, você deve concluir os pré-requisitos a seguir:

Insira o código a seguir em uma célula do bloco de anotações:

    %sh wget -P /tmp https://raw.githubusercontent.com/Azure/usql/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json

Na célula, pressione **SHIFT + ENTER** para executar o código.

Agora, em uma nova célula abaixo dessa, insira o seguinte código e substitua os valores exibidos entre colchetes pelos mesmos valores usados anteriormente:

    dbutils.fs.cp("file:///tmp/small_radio_json.json", "abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/")

Na célula, pressione **SHIFT + ENTER** para executar o código.

## <a name="extract-data-from-the-azure-data-lake-storage-gen2-account"></a>Extrair dados da conta do Azure Data Lake Storage Gen2

1. Agora é possível carregar o arquivo json de exemplo como um dataframe no Azure Databricks. Cole o código a seguir em uma nova célula. Substitua os espaços reservados mostrados entre colchetes pelos seus valores.

   ```scala
   val df = spark.read.json("abfss://" + fileSystemName + "@" + storageAccountName + ".dfs.core.windows.net/small_radio_json.json")
   ```
2. Pressione as teclas **SHIFT+ENTER** para executar o código nesse bloco.

3. Execute o seguinte código para ver o conteúdo do quadro de dados:

    ```scala
    df.show()
    ```
   Você verá um resultado semelhante ao seguinte snippet:

   ```output
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   |               artist|     auth|firstName|gender|itemInSession|  lastName|   length|  level|            location|method|    page| registration|sessionId|                song|status|           ts|userId|
   +---------------------+---------+---------+------+-------------+----------+---------+-------+--------------------+------+--------+-------------+---------+--------------------+------+-------------+------+
   | El Arrebato         |Logged In| Annalyse|     F|            2|Montgomery|234.57914| free  |  Killeen-Temple, TX|   PUT|NextSong|1384448062332|     1879|Quiero Quererte Q...|   200|1409318650332|   309|
   | Creedence Clearwa...|Logged In|   Dylann|     M|            9|    Thomas|340.87138| paid  |       Anchorage, AK|   PUT|NextSong|1400723739332|       10|        Born To Move|   200|1409318653332|    11|
   | Gorillaz            |Logged In|     Liam|     M|           11|     Watts|246.17751| paid  |New York-Newark-J...|   PUT|NextSong|1406279422332|     2047|                DARE|   200|1409318685332|   201|
   ...
   ...
   ```

   Você extraiu os dados do Azure Data Lake Storage Gen2 para o Azure Databricks.

## <a name="transform-data-in-azure-databricks"></a>Transformar dados no Azure Databricks

Os dados brutos de exemplo **small_radio_json.json** capturam o público-alvo de uma estação de rádio e contêm várias colunas. Nesta seção, você transforma os dados para recuperar somente colunas específicas do conjunto de dados.

1. Primeiro, recupere apenas as colunas **firstName**, **lastName**, **gender**, **location** e **level** do quadro de dados que você criou.

   ```scala
   val specificColumnsDf = df.select("firstname", "lastname", "gender", "location", "level")
   specificColumnsDf.show()
   ```

   Você receberá uma saída como a mostrada no snippet a seguir:

   ```output
   +---------+----------+------+--------------------+-----+
   |firstname|  lastname|gender|            location|level|
   +---------+----------+------+--------------------+-----+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX| free|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...| free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Tess|  Townsend|     F|Nashville-Davidso...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |     Liam|     Watts|     M|New York-Newark-J...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   |     Alan|     Morse|     M|Chicago-Napervill...| paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK| paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...| free|
   +---------+----------+------+--------------------+-----+
   ```

2. Você pode transformar ainda mais esses dados para renomear a coluna **level** como **subscription_type**.

   ```scala
   val renamedColumnsDF = specificColumnsDf.withColumnRenamed("level", "subscription_type")
   renamedColumnsDF.show()
   ```

   Você receberá uma saída como a mostrada no snippet a seguir.

   ```output
   +---------+----------+------+--------------------+-----------------+
   |firstname|  lastname|gender|            location|subscription_type|
   +---------+----------+------+--------------------+-----------------+
   | Annalyse|Montgomery|     F|  Killeen-Temple, TX|             free|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |Gabriella|   Shelton|     F|San Jose-Sunnyval...|             free|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Tess|  Townsend|     F|Nashville-Davidso...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |     Liam|     Watts|     M|New York-Newark-J...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Elijah|  Williams|     M|Detroit-Warren-De...|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   |     Alan|     Morse|     M|Chicago-Napervill...|             paid|
   |   Dylann|    Thomas|     M|       Anchorage, AK|             paid|
   |  Margaux|     Smith|     F|Atlanta-Sandy Spr...|             free|
   +---------+----------+------+--------------------+-----------------+
   ```

## <a name="load-data-into-azure-synapse"></a>Carregar dados no Azure Synapse

Nesta seção, você carregará os dados transformados no Azure Synapse. Você usará o conector do Azure Synapse para Azure Databricks para carregar diretamente um dataframe como uma tabela em um Pool do Spark do Synapse.

Como mencionado anteriormente, o conector do Azure Synapse usa o Armazenamento de Blobs do Azure como armazenamento temporário para carregar dados entre o Azure Databricks e o Azure Synapse. Portanto, comece fornecendo a configuração para se conectar à conta de armazenamento. Você já precisa ter criado a conta como parte dos pré-requisitos deste artigo.

1. Forneça a configuração para acessar a conta do Armazenamento do Azure pelo Azure Databricks.

   ```scala
   val blobStorage = "<blob-storage-account-name>.blob.core.windows.net"
   val blobContainer = "<blob-container-name>"
   val blobAccessKey =  "<access-key>"
   ```

2. Especifique uma pasta temporária a ser usada ao mover dados entre o Azure Databricks e o Azure Synapse.

   ```scala
   val tempDir = "wasbs://" + blobContainer + "@" + blobStorage +"/tempDirs"
   ```

3. Execute o snippet de código a seguir para armazenar chaves de acesso do Armazenamento de Blobs do Azure na configuração. Essa ação garante que você não precise manter a chave de acesso no notebook em texto sem formatação.

   ```scala
   val acntInfo = "fs.azure.account.key."+ blobStorage
   sc.hadoopConfiguration.set(acntInfo, blobAccessKey)
   ```

4. Forneça os valores para se conectar à instância do Azure Synapse. Você deve ter criado um serviço do Azure Synapse Analytics como um pré-requisito. Use o nome totalmente qualificado do servidor para o **dwServer**. Por exemplo, `<servername>.database.windows.net`.

   ```scala
   //Azure Synapse related settings
   val dwDatabase = "<database-name>"
   val dwServer = "<database-server-name>"
   val dwUser = "<user-name>"
   val dwPass = "<password>"
   val dwJdbcPort =  "1433"
   val dwJdbcExtraOptions = "encrypt=true;trustServerCertificate=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
   val sqlDwUrl = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass + ";$dwJdbcExtraOptions"
   val sqlDwUrlSmall = "jdbc:sqlserver://" + dwServer + ":" + dwJdbcPort + ";database=" + dwDatabase + ";user=" + dwUser+";password=" + dwPass
   ```

5. Execute o snippet de código a seguir para carregar o dataframe transformado, **renamedColumnsDF**, como uma tabela no Azure Synapse. Esse snippet de código cria uma tabela chamada **SampleTable** no banco de dados SQL.

   ```scala
   spark.conf.set(
       "spark.sql.parquet.writeLegacyFormat",
       "true")

   renamedColumnsDF.write.format("com.databricks.spark.sqldw").option("url", sqlDwUrlSmall).option("dbtable", "SampleTable")       .option( "forward_spark_azure_storage_credentials","True").option("tempdir", tempDir).mode("overwrite").save()
   ```

   > [!NOTE]
   > Este exemplo usa o sinalizador `forward_spark_azure_storage_credentials`, o que faz o Azure Synapse acessar os dados do Armazenamento de Blobs usando uma Chave de Acesso. Esse é o único método de autenticação compatível.
   >
   > Se o Armazenamento de Blobs do Azure estiver restrito para selecionar as redes virtuais, o Azure Synapse exigirá a [Identidade de Serviço Gerenciada em vez de Chaves de Acesso](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Isso causará o erro: "Esta solicitação não está autorizada a executar esta operação".

6. Conecte-se ao banco de dados SQL e verifique se você consegue ver um banco de dados **SampleTable**.

   ![Verificar a tabela de exemplo](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table.png "Verificar tabela de exemplo")

7. Execute uma consulta select para verificar o conteúdo da tabela. A tabela deve ter os mesmos dados que o quadro de dados **renamedColumnsDF**.

    ![Verificar o conteúdo da tabela de exemplo](./media/databricks-extract-load-sql-data-warehouse/verify-sample-table-content.png "Verificar o conteúdo da tabela de exemplo")

## <a name="clean-up-resources"></a>Limpar os recursos

Depois de concluir o tutorial, você poderá terminar o cluster. No workspace do Azure Databricks, selecione **Clusters** à esquerda. Para que o cluster seja terminado, em **Ações**, aponte para o botão de reticências (...) e selecione o ícone **Terminar**.

![Interromper um cluster Databricks](./media/databricks-extract-load-sql-data-warehouse/terminate-databricks-cluster.png "Interromper um cluster Databricks")

Se você não terminar o cluster manualmente, ele será interrompido automaticamente, desde que você tenha marcado a caixa de seleção **Terminar depois de \_\_ minutos de inatividade** ao criar o cluster. Nesse caso, o cluster será interrompido automaticamente se ficar inativo durante o tempo especificado.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a:

> [!div class="checklist"]
> * Criar um serviço do Azure Databricks
> * Criar um cluster Spark no Azure Databricks
> * Criar um bloco de anotações no Azure Databricks
> * Extrair dados de uma conta do Data Lake Storage Gen2
> * Transformar dados no Azure Databricks
> * Carregar dados no Azure Synapse

Avance para o próximo tutorial a fim de saber mais sobre o fluxo de dados em tempo real no Azure Databricks usando os Hubs de Eventos do Azure.

> [!div class="nextstepaction"]
>[Transmitir dados para o Azure Databricks usando Hubs de Eventos](databricks-stream-from-eventhubs.md)
