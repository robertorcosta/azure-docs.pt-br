---
title: Criar e gerenciar um instantâneo de blob no .NET-armazenamento do Azure
description: Saiba como criar um instantâneo somente leitura de um blob para fazer backup de dados de BLOB em um determinado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 09/06/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: d9650db601426cef195c27e6eb874ec26cf13bb8
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/12/2020
ms.locfileid: "79137672"
---
# <a name="create-and-manage-a-blob-snapshot-in-net"></a>Criar e gerenciar um instantâneo de blob no .NET

Um instantâneo é uma versão somente leitura de um blob capturada em um momento no tempo. Os instantâneos são úteis para fazer backup de blobs. Este artigo mostra como criar e gerenciar instantâneos de BLOB usando a [biblioteca de cliente de armazenamento do Azure para .net](/dotnet/api/overview/azure/storage?view=azure-dotnet).

## <a name="about-blob-snapshots"></a>Sobre instantâneos de BLOB

O instantâneo de um blob é idêntico ao respectivo blob de base, exceto pelo fato de que o URI do blob tem um valor **DateTime** para indicar a hora em que o instantâneo foi criado. Por exemplo, se um URI de blob de páginas for `http://storagesample.core.blob.windows.net/mydrives/myvhd`, o URI do instantâneo será semelhante a `http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z`.

> [!NOTE]
> Todos os instantâneos compartilham o URI do blob de base. A única diferença entre o blob de base e o instantâneo é o acréscimo do valor **DateTime** .
>

Um blob pode ter qualquer número de instantâneos. Os instantâneos persistem até serem explicitamente excluídos, o que significa que um instantâneo não pode sobreviver além seu blob de base. Você pode enumerar os instantâneos associados ao blob de base para acompanhar seus instantâneos atuais.

Quando você cria um instantâneo de um blob, as propriedades do sistema são copiadas para o instantâneo com os mesmos valores. Os metadados do blob de base também são copiados no instantâneo, a menos que você especifique metadados separados para o instantâneo ao criá-lo. Depois de criar um instantâneo, você pode lê-lo, copiá-lo ou excluí-lo, mas não é possível modificá-lo.

Quaisquer concessões associadas ao blob básico não afetam o instantâneo. Não é possível adquirir uma concessão em um instantâneo.

Um arquivo VHD é usado para armazenar as informações atuais e o status de um disco da VM. Você pode desanexar um disco de dentro da VM ou desligar a VM e, em seguida, tirar um instantâneo de seu arquivo VHD. É possível usar esse arquivo de instantâneo posteriormente para recuperar o arquivo VHD nesse ponto no tempo e recriar a VM.

## <a name="create-a-snapshot"></a>Criar um instantâneo

Para criar um instantâneo de um blob de blocos, use um dos seguintes métodos:

- [CreateSnapshot](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshot)
- [CreateSnapshotAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblockblob.createsnapshotasync)

O exemplo de código a seguir mostra como criar um instantâneo. Este exemplo especifica metadados adicionais para o instantâneo quando ele é criado.

