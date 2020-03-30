---
title: C#, Colmeia Apache & Porco Apache em Apache Hadoop - Azure HDInsight
description: Aprenda a usar as funções definidas pelo usuário (UDF) do C# com o Apache Hive e o streaming do Apache Pig no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 9ef9eada9b9aec50642a8bf357edab0677868817
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74949382"
---
# <a name="use-c-user-defined-functions-with-apache-hive-and-apache-pig-on-apache-hadoop-in-hdinsight"></a>Use funções c# definidas pelo usuário com Colmeia Apache e Porco Apache no Apache Hadoop no HDInsight

Aprenda a usar funções c# definidas pelo usuário (UDF) com [Apache Hive](https://hive.apache.org) e [Apache Pig](https://pig.apache.org) no HDInsight.

> [!IMPORTANT]
> As etapas deste documento funcionam com clusters HDInsight baseados em Linux. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Controle de versão do componente do HDInsight](../hdinsight-component-versioning.md).

Tanto o Hive quanto o Pig podem passar dados para aplicativos externos para processamento. Este processo é conhecido como _streaming_. Ao usar um aplicativo .NET, os dados são passados para o aplicativo em STDIN e o aplicativo retornará os resultados em STDOUT. Para ler e gravar por meio de STDIN e STDOUT, use `Console.ReadLine()` e `Console.WriteLine()` de um aplicativo de console.

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com gravação e compilação de código em C# que se destina ao .NET Framework 4.5.

    Use o IDE que preferir. Recomendamos [visual studio](https://www.visualstudio.com/vs) ou visual studio [code](https://code.visualstudio.com/). As etapas deste documento utilizam o Visual Studio 2019.

* Uma forma de carregar arquivos .exe para o cluster e executar trabalhos de Pig e Hive. Recomendamos [ferramentas data lake para visual studio,](../../data-lake-analytics/data-lake-analytics-data-lake-tools-install.md) [Azure PowerShell](/powershell/azure)e [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). As etapas neste documento usam as Ferramentas do Data Lake para Visual Studio para carregar os arquivos e executar o exemplo de consulta do Hive.

    Para obter informações sobre outras formas de executar consultas da Colmeia, consulte [O que é colmeia apache e hiveQL no Azure HDInsight?](hdinsight-use-hive.md).

* Um Hadoop no cluster do HDInsight. Para obter mais informações sobre a criação de um cluster, consulte [Criar clusters HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET no HDInsight

Clusters *HDInsight baseados em Linux* usam [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicativos .NET. A versão 4.2.1 do Mono está incluída no HDInsight versão 3.6.

Para obter mais informações sobre compatibilidade de Mono com versões do .NET Framework, consulte [Compatibilidade de Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

Para obter mais informações sobre a versão do .NET Framework e Mono incluídos nas versões HDInsight, consulte [versões componentes do HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Criar os projetos em C\#

As seções a seguir descrevem como criar um projeto C# no Visual Studio para uma Colmeia Apache UDF e um Apache Pig UDF.

### <a name="apache-hive-udf"></a>UDF do Apache Hive

Para criar um projeto C# para uma UDF colmeia apache:

1. Inicie o Visual Studio.

2. Selecione **Criar um novo projeto**.

3. Na **criação de uma nova** janela de projeto, escolha o modelo Console App **(.NET Framework)** (a versão C#). Em seguida, selecione **Next**.

4. Na **Configure sua nova** janela de projeto, digite um nome de **projeto** do *HiveCSharp*e navegue até ou crie um **Local** para salvar o novo projeto. Em seguida, **selecione Criar**.

5. No Visual Studio IDE, substitua o conteúdo do *Program.cs* pelo seguinte código:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

6. Na barra de menus, selecione **Build** > **Build Solution** para construir o projeto.

7. Feche a solução.

### <a name="apache-pig-udf"></a>UDF do Apache Pig

Para criar um projeto C# para uma UDF colmeia apache:

1. Abra o Visual Studio.

2. Na janela **Iniciar**, selecione **Criar projeto**.

3. Na **criação de uma nova** janela de projeto, escolha o modelo Console App **(.NET Framework)** (a versão C#). Em seguida, selecione **Next**.

4. Na **Configure sua nova** janela de projeto, digite um nome de **projeto** de *PigUDF*e vá ou crie um **Local** para salvar o novo projeto. Em seguida, **selecione Criar**.

5. No Visual Studio IDE, substitua o conteúdo do *Program.cs* pelo seguinte código:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Esse código analisa as linhas enviadas do Pig e reformata as linhas que começam com `java.lang.Exception`.

6. Na barra de menus, escolha **Build** > **Build Solution** para construir o projeto.

7. Deixe a solução aberta.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

Em seguida, carregue os aplicativos Hive e Pig UDF para armazenamento em um cluster HDInsight.

1. No Visual Studio, navegue até **o View** > **Server Explorer**.

1. No **Server Explorer**, clique com o botão direito do mouse No **Azure,** selecione Conectar à assinatura do Microsoft **Azure**e complete o processo de login.

1. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto **(Conta de armazenamento padrão)** é listada.

    ![Conta de armazenamento padrão, cluster HDInsight, Server Explorer](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-storage-account.png)

    * Se essa entrada puder ser expandida, você estará usando uma **conta de armazenamento do Azure** como armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão para o cluster, expanda a entrada e clique duas vezes no **(Contêiner Padrão)**.

    * Se essa entrada não puder ser expandida, você estará usando **o Azure Data Lake Storage** como o armazenamento padrão para o cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada **(Conta de Armazenamento Padrão)**.

1. Para carregar os arquivos .exe, use um dos seguintes métodos:

    * Se você estiver usando **uma conta de armazenamento azure,** selecione o ícone Upload **Blob.**

        ![Ícone de upload do HDInsight para novo projeto](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/hdinsight-upload-icon.png)

        Na caixa de diálogo **Enviar novo arquivo,** em **nome do arquivo,** selecione **Procurar**. Na caixa de diálogo **Upload Blob,** vá para a pasta *bin\debug* para o projeto *HiveCSharp* e, em seguida, escolha o arquivo *HiveCSharp.exe.* Por fim, selecione **Abrir** e, em seguida, **OK** para concluir o upload.

    * Se você estiver usando **o Azure Data Lake Storage,** clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione **Upload**. Por fim, escolha o arquivo *HiveCSharp.exe* e selecione **Abrir**.

    Após o *HiveCSharp.exe* ser carregado, repita o processo de upload para o arquivo *PigUDF.exe*.

## <a name="run-an-apache-hive-query"></a>Executar uma consulta do Apache Hive

Agora você pode executar uma consulta hive que usa o seu aplicativo Hive UDF.

1. No Visual Studio, navegue até **o View** > **Server Explorer**.

2. Expanda **Azure** e expanda **HDInsight**.

3. Clique com o botão direito do mouse no cluster em que você implantou o aplicativo *HiveCSharp* e, em seguida, selecione **Escrever uma consulta de Hive**.

4. Use o texto a seguir para a consulta de Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasbs:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen1
    -- add file adl:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Storage Gen2
    -- add file abfs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Remova a marca de comentário da instrução `add file` que corresponde ao tipo de armazenamento padrão usado para o cluster.

    Esta consulta seleciona `clientid` `devicemake`o `devicemodel` , `hivesampletable`e campos de , e, em seguida, passa os campos para o aplicativo *HiveCSharp.exe.* A consulta espera que o aplicativo retorne três campos, que são armazenados como `clientid`, `phoneLabel` e `phoneHash`. A consulta também espera encontrar *hiveCSharp.exe* na raiz do recipiente de armazenamento padrão.

5. Alterne o **padrão Interativo** para **Lote**e, em seguida, **selecione Enviar** para enviar o trabalho para o cluster HDInsight. A janela **Resumo do trabalho Hive** é aberta.

6. Selecione **Atualizar** para atualizar o resumo até que **o status do trabalho** seja alterado para **Concluído**. Para visualizar a saída de trabalho, selecione **Saída de trabalho**.

## <a name="run-an-apache-pig-job"></a>Executar um trabalho do Apache Pig

Você também pode executar um trabalho de Porco que usa seu aplicativo Pig UDF.

1. Use o SSH para conectar-se ao cluster HDInsight. (Por exemplo, execute `ssh sshuser@<clustername>-ssh.azurehdinsight.net`o comando .) Para obter mais informações, consulte [Use SSH withHDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use o seguinte comando para iniciar a linha de comando Pig:

    ```shell
    pig
    ```

    Um prompt de `grunt>` é exibido.

3. Digite o seguinte para executar um trabalho do Pig que usa o aplicativo do .NET Framework:

    ```pig
    DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
    LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
    DUMP DETAILS;
    ```

    A `DEFINE` instrução cria `streamer` um alias do aplicativo *PigUDF.exe* e `CACHE` a carrega do armazenamento padrão para o cluster. Posteriormente, `streamer` é `STREAM` usado com o operador `LOG` para processar as linhas únicas contidas e retornar os dados como uma série de colunas.

    > [!NOTE]
    > O nome do aplicativo usado para streaming \` deve ser cercado pelo caractere (backtick) quando aliased, `SHIP`e pelo caractere ' (citação única) quando usado com .

4. Depois de inserir a última linha, o trabalho deve ser iniciado. Isso retorna saídas semelhantes ao seguinte texto:

    ```output
    (2019-07-15 16:43:25 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
    (2019-07-15 16:43:25 SampleClass5 [DEBUG] detail for id 1976092771)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1317358561)
    (2019-07-15 16:43:25 SampleClass5 [TRACE] verbose detail for id 1737534798)
    (2019-07-15 16:43:25 SampleClass7 [DEBUG] detail for id 1475865947)
    ```

5. Use `exit` para sair porco.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu como usar um aplicativo .NET Framework da Hive and Pig no HDInsight. Caso deseje saber como usar o Python com o Hive e o Pig, confira [Usar o Python com o Apache Hive e o Apache Pig no HDInsight](python-udf-hdinsight.md).

Para obter outras formas de usar a Colmeia e aprender sobre como usar o MapReduce, consulte os seguintes artigos:

* [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md)
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)
* [Noções básicas do latim de porco](https://pig.apache.org/docs/latest/basic.html)