---
title: Executar tarefas do Apache Sqoop com o Azure HDInsight (Apache Hadoop)
description: Saiba como usar o Azure PowerShell em uma estação de trabalho para executar importação e exportação do Sqoop entre um cluster do Hadoop e um Banco de Dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951846"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Usar Apache Sqoop com o Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Apache Sqoop no HDInsight para importar e exportar dados entre um cluster HDInsight e um banco de dados Azure SQL.

Embora o Apache Hadoop seja uma escolha natural para o processamento de dados não estruturados e semiestruturados, como logs e arquivos, também pode haver a necessidade de processar dados estruturados que são armazenados em bancos de dados relacionais.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) é uma ferramenta projetada para transferir dados entre clusters do Hadoop e bancos de dados relacionais. Você pode usá-lo para importar dados de um RDBMS (sistema de gerenciamento de banco de dados relacional), como SQL Server, MySQL ou Oracle para o HDFS (Sistema de Arquivos Distribuído) do Hadoop, transformar os dados no Hadoop com o MapReduce ou o Apache Hive e, em seguida, exportar os dados de volta para um RDBMS. Neste artigo, você está usando um banco de dados SQL Server para seu banco de dados relacional.

> [!IMPORTANT]  
> Este artigo configura um ambiente de teste para realizar a transferência de dados. Em seguida, você escolhe um método de transferência de dados para este ambiente a partir de um dos métodos na seção [Executar trabalhos Sqoop](#run-sqoop-jobs), mais abaixo.

Para versões sqoop que são suportadas em clusters HDInsight, veja [O que há de novo nas versões de cluster fornecidas pelo HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Compreender o cenário

O cluster HDInsight é fornecido com alguns dados de exemplo. Você usa estas duas amostras:

* Um arquivo de log do Apache Log4j, que está localizado em `/example/data/sample.log`. Os seguintes logs são extraídos do arquivo:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Uma tabela colmeia chamada `hivesampletable`, que faz `/hive/warehouse/hivesampletable`referência ao arquivo de dados localizado em . A tabela contém alguns dados de dispositivo móvel.
  
  | Campo | Tipo de dados |
  | --- | --- |
  | clientid |string |
  | querytime |string |
  | market |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | country |string |
  | querydwelltime |double |
  | sessionid |BIGINT |
  | sessionpagevieworder |BIGINT |

Neste artigo, você usa esses dois conjuntos de dados para testar a importação e exportação do Sqoop.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Configurar o ambiente de teste

O cluster, o banco de dados SQL e outros objetos são criados através do portal Azure usando um modelo do Azure Resource Manager. O modelo pode ser encontrado em [modelos de partida rápida do Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). O modelo do Gerenciador de recursos chama um pacote bacpac para implantar os esquemas de tabela em um banco de dados SQL.  O pacote bacpac está localizado em um contêiner de blob público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se você quiser usar um contêiner particular para os arquivos bacpac, use os seguintes valores no modelo:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Importar usando um modelo ou o Portal do Azure é compatível apenas com a importação de um arquivo BACPAC do Armazenamento de Blobs do Azure.

1. Selecione a seguinte imagem para abrir o modelo gerenciador de recursos no portal Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Insira as seguintes propriedades:

    |Campo |Valor |
    |---|---|
    |Subscription |Selecione sua assinatura do Azure na lista de paradas.|
    |Resource group |Selecione seu grupo de recursos na lista de paradas ou crie um novo|
    |Location |Selecione uma região na lista suspensa.|
    |Nome do cluster |Insira um nome para o cluster Hadoop. Use apenas letras minúsculas.|
    |Nome de usuário de logon do cluster |Mantenha o valor `admin`pré-povoado .|
    |Senha de logon do cluster |Digite uma senha.|
    |Nome do usuário SSH |Mantenha o valor `sshuser`pré-povoado .|
    |Senha Ssh |Digite uma senha.|
    |Sql Admin Login |Mantenha o valor `sqluser`pré-povoado .|
    |Senha do Sql Admin |Digite uma senha.|
    |_artifacts localização | Use o valor padrão a menos que você queira usar seu próprio arquivo bacpac em um local diferente.|
    |_artifacts Localização Sas Token |Deixe em branco.|
    |Nome do arquivo bacpac |Use o valor padrão a menos que você queira usar seu próprio arquivo bacpac.|
    |Location |Use o valor padrão.|

    O nome do Servidor Azure `<ClusterName>dbserver`SQL será . O nome do `<ClusterName>db`banco de dados será . O nome padrão da `e6qhezrh2pdqu`conta de armazenamento será .

3. Selecione **Concordo com os termos e as condições declarados acima**.

4. Selecione **Comprar**. Você poderá ver um novo bloco intitulado Como enviar a implantação para a implantação do modelo. É preciso sobre cerca de 20 minutos para criar o cluster e o banco de dados SQL.

## <a name="run-sqoop-jobs"></a>Executar trabalhos do Sqoop

O HDInsight pode executar trabalhos do Sqoop usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use** se quiser... | ...um shell **interativo** | ... processamento **em lote** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X ou Windows |
| [SDK .NET para Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (por enquanto) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Limitações

* Exportação em massa - Com o HDInsight baseado em Linux, o conector Sqoop usado para exportar dados para o Microsoft SQL Server ou O Banco de Dados SQL do Azure não suporta atualmente inserções em massa.
* Envio em lote — Com HDInsight baseado em Linux, ao usar o comutador `-batch` ao executar inserções, o Sqoop realizará várias inserções em vez de operações de inserção em lotes.

## <a name="next-steps"></a>Próximas etapas

Agora você aprendeu a usar Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Hive com o HDInsight](../hdinsight-use-hive.md)
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight/Armazenamento de Blob do Azure.
* [Usar o Apache Sqoop para importar e exportar dados entre o Apache Hadoop no HDInsight e o Banco de Dados SQL](./apache-hadoop-use-sqoop-mac-linux.md)