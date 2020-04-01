---
title: Carregue um VHD para o Azure ou copie um disco em todas as regiões - Azure CLI
description: Aprenda a carregar um VHD em um disco gerenciado pelo Azure e copie um disco gerenciado entre regiões, usando o Cli do Azure, via upload direto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/27/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 6813206aebe67e4e6d2afd0d9c78d03f0c20c952
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420966"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Carregue um VHD para o Azure ou copie um disco gerenciado para outra região - Azure CLI

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>Pré-requisitos

- Baixe a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale a CLI do Azure](/cli/azure/install-azure-cli).
- Se você pretende carregar um VHD no local: um VHD de tamanho fixo que [foi preparado para o Azure](../windows/prepare-for-upload-vhd-image.md), armazenado localmente.
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

Antes de criar um HDD padrão vazio para upload, você precisará do tamanho do arquivo do VHD que deseja carregar, em bytes. Para conseguir isso, você `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`pode usar qualquer um ou . Esse valor é usado ao especificar o parâmetro **--upload-size-bytes.**

Crie um HDD padrão vazio para upload especificando tanto o parâmetro **--for-upload** quanto o parâmetro **--upload-size-bytes** em um [disco criar](/cli/azure/disk#az-disk-create) cmdlet:

`<yourdiskname>`Substitua `<yourresourcegroupname>` `<yourregion>` , com valores de sua escolha. O `--upload-size-bytes` parâmetro contém um `34359738880`exemplo de valor de , substituí-lo por um valor apropriado para você.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se você quiser carregar um SSD premium ou um SSD padrão, substitua **standard_lrs** por **premium_LRS** ou **standardssd_lrs**. Os discos ultra não são suportados por enquanto.

Agora que você criou um disco gerenciado vazio configurado para o processo de upload, você pode carregar um VHD para ele. Para carregar um VHD para o disco, você precisará de um SAS gravável, para que você possa referencia-lo como o destino para o seu upload.

Para gerar um SAS gravável do disco `<yourdiskname>`gerenciado `<yourresourcegroupname>`vazio, substitua e, em seguida, use o seguinte comando:

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

Valor devolvido da amostra:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>Carregar um VHD

Agora que você tem um SAS para o seu disco gerenciado vazio, você pode usá-lo para definir seu disco gerenciado como o destino para o seu comando de upload.

Use o AzCopy v10 para carregar seu arquivo VHD local em um disco gerenciado especificando o URI SAS que você gerou.

Este upload tem o mesmo throughput do [HDD padrão](disks-types.md#standard-hdd)equivalente . Por exemplo, se você tiver um tamanho que equivale a S4, você terá um throughput de até 60 MiB/s. Mas, se você tem um tamanho que equivale a S70, você terá um throughput de até 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Depois que o upload estiver concluído, e você não precisar mais gravar mais dados no disco, revogue o SAS. A revogação do SAS mudará o estado do disco gerenciado e permitirá que você conecte o disco a uma VM.

`<yourdiskname>`Substitua `<yourresourcegroupname>`e, em seguida, use o seguinte comando para tornar o disco utilizável:

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>Copiar um disco gerenciado

O upload direto também simplifica o processo de cópia de um disco gerenciado. Você pode copiar dentro da mesma região ou região transversal (para outra região).

O script a seguir fará isso para você, o processo é semelhante às etapas descritas anteriormente, com algumas diferenças já que você está trabalhando com um disco existente.

> [!IMPORTANT]
> Você precisa adicionar um deslocamento de 512 quando estiver fornecendo o tamanho do disco em bytes de um disco gerenciado do Azure. Isso porque o Azure omite o rodapé ao retornar o tamanho do disco. A cópia falhará se você não fizer isso. O seguinte script já faz isso por você.

Substitua `<sourceResourceGroupHere>` `<sourceDiskNameHere>`o `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourTargetLocationHere>` , , , e (um exemplo de um valor de localização seria uswest2) por seus valores, em seguida, execute o script a seguir para copiar um disco gerenciado.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Próximas etapas

Agora que você fez o upload com sucesso de um VHD em um disco gerenciado, você pode anexar o disco como um [disco de dados a uma VM existente](add-disk.md) ou anexar o disco a uma [VM como um disco do SISTEMA OPERACIONAL,](upload-vhd.md#create-the-vm)para criar uma nova VM. 

