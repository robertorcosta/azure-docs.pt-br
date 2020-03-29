---
title: Enviar trabalhos MapReduce usando o SDK do .NET do Azure HDInsight
description: Saiba como enviar tarefas do MapReduce para o Azure HDInsight Apache Hadoop usando o SDK do .NET do HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/15/2020
ms.openlocfilehash: e50510f2420d69be37af584a2648a794e1561ee3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157042"
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Executar trabalhos MapReduce usando o SDK do .NET do HDInsight

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Saiba como enviar trabalhos MapReduce usando o SDK do .NET do HDInsight. Os clusters HDInsight vêm com um arquivo jar com alguns exemplos de MapReduce. O arquivo `/example/jars/hadoop-mapreduce-examples.jar`do frasco é.  Um dos exemplos é **wordcount**. Você desenvolve um aplicativo do console C# para enviar um trabalho wordcount.  O trabalho lê `/example/data/gutenberg/davinci.txt` o arquivo, e `/example/data/davinciwordcount`produz os resultados para .  Se você desejar executar novamente o aplicativo, você deve limpar a pasta de saída.

> [!NOTE]  
> As etapas neste artigo devem ser executadas em um cliente do Windows. Para obter informações sobre como usar um cliente Linux, OS X ou Unix para trabalhar com o Hive, use o seletor de tabulação mostrado na parte superior do artigo.

## <a name="prerequisites"></a>Pré-requisitos

* Um cluster do Apache Hadoop no HDInsight. Consulte [Criar clusters Apache Hadoop usando o portal Azure](../hdinsight-hadoop-create-linux-clusters-portal.md).

* [Estúdio Visual](https://visualstudio.microsoft.com/vs/community/).

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Enviar trabalhos MapReduce usando o SDK do .NET do HDInsight

O HDInsight .NET SDK fornece bibliotecas de clientes .NET, o que facilita o trabalho com clusters HDInsight a partir de .NET.

1. Inicie o Visual Studio e crie um aplicativo de console C#.

1. Navegue até **ferramentas** > **Do Gerenciador de** > **pacotes NuGet Console do Gerenciador de pacotes** e digite o seguinte comando:

    ```   
    Install-Package Microsoft.Azure.Management.HDInsight.Job
    ```

1. Copie o código abaixo em **Program.cs**. Em seguida, edite o `existingClusterName`código `existingClusterPassword` `defaultStorageAccountName`definindo os valores para: , , `defaultStorageAccountKey`e `defaultStorageContainerName`.

    ```csharp
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading;
    using Microsoft.Azure.Management.HDInsight.Job;
    using Microsoft.Azure.Management.HDInsight.Job.Models;
    using Hyak.Common;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    
    namespace SubmitHDInsightJobDotNet
    {
        class Program
        {
            private static HDInsightJobManagementClient _hdiJobManagementClient;
    
            private const string existingClusterName = "<Your HDInsight Cluster Name>";
            private const string existingClusterPassword = "<Cluster User Password>";
            private const string defaultStorageAccountName = "<Default Storage Account Name>"; 
            private const string defaultStorageAccountKey = "<Default Storage Account Key>";
            private const string defaultStorageContainerName = "<Default Blob Container Name>";
    
            private const string existingClusterUsername = "admin";
            private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
            private const string sourceFile = "/example/data/gutenberg/davinci.txt";
            private const string outputFolder = "/example/data/davinciwordcount";
    
            static void Main(string[] args)
            {
                System.Console.WriteLine("The application is running ...");
    
                var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
    
                SubmitMRJob();
    
                System.Console.WriteLine("Press ENTER to continue ...");
                System.Console.ReadLine();
            }
    
            private static void SubmitMRJob()
            {
                List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
    
                var paras = new MapReduceJobSubmissionParameters
                {
                    JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                    JarClass = "wordcount",
                    Arguments = args
                };
    
                System.Console.WriteLine("Submitting the MR job to the cluster...");
                var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                System.Console.WriteLine("JobId is " + jobId);
    
                System.Console.WriteLine("Waiting for the job completion ...");
    
                // Wait for job completion
                var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                while (!jobDetail.Status.JobComplete)
                {
                    Thread.Sleep(1000);
                    jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                }
    
                // Get job output
                System.Console.WriteLine("Job output is: ");
                var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                    defaultStorageContainerName);
    
                if (jobDetail.ExitValue == 0)
                {
                    // Create the storage account object
                    CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" +
                        defaultStorageAccountName +
                        ";AccountKey=" + defaultStorageAccountKey);
    
                    // Create the blob client.
                    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
    
                    // Retrieve reference to a previously created container.
                    CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
    
                    CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
    
                    using (var stream = blockBlob.OpenRead())
                    {
                        using (StreamReader reader = new StreamReader(stream))
                        {
                            while (!reader.EndOfStream)
                            {
                                System.Console.WriteLine(reader.ReadLine());
                            }
                        }
                    }
                }
                else
                {
                    // fetch stderr output in case of failure
                    var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess);
    
                    using (var reader = new StreamReader(output, Encoding.UTF8))
                    {
                        string value = reader.ReadToEnd();
                        System.Console.WriteLine(value);
                    }
    
                }
            }
        }
    }

    ```

1. Pressione **F5** para executar o aplicativo.

Para executar o trabalho novamente, você deve alterar o nome `/example/data/davinciwordcount`da pasta de saída de emprego, na amostra é .

Quando o trabalho é concluído com sucesso, o `part-r-00000`aplicativo imprime o conteúdo do arquivo de saída .

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, leia os seguintes artigos:

* Para enviar um trabalho de Hive, consulte [Executar consultas Apache Hive usando o SDK do .NET HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Para criar clusters HDInsight, confira [Criar Clusters Apache Hadoop baseados em Linux no HDInsight.](../hdinsight-hadoop-provision-linux-clusters.md).
* Para gerenciar clusters HDInsight, consulte [Gerenciar clusters Apache Hadoop no HDInsight](../hdinsight-administer-use-portal-linux.md).
* Para aprender sobre o SDK do .NET do HDInsight, consulte [referência do SDK do .NET do HDInsight](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight).
* Para autenticação não interativa no Azure, confira [Criar aplicativos .NET HDInsight de autenticação não interativa](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).