```csharp
private static async Task CreateBlockBlobSnapshot(CloudBlobContainer container)
{
    // Create a new block blob in the container.
    CloudBlockBlob baseBlob = container.GetBlockBlobReference("sample-base-blob.txt");

    // Add blob metadata.
    baseBlob.Metadata.Add("ApproxBlobCreatedDate", DateTime.UtcNow.ToString());

    try
    {
        // Upload the blob to create it, with its metadata.
        await baseBlob.UploadTextAsync(string.Format("Base blob: {0}", baseBlob.Uri.ToString()));

        // Sleep 5 seconds.
        System.Threading.Thread.Sleep(5000);

        // Create a snapshot of the base blob.
        // You can specify metadata at the time that the snapshot is created.
        // If no metadata is specified, then the blob's metadata is copied to the snapshot.
        Dictionary<string, string> metadata = new Dictionary<string, string>();
        metadata.Add("ApproxSnapshotCreatedDate", DateTime.UtcNow.ToString());
        await baseBlob.CreateSnapshotAsync(metadata, null, null, null);
    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="delete-snapshots"></a>Excluir instantâneos

Para excluir um blob, você deve primeiro excluir todos os instantâneos desse blob. Você pode excluir um instantâneo individualmente ou especificar que todos os instantâneos sejam excluídos quando o blob de origem for excluído. Se você tentar excluir um blob que ainda possua instantâneos, um erro será gerado.

Para excluir instantâneos de BLOB, use um dos seguintes métodos de exclusão de BLOB e inclua a enumeração [DeleteSnapshotsOption](/dotnet/api/microsoft.azure.storage.blob.deletesnapshotsoption) .

- [Delete (excluir)](/dotnet/api/microsoft.azure.storage.blob.cloudblob.delete)
- [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteasync)
- [DeleteIfExists](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexists)
- [DeleteIfExistsAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblob.deleteifexistsasync)

O exemplo de código a seguir mostra como excluir um blob e seus instantâneos no .NET, em que `blockBlob` é um objeto do tipo [CloudBlockBlob][dotnet_CloudBlockBlob]:

```csharp
await blockBlob.DeleteIfExistsAsync(DeleteSnapshotsOption.IncludeSnapshots, null, null, null);
```

## <a name="return-the-absolute-uri-to-a-snapshot"></a>Retornar o URI absoluto para um instantâneo

O exemplo de código a seguir cria um instantâneo e grava o URI absoluto para o local primário.

```csharp
//Create the blob service client object.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";

CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

//Get a reference to a container.
CloudBlobContainer container = blobClient.GetContainerReference("sample-container");
container.CreateIfNotExists();

//Get a reference to a blob.
CloudBlockBlob blob = container.GetBlockBlobReference("sampleblob.txt");
blob.UploadText("This is a blob.");

