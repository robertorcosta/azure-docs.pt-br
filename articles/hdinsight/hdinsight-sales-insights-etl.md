---
title: 'Tutorial: Criar um pipeline de ETL de ponta a ponta para obter insights de vendas no Azure HDInsight'
description: Saiba como usar criar pipelines de ETL com o Azure HDInsight para obter insights de dados de vendas usando clusters sob demanda do Spark e o Power BI.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/24/2020
ms.openlocfilehash: a4df99c45b27ad662133010422cae2e30e36e584
ms.sourcegitcommit: 940e16ff194d5163f277f98d038833b1055a1a3e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2020
ms.locfileid: "80247258"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights-in-azure-hdinsight"></a>Tutorial: Criar um pipeline de dados de ponta a ponta para obter insights de vendas no Azure HDInsight

Neste tutorial, você criará um pipeline de dados ponta a ponta, que executa operações de ETL (extração, transformação e carregamento). O pipeline usará clusters [Apache Spark](./spark/apache-spark-overview.md) e Apache Hive em execução no Azure HDInsight para consulta e manipulação dos dados. Você também usará tecnologias como o Azure Data Lake Storage Gen2 para armazenamento de dados e o Power BI para visualização.

Esse pipeline de dados combina os dados de vários repositórios, remove os dados indesejados, acrescenta novos dados e carrega tudo isso novamente no armazenamento para visualizar insights empresariais. Leia mais sobre pipelines de ETL [(extração, transformação e carregamento) em escala](./hadoop/apache-hadoop-etl-at-scale.md).

