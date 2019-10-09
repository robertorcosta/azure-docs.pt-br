---
title: Desenvolver para os Arquivos do Azure com .NET | Microsoft Docs
description: Saiba como desenvolver aplicativos e serviços .NET que usam o Arquivos do Azure para armazenar dados de arquivo.
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 10/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6f2159ddf3e3039dc0c38fc8f942c508ac177f06
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038158"
---
# <a name="develop-for-azure-files-with-net"></a>Desenvolvimento para o Arquivos do Azure com .NET

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

Este tutorial demonstra as noções básicas de usar o .NET para desenvolver aplicativos que usam os [Arquivos do Azure](storage-files-introduction.md) para armazenar dados de arquivo. Este tutorial cria um aplicativo de console simples para executar ações básicas com o .NET e os arquivos do Azure:

* Obter o conteúdo de um arquivo.
* Defina o tamanho máximo ou a *cota* para o compartilhamento de arquivos.
* Crie uma assinatura de acesso compartilhado (chave SAS) para um arquivo que usa uma política de acesso compartilhado definida no compartilhamento.
* Copie um arquivo em outro arquivo na mesma conta de armazenamento.
* Copie um arquivo em um blob na mesma conta de armazenamento.
* Use as métricas de armazenamento do Azure para solução de problemas.

Para saber mais sobre os arquivos do Azure, confira [o que são os arquivos do Azure?](storage-files-introduction.md)

[!INCLUDE [storage-check-out-samples-dotnet](../../../includes/storage-check-out-samples-dotnet.md)]

## <a name="understanding-the-net-apis"></a>Compreendendo as APIs do .NET

Os Arquivos do Azure fornecem duas abordagens amplas para aplicativos clientes: SMB (Protocolo SMB) e REST. No .NET, as APIs `System.IO` e `WindowsAzure.Storage` abstraim essas abordagens.

