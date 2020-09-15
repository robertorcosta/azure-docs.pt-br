---
title: Habilitar e gerenciar a restauração pontual para blobs de blocos (versão prévia)
titleSuffix: Azure Storage
description: Saiba como usar a restauração pontual (versão prévia) para restaurar um conjunto de blobs de bloco para um estado anterior.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/11/2020
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 140e1203a29dcebec9d6483e73e906591b2213fb
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2020
ms.locfileid: "90068462"
---
# <a name="enable-and-manage-point-in-time-restore-for-block-blobs-preview"></a>Habilitar e gerenciar a restauração pontual para blobs de blocos (versão prévia)

Você pode usar a restauração pontual (visualização) para restaurar um conjunto de blobs de bloco para um estado anterior. Este artigo descreve como habilitar a restauração pontual para uma conta de armazenamento com o PowerShell. Ele também mostra como executar uma operação de restauração com o PowerShell.

Para obter mais informações e saber como se registrar para a versão prévia, consulte [Restauração pontual para blobs de blocos (versão prévia)](point-in-time-restore-overview.md).

> [!CAUTION]
> A restauração pontual dá suporte a operações de restauração somente em blobs de blocos. Não é possível restaurar operações em contêineres. Se você excluir um contêiner da conta de armazenamento chamando a operação [Excluir contêiner](/rest/api/storageservices/delete-container) durante a versão prévia de restauração pontual, esse contêiner não poderá ser restaurado com uma operação de restauração. Durante a versão prévia, em vez de excluir um contêiner, exclua blobs individuais se você quiser restaurá-los.

> [!IMPORTANT]
> A versão prévia de restauração pontual é destinada apenas para uso fora de produção.

## <a name="enable-and-configure-point-in-time-restore"></a>Habilitar e configurar a restauração pontual

Antes de habilitar e configurar a restauração pontual, habilite seus pré-requisitos para a conta de armazenamento: exclusão reversível, feed de alteração e controle de versão de BLOB. Para obter mais informações sobre esses recursos, confira estes artigos:

