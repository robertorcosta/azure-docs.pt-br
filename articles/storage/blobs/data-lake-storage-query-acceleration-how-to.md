---
title: Filtrar dados usando a aceleração da consulta de armazenamento do Lago de Dados do Azure (visualização) | Microsoft Docs
description: Use a aceleração de consulta (visualização) para recuperar um subconjunto de dados da sua conta de armazenamento.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771840"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Filtrar dados usando a aceleração da consulta de armazenamento do Lago de Dados do Azure (visualização)

Este artigo mostra como usar a aceleração de consulta (visualização) para recuperar um subconjunto de dados da sua conta de armazenamento. 

A aceleração de consultas (preview) é um novo recurso para o Azure Data Lake Storage que permite que aplicativos e frameworks de análise otimizem drasticamente o processamento de dados, recuperando apenas os dados necessários para realizar uma determinada operação. Para saber mais, consulte [Azure Data Lake Storage Query Acceleration (visualização)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> O recurso de aceleração de consultas está em visualização pública e está disponível nas regiões Central e Central do Canadá. Para rever limitações, consulte o artigo [Questões Conhecidas.](data-lake-storage-known-issues.md) Para se inscrever na pré-visualização, consulte [este formulário](https://aka.ms/adls/qa-preview-signup).  

## <a name="prerequisites"></a>Pré-requisitos

### <a name="net"></a>[.NET](#tab/dotnet)

- Para acessar o Armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento **v2 de uso geral.** veja [Criar uma conta de armazenamento.](../common/storage-quickstart-create-account.md)

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- Para acessar o Armazenamento do Azure, você precisará de uma assinatura do Azure. Se você ainda não tiver uma assinatura, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Uma conta de armazenamento **v2 de uso geral.** veja [Criar uma conta de armazenamento.](../common/storage-quickstart-create-account.md)

- [JDK (Java Development Kit)](/java/azure/jdk/?view=azure-java-stable) versão 8 ou superior.

- [Apache Maven.](https://maven.apache.org/download.cgi) 

  > [!NOTE] 
  > Este artigo pressupõe que você criou um projeto Java usando Apache Maven. Para um exemplo de como criar um projeto usando o Apache Maven, consulte [Configuração](storage-quickstart-blobs-java.md#setting-up).
  
---

## <a name="install-packages"></a>Instalar Pacotes 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Baixe os pacotes de aceleração de consulta. Você pode obter um arquivo .zip compactado que [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)contém esses pacotes usando este link: . 

2. Extrair o conteúdo deste arquivo para o diretório do projeto.

3. Abra seu arquivo de projeto *(.csproj)* em um editor de \<\> texto e adicione essas referências de pacote dentro do elemento Projeto.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Restaurar os pacotes SDK de visualização. Este comando de exemplo restaura os pacotes `dotnet restore` SDK de visualização usando o comando. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Restaurar todas as outras dependências do repositório público NuGet.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Crie diretório na raiz do seu projeto. O diretório raiz é o diretório que contém o arquivo **pom.xml.**

   > [!NOTE]
   > Os exemplos neste artigo assumem que o nome do diretório é **lib**.

2. Baixe os pacotes de aceleração de consulta. Você pode obter um arquivo .zip compactado que [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)contém esses pacotes usando este link: . 

3. Extrair os arquivos neste arquivo .zip para o diretório que você criou. Em nosso exemplo, esse diretório é chamado **lib**. 

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

## <a name="add-statements"></a>Adicionar declarações


### <a name="net"></a>[.NET](#tab/dotnet)

Adicione `using` essas instruções ao topo do seu arquivo de código.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

A aceleração da consulta recupera dados formatados de CSV e Json. Portanto, certifique-se de adicionar instruções usando para quaisquer bibliotecas de parsing CSV ou Json que você escolher usar. Os exemplos que aparecem neste artigo analisam um arquivo CSV usando a biblioteca [CsvHelper](https://www.nuget.org/packages/CsvHelper/) que está disponível no NuGet. Portanto, adicionaríamos essas `using` declarações ao topo do arquivo de código.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Para compilar exemplos apresentados neste artigo, você também `using` precisará adicionar essas declarações.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Adicione `import` essas instruções ao topo do seu arquivo de código.

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

Você pode usar o SQL para especificar os predicados do filtro de linha e as projeções da coluna em uma solicitação de aceleração de consulta. O código a seguir consulta um arquivo CSV no armazenamento e retorna `Hemingway, Ernest`todas as linhas de dados onde a terceira coluna corresponde ao valor . 

- Na consulta SQL, a `BlobStorage` palavra-chave é usada para denotar o arquivo que está sendo consultado.

- As referências da `_N` coluna são especificadas como onde está `_1`a primeira coluna . Se o arquivo de origem contiver uma linha de cabeçalho, então você pode se referir a colunas pelo nome especificado na linha de cabeçalho. 

### <a name="net"></a>[.NET](#tab/dotnet)

O método `BlobQuickQueryClient.QueryAsync` async envia a consulta para a API de aceleração de consulta e, em seguida, transmite os resultados de volta para o aplicativo como um objeto [De fluxo.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

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
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
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

O `BlobQuickQueryClient.openInputStream()` método envia a consulta para a API de aceleração de consulta `InputStream` e, em seguida, transmite os resultados de volta para o aplicativo como um objeto que pode ser lido como qualquer outro objeto InputStream.

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

Você pode escopo de seus resultados para um subconjunto de colunas. Dessa forma, você recupera apenas as colunas necessárias para realizar um determinado cálculo. Isso melhora o desempenho do aplicativo e reduz o custo porque menos dados são transferidos pela rede. 

Este código recupera `PublicationYear` apenas a coluna para todos os livros no conjunto de dados. Ele também usa as informações da linha cabeçalho no arquivo de origem para referenciar colunas na consulta.


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

O código a seguir combina filtragem de linha e projeções de coluna na mesma consulta. 

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

- [Formulário de inscrição para aceleração de consulta](https://aka.ms/adls/queryaccelerationpreview)    
- [Aceleração da consulta de armazenamento do Lago de Dados Do Azure (visualização)](data-lake-storage-query-acceleration.md)
- [Referência de linguagem SQL de aceleração de consulta (visualização)](query-acceleration-sql-reference.md)
- Referência de API de aceleração de consulta REST