API | Quando usar | Observações
----|-------------|------
[System.IO](https://docs.microsoft.com/dotnet/api/system.io) | Seu aplicativo: <ul><li>Precisa ler/gravar arquivos usando SMB</li><li>Está em execução em um dispositivo que tem acesso pela porta 445 à sua conta do Arquivos do Azure</li><li>Não precisa gerenciar nenhum das configurações administrativas do compartilhamento de arquivo</li></ul> | A e/s de arquivo implementada com os arquivos do Azure via SMB geralmente é a mesma de e/s com qualquer compartilhamento de arquivos de rede ou dispositivo de armazenamento local. Para obter uma introdução a vários recursos no .NET, incluindo e/s de arquivo, consulte o tutorial [aplicativo de console](https://docs.microsoft.com/dotnet/csharp/tutorials/console-teleprompter) .
[Microsoft.Azure.Storage.File](https://docs.microsoft.com/dotnet/api/overview/azure/storage#client-library) | Seu aplicativo: <ul><li>Não é possível acessar os arquivos do Azure usando SMB na porta 445 devido a restrições de firewall ou ISP</li><li>Requer a funcionalidade administrativa, como a capacidade de definir uma cota de compartilhamento de arquivos ou criar uma assinatura de acesso compartilhado</li></ul> | Este artigo demonstra o uso de `Microsoft.Azure.Storage.File` para e/s de arquivo usando REST em vez de SMB e gerenciamento do compartilhamento de arquivos.

## <a name="create-the-console-application-and-obtain-the-assembly"></a>Criar o aplicativo do console e obter o assembly

No Visual Studio, crie um novo aplicativo de console do Windows. As etapas a seguir mostram como criar um aplicativo de console no Visual Studio 2019. As etapas são semelhantes em outras versões do Visual Studio.

1. Inicie o Visual Studio e selecione **Criar um projeto**.
1. Em **criar um novo projeto**, escolha **aplicativo de console (.NET Framework)** para C#e, em seguida, selecione **Avançar**.
1. Em **configurar seu novo projeto**, insira um nome para o aplicativo e selecione **criar**.

Você pode adicionar todos os exemplos de código neste tutorial ao método `Main()` do arquivo `Program.cs` do seu aplicativo de console.

Você pode usar a biblioteca de cliente de armazenamento do Azure em qualquer tipo de aplicativo .NET. Esses tipos incluem um serviço de nuvem do Azure ou aplicativo Web, e aplicativos móveis e de desktop. Neste guia, usamos um aplicativo de console para simplificar.

## <a name="use-nuget-to-install-the-required-packages"></a>Use o NuGet para instalar os pacotes necessários

Consulte estes pacotes em seu projeto para concluir este tutorial:

* [Armazenamento do Microsoft Azure biblioteca comum para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/)
  
  Este pacote fornece acesso programático a recursos comuns em sua conta de armazenamento.
* [Armazenamento do Microsoft Azure biblioteca de BLOBs para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/)

  Este pacote fornece acesso programático a recursos de BLOB em sua conta de armazenamento.
* [Biblioteca de arquivos Armazenamento do Microsoft Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.File/)

  Este pacote fornece acesso programático a recursos de arquivo em sua conta de armazenamento.
* [Microsoft Azure Configuration Manager biblioteca para .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/)

  Esse pacote fornece uma classe para analisar uma cadeia de conexão em um arquivo de configuração, onde quer que seu aplicativo seja executado.

Você pode usar NuGet para obter os dois pacotes. Siga estas etapas:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse em seu projeto e escolha **gerenciar pacotes NuGet**.
1. No **Gerenciador de pacotes NuGet**, selecione **procurar**. Em seguida, pesquise e escolha **Microsoft. Azure. Storage. blob**e, em seguida, selecione **instalar**.

   Esta etapa instala o pacote e suas dependências.
1. Pesquise e instale esses pacotes:

   * **Microsoft. Azure. Storage. Common**
   * **Microsoft.Azure.Storage.File**
   * **Microsoft. Azure. ConfigurationManager**

## <a name="save-your-storage-account-credentials-to-the-appconfig-file"></a>Salvar as credenciais da conta de armazenamento no arquivo app. config

Em seguida, salve suas credenciais no arquivo `App.config` do seu projeto. No **Gerenciador de soluções**, clique duas vezes em `App.config` e edite o arquivo para que ele seja semelhante ao exemplo a seguir. Substitua `myaccount` pelo nome da sua conta de armazenamento e `mykey` pela sua chave de conta de armazenamento.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
    <startup>
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
    </startup>
    <appSettings>
        <add key="StorageConnectionString" value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=StorageAccountKeyEndingIn==" />
    </appSettings>
</configuration>
```

> [!NOTE]
> A versão mais recente do emulador de armazenamento do Azure não dá suporte aos Arquivos do Azure. Sua cadeia de conexão deve ter como destino uma Conta de Armazenamento do Azure na nuvem para funcionar com os Arquivos do Azure.

## <a name="add-using-directives"></a>Adicionar diretivas using

No **Gerenciador de soluções**, abra o arquivo `Program.cs` e adicione o seguinte usando as diretivas na parte superior do arquivo.

```csharp
using Microsoft.Azure; // Namespace for Azure Configuration Manager
using Microsoft.Azure.Storage; // Namespace for Storage Client Library
using Microsoft.Azure.Storage.Blob; // Namespace for Azure Blobs
using Microsoft.Azure.Storage.File; // Namespace for Azure Files
```

[!INCLUDE [storage-cloud-configuration-manager-include](../../../includes/storage-cloud-configuration-manager-include.md)]

## <a name="access-the-file-share-programmatically"></a>Acessar o compartilhamento de arquivos programaticamente

Em seguida, adicione o seguinte conteúdo ao método `Main()`, após o código mostrado acima, para recuperar a cadeia de conexão. Esse código obtém uma referência ao arquivo que criamos anteriormente e gera seu conteúdo.

```csharp
// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile file = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the file exists.
        if (file.Exists())
        {
            // Write the contents of the file to the console window.
            Console.WriteLine(file.DownloadTextAsync().Result);
        }
    }
}
```

Execute o aplicativo de console para ver a saída.

## <a name="set-the-maximum-size-for-a-file-share"></a>Definir o tamanho máximo de um compartilhamento de arquivos

A partir da versão 5. x da biblioteca de cliente do armazenamento do Azure, você pode definir a cota (tamanho máximo) para um compartilhamento de arquivos. Você também pode verificar a quantidade de dados atualmente armazenada no compartilhamento.

Definir a cota de um compartilhamento limita o tamanho total dos arquivos armazenados no compartilhamento. Se o tamanho total dos arquivos no compartilhamento exceder a cota definida no compartilhamento, os clientes não poderão aumentar o tamanho dos arquivos existentes. Os clientes não podem criar novos arquivos, a menos que esses arquivos estejam vazios.

O exemplo a seguir mostra como verificar o uso atual de um compartilhamento e como definir a cota para o compartilhamento.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Check current usage stats for the share.
    // Note that the ShareStats object is part of the protocol layer for the File service.
    Microsoft.Azure.Storage.File.Protocol.ShareStats stats = share.GetStats();
    Console.WriteLine("Current share usage: {0} GB", stats.Usage.ToString());

    // Specify the maximum size of the share, in GB.
    // This line sets the quota to be 10 GB greater than the current usage of the share.
    share.Properties.Quota = 10 + stats.Usage;
    share.SetProperties();

    // Now check the quota for the share. Call FetchAttributes() to populate the share's properties.
    share.FetchAttributes();
    Console.WriteLine("Current share quota: {0} GB", share.Properties.Quota);
}
```

