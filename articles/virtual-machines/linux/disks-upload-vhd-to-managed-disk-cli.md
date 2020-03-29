---
title: Faça upload de um VHD usando o Azure CLI
description: Aprenda a carregar um vhd em um disco gerenciado pelo Azure e copie um disco gerenciado entre regiões, usando o Cli do Azure, via upload direto.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062658"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Faça upload de um vhd para o Azure usando o Azure CLI

Este artigo explica como carregar um vhd de sua máquina local para um disco gerenciado do Azure. Anteriormente, você tinha que seguir um processo mais envolvido que incluía o estágio de seus dados em uma conta de armazenamento e o gerenciamento dessa conta de armazenamento. Agora, você não precisa mais gerenciar uma conta de armazenamento ou encenar dados nela para carregar um vhd. Em vez disso, você cria um disco gerenciado vazio e carrega um vhd diretamente para ele. Isso simplifica o upload de VMs no local para o Azure e permite que você carregue um vhd até 32 TiB diretamente em um grande disco gerenciado.

Se você estiver fornecendo uma solução de backup para VMs IaaS no Azure, recomendamos que você use upload direto para restaurar backups de clientes em discos gerenciados. Se você estiver carregando um VHD de uma máquina externa para o Azure, as velocidades dependerão da largura de banda local. Se você estiver usando uma VM Azure, então sua largura de banda será a mesma dos HDDs padrão.

Atualmente, o upload direto é suportado para HDD padrão, SSD padrão e discos gerenciados premium SSD. Ainda não é suportado para ultra SSDs.

## <a name="prerequisites"></a>Pré-requisitos

- Baixe a versão mais recente [do AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Instale a CLI do Azure](/cli/azure/install-azure-cli).
- Um arquivo vhd, armazenado localmente
- Se você pretende carregar um vhd do local: um vhd de tamanho fixo que [foi preparado para o Azure](../windows/prepare-for-upload-vhd-image.md), armazenado localmente.
- Ou, um disco gerenciado no Azure, se você pretende executar uma ação de cópia.

## <a name="create-an-empty-managed-disk"></a>Criar um disco gerenciado vazio

Para carregar seu vhd no Azure, você precisará criar um disco gerenciado vazio configurado para este processo de upload. Antes de criar um, há algumas informações adicionais que você deve saber sobre esses discos.

Este tipo de disco gerenciado tem dois estados únicos:

- ReadToUpload, o que significa que o disco está pronto para receber um upload, mas, nenhuma [assinatura de acesso seguro](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) foi gerada.
- ActiveUpload, o que significa que o disco está pronto para receber um upload e o SAS foi gerado.

Enquanto em qualquer um desses estados, o disco gerenciado será cobrado a [preços de HDD padrão,](https://azure.microsoft.com/pricing/details/managed-disks/)independentemente do tipo real de disco. Por exemplo, um P10 será cobrado como um S10. Isso será verdade `revoke-access` até que seja chamado no disco gerenciado, que é necessário para anexar o disco a uma VM.

Antes de criar um HDD padrão vazio para upload, você precisará ter o tamanho do arquivo do vhd que deseja carregar, em bytes. Para conseguir isso, você `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`pode usar qualquer um ou . Esse valor é usado ao especificar o parâmetro **--upload-size-bytes.**

Crie um HDD padrão vazio para upload especificando tanto o parâmetro **--for-upload** quanto o parâmetro **--upload-size-bytes** em um [disco criar](/cli/azure/disk#az-disk-create) cmdlet:

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Se você quiser carregar um SSD premium ou um SSD padrão, substitua **standard_lrs** por **premium_LRS** ou **standardssd_lrs**. O Ultra SSD ainda não tem suporte.

Agora você criou um disco gerenciado vazio configurado para o processo de upload. Para carregar um vhd para o disco, você precisará de um SAS gravável, para que você possa referencia-lo como o destino para o seu upload.

Para gerar um SAS gravável do seu disco gerenciado vazio, use o seguinte comando:

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Valor devolvido da amostra:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Carregar vhd

Agora que você tem um SAS para o seu disco gerenciado vazio, você pode usá-lo para definir seu disco gerenciado como o destino para o seu comando de upload.

Use o AzCopy v10 para carregar seu arquivo VHD local em um disco gerenciado especificando o URI SAS que você gerou.

Este upload tem o mesmo throughput do [HDD padrão](disks-types.md#standard-hdd)equivalente . Por exemplo, se você tiver um tamanho que equivale a S4, você terá um throughput de até 60 MiB/s. Mas, se você tem um tamanho que equivale a S70, você terá um throughput de até 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

Depois que o upload estiver concluído, e você não precisar mais gravar mais dados no disco, revogue o SAS. A revogação do SAS mudará o estado do disco gerenciado e permitirá que você conecte o disco a uma VM.

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
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

Agora que você fez o upload com sucesso de um vhd em um disco gerenciado, você pode anexar o disco como um [disco de dados a uma VM existente](add-disk.md) ou anexar o disco a uma [VM como um disco do SISTEMA OPERACIONAL,](upload-vhd.md#create-the-vm)para criar uma nova VM. 

