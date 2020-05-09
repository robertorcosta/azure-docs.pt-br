---
title: Habilitar exclusão reversível para BLOBs
titleSuffix: Azure Storage
description: Habilite a exclusão reversível para objetos de BLOB para recuperar seus dados com mais facilidade quando eles forem modificados ou excluídos erroneamente.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 26a16d0eeb81c12faede1c00bdf5a0d724f7a6c6
ms.sourcegitcommit: d815163a1359f0df6ebfbfe985566d4951e38135
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82884677"
---
# <a name="enable-soft-delete-for-blobs"></a>Habilitar exclusão reversível para BLOBs

As etapas a seguir mostram como começar a usar a exclusão reversível.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Habilite a exclusão reversível para BLOBs em sua conta de armazenamento usando portal do Azure:

1. Na [portal do Azure](https://portal.azure.com/), selecione sua conta de armazenamento. 

2. Navegue até a opção **proteção de dados** em **serviço blob**.

3. Clique em **habilitado** em **blob exclusão reversível**

4. Insira o número de dias que você deseja *reter para* **as políticas de retenção**

5. Escolha o botão **salvar** para confirmar suas configurações de proteção de dados

![](media/soft-delete-enable/storage-blob-soft-delete-portal-configuration.png)

Para exibir blobs com exclusão reversível, marque a caixa de seleção **Mostrar blobs excluídos**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted.png)

Para exibir instantâneos com exclusão reversível para um determinado blob, selecione o blob e clique em **Exibir instantâneos**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots.png)

Verifique se a caixa de seleção **Mostrar instantâneos excluídos** está selecionada.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-view-soft-deleted-snapshots-check.png)

Quando você clica em um blob com exclusão reversível ou um instantâneo, observe as novas propriedades do blob. Elas indicam quando o objeto foi excluído e o número de dias até que o blob ou o instantâneo de blob expire permanentemente. Se o objeto com exclusão reversível não for um instantâneo, você também terá a opção de restaurá-lo.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-properties.png)

Lembre-se de que restaurar um blob também restaurará todos os instantâneos associados. Para restaurar instantâneos com exclusão reversível para um blob ativo, clique no blob e selecione **Restaurar todos os instantâneos**.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-undelete-all-snapshots.png)

Depois que restaurar os instantâneos de um blob, você poderá clicar em **Promover** para copiar um instantâneo sobre o blob raiz, restaurando assim o blob para o instantâneo.

![](media/soft-delete-enable/storage-blob-soft-delete-portal-promote-snapshot.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob. O exemplo a seguir habilita a exclusão reversível para um subconjunto de contas em uma assinatura:

```powershell
Set-AzContext -Subscription "<subscription-name>"
$MatchingAccounts = Get-AzStorageAccount | where-object{$_.StorageAccountName -match "<matching-regex>"}
$MatchingAccounts | Enable-AzStorageDeleteRetentionPolicy -RetentionDays 7
```
É possível verificar se a exclusão reversível foi ativada usando o comando a seguir:

```powershell
$MatchingAccounts | $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context | Select-Object -ExpandProperty DeleteRetentionPolicy
```

Para recuperar os blobs que foram excluídos acidentalmente, chame Restaurar nesses blobs. Lembre-se de que chamar **Restaurar Blob** em blobs ativos e com exclusão reversível restaurará todos os instantâneos com exclusão reversível associados como ativos. O exemplo a seguir chama Restaurar em todos os blobs com exclusão reversível e ativos em um contêiner:

```powershell
# Create a context by specifying storage account name and key
$ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

# Get the blobs in a given container and show their properties
$Blobs = Get-AzStorageBlob -Container $StorageContainerName -Context $ctx -IncludeDeleted
$Blobs.ICloudBlob.Properties

# Undelete the blobs
$Blobs.ICloudBlob.Undelete()
```
Para localizar a política de retenção de exclusão reversível atual, use o comando a seguir:

```azurepowershell-interactive
   $account = Get-AzStorageAccount -ResourceGroupName myresourcegroup -Name storageaccount
   Get-AzStorageServiceProperty -ServiceType Blob -Context $account.Context
```

# <a name="cli"></a>[CLI](#tab/azure-CLI)

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```azurecli-interactive
az storage blob service-properties delete-policy update --days-retained 7  --account-name mystorageaccount --enable true
```

Para verificar se a exclusão reversível está ativada, use o seguinte comando: 

```azurecli-interactive
az storage blob service-properties delete-policy show --account-name mystorageaccount 
```

# <a name="python"></a>[Python](#tab/python)

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```python
# Make the requisite imports
from azure.storage.blob import BlockBlobService
from azure.storage.common.models import DeleteRetentionPolicy

# Initialize a block blob service
block_blob_service = BlockBlobService(
    account_name='<enter your storage account name>', account_key='<enter your storage account key>')

# Set the blob client's service property settings to enable soft delete
block_blob_service.set_blob_service_properties(
    delete_retention_policy=DeleteRetentionPolicy(enabled=True, days=7))
```

# <a name="net"></a>[.NET](#tab/net)

Para habilitar a exclusão reversível, atualize as propriedades do serviço do cliente de um blob:

```csharp
// Get the blob client's service property settings
ServiceProperties serviceProperties = blobClient.GetServiceProperties();

// Configure soft delete
serviceProperties.DeleteRetentionPolicy.Enabled = true;
serviceProperties.DeleteRetentionPolicy.RetentionDays = RetentionDays;

// Set the blob client's service property settings
blobClient.SetServiceProperties(serviceProperties);
```

Para recuperar os blobs que foram excluídos acidentalmente, chame Restaurar nesses blobs. Lembre-se de que chamar **Restaurar Blob** em blobs ativos e com exclusão reversível restaurará todos os instantâneos com exclusão reversível associados como ativos. O exemplo a seguir chama Restaurar em todos os blobs com exclusão reversível e ativos em um contêiner:

```csharp
// Recover all blobs in a container
foreach (CloudBlob blob in container.ListBlobs(useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Deleted))
{
       await blob.UndeleteAsync();
}
```

Para recuperar para uma versão específica do blob, primeiro chame Restaurar em um blob e depois copie o instantâneo desejado sobre o blob. O exemplo a seguir recupera um blob de blocos para o seu instantâneo gerado mais recentemente:

```csharp
// Undelete
await blockBlob.UndeleteAsync();

// List all blobs and snapshots in the container prefixed by the blob name
IEnumerable<IListBlobItem> allBlobVersions = container.ListBlobs(
    prefix: blockBlob.Name, useFlatBlobListing: true, blobListingDetails: BlobListingDetails.Snapshots);

// Restore the most recently generated snapshot to the active blob    
CloudBlockBlob copySource = allBlobVersions.First(version => ((CloudBlockBlob)version).IsSnapshot &&
    ((CloudBlockBlob)version).Name == blockBlob.Name) as CloudBlockBlob;
blockBlob.StartCopy(copySource);
```

---