### <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>Gerar uma assinatura de acesso compartilhado para um arquivo ou compartilhamento de arquivos

A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode gerar uma SAS (assinatura de acesso compartilhado) para um compartilhamento de arquivos ou para um arquivo individual. Você também pode criar uma política de acesso compartilhado em um compartilhamento de arquivos para gerenciar assinaturas de acesso compartilhado. É recomendável criar uma política de acesso compartilhado, pois ela permite revogar a SAS se ela for comprometida.

O exemplo a seguir cria uma política de acesso compartilhado em um compartilhamento. O exemplo usa essa política para fornecer as restrições para uma SAS em um arquivo no compartilhamento.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    string policyName = "sampleSharePolicy" + DateTime.UtcNow.Ticks;

    // Create a new shared access policy and define its constraints.
    SharedAccessFilePolicy sharedPolicy = new SharedAccessFilePolicy()
        {
            SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24),
            Permissions = SharedAccessFilePermissions.Read | SharedAccessFilePermissions.Write
        };

    // Get existing permissions for the share.
    FileSharePermissions permissions = share.GetPermissions();

    // Add the shared access policy to the share's policies. Note that each policy must have a unique name.
    permissions.SharedAccessPolicies.Add(policyName, sharedPolicy);
    share.SetPermissions(permissions);

    // Generate a SAS for a file in the share and associate this access policy with it.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");
    CloudFile file = sampleDir.GetFileReference("Log1.txt");
    string sasToken = file.GetSharedAccessSignature(null, policyName);
    Uri fileSasUri = new Uri(file.StorageUri.PrimaryUri.ToString() + sasToken);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    CloudFile fileSas = new CloudFile(fileSasUri);
    fileSas.UploadText("This write operation is authorized via SAS.");
    Console.WriteLine(fileSas.DownloadText());
}
```

Para obter mais informações sobre como criar e usar assinaturas de acesso compartilhado, consulte [como funciona uma assinatura de acesso compartilhado](../common/storage-sas-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#how-a-shared-access-signature-works).

## <a name="copy-files"></a>Copiar arquivos

A partir da versão 5.x da Biblioteca de Cliente do Armazenamento do Azure, você pode copiar um arquivo em outro arquivo, um arquivo em um blob ou um blob em um arquivo. Nas próximas seções, demonstraremos como fazer essas operações de cópia programaticamente.

Você também pode usar AzCopy para copiar um arquivo para outro ou para copiar um blob para um arquivo ou o contrário. Confira [Introdução ao AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

> [!NOTE]
> Se você estiver copiando um blob em um arquivo, ou um arquivo em um blob, use uma assinatura de acesso compartilhado (SAS) para autorizar o acesso ao objeto de origem, mesmo se estiver copiando dentro da mesma conta de armazenamento.
>

### <a name="copy-a-file-to-another-file"></a>Copiar um arquivo para outro arquivo

O exemplo a seguir copia um arquivo em outro arquivo no mesmo compartilhamento. Como essa operação de cópia copia entre arquivos na mesma conta de armazenamento, você pode usar a autenticação de chave compartilhada para fazer a cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Get a reference to the file share we created previously.
CloudFileShare share = fileClient.GetShareReference("logs");

// Ensure that the share exists.
if (share.Exists())
{
    // Get a reference to the root directory for the share.
    CloudFileDirectory rootDir = share.GetRootDirectoryReference();

    // Get a reference to the directory we created previously.
    CloudFileDirectory sampleDir = rootDir.GetDirectoryReference("CustomLogs");

    // Ensure that the directory exists.
    if (sampleDir.Exists())
    {
        // Get a reference to the file we created previously.
        CloudFile sourceFile = sampleDir.GetFileReference("Log1.txt");

        // Ensure that the source file exists.
        if (sourceFile.Exists())
        {
            // Get a reference to the destination file.
            CloudFile destFile = sampleDir.GetFileReference("Log1Copy.txt");

            // Start the copy operation.
            destFile.StartCopy(sourceFile);

            // Write the contents of the destination file to the console window.
            Console.WriteLine(destFile.DownloadText());
        }
    }
}
```