//Create a snapshot of the blob and write out its primary URI.
CloudBlockBlob blobSnapshot = blob.CreateSnapshot();
Console.WriteLine(blobSnapshot.SnapshotQualifiedStorageUri.PrimaryUri);
```

## <a name="understand-how-snapshots-accrue-charges"></a>Entender como instantâneos acumulam cobranças

Criar um instantâneo, que é uma cópia somente leitura de um blob, pode resultar em cobranças de armazenamento de dados adicionais para sua conta. Ao projetar o aplicativo, é importante estar ciente de como essas cobranças podem aumentar para que você possa minimizar custos.

### <a name="important-billing-considerations"></a>Considerações importantes sobre cobrança

A lista a seguir inclui os principais pontos a considerar ao criar um instantâneo.

- Sua conta de armazenamento é cobrada por páginas ou blocos exclusivos, estejam eles no blob ou no instantâneo. Sua conta não incorre em cobranças adicionais para instantâneos associados a um blob até que você atualize o blob no qual eles se baseiam. Depois de atualizar o blob de base, ele divergirá dos respectivos instantâneos. Quando isso acontece, você é cobrado por páginas ou blocos exclusivos em cada blob ou instantâneo.
- Quando você substituir um bloco em um blob de blocos, esse bloco será cobrado subsequentemente como um único bloco. Isso é verdadeiro mesmo se o bloco tiver a mesma ID de bloco e os mesmos dados que tem no instantâneo. Depois que o bloco for confirmado novamente, ele divergirá de sua contraparte em qualquer instantâneo e você será cobrado por seus dados. O mesmo se aplica para uma página em um blob de páginas é atualizada com os dados idênticos.
- Substituir um blob de bloco chamando o método [Uploadfromfile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]ou [UploadFromByteArray][dotnet_UploadFromByteArray] substitui todos os blocos no BLOB. Se você tiver um instantâneo associado a esse blob, todos os blocos no blob de base e o instantâneo agora divergirão e você será cobrado por todos os blocos em ambos os blobs. Isso é verdadeiro mesmo se os dados no blob de base e o instantâneo permanecerem idênticos.
- O serviço Blob do Azure não tem um meio para determinar se dois blocos contêm dados idênticos. Cada bloco que é carregado e confirmado é tratado como exclusivo, mesmo se tiver os mesmos dados e a mesma ID de bloco. Já que as cobranças se acumulam para blocos exclusivos, é importante considerar que a atualização de um blob que tenha um instantâneo resulta em blocos exclusivos adicionais e cobranças adicionais.

### <a name="minimize-cost-with-snapshot-management"></a>Minimizar o custo com gerenciamento de instantâneos

É recomendável gerenciar seus instantâneos com cuidado para evitar cobranças extras. Você pode seguir estas práticas recomendadas para ajudar a minimizar os custos incorridos pelo armazenamento de seus instantâneos:

- Exclua e recrie instantâneos associados a um blob sempre que você atualiza o blob, mesmo se você estiver atualizando com dados idênticos, a menos que o design do seu aplicativo requer que você mantenha os instantâneos. Ao excluir e recriar os instantâneos do blob, você pode garantir que o blob e os instantâneos não divirjam.
- Se você estiver mantendo instantâneos para um blob, evite chamar [Uploadfromfile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]ou [UploadFromByteArray][dotnet_UploadFromByteArray] para atualizar o blob. Esses métodos substituem todos os blocos no blob, fazendo com que o blob base e os respectivos instantâneos divirjam consideravelmente. Em vez disso, atualize o menor número possível de blocos usando os métodos [PutBlock][dotnet_PutBlock] e [PutBlockList][dotnet_PutBlockList].

### <a name="snapshot-billing-scenarios"></a>Cenários de cobrança de instantâneo

Os cenários a seguir demonstram como cobranças se acumulam para um blob de blocos e seus instantâneos.

#### <a name="scenario-1"></a>Cenário 1

No cenário 1, o blob de base não foi atualizado depois que o instantâneo foi tirado, portanto, as cobranças incorrem apenas para os blocos exclusivos 1, 2 e 3.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-1.png)

#### <a name="scenario-2"></a>Cenário 2

No cenário 2, o blob de base foi atualizado, mas o instantâneo, não. O bloco 3 foi atualizado e mesmo que ele contenha os mesmos dados e a mesma ID, não é igual ao bloco 3 no instantâneo. Como resultado, a conta é cobrada por quatro blocos.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-2.png)

#### <a name="scenario-3"></a>Cenário 3

No cenário 3, o blob de base foi atualizado, mas o instantâneo, não. O bloco 3 foi substituído pelo bloco 4 no blob de base, mas o instantâneo ainda reflete o bloco 3. Como resultado, a conta é cobrada por quatro blocos.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-3.png)

#### <a name="scenario-4"></a>Cenário 4

No cenário 4, o blob de base foi totalmente atualizado e não contém nenhum dos seus blocos originais. Como resultado, a conta é cobrada por todos os oito blocos exclusivos. Esse cenário pode ocorrer se você estiver usando um método Update, como [Uploadfromfile][dotnet_UploadFromFile], [UploadText][dotnet_UploadText], [UploadFromStream][dotnet_UploadFromStream]ou [UploadFromByteArray][dotnet_UploadFromByteArray], porque esses métodos substituem todo o conteúdo de um blob.

![Recursos de Armazenamento do Azure](./media/storage-blob-snapshots/storage-blob-snapshots-billing-scenario-4.png)

## <a name="next-steps"></a>Próximas etapas

- Você pode encontrar mais informações sobre como trabalhar com instantâneos de disco de VM (máquina virtual) em [Fazer backup dos discos não gerenciados de VM do Azure com instantâneos incrementais](../../virtual-machines/windows/incremental-snapshots.md)

- Para obter exemplos de código adicionais usando o Armazenamento de Blobs, confira [Exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?service=storage&term=blob). Você pode baixar um aplicativo de exemplo e executá-lo ou procurar o código no GitHub.

[dotnet_AccessCondition]: https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.accesscondition
[dotnet_CloudBlockBlob]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob
[dotnet_CreateSnapshotAsync]: https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudpageblob.createsnapshotasync
[dotnet_HTTPStatusCode]: https://docs.microsoft.com/java/api/com.microsoft.store.partnercenter.network.httpstatuscode
[dotnet_PutBlockList]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblocklist
[dotnet_PutBlock]: /dotnet/api/microsoft.azure.storage.blob.cloudblockblob.putblock
[dotnet_UploadFromByteArray]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfrombytearray
[dotnet_UploadFromFile]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.uploadfromfile
[dotnet_UploadFromStream]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadfromstream
[dotnet_UploadText]: /dotnet/api/microsoft.azure.storage.blob.cloudappendblob.uploadtext