- [Habilitar exclusão reversível para blobs](soft-delete-enable.md)
- [Habilitar e desabilitar o feed de alterações](storage-blob-change-feed.md#enable-and-disable-the-change-feed)
- [Habilitar e gerenciar o controle de versão de blob](versioning-enable.md)

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para configurar a restauração pontual com o portal do Azure, siga estas etapas:

1. Navegue até sua conta de armazenamento no portal do Azure.
1. Em **configurações**, escolha **proteção de dados**.
1. Selecione **ativar a restauração pontual** . Quando você seleciona essa opção, a exclusão reversível para BLOBs, controle de versão e feed de alteração também são habilitadas.
1. Defina o ponto de restauração máximo para a restauração pontual, em dias. Esse número deve ser pelo menos um dia menor que o período de retenção especificado para exclusão reversível do blob.
1. Salve as alterações.

A imagem a seguir mostra uma conta de armazenamento configurada para a restauração pontual com um ponto de restauração de sete dias atrás e um período de retenção para exclusão reversível de blob de 14 dias.

:::image type="content" source="media/point-in-time-restore-manage/configure-point-in-time-restore-portal.png" alt-text="Captura de tela mostrando como configurar a restauração pontual no portal do Azure":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para configurar a restauração pontual com o PowerShell, primeiro instale o módulo de visualização AZ. Storage versão 1.14.1-Preview ou uma versão posterior do módulo de visualização. Remova todas as outras versões do módulo AZ. Storage.

Verifique se você instalou a versão 2.2.4.1 ou posterior do PowerShellGet. Para determinar qual versão está instalada no momento, execute o seguinte comando:

```powershell
Get-InstalledModule PowerShellGet
```

Em seguida, instale o módulo de visualização AZ. Storage. O comando a seguir instala a versão [2.5.2-Preview](https://www.powershellgallery.com/packages/Az.Storage/2.5.2-preview) do módulo AZ. Storage:

```powershell
Install-Module -Name Az.Storage -RequiredVersion 2.5.2-preview -AllowPrerelease
```

Para obter mais informações sobre como instalar Azure PowerShell, consulte [instalar o powershellget](/powershell/scripting/gallery/installing-psget) e [instalar o Azure PowerShell com o PowerShellGet](/powershell/azure/install-az-ps).

Para configurar a restauração pontual do Azure com o PowerShell, chame o comando Enable-AzStorageBlobRestorePolicy. O exemplo a seguir habilita a exclusão reversível e define o período de retenção de exclusão reversível, habilita o feed de alterações e, em seguida, habilita a restauração pontual. Antes de executar o exemplo, use o portal do Azure ou um modelo de Azure Resource Manager para habilitar também o controle de versão de blob.

Lembre-se de substituir os valores entre colchetes angulares pelos seus próprios valores quando executar o exemplo:

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

# Enable change feed.
Update-AzStorageBlobServiceProperty -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -EnableChangeFeed $true

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

Somente os blobs de blocos são restaurados. Blobs de páginas e blobs de acréscimo não são incluídos em uma operação de restauração. Para obter mais informações sobre as limitações relacionadas a blobs de acréscimo, consulte [problemas conhecidos](#known-issues).

> [!IMPORTANT]
> Ao executar uma operação de restauração, o armazenamento do Azure bloqueia operações de dados nos BLOBs nos intervalos que estão sendo restaurados durante a operação. As operações de leitura, gravação e exclusão são bloqueadas no local principal. Por esse motivo, as operações como os contêineres de listagem no portal do Azure podem não ser executadas conforme o esperado enquanto a operação de restauração está em andamento.
>
> As operações de leitura do local secundário podem continuar durante a operação de restauração se a conta de armazenamento for replicada geograficamente.

### <a name="restore-all-containers-in-the-account"></a>Restaurar todos os contêineres na conta

Você pode restaurar todos os contêineres na conta de armazenamento para retorná-los ao estado anterior em um determinado momento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para restaurar todos os contêineres e blobs na conta de armazenamento com o portal do Azure, siga estas etapas:

1. Navegue até a lista de contêineres para sua conta de armazenamento.
1. Na barra de ferramentas, escolha **restaurar contêineres**e **restaurar tudo**.
1. No painel **restaurar todos os contêineres** , especifique o ponto de restauração fornecendo uma data e hora.
1. Confirme que você deseja continuar marcando a caixa.
1. Selecione **restaurar** para iniciar a operação de restauração.

    :::image type="content" source="media/point-in-time-restore-manage/restore-all-containers-portal.png" alt-text="Captura de tela mostrando como restaurar todos os contêineres para um ponto de restauração especificado":::

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar todos os contêineres e blobs na conta de armazenamento com o PowerShell, chame o comando **Restore-AzStorageBlobRange** , omitindo o `-BlobRestoreRange` parâmetro. O exemplo a seguir restaura os contêineres na conta de armazenamento para seu estado 12 horas antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddHours(-12)
```

---

### <a name="restore-ranges-of-block-blobs"></a>Restaurar intervalos de blobs de blocos

Você pode restaurar um ou mais intervalos de lexicográfica de BLOBs dentro de um único contêiner ou em vários contêineres para retornar esses BLOBs para seu estado anterior em um determinado momento.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Para restaurar um intervalo de BLOBs em um ou mais contêineres com o portal do Azure, siga estas etapas:

1. Navegue até a lista de contêineres para sua conta de armazenamento.
1. Selecione o contêiner ou contêineres a serem restaurados.
1. Na barra de ferramentas, escolha **restaurar contêineres**e **restaurar selecionado**.
1. No painel **restaurar contêineres selecionados** , especifique o ponto de restauração fornecendo uma data e hora.
1. Especifique os intervalos a serem restaurados. Use uma barra (/) para delinear o nome do contêiner do prefixo do blob.
1. Por padrão, o painel **restaurar contêineres selecionados** especifica um intervalo que inclui todos os BLOBs no contêiner. Exclua esse intervalo se você não quiser restaurar todo o contêiner. O intervalo padrão é mostrado na imagem a seguir.

    :::image type="content" source="media/point-in-time-restore-manage/delete-default-blob-range.png" alt-text="Captura de tela mostrando o intervalo de BLOBs padrão a ser excluído antes de especificar o intervalo personalizado":::

1. Confirme que você deseja continuar marcando a caixa.
1. Selecione **restaurar** para iniciar a operação de restauração.

A imagem a seguir mostra uma operação de restauração em um conjunto de intervalos.

:::image type="content" source="media/point-in-time-restore-manage/restore-multiple-container-ranges-portal.png" alt-text="Captura de tela mostrando como restaurar intervalos de BLOBs em um ou mais contêineres":::

A operação de restauração mostrada na imagem executa as seguintes ações:

- Restaura o conteúdo completo de *Container1*.
- Restaura os BLOBs no intervalo de lexicográfica *blob1* por meio de *blob5* no *container2*. Esse intervalo restaura os BLOBs com nomes como *blob1*, *blob11*, *blob100*, *blob2*e assim por diante. Como o final do intervalo é exclusivo, ele restaura os BLOBs cujos nomes começam com *blob4*, mas não restaura os BLOBs cujos nomes começam com *blob5*.
- Restaura todos os BLOBs em *container3* e *Container4*. Como o final do intervalo é exclusivo, esse intervalo não restaura *container5*.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Para restaurar um único intervalo de BLOBs, chame o comando **Restore-AzStorageBlobRange** e especifique um intervalo lexicográfica de nomes de contêiner e BLOB para o `-BlobRestoreRange` parâmetro. Por exemplo, para restaurar os blobs em um único contêiner chamado *exemplo-contêiner*, você pode especificar um intervalo que comece com *exemplo-contêiner* e termine com *exemplo-contêiner1*. Não há nenhum requisito para os contêineres nomeados nos intervalos de início e de término existirem. Como o final do intervalo é exclusivo, mesmo que a conta de armazenamento inclua um contêiner chamado *exemplo-contêiner1*, somente o contêiner chamado *exemplo-contêiner* será restaurado:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container `
    -EndRange sample-container1
```

Para especificar um subconjunto de BLOBs em um contêiner a ser restaurado, use uma barra (/) para separar o nome do contêiner do padrão de prefixo de BLOB. Por exemplo, o intervalo a seguir seleciona blobs em um único contêiner cujos nomes começam com as letras *d* até *f*:

```powershell
$range = New-AzStorageBlobRangeToRestore -StartRange sample-container/d `
    -EndRange sample-container/g
```

Em seguida, forneça o intervalo para o comando **Restore-AzStorageBlobRange** . Especifique o ponto de restauração fornecendo um valor UTC **DateTime** para o parâmetro `-TimeToRestore`. O exemplo a seguir restaura os blobs no intervalo especificado para seu estado 3 dias antes de agora:

```powershell
# Specify -TimeToRestore as a UTC value
Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -BlobRestoreRange $range `
    -TimeToRestore (Get-Date).AddDays(-3)
```

Para restaurar vários intervalos de blobs de blocos, especifique uma matriz de intervalos para o parâmetro `-BlobRestoreRange`. O exemplo a seguir especifica dois intervalos para restaurar o conteúdo completo de *Container1* e *Container4*:

```powershell
# Specify a range that includes the complete contents of container1.
$range1 = New-AzStorageBlobRangeToRestore -StartRange container1 `
    -EndRange container2
# Specify a range that includes the complete contents of container4.
$range2 = New-AzStorageBlobRangeToRestore -StartRange container4 `
    -EndRange container5

Restore-AzStorageBlobRange -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -TimeToRestore (Get-Date).AddMinutes(-30) `
    -BlobRestoreRange @($range1, $range2)
```

---

### <a name="restore-block-blobs-asynchronously-with-powershell"></a>Restaurar blobs de blocos de forma assíncrona com o PowerShell

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

Para um subconjunto de operações de restauração em que os blobs de acréscimo estão presentes, a operação de restauração falhará. A Microsoft recomenda que você não execute uma restauração pontual durante a visualização se os blobs de acréscimo estiverem presentes na conta.

## <a name="next-steps"></a>Próximas etapas

- [Restauração pontual para blobs de blocos (versão prévia)](point-in-time-restore-overview.md)
- [Exclusão reversível](soft-delete-overview.md)
- [Feed de alterações (versão prévia)](storage-blob-change-feed.md)
- [Controle de versão de BLOB](versioning-overview.md)
