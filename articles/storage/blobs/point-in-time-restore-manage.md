---
title: Executar uma restauração pontual em dados de blob de blocos
titleSuffix: Azure Storage
description: Saiba como usar a restauração pontual para restaurar um conjunto de blobs de blocos para seu estado anterior em um determinado momento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 828b5c34aaccf2a53aa197f921a8ef02d46821ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91280463"
---
# <a name="perform-a-point-in-time-restore-on-block-blob-data"></a>Executar uma restauração pontual em dados de blob de blocos

Você pode usar a restauração pontual para restaurar um ou mais conjuntos de blobs de blocos para um estado anterior. Este artigo descreve como habilitar a restauração pontual para uma conta de armazenamento e como executar uma operação de restauração.

Para saber mais sobre a restauração pontual, consulte [restauração pontual para BLOBs de blocos](point-in-time-restore-overview.md).

> [!CAUTION]
> A restauração pontual dá suporte a operações de restauração somente em blobs de blocos. Não é possível restaurar operações em contêineres. Se você excluir um contêiner da conta de armazenamento chamando a operação [excluir contêiner](/rest/api/storageservices/delete-container) , esse contêiner não poderá ser restaurado com uma operação de restauração. Em vez de excluir um contêiner, exclua BLOBs individuais se você quiser restaurá-los.

## <a name="enable-and-configure-point-in-time-restore"></a>Habilitar e configurar a restauração pontual

Antes de habilitar e configurar a restauração pontual, habilite seus pré-requisitos para a conta de armazenamento: exclusão reversível, feed de alteração e controle de versão de BLOB. Para obter mais informações sobre esses recursos, confira estes artigos:

- [Habilitar exclusão reversível para blobs](soft-delete-enable.md)
- [Habilitar e desabilitar o feed de alterações](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)

> [!IMPORTANT]
> Habilitar a exclusão reversível, o feed de alterações e o controle de versão de blob pode resultar em encargos adicionais. Para obter mais informações, consulte [exclusão reversível para BLOBs](soft-delete-blob-overview.md), [suporte ao feed de alterações no armazenamento de BLOBs do Azure](storage-blob-change-feed.md)e [controle de versão de blob](versioning-overview.md).

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para configurar a restauração pontual com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Em **configurações**, escolha **proteção de dados**.
1. Selecione **ativar a restauração pontual** . Quando você seleciona essa opção, a exclusão reversível para BLOBs, controle de versão e feed de alteração também são habilitadas.
1. Defina o ponto de restauração máximo para a restauração pontual, em dias. Esse número deve ser pelo menos um dia menor que o período de retenção especificado para exclusão reversível do blob.
1. Salve suas alterações.

