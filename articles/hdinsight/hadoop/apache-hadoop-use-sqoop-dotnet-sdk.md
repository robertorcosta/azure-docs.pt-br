---
title: Executar trabalhos do Apache Sqoop usando .NET e HDInsight – Azure
description: Saiba como usar o SDK do .NET do HDInsight para executar a importação e exportação do Apache Sqoop entre um cluster Apache Hadoop e um banco de dados SQL do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "76157049"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar trabalhos do Apache Sqoop usando .NET SDK para Apache Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como usar o SDK do .NET do Azure HDInsight para executar trabalhos do Apache Sqoop no HDInsight para importar e exportar entre um cluster HDInsight e um banco de dados SQL do Azure ou um banco de dados SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão da [configuração do ambiente de teste](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) de [usar o Apache Sqoop com o Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* [Visual Studio](https://visualstudio.microsoft.com/vs/community/).

* Familiaridade com o Sqoop. Para obter mais informações, consulte [Guia do usuário do Sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Usar o Sqoop em clusters HDInsight com o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. Nesta seção, você cria um aplicativo de console em C# para exportar `hivesampletable` o para a tabela de banco de dados SQL do Azure que você criou com os pré-requisitos.

## <a name="set-up"></a>Configuração

1. Inicie o Visual Studio e crie um aplicativo de console em C#.

1. Navegue até **ferramentas** > **Gerenciador** > de pacotes NuGet**console do Gerenciador de pacotes** e execute o seguinte comando:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Exportação do Sqoop

De Hive para SQL Server.  Este exemplo exporta dados da tabela Hive `hivesampletable` para a `mobiledata` tabela no banco de dados SQL.

1. Use o código a seguir no arquivo Program.cs. Edite o código para definir os valores `ExistingClusterName`para, `ExistingClusterPassword`e.

    ```csharp
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
            private const string ExistingClusterPassword = "<Cluster User Password>";
            private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
            private const string ExistingClusterUsername = "admin";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
    
                SubmitSqoopJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitSqoopJob()
            {
                var sqlDatabaseServerName = ExistingClusterName + "dbserver";
                var sqlDatabaseLogin = "sqluser";
                var sqlDatabaseLoginPassword = ExistingClusterPassword;
                var sqlDatabaseDatabaseName = ExistingClusterName + "db";
    
                // Connection string for using Azure SQL Database; Comment if using SQL Server
                var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                // Connection string for using SQL Server; Uncomment if using SQL Server
                // var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
    
                //sqoop start
                var tableName = "mobiledata";
    
                var parameters = new SqoopJobSubmissionParameters
                {
                     Command = "export --connect " + connectionString + " --table " + tableName + " --hcatalog-table hivesampletable"
                };
                //sqoop end
    
                System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                System.Console.WriteLine("Validating that the response is as expected...");
                System.Console.WriteLine("Response status code is " + response.StatusCode);
                System.Console.WriteLine("Validating the response object...");
                System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
            }
        }
    }
    ```

1. Para executar o programa, aperte a tecla **F5**.

## <a name="sqoop-import"></a>Importação do Sqoop

De SQL Server para o armazenamento do Azure. Este exemplo depende da exportação acima que foi executada.  Este exemplo importa dados da `mobiledata` tabela no banco de dados SQL para `wasb:///tutorials/usesqoop/importeddata` o diretório na conta de armazenamento padrão do cluster.

1. Substitua o código acima no `//sqoop start //sqoop end` bloco pelo seguinte código:

    ```csharp
    var tableName = "mobiledata";
    var exportDir = "/tutorials/usesqoop/importeddata";
    
    var parameters = new SqoopJobSubmissionParameters
    {
        Command = "import --connect " + connectionString + " --table " + tableName + " --target-dir " +  exportDir + " --fields-terminated-by \\t --lines-terminated-by \\n -m 1"
    };
    ```

1. Para executar o programa, aperte a tecla **F5**.

## <a name="limitations"></a>Limitações

O HDInsight baseado em Linux tem as seguintes limitações:

* Exportação em massa: o conector do Sqoop que é usado para exportar dados para Microsoft SQL Server ou o Azure SQL Database atualmente não dá suporte a inserções em massa.

* Envio em lote: usando a `-batch` opção, Sqoop executa várias inserções em vez de executar o lote de operações de inserção.

## <a name="next-steps"></a>Próximas etapas

Agora você aprendeu a usar o Sqoop. Para obter mais informações, consulte:

* [Usar o Apache Oozie com o HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho Oozie.
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight ou no Armazenamento de Blobs do Azure.
