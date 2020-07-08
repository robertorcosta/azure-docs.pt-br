---
title: Configurar a replicação de objeto (versão prévia)
titleSuffix: Azure Storage
description: Saiba como configurar a replicação de objeto para copiar blobs de blocos de maneira assíncrona de um contêiner em uma conta de armazenamento para outra.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 9cb9f1a33c37487f4bfb1419d45d4e42a862d815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888110"
---
# <a name="configure-object-replication-for-block-blobs-preview"></a>Configurar a replicação de objeto para blobs de blocos (versão prévia)

A replicação de objeto (versão prévia) copia de maneira assíncrona os blobs de blocos entre uma conta de armazenamento de origem e uma conta de destino. Para saber mais sobre a replicação de objeto, confira [Replicação de objeto (versão prévia)](object-replication-overview.md).

Ao configurar a replicação de objeto, você cria uma política de replicação que especifica a conta de armazenamento de origem e a conta de destino. Uma política de replicação inclui uma ou mais regras que especificam um contêiner de origem e um contêiner de destino e indicam quais blobs de blocos no contêiner de origem serão replicados.

Este artigo descreve como configurar a replicação de objeto para sua conta de armazenamento usando o portal do Azure, o PowerShell ou a CLI do Azure. Você também pode usar uma das bibliotecas de clientes do provedor de recursos do Armazenamento do Azure para configurar a replicação de objeto.

## <a name="create-a-replication-policy-and-rules"></a>Criar uma política de replicação e regras

Antes de configurar a replicação de objeto, crie as contas de armazenamento de origem e de destino se elas ainda não existirem. Ambas as contas precisam ser contas de armazenamento de uso geral v2. Para saber mais informações, confira [Criar uma conta do Armazenamento do Azure](../common/storage-account-create.md).

Uma conta de armazenamento pode servir como a conta de origem para até duas contas de destino. E uma conta de destino pode não ter mais do que duas contas de origem. As contas de origem e de destino podem estar em regiões diferentes. É possível configurar políticas de replicação separadas para replicar dados para cada uma das contas de destino.

Antes de começar, certifique-se de que você se registrou para as seguintes visualizações de recurso:

- [Replicação de objeto (versão prévia)](object-replication-overview.md)
- [Controle de versão de blob (versão prévia)](versioning-overview.md)
- [Suporte ao feed de alterações no Armazenamento de Blobs do Azure (versão prévia)](storage-blob-change-feed.md)

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Antes de configurar a replicação de objeto no portal do Azure, crie os contêineres de origem e de destino nas respectivas contas de armazenamento se eles ainda não existirem. Além disso, habilite o controle de versão de blob e o feed de alterações na conta de origem e habilite o controle de versão de blob na conta de destino.

Para criar uma política de replicação no portal do Azure, siga estas etapas:

1. Navegue até a conta de armazenamento de origem no portal do Azure.
1. Em **serviço blob**, selecione **replicação de objeto**.
1. Selecione **Configurar a replicação**.
1. Selecione a assinatura de destino e a conta de armazenamento.
1. Na seção **Pares de contêineres**, selecione um contêiner de origem da conta de origem e um contêiner de destino da conta de destino. Você pode criar até dez pares de contêineres por política de replicação.

    A imagem a seguir mostra um conjunto de regras de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-policy.png" alt-text="Captura de tela mostrando regras de replicação no portal do Azure":::

1. Se desejar, especifique um ou mais filtros para copiar somente os blobs que correspondam a um padrão de prefixo. Por exemplo, se você especificar um prefixo `b`, somente os blobs cujo nome começar com essa letra serão replicados. Você pode especificar um diretório virtual como parte do prefixo.

    A imagem a seguir mostra filtros que restringem quais blobs são copiados como parte de uma regra de replicação.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-prefix.png" alt-text="Captura de tela mostrando filtros para uma regra de replicação":::

1. Por padrão, o escopo de cópia é definido para copiar somente objetos novos. Para copiar todos os objetos no contêiner ou para copiar objetos de uma data e hora personalizadas, selecione o link **alterar** e configure o escopo de cópia para o par de contêineres.

    A imagem a seguir mostra um escopo de cópia personalizado.

    :::image type="content" source="media/object-replication-configure/configure-replication-copy-scope.png" alt-text="Captura de tela mostrando o escopo de cópia personalizados para a replicação de objeto":::

1. Selecione **Salvar e aplicar** para criar a política de replicação e iniciar a replicação de dados.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para criar uma política de replicação com o PowerShell, primeiro instale a versão [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) do módulo Az.Storage do PowerShell. Para instalar o módulo de versão prévia, siga estas etapas:

1. Remova as instalações anteriores do Azure PowerShell do Windows usando a configuração **Aplicativos e recursos** em **Configurações**.

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

    Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instale o módulo Az.Storage de versão prévia:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 2.0.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