![Arquitetura de ETL](./media/hdinsight-sales-insights-etl/architecture.png)

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* CLI do Azure. Confira [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

* Um membro da [Função interna do Azure – proprietário](../role-based-access-control/built-in-roles.md).

* [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) para visualizar insights empresariais no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonar o repositório com scripts e dados

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra o Azure Cloud Shell na barra de menus superior. Selecione sua assinatura para criar um compartilhamento de arquivo, se o Cloud Shell solicitar.

   ![Abrir o Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)

1. No menu suspenso **Selecionar ambiente**, selecione **Bash**.

1. Verifique se você é membro da função [proprietário](../role-based-access-control/built-in-roles.md) do Azure. Substitua `user@contoso.com` pela sua conta e, em seguida, insira o comando:

    ```azurecli
    az role assignment list \
    --assignee "user@contoso.com" \
    --role "Owner"
    ```

    Se nenhum registro foi retornado, você não é um membro e não pode concluir este tutorial.

1. Liste suas assinaturas inserindo o comando:

    ```azurecli
    az account list --output table
    ```

    Observe a ID da assinatura que você usará para este projeto.

1. Defina a assinatura que você usará para este projeto. Substitua `SUBSCRIPTIONID` pelo valor real e, em seguida, digite o comando.

    ```azurecli
    subscriptionID="SUBSCRIPTIONID"
    az account set --subscription $subscriptionID
    ```

1. Criar um grupo de recursos para o projeto. Substitua `RESOURCEGROUP` pelo nome desejado e, em seguida, digite o comando.

    ```azurecli
    resourceGroup="RESOURCEGROUP"
    az group create --name $resourceGroup --location westus
    ```

1. Baixe os dados e os scripts deste tutorial no [repositório de ETL de insights de vendas do HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl).  Insira o seguinte comando:

    ```bash
    git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
    cd hdinsight-sales-insights-etl
    ```

1. Verifique se `salesdata scripts templates` foi criado. Verifique com o seguinte comando:

   ```bash
   ls
   ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Implantar os recursos do Azure necessários para o pipeline

1. Adicione permissões de execução para todos os scripts digitando:

    ```bash
    chmod +x scripts/*.sh
    ````

1. Execute o script. Substitua `RESOURCE_GROUP_NAME` e `LOCATION` pelos respectivos valores; em seguida, insira o comando:

    ```bash
    ./scripts/resources.sh RESOURCE_GROUP_NAME LOCATION
    ```

    O comando exibirá os seguintes recursos:

    * Uma conta de Armazenamento de Blobs do Azure. Essa conta armazenará os dados de vendas da empresa.
    * Uma conta do Azure Data Lake Storage Gen2. Essa conta servirá como a conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre o HDInsight e o Data Lake Storage Gen2 na [integração do Azure HDInsight ao Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    * Uma identidade gerenciada atribuída ao usuário. Essa conta fornece aos clusters HDInsight o acesso à conta do Data Lake Storage Gen2.
    * Um cluster do Apache Spark. Esse cluster será usado para limpar e transformar os dados brutos.
    * Um cluster [Interactive Query](./interactive-query/apache-interactive-query-get-started.md) do Apache Hive. Esse cluster permitirá consultar os dados de vendas visualizando-os com o Power BI.
    * Uma rede virtual do Azure compatível com as regras do NSG (grupo de segurança de rede). Essa rede virtual permite que os clusters se comuniquem e protejam suas comunicações.

A criação do cluster pode levar cerca de 20 minutos.

O script `resources.sh` contém os comandos a seguir. Não será necessário executar esses comandos se você já tiver executado o script na etapa anterior.

* `az group deployment create` – esse comando usa um modelo do Azure Resource Manager (`resourcestemplate.json`) para criar os recursos especificados com a configuração desejada.

    ```azurecli
    az group deployment create --name ResourcesDeployment \
        --resource-group $resourceGroup \
        --template-file resourcestemplate.json \
        --parameters "@resourceparameters.json"
    ```

* `az storage blob upload-batch` – esse comando carrega os arquivos .csv dos dados de vendas na conta de Armazenamento de Blobs recém-criada usando o comando:

    ```azurecli
    az storage blob upload-batch -d rawdata \
        --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
    ```

A senha padrão usada para acesso SSH aos clusters é `Thisisapassword1`. Caso deseje alterar a senha, vá até o arquivo `resourcesparameters.json` e altere a senha para os parâmetros `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implantação e coletar informações de recursos

1. Caso deseje verificar o status da implantação, vá até o grupo de recursos no portal do Azure. Selecione **Implantações** em **Configurações**. Selecione o nome da implantação, `ResourcesDeployment`. Aqui você pode ver os recursos que foram implantados com êxito e aqueles que ainda estão em andamento.

1. Para exibir os nomes dos clusters, digite o seguinte comando:

    ```azurecli
    sparkCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'spark')].{clusterName:name}" -o tsv)

    llapCluster=$(az hdinsight list \
        --resource-group $resourceGroup \
        --query "[?contains(name,'llap')].{clusterName:name}" -o tsv)

    echo $sparkCluster
    echo $llapCluster
    ```

1. Para exibir a conta de armazenamento e a chave de acesso do Azure, insira o seguinte comando:

    ```azurecli
    blobStorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.blobStorageName.value')

    blobKey=$(az storage account keys list \
        --account-name $blobStorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $blobStorageName
    echo $blobKey
    ```

1. Para exibir a conta e a chave de acesso do Azure Data Lake Storage Gen2, insira o seguinte comando:

    ```azurecli
    ADLSGen2StorageName=$(cat resourcesoutputs.json | jq -r '.properties.outputs.adlsGen2StorageName.value')

    adlsKey=$(az storage account keys list \
        --account-name $ADLSGen2StorageName \
        --resource-group $resourceGroup \
        --query [0].value -o tsv)

    echo $ADLSGen2StorageName
    echo $adlsKey
    ```

### <a name="create-a-data-factory"></a>Criar uma data factory

O Azure Data Factory é uma ferramenta que ajuda a automatizar Azure Pipelines. Não é a única maneira de realizar essas tarefas, mas é uma ótima forma de automatizar esses processos. Para obter mais informações sobre o Azure Data Factory, veja a [Documentação do Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

Esse data factory terá um pipeline com duas atividades:

* A primeira atividade copiará os dados do Armazenamento de Blobs do Azure para a conta de armazenamento do Data Lake Storage Gen 2 para simular a ingestão de dados.
* A segunda atividade transformará os dados no cluster Spark. O script transforma os dados removendo colunas indesejadas. Ele também acrescenta uma nova coluna que calcula a receita que uma única transação gera.

Para configurar o pipeline do Azure Data Factory, execute o seguinte comando:

```bash
./scripts/adf.sh
```

Esse script realiza as seguintes ações:

1. Cria uma entidade de serviço com permissões `Storage Blob Data Contributor` na conta de armazenamento do Data Lake Storage Gen2.
1. Obtém um token de autenticação para autorizar solicitações POST para a [API REST do sistema de arquivos do Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real de sua conta de armazenamento do Data Lake Storage Gen2 nos arquivos `sparktransform.py` e `query.hql`.
1. Obtém as chaves de armazenamento para o Data Lake Storage Gen2 e as Contas de Armazenamento de Blobs.
1. Cria outra implantação de recurso para criar um pipeline do Azure Data Factory, com suas atividades e seus serviços vinculados associados. Ela passa as chaves de armazenamento como parâmetros para o arquivo de modelo, de modo que os serviços vinculados possam acessar as contas de armazenamento corretamente.

O pipeline do Data Factory é implantado por meio do seguinte comando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Executar o pipeline de dados

### <a name="trigger-the-data-factory-activities"></a>Disparar atividades do Data Factory

A primeira atividade no pipeline do Data Factory que você criou move os dados do Armazenamento de Blobs para o Data Lake Storage Gen2. A segunda atividade aplica as transformações do Spark nos dados e salva os arquivos .csv transformados em uma nova localização. O pipeline inteiro pode levar alguns minutos para ser concluído.

Para disparar os pipelines, você pode:

* Disparar os pipelines do Data Factory no PowerShell. Substitua `DataFactoryName` pelo nome real do Data Factory e, em seguida, execute os seguintes comandos:

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "CopyPipeline_k8z"
    Invoke-AzDataFactoryV2Pipeline -DataFactory DataFactoryName -PipelineName "sparkTransformPipeline"
    ```

    Ou

* Abra o data factory e selecione **Criar e Monitorar**. Dispare o pipeline de cópia e, em seguida, o pipeline do Spark do portal. Para obter informações sobre como disparar pipelines por meio do portal, veja [Criar clusters Apache Hadoop sob demanda no HDInsight usando o Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md#trigger-a-pipeline).

Para verificar se os pipelines foram executados, execute uma das seguintes etapas:

* Vá até a seção **Monitorar** no data factory por meio do portal.
* Em Gerenciador de Armazenamento do Azure, vá para sua conta de armazenamento do Data Lake Storage Gen 2. Vá para o sistema de arquivos `files` e, em seguida, vá para a pasta `transformed` e verifique seu conteúdo para ver se o pipeline foi bem-sucedido.

Para obter outras maneiras de transformar dados usando o HDInsight, veja [artigo sobre como usar o Jupyter Notebook](/azure/hdinsight/spark/apache-spark-load-data-run-query).

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Criar uma tabela no cluster da Consulta Interativa para exibir dados no Power BI

1. Copie o arquivo `query.hql` para o cluster LLAP usando o SCP. Substitua `LLAPCLUSTERNAME` pelo nome real e, em seguida, digite o comando:

    ```bash
    scp scripts/query.hql sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Use SSH para acessar o cluster LLAP. Substitua `LLAPCLUSTERNAME` pelo nome real e, em seguida, digite o comando. Caso você não tenha alterado o arquivo `resourcesparameters.json`, a senha será `Thisisapassword1`.

    ```bash
    ssh sshuser@LLAPCLUSTERNAME-ssh.azurehdinsight.net
    ```

3. Use o comando a seguir para executar o script:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Esse script criará uma tabela gerenciada no cluster da Consulta Interativa que pode ser acessado no Power BI.

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um dashboard do Power BI dos dados de vendas

1. Abra o Power BI Desktop.
1. Selecione **Obter Dados**.
1. Pesquise **cluster da Consulta Interativa do HDInsight**.
1. Cole o URI do cluster nele. Ela deve estar no formato `https://LLAPCLUSTERNAME.azurehdinsight.net`.

   Insira `default` no banco de dados.
1. Insira o nome de usuário e a senha que você usa para acessar o cluster.

Depois que os dados forem carregados, você poderá experimentar o painel que deseja criar. Confira os seguintes links para obter uma introdução aos dashboards do Power BI:

* [Introdução aos dashboards para designers do Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Introdução ao serviço do Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Limpar os recursos

Se não desejar continuar usando este aplicativo, exclua todos os recursos usando as etapas a seguir para não ser cobrado por eles.

```azurecli-interactive
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [ETL (extração, transformação e carregamento) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