### <a name="copy-a-file-to-a-blob"></a>Copiar um arquivo para um blob

O exemplo a seguir cria um arquivo e o copia em um blob dentro da mesma conta de armazenamento. O exemplo cria uma SAS para o arquivo de origem, que o serviço usa para autorizar o acesso ao arquivo de origem durante a operação de cópia.

```csharp
// Parse the connection string for the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create a CloudFileClient object for credentialed access to Azure Files.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Create a new file share, if it does not already exist.
CloudFileShare share = fileClient.GetShareReference("sample-share");
share.CreateIfNotExists();

// Create a new file in the root directory.
CloudFile sourceFile = share.GetRootDirectoryReference().GetFileReference("sample-file.txt");
sourceFile.UploadText("A sample file in the root directory.");

// Get a reference to the blob to which the file will be copied.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();
CloudBlockBlob destBlob = container.GetBlockBlobReference("sample-blob.txt");

// Create a SAS for the file that's valid for 24 hours.
// Note that when you are copying a file to a blob, or a blob to a file, you must use a SAS
// to authorize access to the source object, even if you are copying within the same
// storage account.
string fileSas = sourceFile.GetSharedAccessSignature(new SharedAccessFilePolicy()
{
    // Only read permissions are required for the source file.
    Permissions = SharedAccessFilePermissions.Read,
    SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24)
});

// Construct the URI to the source file, including the SAS token.
Uri fileSasUri = new Uri(sourceFile.StorageUri.PrimaryUri.ToString() + fileSas);

// Copy the file to the blob.
destBlob.StartCopy(fileSasUri);

// Write the contents of the file to the console window.
Console.WriteLine("Source file contents: {0}", sourceFile.DownloadText());
Console.WriteLine("Destination blob contents: {0}", destBlob.DownloadText());
```

Você pode copiar um blob em um arquivo da mesma maneira. Se o objeto de origem for um blob, crie uma SAS para autorizar o acesso ao blob durante a operação de cópia.

## <a name="share-snapshots"></a>Instantâneos de compartilhamento

A partir da versão 8.5 da Biblioteca de Cliente do Armazenamento do Microsoft Azure, você pode criar um instantâneo de compartilhamento. Também pode listar ou procurar os instantâneos de compartilhamento e excluir os instantâneos de compartilhamento. Instantâneos de compartilhamentos são somente leitura, portanto, nenhuma operação de gravação é permitida em instantâneos de compartilhamento.

