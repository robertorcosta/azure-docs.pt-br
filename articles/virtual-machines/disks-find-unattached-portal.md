---
title: Identificar discos desanexados do Azure – portal do Azure
description: Como encontrar discos gerenciados e não gerenciados desanexados (VHDs/blobs de páginas) do Azure usando o portal do Azure.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 06/01/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: ad01155259ea03806f56c6e75b315ff85b606220
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.contentlocale: pt-BR
ms.lasthandoff: 06/02/2020
ms.locfileid: "84302468"
---
# <a name="find-and-delete-unattached-azure-managed-and-unmanaged-disks---azure-portal"></a>Encontrar e excluir discos gerenciados e não gerenciados desanexados do Azure – portal do Azure

Quando você exclui uma VM (máquina virtual) no Azure, por padrão, nenhum disco anexado à máquina virtual é excluído. Isso ajuda a evitar a perda de dados devido à exclusão não intencional de VMs. Depois que uma VM for excluída, você continuará a pagar pelos discos desanexados. Este artigo mostra como encontrar e excluir discos desanexados usando o portal do Azure e reduzir custos desnecessários.

## <a name="managed-disks-find-and-delete-unattached-disks"></a>Discos gerenciados: Localizar e excluir discos desanexados

Se você tiver discos gerenciados desanexados e não precisar mais dos dados deles, o seguinte processo explicará como encontrá-los no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Procure e selecione **Discos**.

    Na folha **Discos**, você verá uma lista de todos os seus discos. Qualquer disco que tenha " **-** " na coluna **Proprietário** é um disco desanexado.

    [![](media/disks-find-unattached-portal/managed-disk-unattached-owner.png "Screenshot of the managed disks blade, if a disk has - in the Owner column, it is an unattached disk")](media/disks-find-unattached-portal/managed-disk-owner-unattached.png#lightbox)

1. Selecione o disco desanexado que deseja excluir; isso abrirá a folha do disco.
1. Na folha do disco, confirme se o estado do disco é desanexado e selecione **Excluir**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-managed-disk-unattached.png" alt-text="Captura de tela de uma folha de discos gerenciados individuais. Essa folha mostrará desanexado no estado do disco se ele estiver desanexado. Exclua esse disco se não precisar mais preservar os dados dele":::

## <a name="unmanaged-disks-find-and-delete-unattached-disks"></a>Discos não gerenciados: Localizar e excluir discos desanexados

Discos não gerenciados são arquivos VHD armazenados como [blobs de páginas](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-page-blobs) nas [Contas de Armazenamento do Microsoft Azure](../storage/common/storage-account-overview.md).

Se você tiver discos não gerenciados que não estão anexados a uma VM, não precisar mais dos dados deles e desejar excluí-los, o seguinte processo explicará como fazer isso no portal do Azure:

1. Entre no [portal do Azure](https://portal.azure.com/).
1. Procure e selecione **Discos (Clássico)** .

    Você verá uma lista de todos os discos não gerenciados. Qualquer disco que tenha " **-** " na coluna **Anexado a** é um disco desanexado.

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-attached-to.png" alt-text="Captura de tela da folha de discos não gerenciados. Os discos desta folha que têm - na coluna Anexada a estão desanexados.":::

1. Selecione o disco desanexado que deseja excluir; isso abrirá a folha do disco.

1. Na folha do disco, confirme se ele está desanexado, pois o estado **Anexado a** ainda estará **-** .

    :::image type="content" source="media/disks-find-unattached-portal/unmanaged-disk-unattached-select-blade.png" alt-text="Captura de tela da folha de um disco não gerenciado individual. Ele terá um - como o valor Anexado a se ele estiver desanexado. Se você não precisar mais dos dados desse discos, exclua-os.":::

1. Selecione **Excluir**.

    :::image type="content" source="media/disks-find-unattached-portal/delete-unmanaged-disk-unattached.png" alt-text="Captura de tela da folha de um disco não gerenciado individual, realçando Excluir.":::

## <a name="next-steps"></a>Próximas etapas

Caso deseje obter uma forma automatizada de localizar e excluir contas de armazenamento desanexadas, confira nossos artigos sobre a [CLI](linux/find-unattached-disks.md) ou o [PowerShell](windows/find-unattached-disks.md).

Para obter mais informações, confira [Excluir uma conta de armazenamento](../storage/common/storage-account-create.md#delete-a-storage-account) e [Identificar discos órfãos usando o PowerShell](https://blogs.technet.microsoft.com/ukplatforms/2018/02/21/azure-cost-optimisation-series-identify-orphaned-disks-using-powershell/)