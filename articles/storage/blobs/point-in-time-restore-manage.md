---
title: Habilitar e gerenciar a restauração pontual para blobs de blocos (versão prévia)
titleSuffix: Azure Storage
description: Saiba como usar a restauração pontual (versão prévia) para restaurar blobs de blocos para um estado anterior no tempo.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 6948d4d786e918e5f3e32e6bdf2f7e23940f6815
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445433"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Habilitar e gerenciar a restauração pontual para blobs de blocos (versão prévia)

Você pode usar a restauração pontual (versão prévia) para restaurar blobs de blocos para um estado anterior no tempo. Este artigo descreve como habilitar a restauração pontual para uma conta de armazenamento com o PowerShell. Ele também mostra como executar uma operação de restauração com o PowerShell.

Para obter mais informações e saber como se registrar para a versão prévia, consulte [Restauração pontual para blobs de blocos (versão prévia)](point-in-time-restore-overview.md).

> [!CAUTION]
> A restauração pontual dá suporte a operações de restauração somente em blobs de blocos. Não é possível restaurar operações em contêineres. Se você excluir um contêiner da conta de armazenamento chamando a operação [Excluir contêiner](/rest/api/storageservices/delete-container) durante a versão prévia de restauração pontual, esse contêiner não poderá ser restaurado com uma operação de restauração. Durante a versão prévia, em vez de excluir um contêiner, exclua blobs individuais se você quiser restaurá-los.

> [!IMPORTANT]
> A versão prévia de restauração pontual é destinada apenas para uso fora de produção. SLAs (Contratos de Nível de Serviço) não estão disponíveis atualmente.

## <a name="install-the-preview-module"></a>Instalar o módulo de versão prévia

Para configurar a restauração pontual do Azure com o PowerShell, primeiro instale o módulo de visualização AZ. Storage versão 1.14.1-Preview ou posterior. É recomendável usar a versão de visualização mais recente, mas há suporte para a restauração pontual na versão 1.14.1-Preview e posterior. Remova todas as outras versões do módulo AZ. Storage.

O comando a seguir instala o módulo AZ. Storage [2.0.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.0.1-preview) :

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.0.1-preview -AllowPrerelease
```

O comando acima requer que a versão 2.2.4.1 ou posterior do PowerShellGet seja instalada. Para determinar qual versão você carregou atualmente:
```powershell
Get-Module PowerShellGet
```
Para obter mais informações sobre como instalar o Azure PowerShell, consulte [Instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

## <a name="enable-and-configure-point-in-time-restore"></a>Habilitar e configurar a restauração pontual

Antes de habilitar e configurar a restauração pontual, habilite seus pré-requisitos para a conta de armazenamento: exclusão reversível, feed de alteração e controle de versão de BLOB. Para obter mais informações sobre esses recursos, confira estes artigos:

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

Para iniciar uma operação de restauração, chame o comando **Restore-AzStorageBlobRange** , especificando o ponto de restauração como um valor **DateTime** UTC. Você pode especificar intervalos de lexicográfica de BLOBs a serem restaurados ou omitir um intervalo para restaurar todos os BLOBs em todos os contêineres na conta de armazenamento. Há suporte para até 10 intervalos lexicográfica por operação de restauração. Blobs de páginas e blobs de acréscimo não são incluídos na restauração. A operação de restauração pode levar vários minutos para ser concluída.

Tenha em mente as seguintes regras ao especificar um intervalo de blobs a serem restaurados:

- O padrão de contêiner especificado para o intervalo inicial e o intervalo final deve incluir um mínimo de três caracteres. A barra (/) que é usada para separar um nome de contêiner de um nome de blob não conta para esse mínimo.
- Até 10 intervalos podem ser especificados por operação de restauração.
- Não há suporte para caracteres curinga. Eles são tratados como caracteres padrão.
- Você pode restaurar blobs nos contêineres `$root` e `$web` especificando-os explicitamente em um intervalo passado para uma operação de restauração. Os contêineres `$root` e `$web` são restaurados apenas se forem especificados explicitamente. Outros contêineres do sistema não podem ser restaurados.

> [!IMPORTANT]
> Ao executar uma operação de restauração, o armazenamento do Azure bloqueia operações de dados nos BLOBs nos intervalos que estão sendo restaurados durante a operação. As operações de leitura, gravação e exclusão são bloqueadas no local principal. Por esse motivo, as operações como os contêineres de listagem no portal do Azure podem não ser executadas conforme o esperado enquanto a operação de restauração está em andamento.
>
> As operações de leitura do local secundário podem continuar durante a operação de restauração se a conta de armazenamento for replicada geograficamente.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os contêineres na conta

Para restaurar todos os contêineres e blobs na conta de armazenamento, chame o comando **Restore-AzStorageBlobRange** , omitindo o `-BlobRestoreRange` parâmetro. O exemplo a seguir restaura os contêineres na conta de armazenamento para seu estado 12 horas antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

### <a name="restore-a-single-range-of-block-blobs"></a>Restaurar um único intervalo de blobs de blocos

Para restaurar um intervalo de BLOBs, chame o comando **Restore-AzStorageBlobRange** e especifique um intervalo lexicográfica de nomes de contêiner e BLOB para o `-BlobRestoreRange` parâmetro. O início do intervalo é inclusivo e o final do intervalo é exclusivo.

Por exemplo, para restaurar os blobs em um único contêiner chamado *exemplo-contêiner*, você pode especificar um intervalo que comece com *exemplo-contêiner* e termine com *exemplo-contêiner1*. Não há nenhum requisito para os contêineres nomeados nos intervalos de início e de término existirem. Como o final do intervalo é exclusivo, mesmo que a conta de armazenamento inclua um contêiner chamado *exemplo-contêiner1*, somente o contêiner chamado *exemplo-contêiner* será restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container -EndRange sample-container1
```

