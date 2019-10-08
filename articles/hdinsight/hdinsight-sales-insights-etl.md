---
title: 'Tutorial: Criar um pipeline ETL (extração, transformação e carregamento) completo para obter insights de vendas'
description: Saiba como usar criar pipelines ETL com o Azure HDInsight para obter insights de dados de vendas usando clusters sob demanda do Spark e o Power BI.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: hrasheed
ms.openlocfilehash: 52509f886ac9882c372de401ca163ccef418837f
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695701"
---
# <a name="tutorial-create-an-end-to-end-data-pipeline-to-derive-sales-insights"></a>Tutorial: Criar um pipeline de dados completo para obter insights de vendas

Neste tutorial, você criará um pipeline de dados completo, que executa operações de extração, transformação e carregamento. O pipeline usará clusters Apache Spark e Apache Hive em execução no Azure HDInsight para consulta e manipulação dos dados. Outras tecnologias usadas incluem o Data Lake Storage Gen2 para armazenamento de dados e o Power BI para visualização.

Esse pipeline de dados combina os dados de todos os diferentes repositórios, remove os dados indesejados, acrescenta novos dados e carrega tudo isso novamente no armazenamento para visualizar insights empresariais. Leia mais sobre pipelines ETL [(extração, transformação e carregamento) em escala](./hadoop/apache-hadoop-etl-at-scale.md).

![Arquitetura de ETL](./media/hdinsight-sales-insights-etl/architecture.png)

