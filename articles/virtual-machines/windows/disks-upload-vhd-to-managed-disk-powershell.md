---
title: Faça upload de um vhd para o Azure usando o Azure PowerShell
description: Aprenda a carregar um vhd em um disco gerenciado pelo Azure e copie um disco gerenciado entre regiões, usando o Azure PowerShell, via upload direto.
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines-linux
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.openlocfilehash: 883fea1e25ded26c35e96d11edd8f417e96db30e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369549"
---
# <a name="upload-a-vhd-to-azure-using-azure-powershell"></a>Faça upload de um vhd para o Azure usando o Azure PowerShell

Este artigo explica como carregar um vhd de sua máquina local para um disco gerenciado do Azure. Anteriormente, você tinha que seguir um processo mais envolvido que incluía o estágio de seus dados em uma conta de armazenamento e o gerenciamento dessa conta de armazenamento. Agora, você não precisa mais gerenciar uma conta de armazenamento ou encenar dados nela para carregar um vhd. Em vez disso, você cria um disco gerenciado vazio e carrega um vhd diretamente para ele. Isso simplifica o upload de VMs no local para o Azure e permite que você carregue um vhd até 32 TiB diretamente em um grande disco gerenciado.

Se você estiver fornecendo uma solução de backup para VMs IaaS no Azure, recomendamos que você use upload direto para restaurar backups de clientes em discos gerenciados. Se você estiver carregando um VHD de uma máquina externa para o Azure, as velocidades dependem da sua largura de banda local. Se você estiver usando uma VM Azure, então sua largura de banda será a mesma dos HDDs padrão.

Atualmente, o upload direto é suportado para HDD padrão, SSD padrão e discos gerenciados premium SSD. Ainda não é suportado para ultra SSDs.

## <a name="prerequisites"></a>Pré-requisitos

- Baixe a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale o módulo Azure PowerShell](/powershell/azure/install-Az-ps).
- Se você pretende carregar um VHD no local: um VHD de tamanho fixo que [foi preparado para o Azure](prepare-for-upload-vhd-image.md), armazenado localmente.
- Ou, um disco gerenciado no Azure, se você pretende executar uma ação de cópia.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerenciado vazio

Para carregar seu vhd no Azure, você precisará criar um disco gerenciado vazio configurado para este processo de upload. Antes de criar um, há algumas informações adicionais que você deve saber sobre esses discos.

Este tipo de disco gerenciado tem dois estados únicos:

- ReadToUpload, o que significa que o disco está pronto para receber um upload, mas, nenhuma [assinatura de acesso seguro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) foi gerada.
- ActiveUpload, o que significa que o disco está pronto para receber um upload e o SAS foi gerado.

Enquanto em qualquer um desses estados, o disco gerenciado será cobrado a [preços de HDD padrão,](https://azure.microsoft.com/pricing/details/managed-disks/)independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isso será verdade `revoke-access` até que seja chamado no disco gerenciado, que é necessário para anexar o disco a uma VM.

Antes de criar um HDD padrão vazio para upload, você precisará do tamanho do arquivo em bytes do vhd que deseja carregar. O código de exemplo vai conseguir isso para você, `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`mas, para fazê-lo você mesmo pode usar: . Esse valor é usado ao especificar o parâmetro **-UploadSizeInBytes.**

Agora, em seu shell local, crie um HDD padrão vazio para upload especificando a configuração **Upload** no parâmetro **-CreateOption,** bem como o parâmetro **-UploadSizeInBytes** no cmdlet [New-AzDiskConfig.](https://docs.microsoft.com/powershell/module/az.compute/new-azdiskconfig?view=azps-1.8.0) Em seguida, ligue para [o New-AzDisk](https://docs.microsoft.com/powershell/module/az.compute/new-azdisk?view=azps-1.8.0) para criar o disco:

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location 'West US' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName' -Disk $diskconfig
```

Se você quiser carregar um SSD premium ou um SSD padrão, substitua **Standard_LRS** por **Premium_LRS** ou **StandardSSD_LRS**. O Ultra SSD ainda não tem suporte.

Agora você criou um disco gerenciado vazio configurado para o processo de upload. Para carregar um vhd para o disco, você precisará de um SAS gravável, para que você possa referencia-lo como o destino para o seu upload.

Para gerar um SAS gravável do seu disco gerenciado vazio, use o seguinte comando:

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName 'myResouceGroup' -DiskName 'myDiskName' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="upload-vhd"></a>Carregar vhd

Agora que você tem um SAS para o seu disco gerenciado vazio, você pode usá-lo para definir seu disco gerenciado como o destino para o seu comando de upload.

Use o AzCopy v10 para carregar seu arquivo VHD local em um disco gerenciado especificando o URI SAS que você gerou.

Este upload tem o mesmo throughput do [HDD padrão](disks-types.md#standard-hdd)equivalente . Por exemplo, se você tiver um tamanho que equivale a S4, você terá um throughput de até 60 MiB/s. Mas, se você tem um tamanho que equivale a S70, você terá um throughput de até 500 MiB/s.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

Depois que o upload estiver concluído, e você não precisar mais gravar mais dados no disco, revogue o SAS. A revogação do SAS mudará o estado do disco gerenciado e permitirá que você conecte o disco a uma VM.

```powershell
Revoke-AzDiskAccess -ResourceGroupName 'myResourceGroup' -DiskName 'myDiskName'
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerenciado

O upload direto também simplifica o processo de cópia de um disco gerenciado. Você pode copiar dentro da mesma região ou região transversal (para outra região).

O script a seguir fará isso para você, o processo é semelhante às etapas descritas anteriormente, com algumas diferenças já que você está trabalhando com um disco existente.

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

Agora que você fez o upload com sucesso de um vhd em um disco gerenciado, você pode anexar seu disco a uma VM e começar a usá-lo.

Para saber como anexar um disco de dados a uma VM, consulte nosso artigo sobre o assunto: [Conecte um disco de dados a uma VM do Windows com o PowerShell](attach-disk-ps.md). Para usar o disco como disco do SISTEMA OPERACIONAL, consulte [Criar uma VM do Windows a partir de um disco especializado](create-vm-specialized.md#create-the-new-vm).
