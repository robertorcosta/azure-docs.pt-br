---
title: Carregue um VHD para o Azure ou copie um disco em todas as regiões - Azure PowerShell
description: Aprenda a carregar um VHD em um disco gerenciado pelo Azure e copie um disco gerenciado entre regiões, usando o Azure PowerShell, via upload direto.
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 7c66507989357569828d4ef933cfdca735f71570
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085404"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Carregue um VHD para o Azure ou copie um disco gerenciado para outra região - Azure PowerShell

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Baixe a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o módulo Azure PowerShell](/powershell/azure/install-Az-ps).
- Se você pretende carregar um VHD no local: um VHD de tamanho fixo que [foi preparado para o Azure](prepare-for-upload-vhd-image.md), armazenado localmente.
- Ou, um disco gerenciado no Azure, se você pretende executar uma ação de cópia.

## <a name="getting-started"></a>Introdução

Se você preferir carregar discos através de uma GUI, você pode fazê-lo usando o Azure Storage Explorer. Para obter detalhes, consulte: [Use o Azure Storage Explorer para gerenciar discos gerenciados pelo Azure](disks-use-storage-explorer-managed-disks.md)

Para carregar seu VHD para o Azure, você precisará criar um disco gerenciado vazio configurado para este processo de upload. Antes de criar um, há algumas informações adicionais que você deve saber sobre esses discos.

Este tipo de disco gerenciado tem dois estados únicos:

- ReadToUpload, o que significa que o disco está pronto para receber um upload, mas, nenhuma [assinatura de acesso seguro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) foi gerada.
- ActiveUpload, o que significa que o disco está pronto para receber um upload e o SAS foi gerado.

> [!NOTE]
> Enquanto em qualquer um desses estados, o disco gerenciado será cobrado a [preços de HDD padrão,](https://azure.microsoft.com/pricing/details/managed-disks/)independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isso será verdade `revoke-access` até que seja chamado no disco gerenciado, que é necessário para anexar o disco a uma VM.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerenciado vazio

Antes de criar um HDD padrão vazio para upload, você precisará do tamanho do arquivo do VHD que deseja carregar, em bytes. O código de exemplo vai conseguir isso para você, `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`mas, para fazê-lo você mesmo pode usar: . Esse valor é usado ao especificar o parâmetro **-UploadSizeInBytes.**

Agora, em seu shell local, crie um HDD padrão vazio para upload especificando a configuração **Upload** no parâmetro **-CreateOption,** bem como o parâmetro **-UploadSizeInBytes** no cmdlet [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Em [seguida, chame o New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) para criar o disco.

Substituir `<yourdiskname>` `<yourresourcegroupname>`e `<yourregion>` executar os seguintes comandos:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname' -DiskName '<yourdiskname>' -Disk $diskconfig
```

Se você quiser carregar um SSD premium ou um SSD padrão, substitua **Standard_LRS** por **Premium_LRS** ou **StandardSSD_LRS**. Os discos ultra ainda não são suportados.

Agora que você criou um disco gerenciado vazio configurado para o processo de upload, você pode carregar um VHD para ele. Para carregar um VHD para o disco, você precisará de um SAS gravável, para que você possa referencia-lo como o destino para o seu upload.

Para gerar um SAS gravável do disco `<yourdiskname>`gerenciado `<yourresourcegroupname>`vazio, substitua e, em seguida, use os seguintes comandos:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>Carregar um VHD

Agora que você tem um SAS para o seu disco gerenciado vazio, você pode usá-lo para definir seu disco gerenciado como o destino para o seu comando de upload.

Use o AzCopy v10 para carregar seu arquivo VHD local em um disco gerenciado especificando o URI SAS que você gerou.

Este upload tem o mesmo throughput do [HDD padrão](disks-types.md#standard-hdd)equivalente . Por exemplo, se você tiver um tamanho que equivale a S4, você terá um throughput de até 60 MiB/s. Mas, se você tem um tamanho que equivale a S70, você terá um throughput de até 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Depois que o upload estiver concluído, e você não precisar mais gravar mais dados no disco, revogue o SAS. A revogação do SAS mudará o estado do disco gerenciado e permitirá que você conecte o disco a uma VM.

`<yourdiskname>`Substitua `<yourresourcegroupname>`e execute o seguinte comando:

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerenciado

O upload direto também simplifica o processo de cópia de um disco gerenciado. Você pode copiar dentro da mesma região ou copiar seu disco gerenciado para outra região.

O script a seguir fará isso por você, o processo é semelhante às etapas descritas anteriormente, com algumas diferenças, já que você está trabalhando com um disco existente.

> [!IMPORTANT]
> Você precisa adicionar um deslocamento de 512 quando estiver fornecendo o tamanho do disco em bytes de um disco gerenciado do Azure. Isso porque o Azure omite o rodapé ao retornar o tamanho do disco. A cópia falhará se você não fizer isso. O seguinte script já faz isso por você.

Substitua `<sourceResourceGroupHere>` `<sourceDiskNameHere>`o `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourOSTypeHere>` `<yourTargetLocationHere>` , , , e (um exemplo de um valor de localização seria uswest2) por seus valores, em seguida, execute o script a seguir para copiar um disco gerenciado.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>Próximas etapas

Agora que você fez o upload com sucesso de um VHD em um disco gerenciado, você pode anexar seu disco a uma VM e começar a usá-lo.

Para saber como anexar um disco de dados a uma VM, consulte nosso artigo sobre o assunto: [Conecte um disco de dados a uma VM do Windows com o PowerShell](attach-disk-ps.md). Para usar o disco como disco do SISTEMA OPERACIONAL, consulte [Criar uma VM do Windows a partir de um disco especializado](create-vm-specialized.md#create-the-new-vm).