### <a name="create-share-snapshots"></a>Criar instantâneos de compartilhamento

O exemplo a seguir cria um instantâneo de compartilhamento de arquivos.

```csharp
storageAccount = CloudStorageAccount.Parse(ConnectionString); 
fClient = storageAccount.CreateCloudFileClient(); 
string baseShareName = "myazurefileshare"; 
CloudFileShare myShare = fClient.GetShareReference(baseShareName); 
var snapshotShare = myShare.Snapshot();

```

### <a name="list-share-snapshots"></a>Listar instantâneos de compartilhamento

O exemplo a seguir lista os instantâneos de compartilhamento em um compartilhamento.

```csharp
var shares = fClient.ListShares(baseShareName, ShareListingDetails.All);
```

### <a name="browse-files-and-directories-within-share-snapshots"></a>Procurar arquivos e diretórios em instantâneos de compartilhamento

O exemplo a seguir procura arquivos e diretórios nos instantâneos de compartilhamento.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); 
var rootDirectory = mySnapshot.GetRootDirectoryReference(); 
var items = rootDirectory.ListFilesAndDirectories();
```

### <a name="list-shares-and-share-snapshots-and-restore-file-shares-or-files-from-share-snapshots"></a>Listar compartilhamentos e compartilhar instantâneos e restaurar compartilhamentos de arquivos ou arquivos de instantâneos de compartilhamento

Tirar um instantâneo de um compartilhamento de arquivos permite a recuperação de arquivos individuais ou de todo o compartilhamento de arquivo no futuro.

Você pode restaurar um arquivo de um instantâneo de compartilhamento de arquivo consultando os instantâneos de compartilhamento de um compartilhamento de arquivos. Em seguida, você pode recuperar um arquivo que pertence a um instantâneo de compartilhamento específico. Use essa versão para ler e comparar diretamente ou para restaurar o.

```csharp
CloudFileShare liveShare = fClient.GetShareReference(baseShareName);
var rootDirOfliveShare = liveShare.GetRootDirectoryReference();
var dirInliveShare = rootDirOfliveShare.GetDirectoryReference(dirName);
var fileInliveShare = dirInliveShare.GetFileReference(fileName);

CloudFileShare snapshot = fClient.GetShareReference(baseShareName, snapshotTime);
var rootDirOfSnapshot = snapshot.GetRootDirectoryReference();
var dirInSnapshot = rootDirOfSnapshot.GetDirectoryReference(dirName);
var fileInSnapshot = dir1InSnapshot.GetFileReference(fileName);

string sasContainerToken = string.Empty;
SharedAccessFilePolicy sasConstraints = new SharedAccessFilePolicy();
sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
sasConstraints.Permissions = SharedAccessFilePermissions.Read;

//Generate the shared access signature on the container, setting the constraints directly on the signature.
sasContainerToken = fileInSnapshot.GetSharedAccessSignature(sasConstraints);

