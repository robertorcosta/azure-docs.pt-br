---
title: Filtrar dados usando a aceleração de consulta Azure Data Lake Storage (visualização) | Microsoft Docs
description: Use a aceleração de consulta (versão prévia) para recuperar um subconjunto de dados de sua conta de armazenamento.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.custom: devx-track-csharp
ms.openlocfilehash: 6de6661e5c970c7c3cbfc944b8539060b8844a36
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005217"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrar dados usando a aceleração de consulta do Azure Data Lake Storage (versão prévia)

Este artigo mostra como usar a aceleração de consulta (visualização) para recuperar um subconjunto de dados de sua conta de armazenamento. 

A aceleração de consulta (versão prévia) é um novo recurso para Azure Data Lake Storage que permite que os aplicativos e as estruturas de análise otimizem drasticamente o processamento de dados recuperando apenas os dados necessários para executar uma determinada operação. Para saber mais, consulte [aceleração de consulta Azure data Lake Storage (versão prévia)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> O recurso aceleração de consulta está em visualização pública e está disponível nas regiões central do Canadá e central da França. Para revisar as limitações, consulte o artigo [problemas conhecidos](data-lake-storage-known-issues.md) . Para se registrar na versão prévia, consulte [este formulário](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Pré-requisitos

### <a name="net"></a>[.NET](#tab/dotnet)

- Para acessar o Armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento de **uso geral v2** . consulte [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).

- [SDK do .net](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Para acessar o Armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento de **uso geral v2** . consulte [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).

- [JDK (Java Development Kit)](/java/azure/jdk/?view=azure-java-stable) versão 8 ou superior.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > Este artigo pressupõe que você criou um projeto Java usando o Apache Maven. Para obter um exemplo de como criar um projeto usando o Apache Maven, consulte [Configurando](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Instalar Pacotes 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Baixe os pacotes de aceleração de consulta. Você pode obter um arquivo. zip compactado que contém esses pacotes usando este link: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) . 

2. Extraia o conteúdo desse arquivo para o diretório do projeto.

3. Abra o arquivo de projeto (*. csproj*) em um editor de texto e adicione essas referências de pacote dentro do \<Project\> elemento.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Restaure os pacotes do SDK de visualização. Este exemplo de comando restaura os pacotes do SDK de visualização usando o `dotnet restore` comando. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Restaure todas as outras dependências do repositório do NuGet público.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Crie o diretório na raiz do seu projeto. O diretório raiz é o diretório que contém o arquivo de **pom.xml** .

   > [!NOTE]
   > Os exemplos neste artigo pressupõem que o nome do diretório é **lib**.

2. Baixe os pacotes de aceleração de consulta. Você pode obter um arquivo. zip compactado que contém esses pacotes usando este link: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) . 

3. Extraia os arquivos nesse arquivo. zip para o diretório que você criou. Em nosso exemplo, esse diretório é denominado **lib**. 

4. Abra o arquivo *pom.xml* no seu editor de texto. Adicione os seguintes elementos de dependência ao grupo de dependências. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Adicionar instruções


### <a name="net"></a>[.NET](#tab/dotnet)

Adicione essas `using` instruções à parte superior do seu arquivo de código.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

A aceleração de consulta recupera dados CSV e JSON formatados. Portanto, certifique-se de adicionar instruções using para qualquer biblioteca de análise de CSV ou JSON que você escolher usar. Os exemplos que aparecem neste artigo analisam um arquivo CSV usando a biblioteca [CsvHelper](https://www.nuget.org/packages/CsvHelper/) que está disponível no NuGet. Portanto, adicionaremos essas `using` instruções à parte superior do arquivo de código.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Para compilar exemplos apresentados neste artigo, também será necessário adicionar essas `using` instruções.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Adicione essas `import` instruções à parte superior do seu arquivo de código.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Recuperar dados usando um filtro

Você pode usar o SQL para especificar os predicados de filtro de linha e as projeções de coluna em uma solicitação de aceleração de consulta. O código a seguir consulta um arquivo CSV no armazenamento e retorna todas as linhas de dados em que a terceira coluna corresponde ao valor `Hemingway, Ernest` . 

- Na consulta SQL, a palavra-chave `BlobStorage` é usada para denotar o arquivo que está sendo consultado.

- As referências de coluna são especificadas como `_N` onde a primeira coluna é `_1` . Se o arquivo de origem contiver uma linha de cabeçalho, você poderá se referir a colunas pelo nome especificado na linha de cabeçalho. 

### <a name="net"></a>[.NET](#tab/dotnet)

O método Async `BlobQuickQueryClient.QueryAsync` envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta para o aplicativo como um objeto [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) .

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

O método `BlobQuickQueryClient.openInputStream()` envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta para o aplicativo como um `InputStream` objeto que pode ser lido como qualquer outro objeto InputStream.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Recuperar colunas específicas

Você pode fazer o escopo dos resultados para um subconjunto de colunas. Dessa forma, você recupera apenas as colunas necessárias para executar um determinado cálculo. Isso melhora o desempenho do aplicativo e reduz o custo porque menos dados são transferidos pela rede. 

Esse código recupera apenas a `PublicationYear` coluna de todos os livros no conjunto de dados. Ele também usa as informações da linha de cabeçalho no arquivo de origem para fazer referência a colunas na consulta.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

O código a seguir combina a filtragem de linha e as projeções de coluna na mesma consulta. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Próximas etapas

- [Formulário de registro de aceleração de consulta](https://aka.ms/adls/qa-preview-signup)    
- [Aceleração de consulta de Azure Data Lake Storage (versão prévia)](data-lake-storage-query-acceleration.md)
- [Referência da linguagem SQL de aceleração de consulta (versão prévia)](query-acceleration-sql-reference.md)
