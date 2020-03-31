---
title: Executar trabalhos do Apache Sqoop usando .NET e HDInsight – Azure
description: Aprenda a usar o HDInsight .NET SDK para executar a importação e exportação do Apache Sqoop entre um cluster Apache Hadoop e um Banco de Dados SQL Do Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 01/14/2020
ms.openlocfilehash: f0f767273a40bc91b1d49477c896b0b157623106
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157049"
---
# <a name="run-apache-sqoop-jobs-by-using-net-sdk-for-apache-hadoop-in-hdinsight"></a>Executar trabalhos do Apache Sqoop usando .NET SDK para Apache Hadoop no HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar o Azure HDInsight .NET SDK para executar os trabalhos do Apache Sqoop no HDInsight para importar e exportar entre um cluster HDInsight e um banco de dados Azure SQL ou SQL Server.

## <a name="prerequisites"></a>Pré-requisitos

* Conclusão do ambiente de [teste configurar](./hdinsight-use-sqoop.md#create-cluster-and-sql-database) do Uso [apache Sqoop com Hadoop no HDInsight](./hdinsight-use-sqoop.md).

* [Estúdio Visual](https://visualstudio.microsoft.com/vs/community/).

* Familiaridade com Sqoop. Para obter mais informações, consulte [o Guia do Usuário sqoop](https://sqoop.apache.org/docs/1.4.7/SqoopUserGuide.html).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Usar o Sqoop em clusters HDInsight com o SDK do .NET

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com clusters HDInsight no .NET. Nesta seção, você cria um aplicativo `hivesampletable` de console C# para exportar para a tabela Azure SQL Database que você criou a partir dos pré-requisitos.

## <a name="set-up"></a>Configuração

1. Inicie o Visual Studio e crie um aplicativo de console C#.

1. Navegue até **ferramentas** > **Do Gerenciador de** > **pacotes NuGet Console do Gerenciador de pacotes** e execute o seguinte comando:

    ```
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

## <a name="sqoop-export"></a>Exportação do Sqoop

Da Colmeia ao SQL Server.  Este exemplo exporta dados `hivesampletable` da `mobiledata` tabela Colmeia para a tabela no Banco de Dados SQL.

1. Use o seguinte código no arquivo Program.cs. Editar o código para `ExistingClusterName`definir `ExistingClusterPassword`os valores para , e .

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

Do SQL Server ao Armazenamento Azure. Este exemplo depende da exportação acima ter sido realizada.  Este exemplo importa `mobiledata` dados da tabela no `wasb:///tutorials/usesqoop/importeddata` Banco de Dados SQL para o diretório na conta de armazenamento padrão do cluster.

1. Substitua o código `//sqoop start //sqoop end` acima no bloco pelo seguinte código:

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

* Exportação em massa: O conector Sqoop usado para exportar dados para o Microsoft SQL Server ou O Banco de Dados SQL do Azure não suporta atualmente inserções em massa.

* Loteamento: Ao `-batch` usar o interruptor, o Sqoop executa várias pastilhas em vez de lotear as operações de inserção.

## <a name="next-steps"></a>Próximas etapas

Agora você aprendeu a usar Sqoop. Para obter mais informações, consulte:

* [Use o Apache Oozie com HDInsight](../hdinsight-use-oozie-linux-mac.md): Use a ação Sqoop em um fluxo de trabalho Oozie.
* [Carregar dados no HDInsight](../hdinsight-upload-data.md): localize outros métodos de carregamento de dados no HDInsight ou no Armazenamento de Blobs do Azure.
