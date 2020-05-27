---
title: Habilitar e gerenciar a restauração pontual para blobs de blocos (versão prévia)
titleSuffix: Azure Storage
description: Saiba como usar a restauração pontual (versão prévia) para restaurar blobs de blocos para um estado anterior no tempo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 05/06/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: cbfc5667fb35b8f807a3a806dda4647af10e9392
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83118202"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Habilitar e gerenciar a restauração pontual para blobs de blocos (versão prévia)

Você pode usar a restauração pontual (versão prévia) para restaurar blobs de blocos para um estado anterior no tempo. Este artigo descreve como habilitar a restauração pontual para uma conta de armazenamento com o PowerShell. Ele também mostra como executar uma operação de restauração com o PowerShell.

Para obter mais informações e saber como se registrar para a versão prévia, consulte [Restauração pontual para blobs de blocos (versão prévia)](point-in-time-restore-overview.md).

> [!CAUTION]
> A restauração pontual dá suporte a operações de restauração somente em blobs de blocos. Não é possível restaurar operações em contêineres. Se você excluir um contêiner da conta de armazenamento chamando a operação [Excluir contêiner](/rest/api/storageservices/delete-container) durante a versão prévia de restauração pontual, esse contêiner não poderá ser restaurado com uma operação de restauração. Durante a versão prévia, em vez de excluir um contêiner, exclua blobs individuais se você quiser restaurá-los.

> [!IMPORTANT]
> A versão prévia de restauração pontual é destinada apenas para uso fora de produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.

## <a name="install-the-preview-module"></a>Instalar o módulo de versão prévia

Para configurar a restauração pontual do Azure com o PowerShell, primeiro instale a versão [1.14.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.14.1-preview) do módulo Az.Storage do PowerShell. Para instalar o módulo de versão prévia, siga estas etapas:

1. Remova as instalações anteriores do Azure PowerShell do Windows usando a configuração **Aplicativos e recursos** em **Configurações**.

1. Verifique se tem a versão mais recente do PowerShellGet instalado. Abra uma janela do Windows PowerShell e execute o seguinte comando para instalar a versão mais recente:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Feche e reabra a janela do PowerShell depois de instalar o PowerShellGet.

1. Instale a versão mais recente do Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Instale o módulo Az.Storage de versão prévia:

    ```powershell
    Install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.14.1-preview -AllowPrerelease -AllowClobber -Force
    ```

Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Habilitar e configurar a restauração pontual

Antes de habilitar e configurar a restauração pontual, habilite seus pré-requisitos: exclusão reversível, feed de alterações e controle de versão de blob. Para obter mais informações sobre esses recursos, confira estes artigos:

- [Habilitar exclusão reversível para blobs](soft-delete-enable.md)
- [Habilitar e desabilitar o feed de alterações](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)

Para configurar a restauração pontual do Azure com o PowerShell, chame o comando Enable-AzStorageBlobRestorePolicy. O exemplo a seguir habilita a exclusão reversível e define o período de retenção de exclusão reversível, habilita o feed de alterações e, em seguida, habilita a restauração pontual. Antes de executar o exemplo, use o portal do Azure ou um modelo de Azure Resource Manager para habilitar também o controle de versão de blob.

Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores quando executar o exemplo:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 6 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 6

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

# Enable point-in-time restore with a retention period of 5 days.
# The retention period for point-in-time restore must be at least one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 5

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

## <a name="perform-a-restore-operation"></a>Realizar uma operação de restauração

Para iniciar uma operação de restauração, chame o comando Restore-AzStorageBlobRange, especificando o ponto de restauração como um valor UTC **DateTime**. Você pode especificar um ou mais intervalos lexicográficos de blobs a serem restaurados ou omitir um intervalo para restaurar todos os blobs em todos os contêineres na conta de armazenamento. A operação de restauração pode levar vários minutos para ser concluída.

Tenha em mente as seguintes regras ao especificar um intervalo de blobs a serem restaurados:

- O padrão de contêiner especificado para o intervalo inicial e o intervalo final deve incluir um mínimo de três caracteres. A barra (/) que é usada para separar um nome de contêiner de um nome de blob não conta para esse mínimo.
- Apenas um intervalo pode ser especificado por operação de restauração.
- Não há suporte para caracteres curinga. Eles são tratados como caracteres padrão.
- Você pode restaurar blobs nos contêineres `$root` e `$web` especificando-os explicitamente em um intervalo passado para uma operação de restauração. Os contêineres `$root` e `$web` são restaurados apenas se forem especificados explicitamente. Outros contêineres do sistema não podem ser restaurados.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os contêineres na conta

Para restaurar todos os contêineres e blobs na conta de armazenamento, chame o comando Restore-AzStorageBlobRange, omitindo o parâmetro `-BlobRestoreRange`. O exemplo a seguir restaura os contêineres na conta de armazenamento para seu estado 12 horas antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Restaurar um único intervalo de blobs de blocos

Para restaurar um intervalo de blobs, chame o comando Restore-AzStorageBlobRange e especifique um intervalo lexicográfico de nomes de contêiner e blob para o parâmetro `-BlobRestoreRange`. O início do intervalo é inclusivo e o final do intervalo é exclusivo.

Por exemplo, para restaurar os blobs em um único contêiner chamado *exemplo-contêiner*, você pode especificar um intervalo que comece com *exemplo-contêiner* e termine com *exemplo-contêiner1*. Não há nenhum requisito para os contêineres nomeados nos intervalos de início e de término existirem. Como o final do intervalo é exclusivo, mesmo que a conta de armazenamento inclua um contêiner chamado *exemplo-contêiner1*, somente o contêiner chamado *exemplo-contêiner* será restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Para especificar um subconjunto de blobs em um contêiner a ser restaurado, use uma barra (/) para separar o nome do contêiner do padrão de blob. Por exemplo, o intervalo a seguir seleciona blobs em um único contêiner cujos nomes começam com as letras *d* até *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Em seguida, forneça o intervalo para o comando Restore-AzStorageBlobRange. Especifique o ponto de restauração fornecendo um valor UTC **DateTime** para o parâmetro `-TimeToRestore`. O exemplo a seguir restaura os blobs no intervalo especificado para seu estado 3 dias antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Restaurar vários intervalos de blobs de blocos

Para restaurar vários intervalos de blobs de blocos, especifique uma matriz de intervalos para o parâmetro `-BlobRestoreRange`. O exemplo a seguir restaura o conteúdo completo de *contêiner1* e *contêiner4*:

```powershell
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

## <a name="next-steps"></a>Próximas etapas

- [Restauração pontual para blobs de blocos (versão prévia)](point-in-time-restore-overview.md)
- [Exclusão reversível](soft-delete-overview.md)
- [Feed de alterações (versão prévia)](storage-blob-change-feed.md)
- [Controle de versão de blob (versão prévia)](versioning-overview.md)