## <a name="prerequisites"></a>Pré-requisitos

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Baixe o [Power BI Desktop](https://www.microsoft.com/download/details.aspx?id=45331) para visualizar insights empresariais no final deste tutorial.

## <a name="create-resources"></a>Criar recursos

### <a name="clone-the-repository-with-scripts-and-data"></a>Clonar o repositório com scripts e dados

1. Entre no [Portal do Azure](https://portal.azure.com)
1. Abra o Cloud Shell na barra de menus superior. Selecione sua assinatura para criar um compartilhamento de arquivo, caso receba um aviso do Azure Cloud Shell.

    ![Abrir o Azure Cloud Shell](./media/hdinsight-sales-insights-etl/hdinsight-sales-insights-etl-click-cloud-shell.png)
1. Selecione "Bash" no menu suspenso "Selecionar ambiente".
1. Entre em sua conta do Azure e defina a assinatura. 
1. Configure o grupo de recursos para o projeto.
    1. Escolha um nome exclusivo de grupo de recursos.
    1. Execute o snippet de código abaixo no Azure Cloud Shell para definir variáveis que serão usadas em etapas posteriores

        ```azurecli-interactive 
        resourceGroup="<RESOURCE GROUP NAME>"
        subscriptionID="<SUBSCRIPTION ID>"
        
        az account set --subscription $subscriptionID
        az group create --name $resourceGroup --location westus
        ```

1. Baixe os dados e os scripts deste tutorial no [repositório de ETL de insights de vendas do HDInsight](https://github.com/Azure-Samples/hdinsight-sales-insights-etl) digitando os seguintes comandos no Cloud Shell:

        ```azurecli-interactive 
        git clone https://github.com/Azure-Samples/hdinsight-sales-insights-etl.git
        cd hdinsight-sales-insights-etl
        ```

1. Digite `ls` no prompt do shell para ver se os seguintes arquivos e diretórios foram criados:

    ```output
    /salesdata/
    /scripts/
    /templates/
    ```

### <a name="deploy-azure-resources-needed-for-the-pipeline"></a>Implantar os recursos do Azure necessários para o pipeline 

1. Adicionar permissões de execução para o script `chmod +x scripts/*.sh`
1. Use o comando `./scripts/resources.sh <RESOURCE_GROUP_NAME> <LOCATION>` para executar o script para implantar os seguintes recursos no Azure:

    1. Uma conta de Armazenamento de Blobs do Azure – essa conta armazenará os dados de vendas da empresa
    2. Uma conta do Azure Data Lake Storage Gen2 – essa conta servirá como a conta de armazenamento para ambos os clusters HDInsight. Leia mais sobre o HDInsight e o Data Lake Storage Gen2 na [integração do Azure HDInsight ao Data Lake Storage Gen2](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/).
    3. Uma identidade gerenciada atribuída ao usuário – essa conta fornece aos clusters HDInsight o acesso à conta do Data Lake Storage Gen2.
    4. Um cluster Apache Spark – esse cluster será usado para limpar e transformar os dados brutos
    5. Um cluster da Consulta Interativa do Apache Hive – esse cluster permitirá consultar os dados de vendas visualizando-os com o Power BI
    6. Uma rede virtual do Azure compatível com as regras do NSG (grupo de segurança de rede) – essa rede virtual permite que os clusters se comuniquem e também protejam sua comunicação. 

A criação do cluster pode levar cerca de 20 minutos.

O script `resources.sh` contém o comando a seguir, que usa um modelo do Resource Manager (`resourcestemplate.json`) para criar os recursos especificados com a configuração desejada.

```azurecli-interactive 
az group deployment create --name ResourcesDeployment \
    --resource-group $resourceGroup \
    --template-file resourcestemplate.json \
    --parameters "@resourceparameters.json"
```

O script `resources.sh` também carrega os arquivos CSV dos dados de vendas na conta de Armazenamento de Blobs recém-criada usando o comando:

```
az storage blob upload-batch -d rawdata \
    --account-name <BLOB STORAGE NAME> -s ./ --pattern *.csv
```

A senha padrão usada para acesso SSH aos clusters é `Thisisapassword1`. Caso deseje alterar a senha, navegue até o arquivo `resourcesparameters.json` e altere a senha para os parâmetros `sparksshPassword`, `sparkClusterLoginPassword`, `llapClusterLoginPassword` e `llapsshPassword`.

### <a name="verify-deployment-and-collect-resource-information"></a>Verificar a implantação e coletar informações de recursos

1. Caso deseje verificar o status da implantação, navegue até o grupo de recursos no portal do Azure. Clique em **Implantações** em **Configurações**. Clique no nome da implantação `ResourcesDeployment`. Aqui você pode ver os recursos que foram implantados com êxito e aqueles que ainda estão em andamento.
1. Depois que a implantação for concluída, acesse o portal do Azure > **Grupos de recursos** > <NOME_DO_GRUPO_DE_RECURSOS>
1. Localize a nova conta de armazenamento do Azure que foi criada para armazenar os arquivos de vendas. O nome da conta de armazenamento começa com `blob` e, em seguida, contém uma cadeia de caracteres aleatória. 
    1. Anote o nome da conta de armazenamento para uso posterior.
    1. Clique no nome da conta de Armazenamento de Blobs.
    1. No lado esquerdo do portal, em **Configurações**, clique em **Chaves de acesso**.
    1. Copie a cadeia de caracteres na caixa **Chave1** e salve-a para uso posterior.
1. Localize a conta do Data Lake Storage Gen2 que foi criada como o armazenamento para os clusters HDInsight. Essa conta está localizada no mesmo grupo de recursos da conta de Armazenamento de Blobs, mas começa com `adlsgen2`.
    1. Anote o nome da conta do Data Lake Storage Gen2.
    1. Clique no nome da conta do Data Lake Storage Gen2.
    1. No lado esquerdo do portal, em **Configurações**, clique em **Chaves de acesso**
    1. Copie a cadeia de caracteres na caixa **Chave1** e salve-a para uso posterior.

> [!Note]
> Depois de saber os nomes das contas de armazenamento, você também pode obter as chaves de conta usando o seguinte comando no prompt do Azure Cloud Shell:
> ```azurecli-interactive
> az storage account keys list \
>    --account-name <STORAGE NAME> \
>    --resource-group $rg \
>    --output table
> ```

### <a name="create-an-azure-data-factory"></a>Criar um Azure Data Factory

O Azure Data Factory é uma ferramenta que ajuda a automatizar Azure Pipelines. Não é a única maneira de realizar essas tarefas, mas é uma ótima forma de automatizar esses processos. Para obter mais informações sobre o Azure Data Factory, leia mais sobre ele na [Documentação do Azure Data Factory](https://azure.microsoft.com/services/data-factory/). 

Esse Azure Data Factory terá um pipeline com duas atividades: 

1. A primeira atividade copiará os dados do Armazenamento de Blobs do Azure para a conta de armazenamento do Data Lake Storage Gen2 para simular a ingestão de dados.
2. A segunda atividade transformará os dados no cluster Spark. O script transforma os dados removendo colunas indesejadas, bem como acrescentando uma nova coluna que calcula a receita gerada por uma única transação.

Para configurar o pipeline do Azure Data Factory, execute o script `adf.sh`:

1. Adicionar permissões de execução no arquivo usando `chmod +x adf.sh`
1. Execute o script com `./adf.sh` 

Esse script realiza as seguintes ações:

1. Cria uma entidade de serviço com permissões `Storage Blob Data Contributor` na conta de armazenamento do Data Lake Storage Gen2.
1. Obtém um token de autenticação para autorizar solicitações POST para a [API REST do Sistema de Arquivos do Data Lake Storage Gen2](https://docs.microsoft.com/rest/api/storageservices/datalakestoragegen2/filesystem/create).
1. Preenche o nome real de sua conta de armazenamento do Data Lake Storage Gen2 nos arquivos `sparktransform.py` e `query.hql`.
1. Obtém as chaves de armazenamento para o Data Lake Storage Gen2 e a conta de Armazenamento de Blobs.
1. Cria outra implantação de recurso para criar um pipeline do Azure Data Factory, com suas atividades e seus serviços vinculados associados. Ela passa as chaves de armazenamento como parâmetros para o arquivo de modelo, de modo que os serviços vinculados possam acessar as contas de armazenamento corretamente.

O pipeline do ADF é implantado usando o seguinte comando:

```azurecli-interactive
az group deployment create --name ADFDeployment \
    --resource-group $resourceGroup \
    --template-file adftemplate.json \
    --parameters "@adfparameters.json"
```

## <a name="run-the-data-pipeline"></a>Executar o pipeline de dados

### <a name="trigger-the-adf-activities"></a>Disparar as atividades do ADF

A primeira atividade no pipeline do ADF que você criou move os dados do Armazenamento de Blobs para o Data Lake Storage Gen2. A segunda atividade aplica as transformações do Spark nos dados e salva os arquivos CSV transformados em uma nova localização. O pipeline inteiro pode levar alguns minutos para ser concluído.

Para disparar os pipelines, você pode:

1. Executar os comandos a seguir para disparar os pipelines do ADF no PowerShell. 

    ```powershell
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "CopyPipeline_k8z" 
    Invoke-AzDataFactoryV2Pipeline -DataFactory $df -PipelineName "sparkTransformPipeline"
    ```

1. Você também pode abrir o Data Factory, selecionar Criar e Monitorar e disparar o pipeline de cópia e, em seguida, o pipeline do Spark no portal. Confira [Criar clusters Apache Hadoop sob demanda no HDInsight usando o Azure Data Factory](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-create-linux-clusters-adf#trigger-a-pipeline) para obter informações sobre como disparar pipelines por meio do portal.

Para verificar se os pipelines foram executados, execute uma das seguintes etapas:

1. Navegue até a seção **Monitorar** no Azure Data Factory por meio do portal.
2. Acesse o Gerenciador de Armazenamento da conta de armazenamento do Data Lake Storage Gen2, acesse o Sistema de Arquivos do `files`, navegue até a pasta `transformed` e verifique seu conteúdo para ver se o pipeline foi bem-sucedido.

Para obter outras maneiras de transformar dados usando o HDInsight, confira este artigo sobre como usar o [Jupyter Notebook](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-load-data-run-query)

### <a name="create-a-table-on-the-interactive-query-cluster-to-view-data-on-power-bi"></a>Criar uma tabela no cluster da Consulta Interativa para exibir dados no Power BI

1. Copie o arquivo query.hql para o cluster LLAP usando o SCP:

    ```
    scp scripts/query.hql sshuser@<clustername>-ssh.azurehdinsight.net:/home/sshuser/
    ```

2. Execute o SSH no cluster LLAP usando o comando a seguir e, em seguida, insira sua senha. Caso você não tenha alterado o arquivo `resourcesparameters.json`, a senha será `Thisisapassword1`.

    ```
    ssh sshuser@<clustername>-ssh.azurehdinsight.net
    ```

3. Executar o comando a seguir para executar o script

    ```
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -f query.hql
    ```

Esse script criará uma tabela gerenciada no cluster da Consulta Interativa que pode ser acessado no Power BI. 

### <a name="create-a-power-bi-dashboard-from-sales-data"></a>Criar um dashboard do Power BI dos dados de vendas

1. Abra o Power BI Desktop
1. Selecione **Obter Dados**.
1. Pesquise **cluster da Consulta Interativa do HDInsight**.
1. Cole o URI do cluster nele. Ele deverá estar no formato `https://<LLAP CLUSTER NAME>.azurehdinsight.net` tipo `default` para o banco de dados.
1. Insira o nome de usuário e a senha que você usa para acessar o cluster.

Depois que os dados forem carregados, você poderá experimentar o dashboard que deseja criar. Confira os seguintes links para obter uma introdução aos dashboards do Power BI:

* [Introdução aos dashboards para designers do Power BI](https://docs.microsoft.com/power-bi/service-dashboards)
* [Tutorial: Introdução ao serviço do Power BI](https://docs.microsoft.com/power-bi/service-get-started)

## <a name="clean-up-resources"></a>Limpar recursos

Se não desejar continuar usando este aplicativo, exclua todos os recursos com as etapas a seguir para não ser cobrado por eles.

```azurecli-interactive 
az group delete -n $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [ETL (extração, transformação e carregamento) em escala](./hadoop/apache-hadoop-etl-at-scale.md)
