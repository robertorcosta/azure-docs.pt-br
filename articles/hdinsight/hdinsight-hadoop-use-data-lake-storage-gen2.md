---
title: Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight
description: Saiba como usar o Azure Data Lake Storage Gen2 com clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272286"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight

O Azure Data Lake Storage Gen2 é um serviço de armazenamento em nuvem dedicado à análise de big data, baseado no armazenamento Azure Blob. Data Lake Storage Gen2 combina os recursos do armazenamento Azure Blob e do Azure Data Lake Storage Gen1. O serviço resultante oferece recursos do Azure Data Lake Storage Gen1, como semântica do sistema de arquivos, segurança em nível de diretório e nível de arquivos e escalabilidade, juntamente com os recursos de baixo custo, armazenamento hierárquico, alta disponibilidade e recuperação de desastres do armazenamento Azure Blob.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidade o Data Lake Storage Gen2

Data Lake Storage Gen2 está disponível como uma opção de armazenamento para quase todos os tipos de cluster Azure HDInsight como uma conta de armazenamento padrão e adicional. O HBase, no entanto, pode ter apenas uma conta Data Lake Storage Gen2.

Para obter uma comparação completa das opções de criação de clusters usando data lake storage Gen2, consulte [Comparar opções de armazenamento para uso com clusters Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Depois de selecionar data lake storage Gen2 como seu **tipo de armazenamento principal,** não é possível selecionar uma conta Data Lake Storage Gen1 como armazenamento adicional.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Crie um cluster com data lake storage Gen2 através do portal Azure

Para criar um cluster HDInsight que usa data lake storage Gen2 para armazenamento, siga essas etapas para configurar uma conta Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Criar uma identidade gerenciada atribuída ao usuário

Crie uma identidade gerenciada atribuída pelo usuário, se ainda não tiver uma.

1. Faça login no [portal Azure](https://portal.azure.com).
1. No clique superior esquerdo **Crie um recurso**.
1. Na caixa de pesquisa, **digite o usuário atribuído** e clique em **'Identidade gerenciada atribuída ao usuário'.**
1. Clique em **Criar**.
1. Digite um nome para sua identidade gerenciada, selecione a assinatura, o grupo de recursos e a localização corretos.
1. Clique em **Criar**.

Para obter mais informações sobre como as identidades gerenciadas funcionam no Azure HDInsight, consulte [Identidades gerenciadas no Azure HDInsight](hdinsight-managed-identities.md).

![Criar uma identidade gerenciada atribuída ao usuário](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Criar uma conta do Data Lake Storage Gen2

Criar uma conta de armazenamento do Azure Data Lake Storage Gen2.

1. Faça login no [portal Azure](https://portal.azure.com).
1. No clique superior esquerdo **Crie um recurso**.
1. Na caixa de pesquisa, digite **armazenamento** e clique **em Armazenamento .**
1. Clique em **Criar**.
1. Na tela **criar conta de armazenamento:**
    1. Selecione o grupo de assinatura e recursos corretos.
    1. Digite um nome para sua conta Data Lake Storage Gen2. Para obter mais informações sobre convenções de nomeação de contas de armazenamento, consulte [Convenções de nomeação para recursos do Azure](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage).
    1. Clique na guia **Avançado.**
    1. Clique **em Ativado** ao lado do **namespace hierárquico** em **Data Lake Storage Gen2**.
    1. Clique **em Rever + criar**.
    1. Clique **em Criar**

Para obter mais informações sobre outras opções durante a criação da conta de armazenamento, consulte [Quickstart: Crie uma conta de armazenamento Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Captura de tela mostrando a criação da conta de armazenamento no portal do Azure](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configure permissões para a identidade gerenciada na conta Data Lake Storage Gen2

Atribuir a identidade gerenciada à função **De proprietário de dados do Blob de armazenamento** na conta de armazenamento.

1. No [portal do Microsoft Azure](https://portal.azure.com), acesse sua conta de armazenamento.
1. Selecione sua conta de armazenamento e selecione **O controle de acesso (IAM)** para exibir as configurações de controle de acesso da conta. Selecione a guia **Atribuições de função** para ver as atribuições de função atuais.

    ![Captura de tela mostrando as configurações de controle de acesso de armazenamento](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Selecione o botão **+ Adicionar função** para adicionar uma nova função.
1. Na janela **Adicionar função de função,** selecione a função **'Proprietário de dados' ''Proprietário de dados' de armazenamento Blob.** Em seguida, selecione a assinatura que tem a conta de armazenamento e a identidade gerenciada. Em seguida, pesquise para localizar a identidade gerenciada atribuída pelo usuário que você criou anteriormente. Por fim, selecione a identidade gerenciada e ela será listada em **membros selecionados**.

    ![Captura de tela que mostra como atribuir uma função do RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Selecione **Salvar**. A identidade atribuída pelo usuário que você selecionou está agora listada na função selecionada.
1. Ao concluir a configuração inicial, você poderá criar um cluster usando o portal. O cluster deve estar na mesma região do Azure da conta de armazenamento. Na guia **Armazenamento** do menu criação de cluster, selecione as seguintes opções:

    * Para **o tipo de armazenamento principal,** selecione **Azure Data Lake Storage Gen2**.
    * Em **conta de armazenamento principal,** procure e selecione a conta de armazenamento gen2 do Data Lake Storage recém-criada.

    * Em **Identity,** selecione a identidade gerenciada recém-criada pelo usuário.

        ![Configurações do armazenamento para usar o Data Lake Storage Gen2 com o Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Para adicionar uma conta secundária do Data Lake Storage Gen2, no nível da conta de armazenamento, basta atribuir a identidade gerenciada criada anteriormente à nova conta de armazenamento Data Lake Storage Gen2 que você deseja adicionar. Por favor, esteja avisado de que a adição de uma conta secundária do Data Lake Storage Gen2 através da lâmina "Contas adicionais de armazenamento" no HDInsight não é suportada.
    > * Você pode ativar RA-GRS ou RA-ZRS na conta de armazenamento Azure que o HDInsight usa. No entanto, a criação de um cluster contra o ponto final secundário RA-GRS ou RA-ZRS não é suportada.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Crie um cluster com data lake storage Gen2 através do Azure CLI

Você pode [baixar um arquivo de modelo de exemplo](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) e baixar um arquivo de [parâmetros de amostra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de usar o modelo e o trecho de código Azure CLI abaixo, substitua os seguintes espaços reservados com seus valores corretos:

| Espaço reservado | Descrição |
|---|---|
| `<SUBSCRIPTION_ID>` | O ID da sua assinatura do Azure |
| `<RESOURCEGROUPNAME>` | O grupo de recursos onde você deseja que a nova conta de cluster e armazenamento seja criada. |
| `<MANAGEDIDENTITYNAME>` | O nome da identidade gerenciada que receberá permissões em sua conta Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | A nova conta Azure Data Lake Storage Gen2 que será criada. |
| `<CLUSTERNAME>` | O nome do seu cluster HDInsight. |
| `<PASSWORD>` | A senha escolhida para fazer login no cluster usando o SSH, bem como o painel Ambari. |

O trecho de código abaixo faz as seguintes etapas iniciais:

1. Faça login na sua conta do Azure.
1. Define a assinatura ativa onde as operações de criação serão feitas.
1. Cria um novo grupo de recursos para as novas atividades de implantação.
1. Cria uma identidade gerenciada atribuída pelo usuário.
1. Adiciona uma extensão ao Azure CLI para usar recursos para Data Lake Storage Gen2.
1. Cria uma nova conta Data Lake `--hierarchical-namespace true` Storage Gen2 usando o sinalizador.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Em seguida, faça login no portal. Adicione a nova identidade gerenciada atribuída pelo usuário à função **Decontribuinte de dados blob de armazenamento** na conta de armazenamento, conforme descrito na etapa 3 em [Usando o portal Azure](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Depois de atribuir a função à identidade gerenciada atribuída pelo usuário, implante o modelo usando o seguinte trecho de código.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Crie um cluster com data lake storage Gen2 através do Azure PowerShell

O uso do PowerShell para criar um cluster HDInsight com o Azure Data Lake Storage Gen2 não é suportado no momento.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Controle de acesso para Data Lake Storage Gen2 no HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>O Data Lake Storage Gen2 dá suporte a quais tipos de permissões?

O Data Lake Storage Gen2 usa um modelo de controle de acesso que suporta o RBAC (Control de acesso baseado em função) e as listas de controle de acesso semelhantes ao POSIX (ACLs). Data Lake Storage Gen1 suporta listas de controle de acesso apenas para controlar o acesso aos dados.

O RBAC usa atribuições de função para aplicar efetivamente conjuntos de permissões a usuários, grupos e diretores de serviços para os recursos do Azure. Normalmente, esses recursos do Azure são restritos a recursos de nível superior (por exemplo, contas de Armazenamento do Azure). Para o Azure Storage, e também para o Data Lake Storage Gen2, esse mecanismo foi estendido ao recurso do sistema de arquivos.

 Para obter mais informações sobre permissões de arquivos com o RBAC, consulte [o RBAC (Role-Based Access Control, controle de acesso baseado em função) do Azure.](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac)

Para obter mais informações sobre permissões de arquivos com ACLs, consulte [listas de controle de acesso em arquivos e diretórios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>Como controlo o acesso aos meus dados no Data Lake Storage Gen2?

A capacidade do cluster HDInsight de acessar arquivos no Data Lake Storage Gen2 é controlada através de identidades gerenciadas. Uma identidade gerenciada é uma identidade registrada no Azure Active Directory (Azure AD), cujas credenciais são gerenciadas pelo Azure. Com identidades gerenciadas, você não precisa registrar os diretores de serviço no Azure AD ou manter credenciais, como certificados.

Os serviços do Azure têm dois tipos de identidades gerenciadas: atribuídas ao sistema e atribuídas ao usuário. O HDInsight usa identidades gerenciadas atribuídas pelo usuário para acessar o Data Lake Storage Gen2. Uma identidade gerenciada atribuída pelo usuário é criada como um recurso autônomo do Azure. Por meio de um processo de criação, o Microsoft Azure cria uma identidade no locatário do Azure AD confiado pela assinatura em uso. Depois que a identidade é criada, ela pode ser atribuída a uma ou mais instâncias de serviço do Azure.

O ciclo de vida de uma identidade atribuída pelo usuário é gerenciado separadamente do ciclo de vida das instâncias de serviço do Azure a que ela é atribuída. Para obter mais informações sobre identidades gerenciadas, consulte [Como funcionam as identidades gerenciadas para os recursos do Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>Como definir permissões para usuários do Azure AD consultar dados no Data Lake Storage Gen2 usando hive ou outros serviços?

Para definir permissões para os usuários consultarem dados, use os grupos de segurança Azure AD como o principal atribuído em ACLs. Não atribua diretamente permissões de acesso a arquivos a usuários individuais ou diretores de serviço. Quando você usa grupos de segurança Azure AD para controlar o fluxo de permissões, você pode adicionar e remover usuários ou diretores de serviço sem reaplicar ACLs a toda uma estrutura de diretório. Você só precisa adicionar ou remover os usuários do grupo de segurança do Azure AD pertinente. As ACLs não são herdadas, então a reaplicação de ACLs requer a atualização da ACL em cada arquivo e subdiretório.

## <a name="access-files-from-the-cluster"></a>Acessar arquivos do cluster

Existem várias maneiras de acessar os arquivos no Data Lake Storage Gen2 a partir de um cluster HDInsight.

* **Usando o nome totalmente qualificado**. Com essa abordagem, é necessário fornecer o caminho completo para o arquivo que você deseja acessar.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Usando o formato de caminho encurtado**. Com esta abordagem, você substitui o caminho até a raiz do cluster por:

    ```
    abfs:///<file.path>/
    ```

* **Usando o caminho relativo**. Com essa abordagem, você só fornece o caminho relativo para o arquivo que deseja acessar.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Exemplos de acesso a dados

Exemplos são baseados em uma [conexão ssh](./hdinsight-hadoop-linux-use-ssh-unix.md) com o nó principal do cluster. Os exemplos usam todos os três esquemas URI. Substituir `CONTAINERNAME` `STORAGEACCOUNT` e com os valores relevantes

#### <a name="a-few-hdfs-commands"></a>Alguns comandos hdfs

1. Crie um arquivo no armazenamento local.

    ```bash
    touch testFile.txt
    ```

1. Crie diretórios no armazenamento em cluster.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copiar dados do armazenamento local para o armazenamento em cluster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Listar o conteúdo do diretório no armazenamento em cluster.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Criando uma mesa colmeia

Três locais de arquivo são mostrados para fins ilustrativos. Para execução real, use `LOCATION` apenas uma das entradas.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Próximas etapas

* [Integração do Azure HDInsight com a versão prévia do Data Lake Storage Gen2 – ACL e atualização de segurança](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/)
* [Introdução ao Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutorial: Extraia, transforme e carregue dados usando consulta interativa no Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
