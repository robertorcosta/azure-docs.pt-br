---
title: Usar C# com MapReduce no Hadoop no HDInsight – Azure
description: Saiba como usar C# para criar soluções de MapReduce com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: 7a299ce16f6e9c7292cebf198c9c3077f8e05fcb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417604"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Use C# com fluxo de MapReduce no Apache Hadoop no HDInsight

Saiba como usar C# para criar uma solução de MapReduce no HDInsight.

O streaming Apache Hadoop permite executar o MapReduce jobs usando um script ou executável. Aqui, o .NET é usado para implementar o mapeador e o redutor para uma solução de contagem de palavras.

## <a name="net-on-hdinsight"></a>.NET no HDInsight

Os clusters HDInsight usam [mono (https://mono-project.com) ](https://mono-project.com) para executar aplicativos .NET. A versão 4.2.1 do Mono está incluída no HDInsight versão 3.6. Para obter mais informações sobre a versão do Mono incluída no HDInsight, consulte [os componentes apache hadoop disponíveis com diferentes versões HDInsight](../hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions).

Para obter mais informações sobre compatibilidade de Mono com versões do .NET Framework, consulte [Compatibilidade de Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona o Hadoop Streaming

O processo básico usado para streaming neste documento é o seguinte:

1. Hadoop passa dados para o*mapper (mapper.exe* neste exemplo) no STDIN.
2. O mapeador processa os dados e emite pares de chave/valor delimitados por tabulação para STDOUT.
3. A saída é lida por Hadoop e, em seguida, passada para o redutor *(reducer.exe* neste exemplo) em STDIN.
4. O redutor lê os pares de chave/valor delimitados por tabulação, processa os dados e, em seguida, emite o resultado como pares de chave/valor delimitados por tabulação no STDOUT.
5. A saída é lido pelo Hadoop e gravada no diretório de saída.

Para mais informações sobre streaming, consulte [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Pré-requisitos

* Visual Studio.

* Familiaridade com gravação e compilação de código em C# que se destina ao .NET Framework 4.5.

* Uma forma de carregar arquivos .exe no cluster. As etapas neste documento usam o Data Lake Tools para Visual Studio para carregar os arquivos no armazenamento primário do cluster.

* Se estiver usando o PowerShell, você precisará do [Módulo AZ](https://docs.microsoft.com/powershell/azure/overview).

* Um cluster do Apache Hadoop no HDInsight. Veja [Get Started com hdinsight no Linux](../hadoop/apache-hadoop-linux-tutorial-get-started.md).

* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do seu armazenamento primário de clusters. Este esquema `wasb://` seria para o `abfs://` Azure Storage, para o `adl://` Azure Data Lake Storage Gen2 ou para o Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o Armazenamento do Azure ou para o Data Lake Storage Gen2, o URI será `wasbs://` ou `abfss://`, respectivamente. Confira também [transferência segura](../../storage/common/storage-require-secure-transfer.md).

## <a name="create-the-mapper"></a>Criar o mapeador

No Visual Studio, crie um novo aplicativo de console .NET Framework chamado *mapper*. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Depois de criar o aplicativo, construa-o para produzir o arquivo */bin/Debug/mapper.exe* no diretório do projeto.

## <a name="create-the-reducer"></a>Criar o redutor

No Visual Studio, crie um novo aplicativo de console .NET Framework chamado *reducer*. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Depois de criar o aplicativo, construa-o para produzir o arquivo */bin/Debug/reducer.exe* no diretório do projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, você precisa carregar os aplicativos *mapeador* e *redutor* para o armazenamento HDInsight.

1. No Visual Studio, selecione **Exibir** > **explorador de servidores**.

1. Clique com o botão direito **do mouse No Azure**, selecione **Conecte-se à Assinatura Microsoft Azure...** e complete o processo de login.

1. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto **(Conta de armazenamento padrão)** é listada.

   ![Conta de armazenamento, cluster HDInsight, Server Explorer, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-storage-account.png)

   * Se a entrada **(Conta de armazenamento padrão)** puder ser expandida, você estará usando uma **conta de armazenamento do Azure** como armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão para o cluster, expanda a entrada e clique duas vezes **em (Contêiner padrão)**.

   * Se a entrada **(Conta de armazenamento padrão)** não puder ser expandida, você estará usando **o Azure Data Lake Storage** como o armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada **(Conta de Armazenamento Padrão)**.

1. Para carregar os arquivos .exe, use um dos seguintes métodos:

    * Se você estiver usando **uma conta de armazenamento azure,** selecione o ícone Upload **Blob.**

        ![Ícone de upload do HDInsight para mapeador, Visual Studio](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/hdinsight-upload-icon.png)

        Na caixa de diálogo **Enviar novo arquivo,** em **nome do arquivo,** selecione **Procurar**. Na caixa de diálogo **Upload Blob,** vá para a pasta *bin\debug* para o projeto *mapper* e, em seguida, escolha o arquivo *mapper.exe.* Por fim, selecione **Abrir** e, em seguida, **OK** para concluir o upload.

    * Para **o Azure Data Lake Storage**, clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione **Upload**. Por fim, selecione o arquivo *mapper.exe* e selecione **Abrir**.

    Após o *mapper.exe* cser carregado, repita o processo de upload para o arquivo *reducer.exe*.

## <a name="run-a-job-using-an-ssh-session"></a>Executar um trabalho: usando uma sessão SSH

O procedimento a seguir descreve como executar um trabalho MapReduce usando uma sessão SSH:

1. Use [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao seu cluster. Edite o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Use um dos seguintes comandos para iniciar o trabalho MapReduce:

   * Se o armazenamento padrão for **Armazenamento Azure:**

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files wasbs:///mapper.exe,wasbs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

    * Se o armazenamento padrão for **Data Lake Storage Gen1**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files adl:///mapper.exe,adl:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   * Se o armazenamento padrão for **Data Lake Storage Gen2**:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar \
            -files abfs:///mapper.exe,abfs:///reducer.exe \
            -mapper mapper.exe \
            -reducer reducer.exe \
            -input /example/data/gutenberg/davinci.txt \
            -output /example/wordcountout
        ```

   A lista a seguir descreve o que cada parâmetro e opção representa:

   |Parâmetro | Descrição |
   |---|---|
   |hadoop-streaming.jar|Especifica o arquivo de frasco que contém a funcionalidade MapReduce de streaming.|
   |-arquivos|Especifica os arquivos *mapper.exe* e *reducer.exe* para este trabalho. A `wasbs:///` `adl:///`declaração `abfs:///` de protocolo ou de cada arquivo é o caminho para a raiz do armazenamento padrão para o cluster.|
   |-mapeador|Especifica o arquivo que implementa o mapeador.|
   |-redutor|Especifica o arquivo que implementa o redutor.|
   |-entrada|Especifica os dados de entrada.|
   |-saída|Especifica o diretório de saída.|

1. Uma vez que o trabalho MapReduce seja concluído, use o seguinte comando para visualizar os resultados:

   ```bash
   hdfs dfs -text /example/wordcountout/part-00000
   ```

   A seguinte lista é um exemplo dos dados retornados pelos comandos anteriores:

   ```output
   you     1128
   young   38
   younger 1
   youngest        1
   your    338
   yours   4
   yourself        34
   yourselves      3
   youth   17
   ```

## <a name="run-a-job-using-powershell"></a>Executar um trabalho: usando o PowerShell

Use o seguinte script de PowerShell para executar um trabalho MapReduce e baixar os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Esse script solicita nome e senha da conta de logon do cluster, juntamente com o nome do cluster HDInsight. Uma vez que o trabalho é concluído, a saída é baixada para um arquivo chamado *output.txt*. O seguinte texto é um exemplo dos dados no arquivo `output.txt`:

```output
you     1128
young   38
younger 1
youngest        1
your    338
yours   4
yourself        34
yourselves      3
youth   17
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o MapReduce com o HDInsight, consulte [Use MapReduce in Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md).

Para obter informações sobre como usar o C# com o Hive e o Pig, confira [Usar uma função C# definida pelo usuário com o Apache Hive e o Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Para obter informações sobre como usar o C# com o Storm no HDInsight, confira [Desenvolver topologias do C# para o Apache Storm no HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
