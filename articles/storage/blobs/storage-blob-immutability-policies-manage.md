---
title: Definir e gerenciar políticas de imutabilidade para armazenamento Blob - Armazenamento Azure
description: Aprenda a usar o suporte WORM (Write Once, Read Many) para armazenamento Blob (objeto) para armazenar dados em um estado não apagá-la, não modificável, por um intervalo especificado.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970110"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Definir e gerenciar políticas de imutabilidade para armazenamento Blob

O armazenamento imutável para armazenamento Azure Blob permite que os usuários armazenem objetos de dados essenciais para os negócios em um estado WORM (Write Once, Read Many). Esse estado torna os dados não apagáveis e não modificáveis para um intervalo especificado pelo usuário. Durante o intervalo de retenção, as bolhas podem ser criadas e lidas, mas não podem ser modificadas ou excluídas. O armazenamento imutável está disponível para contas de armazenamento v2 e Blob de uso geral em todas as regiões do Azure.

Este artigo mostra como definir e gerenciar políticas de imutabilidade e restrições legais para dados no armazenamento Blob usando o portal Azure, PowerShell ou Azure CLI. Para obter mais informações sobre armazenamento imutável, consulte [Armazenar dados de bolha susdecidos para os negócios com armazenamento imutável](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Definir políticas de retenção e detenção legal

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Crie um novo contêiner ou selecione um existente para armazenar os blobs que precisam ser mantidos no estado imutável. O contêiner deve estar em uma conta de armazenamento v2 ou Blob de uso geral.

2. Selecione **Política de acesso** no menu de contexto. Em seguida, **selecione Adicionar política** em **armazenamento de bolha imutável**.

    ![Configurações de contêiner no portal](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Para habilitar a retenção baseada em tempo, selecione **baseada em tempo de retenção** no menu suspenso.

    !["Com base no tempo de retenção" selecionada em "Tipo de política"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Digite o intervalo de retenção em dias (valores aceitáveis são de 1 a 146000 dias).

    ![Caixa "Período de retenção de atualização para"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    O estado inicial da diretiva é desbloqueado permitindo que você teste o recurso e faça alterações na diretiva antes de bloqueá-lo. O bloqueio da apólice é essencial para o cumprimento de regulamentos como a SEC 17a-4.

5. A política de bloqueio. Clique com o botão direito do mouse na elipse (**...**), e o menu a seguir aparece com ações adicionais:

    !["Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selecione **Política de bloqueio** e confirme o bloqueio. A diretiva está bloqueada e não pode ser excluída, somente extensões do intervalo de retenção serão permitidas. Blob exclui e substituições não são permitidas. 

    ![Confirme "Política de bloqueio" no menu](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Para habilitar os desmeminásos legais, **selecione Adicionar política**. Selecione **Retenção legal** no menu suspenso.

    !["Legal hold" no menu em "Tipo de política"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Crie uma retenção legal com uma ou mais tags.

    ![Caixa "Nome da tag" no tipo de política](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Para limpar uma remoção legal, remova a tag de identificador de reter legal aplicada.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

O recurso está incluído nos seguintes grupos de comandos: `az storage container immutability-policy`e`az storage container legal-hold`. Execute `-h` neles para ver os comandos.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

O módulo Az.Storage suporta armazenamento imutável.  Para habilitar o recurso, siga estas etapas:

1. Certifique-se de que você tenha a versão mais recente do PowerShellGet instalado: `Install-Module PowerShellGet –Repository PSGallery –Force`.
2. Remova qualquer instalação anterior do PowerShell do Azure.
3. Instale o Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

O exemplo de script do PowerShell a seguir é para referência. Este script cria uma nova conta e contêiner de armazenamento. Em seguida, ele mostra como definir e limpar os bloqueios legais, criar e bloquear uma política de retenção baseada no tempo (também conhecida como política de imutabilidade) e estender o intervalo de retenção.

Primeiro, crie uma conta do Azure Storage:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Definir e limpar retenções legais:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Criar ou atualizar políticas de imutabilidade baseadas no tempo:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Recuperar políticas de imutabilidade:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Bloquear políticas de imutabilidade (adicionar `-Force` para descartar o prompt):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Estender as políticas de imutabilidade:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Remova uma política de imutabilidade `-Force` desbloqueada (adicione para descartar o prompt):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Permitindo que as bolhas de apêndice protegidas escrevam

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Permitir gravações adicionais de apêndice](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

O recurso está incluído nos seguintes grupos de comandos: `az storage container immutability-policy`e`az storage container legal-hold`. Execute `-h` neles para ver os comandos.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Próximas etapas

[Armazene dados de bolha críticos para os negócios com armazenamento imutável](storage-blob-immutable-storage.md)