A imagem a seguir mostra uma conta de armazenamento configurada para a restauração pontual com um ponto de restauração de sete dias atrás e um período de retenção para exclusão reversível de blob de 14 dias.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Captura de tela mostrando como configurar a restauração pontual no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar a restauração pontual com o PowerShell, primeiro instale o módulo [AZ. Storage](https://www.powershellgallery.com/packages/Az.Storage) versão 2.6.0 ou posterior. Em seguida, chame o comando Enable-AzStorageBlobRestorePolicy para habilitar a restauração pontual para a conta de armazenamento.

O exemplo a seguir habilita a exclusão reversível e define o período de retenção de exclusão reversível, habilita o feed de alterações e o controle de versão e, em seguida, habilita a restauração pontual.    Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores quando executar o exemplo:

```powershell
# Sign in to your Azure account.
Connect-AzAccount

# Set resource group and account variables.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Enable soft delete with a retention of 14 days.
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RetentionDays 14

# Enable change feed and versioning.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true `
    -IsVersioningEnabled $true

# Enable point-in-time restore with a retention period of 7 days.
# The retention period for point-in-time restore must be at least
# one day less than that set for soft delete.
Enable-AzStorageBlobRestorePolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -RestoreDays 7

# View the service settings.
Get-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName
```

---

## <a name="perform-a-restore-operation"></a>Realizar uma operação de restauração

Ao executar uma operação de restauração, você deve especificar o ponto de restauração como um valor **DateTime** UTC. Contêineres e blobs serão restaurados para seu estado no dia e hora. A operação de restauração pode levar vários minutos para ser concluída.

Você pode restaurar todos os contêineres na conta de armazenamento ou pode restaurar um intervalo de BLOBs em um ou mais contêineres. Um intervalo de BLOBs é definido modo lexicográfico, ou seja, na ordem de dicionário. Há suporte para até dez intervalos de lexicográfica por operação de restauração. O início do intervalo é inclusivo e o final do intervalo é exclusivo.

O padrão de contêiner especificado para o intervalo inicial e o intervalo final deve incluir um mínimo de três caracteres. A barra (/) que é usada para separar um nome de contêiner de um nome de blob não conta para esse mínimo.

Não há suporte para caracteres curinga em um intervalo de lexicográfica. Quaisquer caracteres curinga são tratados como caracteres padrão.

Você pode restaurar blobs nos contêineres `$root` e `$web` especificando-os explicitamente em um intervalo passado para uma operação de restauração. Os contêineres `$root` e `$web` são restaurados apenas se forem especificados explicitamente. Outros contêineres do sistema não podem ser restaurados.

Somente os blobs de blocos são restaurados. Blobs de páginas e blobs de acréscimo não são incluídos em uma operação de restauração. Para obter mais informações sobre as limitações relacionadas a blobs de acréscimo, consulte [restauração pontual para BLOBs de blocos](point-in-time-restore-overview.md).

> [!IMPORTANT]
> Ao executar uma operação de restauração, o armazenamento do Azure bloqueia operações de dados nos BLOBs nos intervalos que estão sendo restaurados durante a operação. As operações de leitura, gravação e exclusão são bloqueadas no local principal. Por esse motivo, as operações como os contêineres de listagem no portal do Azure podem não ser executadas conforme o esperado enquanto a operação de restauração está em andamento.
>
> As operações de leitura do local secundário podem continuar durante a operação de restauração se a conta de armazenamento for replicada geograficamente.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os contêineres na conta

Você pode restaurar todos os contêineres na conta de armazenamento para retorná-los ao estado anterior em um determinado momento.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para restaurar todos os contêineres e blobs na conta de armazenamento com o portal do Azure, siga estas etapas:

1. Navegue até a lista de contêineres para sua conta de armazenamento.
1. Na barra de ferramentas, escolha **restaurar contêineres**e **restaurar tudo**.
1. No painel **restaurar todos os contêineres** , especifique o ponto de restauração fornecendo uma data e hora.
1. Confirme que você deseja continuar marcando a caixa.
1. Selecione **restaurar** para iniciar a operação de restauração.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Captura de tela mostrando como configurar a restauração pontual no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar todos os contêineres e blobs na conta de armazenamento com o PowerShell, chame o comando **Restore-AzStorageBlobRange** . Por padrão, o comando **Restore-AzStorageBlobRange** é executado de forma assíncrona e retorna um objeto do tipo **PSBlobRestoreStatus** que você pode usar para verificar o status da operação de restauração.

O exemplo a seguir restaura de forma assíncrona os contêineres na conta de armazenamento para seu estado 12 horas antes do momento atual e verifica algumas das propriedades da operação de restauração:

```powershell
# Specify -TimeToRestore as a UTC value
$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the restore point in UTC time.
$restoreOperation.Parameters.TimeToRestore
```

Para executar a operação de restauração de forma síncrona, inclua o parâmetro **-WaitForComplete** no comando. Quando o parâmetro **-WaitForComplete** estiver presente, o PowerShell exibirá uma mensagem que inclui a ID de restauração da operação e, em seguida, bloqueará a execução até que a operação de restauração seja concluída. Tenha em mente que o período de tempo exigido por uma operação de restauração depende da quantidade de dados a serem restaurados e uma grande operação de restauração pode levar até uma hora para ser concluída.

```powershell
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12) -WaitForComplete
```

---

### <a name="restore-ranges-of-block-blobs"></a>Restaurar intervalos de blobs de blocos

Você pode restaurar um ou mais intervalos de lexicográfica de BLOBs dentro de um único contêiner ou em vários contêineres para retornar esses BLOBs para seu estado anterior em um determinado momento.

# <a name="azure-portal"></a>[Azure portal](#tab/portal)

Para restaurar um intervalo de BLOBs em um ou mais contêineres com o portal do Azure, siga estas etapas:

1. Navegue até a lista de contêineres para sua conta de armazenamento.
1. Selecione o contêiner ou contêineres a serem restaurados.
1. Na barra de ferramentas, escolha **restaurar contêineres**e **restaurar selecionado**.
1. No painel **restaurar contêineres selecionados** , especifique o ponto de restauração fornecendo uma data e hora.
1. Especifique os intervalos a serem restaurados. Use uma barra (/) para delinear o nome do contêiner do prefixo do blob.
1. Por padrão, o painel **restaurar contêineres selecionados** especifica um intervalo que inclui todos os BLOBs no contêiner. Exclua esse intervalo se você não quiser restaurar todo o contêiner. O intervalo padrão é mostrado na imagem a seguir.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Captura de tela mostrando como configurar a restauração pontual no portal do Azure":::

1. Confirme que você deseja continuar marcando a caixa.
1. Selecione **restaurar** para iniciar a operação de restauração.

A imagem a seguir mostra uma operação de restauração em um conjunto de intervalos.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Captura de tela mostrando como configurar a restauração pontual no portal do Azure":::

A operação de restauração mostrada na imagem executa as seguintes ações:

- Restaura o conteúdo completo de *Container1*.
- Restaura os BLOBs no intervalo de lexicográfica *blob1* por meio de *blob5* no *container2*. Esse intervalo restaura os BLOBs com nomes como *blob1*, *blob11*, *blob100*, *blob2*e assim por diante. Como o final do intervalo é exclusivo, ele restaura os BLOBs cujos nomes começam com *blob4*, mas não restaura os BLOBs cujos nomes começam com *blob5*.
- Restaura todos os BLOBs em *container3* e *Container4*. Como o final do intervalo é exclusivo, esse intervalo não restaura *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar um único intervalo de BLOBs, chame o comando **Restore-AzStorageBlobRange** e especifique um intervalo lexicográfica de nomes de contêiner e BLOB para o `-BlobRestoreRange` parâmetro. Por exemplo, para restaurar os BLOBs em um único contêiner chamado *Container1*, você pode especificar um intervalo que comece com *Container1* e termine com *container2*. Não há nenhum requisito para os contêineres nomeados nos intervalos de início e de término existirem. Como o final do intervalo é exclusivo, mesmo que a conta de armazenamento inclua um contêiner chamado *container2*, somente o contêiner chamado *Container1* será restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
```

