---
title: Java user-defined function (UDF) with Apache Hive Azure HDInsight
description: Saiba como criar uma baseado em Java-função definida pelo usuário (UDF) que funciona com o Apache Hive. Este UDF de exemplo converte uma tabela de cadeias de caracteres de texto em minúsculas.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/20/2019
ms.openlocfilehash: 73a2a612a4eeb4a59f12abf0660fffb092f0547f
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327205"
---
# <a name="use-a-java-udf-with-apache-hive-in-hdinsight"></a>Usar um Java UDF com Apache Hive no HDInsight

Saiba como criar uma baseado em Java-função definida pelo usuário (UDF) que funciona com o Apache Hive. O UDF Java neste exemplo converte uma tabela de cadeias de caracteres de texto em caracteres minúsculos.

## <a name="prerequisites"></a>Pré-requisitos

* A Hadoop cluster on HDInsight. Consulte [Introdução ao HDInsight no Linux](./apache-hadoop-linux-tutorial-get-started.md).
* [JDK (Java Developer Kit) versão 8](https://aka.ms/azure-jdks)
* [Apache Maven](https://maven.apache.org/download.cgi) corretamente [instalado](https://maven.apache.org/install.html) de acordo com o Apache.  O Maven é um sistema de construção de projetos para projetos Java.
* O [esquema de URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) do seu armazenamento primário de clusters. This would be wasb:// for Azure Storage, abfs:// for Azure Data Lake Storage Gen2 or adl:// for Azure Data Lake Storage Gen1. Se a transferência segura estiver habilitada para o Armazenamento do Azure, o URI será `wasbs://`.  Confira também [Transferência segura](../../storage/common/storage-require-secure-transfer.md).

* Um editor de texto ou Java IDE

    > [!IMPORTANT]  
    > Se você criar os arquivos de Python em um cliente Windows, você deverá usar um editor que usa LF como uma terminação de linha. Se você não tem certeza se o seu editor usa LF ou CRLF, veja a seção de [Solução de problemas](#troubleshooting) para ver as etapas para remover o caractere CR.

## <a name="test-environment"></a>Test environment

The environment used for this article was a computer running Windows 10.  The commands were executed in a command prompt, and the various files were edited with Notepad. Modify accordingly for your environment.

From a command prompt, enter the commands below to create a working environment:

```cmd
IF NOT EXIST C:\HDI MKDIR C:\HDI
cd C:\HDI
```

## <a name="create-an-example-java-udf"></a>Criar UDF Java de exemplo

1. Create a new Maven project by entering the following command:

    ```cmd
    mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

    This command creates a directory named `exampleudf`, which contains the Maven project.

2. Once the project has been created, delete the `exampleudf/src/test` directory that was created as part of the project by entering the following command:

    ```cmd
    cd ExampleUDF
    rmdir /S /Q "src/test"
    ```

3. Open `pom.xml` by entering the command below:

    ```cmd
    notepad pom.xml
    ```

    Then replace the existing `<dependencies>` entry with the following XML:

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.3</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hive</groupId>
            <artifactId>hive-exec</artifactId>
            <version>1.2.1</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

    Essas entradas especificam a versão do Hadoop e do Hive incluídas com o HDInsight 3.6. Você pode encontrar informações sobre as versões do Hadoop e do Hive fornecidas com o HDInsight no documento [Controle de versão de componente do HDInsight](../hdinsight-component-versioning.md) .

    Adicione uma seção `<build>` antes da linha `</project>` no final do arquivo. Esta seção deve conter o XML a seguir:

    ```xml
    <build>
        <plugins>
            <!-- build for Java 1.8. This is required by HDInsight 3.6  -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
            <!-- build an uber jar -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>3.2.1</version>
                <configuration>
                    <!-- Keep us from getting a can't overwrite file error -->
                    <transformers>
                        <transformer
                                implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                        </transformer>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                        </transformer>
                    </transformers>
                    <!-- Keep us from getting a bad signature error -->
                    <filters>
                        <filter>
                            <artifact>*:*</artifact>
                            <excludes>
                                <exclude>META-INF/*.SF</exclude>
                                <exclude>META-INF/*.DSA</exclude>
                                <exclude>META-INF/*.RSA</exclude>
                            </excludes>
                        </filter>
                    </filters>
                </configuration>
                <executions>
                    <execution>
                        <phase>package</phase>
                        <goals>
                            <goal>shade</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```

    Essas entradas definem como compilar o projeto. Especificamente, a versão do Java que o projeto usa e como criar um uberjar para implantação no cluster.

    Salve o arquivo depois que as alterações forem feitas.

4. Enter the command below to create and open a new file `ExampleUDF.java`:

    ```cmd
    notepad src/main/java/com/microsoft/examples/ExampleUDF.java
    ```

    Then copy and paste the java code below into the new file. Then close the file.

    ```java
    package com.microsoft.examples;

    import org.apache.hadoop.hive.ql.exec.Description;
    import org.apache.hadoop.hive.ql.exec.UDF;
    import org.apache.hadoop.io.*;

    // Description of the UDF
    @Description(
        name="ExampleUDF",
        value="returns a lower case version of the input string.",
        extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
    )
    public class ExampleUDF extends UDF {
        // Accept a string input
        public String evaluate(String input) {
            // If the value is null, return a null
            if(input == null)
                return null;
            // Lowercase the input string and return it
            return input.toLowerCase();
        }
    }
    ```

    Este código implementa um UDF que aceita um valor de cadeia de caracteres e retorna uma versão da cadeia de caracteres em minúsculas.

## <a name="build-and-install-the-udf"></a>Compilar e instalar o UDF

In the commands below, replace `sshuser` with the actual username if different. Replace `mycluster` with the actual cluster name.

1. Compile and package the UDF by entering the following command:

    ```cmd
    mvn compile package
    ```

    Este comando cria e empacota o UDF no arquivo `exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar`.

2. Use the `scp` command to copy the file to the HDInsight cluster by entering the following command:

    ```cmd
    scp ./target/ExampleUDF-1.0-SNAPSHOT.jar sshuser@mycluster-ssh.azurehdinsight.net:
    ```

3. Connect to the cluster using SSH by entering the following command:

    ```cmd
    ssh sshuser@mycluster-ssh.azurehdinsight.net
    ```

4. From the open SSH session, copy the jar file to HDInsight storage.

    ```bash
    hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars
    ```

## <a name="use-the-udf-from-hive"></a>Usar o UDF do Hive

1. Start the Beeline client from the SSH session by entering the following command:

    ```bash
    beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http'
    ```

    Esse comando presume que você usou o padrão de **admin** para a conta de logon para o cluster.

2. Quando você chegar ao prompt `jdbc:hive2://localhost:10001/>` , digite o seguinte para adicionar o UDF no Hive e expô-lo como uma função.

    ```hiveql
    ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
    CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
    ```

3. Use o UDF para converter valores recuperados de uma tabela em cadeias de caracteres de letras minúsculas.

    ```hiveql
    SELECT tolower(state) AS ExampleUDF, state FROM hivesampletable LIMIT 10;
    ```

    This query selects the state from the table, convert the string to lower case, and then display them along with the unmodified name. A saída é semelhante ao texto a seguir:

        +---------------+---------------+--+
        |  exampleudf   |     state     |
        +---------------+---------------+--+
        | california    | California    |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | pennsylvania  | Pennsylvania  |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | colorado      | Colorado      |
        | utah          | Utah          |
        | utah          | Utah          |
        | colorado      | Colorado      |
        +---------------+---------------+--+

## <a name="troubleshooting"></a>Solução de Problemas

When running the hive job, you may come across an error similar to the following text:

    Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: [Error 20001]: An error occurred while reading or writing to your custom script. It may have crashed with an error.

Esse problema pode ser causado pelas terminações de linha no arquivo do Python. Muitos editores Windows usam CRLF como padrão como a terminação de linha, mas aplicativos Linux geralmente esperam LF.

Você pode seguir as seguintes instruções do PowerShell para remover os caracteres CR antes de carregar o arquivo no HDInsight:

```PowerShell
# Set $original_file to the python file path
$text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
[IO.File]::WriteAllText($original_file, $text)
```

## <a name="next-steps"></a>Próximos passos

Para ver outras maneiras de trabalhar com o Hive, confira [Usar o Apache Hive com o HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre Funções Definidas pelo Usuário do Hive, confira a seção [Operadores e funções definidas pelo usuário do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do wiki do Hive em apache.org.