Para especificar um subconjunto de blobs em um contêiner a ser restaurado, use uma barra (/) para separar o nome do contêiner do padrão de blob. Por exemplo, o intervalo a seguir seleciona blobs em um único contêiner cujos nomes começam com as letras *d* até *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d -EndRange sample-container/g
```

Em seguida, forneça o intervalo para o comando **Restore-AzStorageBlobRange** . Especifique o ponto de restauração fornecendo um valor UTC **DateTime** para o parâmetro `-TimeToRestore`. O exemplo a seguir restaura os blobs no intervalo especificado para seu estado 3 dias antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

### <a name="restore-multiple-ranges-of-block-blobs"></a>Restaurar vários intervalos de blobs de blocos

Para restaurar vários intervalos de blobs de blocos, especifique uma matriz de intervalos para o parâmetro `-BlobRestoreRange`. Há suporte para até 10 intervalos por operação de restauração. O exemplo a seguir especifica dois intervalos para restaurar o conteúdo completo de *Container1* e *Container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

### <a name="restore-block-blobs-asynchronously"></a>Restaurar blobs de blocos de forma assíncrona

Para executar uma operação de restauração de forma assíncrona, adicione o `-AsJob` parâmetro à chamada para **Restore-AzStorageBlobRange** e armazene o resultado da chamada em uma variável. O comando **Restore-AzStorageBlobRange** retorna um objeto do tipo **AzureLongRunningJob**. Você pode verificar a propriedade **State** desse objeto para determinar se a operação de restauração foi concluída. O valor da propriedade de **estado** pode estar **em execução** ou **concluído**.

O exemplo a seguir mostra como chamar uma operação de restauração de forma assíncrona:

```powershell
$job = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-5) `
    -AsJob

# Check the state of the job.
$job.State
```

Para aguardar a conclusão da operação de restauração depois que ela estiver em execução, chame o comando [Wait-Job](/powershell/module/microsoft.powershell.core/wait-job) , conforme mostrado no exemplo a seguir:

```powershell
$job | Wait-Job
```

## <a name="known-issues"></a>Problemas conhecidos
- Para um subconjunto de restaurações em que os blobs de acréscimo estão presentes, a restauração falhará. Por enquanto, não execute restaurações se os blobs de acréscimo estiverem presentes na conta.

## <a name="next-steps"></a>Próximas etapas

- [Restauração pontual para blobs de blocos (versão prévia)](point-in-time-restore-overview.md)
- [Exclusão reversível](soft-delete-overview.md)
- [Feed de alterações (versão prévia)](storage-blob-change-feed.md)
- [Controle de versão de blob (versão prévia)](versioning-overview.md)
