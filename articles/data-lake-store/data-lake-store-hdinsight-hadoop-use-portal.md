---
title: Criar clusters Azure HDInsight com Data Lake Storage Gen1 - portal
description: Use o portal do Azure para criar e usar clusters do HDInsight com o Armazenamento de Data Lake do Azure Gen1
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 1d1368ef8ffb474c6bec1240f567f043961597fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265565"
---
# <a name="create-hdinsight-clusters-with-azure-data-lake-storage-gen1-by-using-the-azure-portal"></a>Criar clusters do HDInsight com o Gen1 do Azure Data Lake Storage usando o portal do Azure

> [!div class="op_single_selector"]
> * [Use o portal Azure](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usar o PowerShell (para armazenamento padrão)](data-lake-store-hdinsight-hadoop-use-powershell-for-default-storage.md)
> * [Usar o PowerShell (para armazenamento adicional)](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usar o Resource Manager](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>
>

Aprenda a usar o portal do Azure para criar um cluster do HDInsight com uma conta do Azure Data Lake Storage Gen1 como o armazenamento padrão ou um armazenamento adicional. Embora o armazenamento adicional seja opcional para um cluster HDInsight, recomenda-se armazenar seus dados de negócios nas contas adicionais de armazenamento.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que cumpriu os seguintes requisitos:

* **Uma assinatura do Azure.** Acesse [Obter uma avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma conta Data Lake Storage Gen1**. Siga as instruções do [Introdução ao Azure Data Lake armazenamento Gen1, usando o portal do Azure](data-lake-store-get-started-portal.md). Você também deve criar uma pasta raiz na conta.  Neste artigo, uma pasta raiz chamada __/clusters__ é usada.
* **Uma entidade de serviço do Azure Active Directory**. Este guia de como fazer fornece instruções sobre como criar um diretor de serviço no Azure Active Directory (Azure AD). No entanto, para criar uma entidade de serviço, você deve ser um administrador do Azure AD. Se você é um administrador, você pode pular este pré-requisito e continuar.

>[!NOTE]
>Você só pode criar um diretor de serviço se você for um administrador azure AD. O administrador do AD do Azure AD deve criar uma entidade de serviço antes de criar um cluster do HDInsight com o Data Lake Storage Gen1. Além disso, a entidade de serviço deve ser criada com um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate).
>

## <a name="create-an-hdinsight-cluster"></a>Crie um cluster HDInsight

Nesta seção, você cria um cluster do HDInsight com contas do Data Lake Storage Gen1 como o padrão ou o armazenamento adicional. Este artigo se concentra apenas na parte de configuração de contas Data Lake Storage Gen1. Para obter informações gerais sobre a criação do cluster, consulte [Criar clusters do Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-default-storage"></a>Criar um cluster com o Data Lake Storage Gen1 como armazenamento padrão

Para criar um cluster HDInsight com uma conta Data Lake Storage Gen1 como a conta de armazenamento padrão:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento principal**, selecione **Armazenamento do Data Lake do Azure Gen1** e insira as seguintes informações:

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.adls.storage.png "Adicionar entidade de serviço no cluster HDInsight")

    * **Selecione a conta do Data Lake Store**: Selecione uma conta existente do Data Lake Storage Gen1. Uma conta existente do Data Lake armazenamento Gen1 é necessária.  Consulte [Pré-requisitos](#prerequisites).
    * **Caminho raiz**: insira um caminho em que os arquivos específicos de cluster deverão ser armazenados. Na captura de tela, é __/clusters/myhdiadlcluster/__, em que a pasta __/clusters__ deve existir e o Portal cria a pasta *myhdicluster*.  O *myhdicluster* é o nome do cluster.
    * **Acesso ao Data Lake Store**: Configure o acesso entre a conta do Data Lake Storage Gen1 e o cluster do HDInsight. Para obter instruções, consulte [Configurar o acesso do Data Lake Storage Gen1](#configure-data-lake-storage-gen1-access).
    * **Contas adicionais de armazenamento**: Adicione contas de armazenamento Azure como contas adicionais de armazenamento para o cluster. Para adicionar outras contas do Data Lake Storage Gen1 é feito dando as permissões do cluster em dados em mais contas do Data Lake Storage Gen1 enquanto configura uma conta do Data Lake Storage Gen1 como o tipo de armazenamento primário. Ver [acesso de configurar o Data Lake armazenamento Gen1](#configure-data-lake-storage-gen1-access).

4. Em **Acesso ao Data Lake Store**, clique em **Selecionar** e continue com a criação do cluster, conforme descrito em [Criar clusters Hadoop no HDInsight](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md).

### <a name="create-a-cluster-with-data-lake-storage-gen1-as-additional-storage"></a>Criar um cluster com o Data Lake Storage Gen1 como armazenamento adicional

As instruções a seguir criam um cluster do HDInsight com uma conta de armazenamento do Azure como armazenamento padrão e uma conta do Data Lake Storage Gen1 como um armazenamento adicional.

Para criar um cluster HDInsight com uma conta Data Lake Storage Gen1 como uma conta de armazenamento adicional:

1. Faça login no [portal Azure](https://portal.azure.com).
2. Siga [Criar clusters](../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md#create-clusters) para obter as informações gerais sobre a criação de clusters do HDInsight.
3. Na folha **Armazenamento**, em **Tipo de armazenamento primário**, selecione **Armazenamento do Azure** e, em seguida, insira as seguintes informações:

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.1.png "Adicionar entidade de serviço no cluster HDInsight")

    * **Método de seleção** - Para especificar uma conta de armazenamento que faz parte da sua assinatura do Azure, selecione **Minhas assinaturas**e selecione a conta de armazenamento. Para especificar uma conta de armazenamento que está fora de sua assinatura do Azure, selecione **Chave de acesso** e, em seguida, forneça as informações da conta de armazenamento externa.

    * **Contêiner padrão** - Use o valor padrão ou especifique seu próprio nome.
    * **Contas adicionais de armazenamento** - Adicione mais contas de armazenamento Do Zure como o armazenamento adicional.
    * **Acesso ao Data Lake Store** - Configure o acesso entre a conta Data Lake Storage Gen1 e o cluster HDInsight. Para obter [instruções, consulte Configure Data Lake Storage Gen1 access](#configure-data-lake-storage-gen1-access).

## <a name="configure-data-lake-storage-gen1-access"></a>Configurar o acesso ao Data Lake armazenamento Gen1

Nesta seção, você configura o acesso do Data Lake Storage Gen1 a partir de clusters do HDInsight usando uma entidade de serviço do Azure Active Directory.

### <a name="specify-a-service-principal"></a>Especificar uma entidade de serviço

No portal do Azure, você pode usar uma entidade de serviço existente ou criar uma nova.

Para criar um diretor de serviço do portal Azure:

1. Selecione **o acesso da Data Lake Store** na lâmina armazenamento.
1. Na lâmina **de acesso Data Lake Storage Gen1,** selecione Criar **novo**.
1. Selecione **o diretor do serviço**e siga as instruções para criar um diretor de serviço.
1. Baixe o certificado se você optar por usá-lo novamente no futuro. É útil baixar o certificado se você desejar usar a mesma entidade de serviço ao criar clusters HDInsight adicionais.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.2.png "Adicionar entidade de serviço no cluster HDInsight")

1. Selecione **Acesso** para configurar o acesso à pasta.  Consulte [Configurar permissões de arquivo](#configure-file-permissions).

Para usar um diretor de serviço existente no portal Azure:

1. Selecione **o acesso da Data Lake Store**.
1. Na lâmina **de acesso Data Lake Storage Gen1,** selecione Usar a **existente**.
1. Selecione **o principal do serviço**e selecione um diretor de serviço.
1. Carregue o certificado (arquivo .pfx) associado à entidade de serviço selecionada e insira a senha do certificado.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.5.png "Adicionar entidade de serviço no cluster HDInsight")

1. Selecione **Acesso** para configurar o acesso à pasta.  Consulte [Configurar permissões de arquivo](#configure-file-permissions).

### <a name="configure-file-permissions"></a><a name="configure-file-permissions"></a>Configurar permissões de arquivo

A configuração é diferente dependendo se a conta é usada como armazenamento padrão ou uma conta de armazenamento adicional:

* Usado como o armazenamento padrão

  * permissão no nível raiz da conta do Data Lake armazenamento Gen1
  * permissão no nível raiz do armazenamento de cluster HDInsight. Por exemplo, a pasta __/clusters__ usada anteriormente no tutorial.

* Usar como um armazenamento adicional

  * Permissões nas pastas em que você precisa de acesso ao arquivo.

Para atribuir permissão no nível raiz da conta Data Lake Storage Gen1:

1. Na lâmina **de acesso Data Lake Storage Gen1,** selecione **Acesso**. A folha **Selecionar permissões de arquivo** é aberta. Ele lista todas as contas do Data Lake Storage Gen1 na sua assinatura.
1. Passe o mouse (não clique) com o mouse sobre o nome da conta Data Lake Storage Gen1 para tornar a caixa de seleção visível e marque a caixa de seleção.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3.png "Adicionar entidade de serviço no cluster HDInsight")

   Por padrão, __READ,__ __WRITE__e __EXECUTE__ são todos selecionados.

1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **Executar** para atribuir permissão.
1. Selecione **Feito**.

Para atribuir permissão no nível raiz do cluster HDInsight:

1. Na lâmina **de acesso Data Lake Storage Gen1,** selecione **Acesso**. A folha **Selecionar permissões de arquivo** é aberta. Ele lista todas as contas do Data Lake Storage Gen1 na sua assinatura.
1. Na lâmina **Seleto de permissões de arquivo,** selecione o nome da conta Data Lake Storage Gen1 para mostrar seu conteúdo.
1. Selecione a raiz de armazenamento de cluster HDInsight marcando a caixa de seleção à esquerda da pasta. De acordo com a captura de tela anterior, a raiz de armazenamento do cluster é a pasta __/ clusters__ que você especificou ao selecionar o Data Lake Storage Gen1 como armazenamento padrão.
1. Defina as permissões na pasta.  Por padrão, as opções ler, gravar e executar estão selecionadas.
1. Clique em **Selecionar** na parte inferior da página.
1. Selecione **Executar**.
1. Selecione **Feito**.

Se você estiver usando o Data Lake Storage Gen1 como armazenamento adicional, atribua permissão somente para as pastas que deseja acessar no cluster do HDInsight. Por exemplo, na captura de tela abaixo, você fornece acesso apenas à pasta **mynewfolder** em uma conta do Data Lake Storage Gen1.

![Atribuir permissões principais de serviço ao cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.3-1.png "Atribuir permissões principais de serviço ao cluster HDInsight")

## <a name="verify-cluster-setup"></a><a name="verify-cluster-set-up"></a>Verificar configuração de cluster

Após a conclusão da configuração do cluster, na folha do cluster, verifique os resultados executando uma ou ambas as etapas a seguir:

* Para verificar se o armazenamento associado para o cluster é a conta Data Lake Storage Gen1 que você especificou, selecione **Contas de armazenamento** no painel esquerdo.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6-1.png "Adicionar entidade de serviço no cluster HDInsight")

* Para verificar se o principal do serviço está corretamente associado ao cluster HDInsight, selecione o **acesso Data Lake Storage Gen1** no painel esquerdo.

    ![Adicionar entidade de serviço no cluster HDInsight](./media/data-lake-store-hdinsight-hadoop-use-portal/hdi.adl.6.png "Adicionar entidade de serviço no cluster HDInsight")

## <a name="examples"></a>Exemplos

Depois de configurar o cluster com data lake storage Gen1 como seu armazenamento, veja esses exemplos de como usar o cluster HDInsight para analisar os dados armazenados no Data Lake Storage Gen1.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-primary-storage"></a>Executar uma consulta do Hive em relação a dados em uma conta do Data Lake Storage Gen1 (como armazenamento primário)

Para executar uma consulta do Hive, use a interface de exibições do Hive no portal do Ambari. Para obter instruções sobre como usar as exibições do Hive no Ambari, consulte [Usar a exibição do Hive com o Hadoop no HDInsight](../hdinsight/hadoop/apache-hadoop-use-hive-ambari-view.md).

Quando você trabalha com dados em uma conta do Data Lake Storage Gen1, há algumas sequências a serem alteradas.

Se você usar, por exemplo, o cluster criado com o Data Lake Storage Gen1 como armazenamento primário, o caminho para os dados será: *adl: // <data_lake_storage_gen1_account_name> /azuredatalakestore.net/path/to/file*. Uma consulta do Hive para criar uma tabela a partir de dados de amostra armazenados na conta do Data Lake Storage Gen1 se parece com a seguinte instrução:

    CREATE EXTERNAL TABLE websitelog (str string) LOCATION 'adl://hdiadlsg1storage.azuredatalakestore.net/clusters/myhdiadlcluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/'

Descrições:

* `adl://hdiadlsg1storage.azuredatalakestore.net/` é a raiz da conta do Data Lake armazenamento Gen1.
* `/clusters/myhdiadlcluster` é a raiz dos dados do cluster especificada durante a criação do cluster.
* `/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/` é a localização do arquivo de exemplo usado na consulta.

### <a name="run-a-hive-query-against-data-in-a-data-lake-storage-gen1-account-as-additional-storage"></a>Executar uma consulta do Hive em relação a dados em uma conta do Data Lake Storage Gen1 (como armazenamento adicional)

Se o cluster que você criou usar o armazenamento de Blobs como armazenamento padrão, os dados de amostra não estarão contidos na conta Data Lake Storage Gen1 usada como armazenamento adicional. Nesse caso, primeiro transfira os dados do armazenamento do Blob para a conta do Data Lake Storage Gen1 e execute as consultas conforme mostrado no exemplo anterior.

Para obter informações sobre como copiar dados do armazenamento do Blob para uma conta do Data Lake Storage Gen1, consulte os seguintes artigos:

* [Use o Distcp para copiar dados entre os blobs de Armazenamento do Azure e o Data Lake Storage Gen1](data-lake-store-copy-data-wasb-distcp.md)
* [Use o AdlCopy para copiar dados de blobs do Azure Storage para o Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)

### <a name="use-data-lake-storage-gen1-with-a-spark-cluster"></a>Usar o Data Lake armazenamento Gen1 com um cluster Spark

Você pode usar um cluster do Spark para executar trabalhos do Spark em dados armazenados em uma conta do Data Lake Storage Gen1. Para obter mais informações, consulte [Use HDInsight Spark cluster para analisar dados no Data Lake Storage Gen1](../hdinsight/spark/apache-spark-use-with-data-lake-store.md).

### <a name="use-data-lake-storage-gen1-in-a-storm-topology"></a>Use o Data Lake Storage Gen1 em uma topologia do Storm

Você pode usar a conta Data Lake Storage Gen1 para gravar dados de uma topologia do Storm. Para obter instruções sobre como alcançar esse cenário, consulte [Usar o armazenamento de dados do Windows Azure Gen1 com o Apache Storm com o HDInsight](../hdinsight/storm/apache-storm-write-data-lake-store.md).

## <a name="see-also"></a>Confira também

* [Usar Data Lake Storage Gen1 com clusters HDInsight do Azure](../hdinsight/hdinsight-hadoop-use-data-lake-store.md)
* [PowerShell: crie um cluster do HDInsight para usar o Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-powershell.md)

[makecert]: https://msdn.microsoft.com/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/library/windows/desktop/ff550672(v=vs.85).aspx