Para especificar um subconjunto de BLOBs em um contêiner a ser restaurado, use uma barra (/) para separar o nome do contêiner do padrão de prefixo de BLOB. Por exemplo, o intervalo a seguir seleciona blobs em um único contêiner cujos nomes começam com as letras *d* até *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange container1/d `
    -EndRange container1/g
```

Em seguida, forneça o intervalo para o comando **Restore-AzStorageBlobRange** . Especifique o ponto de restauração fornecendo um valor UTC **DateTime** para o parâmetro `-TimeToRestore`. O exemplo a seguir restaura os blobs no intervalo especificado para seu estado 3 dias antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Por padrão, o comando **Restore-AzStorageBlobRange** é executado de forma assíncrona. Quando você inicia uma operação de restauração de forma assíncrona, o PowerShell exibe imediatamente uma tabela de propriedades para a operação:  

```powershell
Status     RestoreId                            FailureReason Parameters.TimeToRestore     Parameters.BlobRanges
------     ---------                            ------------- ------------------------     ---------------------
InProgress 459c2305-d14a-4394-b02c-48300b368c63               2020-09-15T23:23:07.1490859Z ["container1/d" -> "container1/g"]
```

Para restaurar vários intervalos de blobs de blocos, especifique uma matriz de intervalos para o parâmetro `-BlobRestoreRange`. O exemplo a seguir especifica dois intervalos para restaurar o conteúdo completo de *Container1* e *Container4* para seu estado 24 horas atrás e salva o resultado em uma variável:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

$restoreOperation = Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-24) `
    -BlobRestoreRange @($range1, $range2)

# Get the status of the restore operation.
$restoreOperation.Status
# Get the ID for the restore operation.
$restoreOperation.RestoreId
# Get the blob ranges specified for the operation.
$restoreOperation.Parameters.BlobRanges
```

Para executar a operação de restauração de forma síncrona e bloquear a execução até que ela seja concluída, inclua o parâmetro **-WaitForComplete** no comando.

---

## <a name="next-steps"></a>Próximas etapas

- [Restauração pontual para BLOBs de blocos](point-in-time-restore-overview.md)
- [Exclusão reversível](soft-delete-overview.md)
- [Feed de alterações](storage-blob-change-feed.md)
- [Controle de versão de BLOB](versioning-overview.md)