string sourceUri = (fileInSnapshot.Uri.ToString() + sasContainerToken + "&" + fileInSnapshot.SnapshotTime.ToString()); ;
fileInliveShare.StartCopyAsync(new Uri(sourceUri));
```

### <a name="delete-share-snapshots"></a>Excluir instantâneos de compartilhamento

O exemplo a seguir exclui um instantâneo de compartilhamento de arquivos.

```csharp
CloudFileShare mySnapshot = fClient.GetShareReference(baseShareName, snapshotTime); mySnapshot.Delete(null, null, null);
```

## Solucionar problemas de arquivos do Azure usando métricas<a name="troubleshooting-azure-files-using-metrics"></a>

A Análise de Armazenamento do Azure agora dá suporte a métricas para os Arquivos do Azure. Com dados de métricas, você pode rastrear solicitações e diagnosticar problemas.

Você pode habilitar as métricas para arquivos do Azure do [portal do Azure](https://portal.azure.com). Você também pode habilitar as métricas programaticamente chamando a operação definir propriedades do serviço de arquivo com a API REST ou uma de suas analogias na biblioteca do cliente de armazenamento.

O exemplo de código a seguir mostra como usar a Biblioteca de Cliente de Armazenamento para .NET para habilitar as métricas para os Arquivos do Azure.

Primeiro, adicione as seguintes diretivas `using` ao arquivo `Program.cs`, juntamente com aquelas que você adicionou acima:

```csharp
using Microsoft.Azure.Storage.File.Protocol;
using Microsoft.Azure.Storage.Shared.Protocol;
```

Embora os BLOBs do Azure, as tabelas do Azure e as filas do Azure usem o tipo `ServiceProperties` compartilhado no namespace `Microsoft.Azure.Storage.Shared.Protocol`, os arquivos do Azure usam seu próprio tipo, o tipo `FileServiceProperties` no namespace `Microsoft.Azure.Storage.File.Protocol`. Você deve referenciar ambos os namespaces do seu código, no entanto, para que o código a seguir seja compilado.

```csharp
// Parse your storage connection string from your application's configuration file.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));
// Create the File service client.
CloudFileClient fileClient = storageAccount.CreateCloudFileClient();

// Set metrics properties for File service.
// Note that the File service currently uses its own service properties type,
// available in the Microsoft.Azure.Storage.File.Protocol namespace.
fileClient.SetServiceProperties(new FileServiceProperties()
{
    // Set hour metrics
    HourMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 14,
        Version = "1.0"
    },
    // Set minute metrics
    MinuteMetrics = new MetricsProperties()
    {
        MetricsLevel = MetricsLevel.ServiceAndApi,
        RetentionDays = 7,
        Version = "1.0"
    }
});

// Read the metrics properties we just set.
FileServiceProperties serviceProperties = fileClient.GetServiceProperties();
Console.WriteLine("Hour metrics:");
Console.WriteLine(serviceProperties.HourMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.HourMetrics.RetentionDays);
Console.WriteLine(serviceProperties.HourMetrics.Version);
Console.WriteLine();
Console.WriteLine("Minute metrics:");
Console.WriteLine(serviceProperties.MinuteMetrics.MetricsLevel);
Console.WriteLine(serviceProperties.MinuteMetrics.RetentionDays);
Console.WriteLine(serviceProperties.MinuteMetrics.Version);
```

Se você encontrar algum problema, poderá consultar [solucionar problemas de arquivos do Azure no Windows](storage-troubleshoot-windows-file-connection-problems.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os arquivos do Azure, consulte os seguintes recursos:

### <a name="conceptual-articles-and-videos"></a>Artigos e vídeos conceituais

* [Arquivos do Azure: um sistema de arquivos SMB de nuvem ininterrupta para Windows e Linux](https://azure.microsoft.com/documentation/videos/azurecon-2015-azure-files-storage-a-frictionless-cloud-smb-file-system-for-windows-and-linux/)
* [Usar os arquivos do Azure com o Linux](storage-how-to-use-files-linux.md)

### <a name="tooling-support-for-file-storage"></a>Suporte de ferramentas para o armazenamento de arquivos

* [Introdução ao AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)
* [Usando a CLI do Azure com o Armazenamento do Azure](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#create-and-manage-file-shares)
* [Solucionar problemas de Arquivos do Azure no Windows](https://docs.microsoft.com/azure/storage/storage-troubleshoot-file-connection-problems)

### <a name="reference"></a>Referência

* [APIs de armazenamento do Azure para .NET](/dotnet/api/overview/azure/storage)
* [API REST do serviço de arquivo](/rest/api/storageservices/File-Service-REST-API)

### <a name="blog-posts"></a>Postagens no blog

* [Armazenamento de arquivos do Azure, agora disponível para o público geral](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/)
* [Por dentro do Armazenamento de arquivos do Azure](https://azure.microsoft.com/blog/inside-azure-file-storage/)
* [Introdução ao serviço de arquivos de Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
* [Persistindo conexões para arquivos do Microsoft Azure](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx)