O exemplo a seguir mostra como criar uma política de replicação nas contas de origem e de destino. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set variables.
$rgName = "<resource-group>"
$srcAccountName = "<source-storage-account>"
$destAccountName = "<destination-storage-account>"
$srcContainerName1 = "source-container1"
$destContainerName1 = "dest-container1"
$srcContainerName2 = "source-container2"
$destContainerName2 = "dest-container2"

# Enable blob versioning and change feed on the source account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable blob versioning on the destination account.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -IsVersioningEnabled $true

# List the service properties for both accounts.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName

# Create containers in the source and destination accounts.
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName1
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $srcAccountName |
    New-AzStorageContainer $srcContainerName2
Get-AzStorageAccount -ResourceGroupName $rgname -StorageAccountName $destAccountName |
    New-AzStorageContainer $destContainerName2

# Define replication rules for each container.
$rule1 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName1 `
    -DestinationContainer $destContainerName1 `
    -PrefixMatch b
$rule2 = New-AzStorageObjectReplicationPolicyRule -SourceContainer $srcContainerName2 `
    -DestinationContainer $destContainerName2  `
    -MinCreationTime 2020-05-10T00:00:00Z

# Create the replication policy on the destination account.
$destPolicy = Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId default `
    -SourceAccount $srcAccountName `
    -Rule $rule1,$rule2

# Create the same policy on the source account.
Set-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -InputObject $destPolicy
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para criar uma política de replicação com a CLI do Azure, primeiro instale a versão prévia da extensão para o Armazenamento do Azure:

```azurecli
az extension add -n storage-or-preview
```

Em seguida, entre com suas credenciais do Azure:

```azurecli
az login
```

Além disso, habilite o controle de versão nas contas de armazenamento de origem e de destino e habilite o feed de alterações na conta de origem. Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores:

```azurecli
az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-versioning

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <source-storage-account> \
    --enable-change-feed

az storage blob service-properties update --resource-group <resource-group> \
    --account-name <dest-storage-account> \
    --enable-versioning
```

Crie os contêineres de origem e de destino nas respectivas contas de armazenamento.

```azurecli
az storage container create --account-name <source-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <source-storage-account> --name source-container4 --auth-mode login

az storage container create --account-name <dest-storage-account> --name source-container3 --auth-mode login
az storage container create --account-name <dest-storage-account> --name source-container4 --auth-mode login
```

Crie uma política de replicação e as regras associadas na conta de destino.

```azurecli
az storage account or-policy create --account-name <dest-storage-account> \
    --resource-group <resource-group> \
    --source-account <source-storage-account> \
    --destination-account <dest-storage-account> \
    --source-container source-container3 \
    --destination-container dest-container3 \
    --min-creation-time '2020-05-10T00:00:00Z' \
    --prefix-match a

az storage account or-policy rule add --account-name <dest-storage-account> \
    --destination-container dest-container4 \
    --policy-id <policy-id> \
    --resource-group <resource-group> \
    --source-container source-container4 \
    --prefix-match b
```

Crie a política na conta de origem usando a ID da política.

```azurecli
az storage account or-policy show --resource-group <resource-group> \
    --name <dest-storage-account> \
    --policy-id <policy-id> |
    --az storage account or-policy create --resource-group <resource-group> \
    --name <source-storage-account> \
    --policy "@-"
```

---

## <a name="remove-a-replication-policy"></a>Remover uma política de replicação

Para remover uma política de replicação e as respectivas regras associadas, use o portal do Azure o PowerShell ou a CLI.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para remover uma política de replicação no portal do Azure, siga estas etapas:

1. Navegue até a conta de armazenamento de origem no portal do Azure.
1. Em **Configurações**, selecione **Replicação de objeto**.
1. Clique no botão **Mais** ao lado do nome da política.
1. Selecione **Excluir Regras**.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para remover uma política de replicação, exclua a política da conta de origem e da conta de destino. A exclusão da política também exclui todas as regras associadas a ela.

```powershell
# Remove the policy from the destination account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $destAccountName `
    -PolicyId $destPolicy.PolicyId

# Remove the policy from the source account.
Remove-AzStorageObjectReplicationPolicy -ResourceGroupName $rgname `
    -StorageAccountName $srcAccountName `
    -PolicyId $destPolicy.PolicyId
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Para remover uma política de replicação, exclua a política da conta de origem e da conta de destino. A exclusão da política também exclui todas as regras associadas a ela.

```azurecli
az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <source-storage-account> \
    --resource-group <resource-group>

az storage account or-policy delete \
    --policy-id $policyid \
    --account-name <dest-storage-account> \
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Próximas etapas

- [Visão geral da replicação de objeto (versão prévia)](object-replication-overview.md)
