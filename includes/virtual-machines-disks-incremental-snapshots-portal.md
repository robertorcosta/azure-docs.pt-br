---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/12/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 64aa01995460837c820c90010d7c4e3f3d78e6a2
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300226"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal

Para criar e configurar instantâneos incrementais no portal do Azure global, você deve usar o seguinte link: [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots). A criação de instantâneo incremental ainda não está disponível no portal do Azure global.

1. Entre no [portal do Azure](https://aka.ms/incrementalsnapshots) com o link fornecido e navegue até o disco do qual você deseja fazer o instantâneo.
1. Em seu disco, selecione **criar um instantâneo**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text=" ":::

1. Selecione o grupo de recursos que você deseja usar e insira um nome.
1. Selecione **incremental** e selecione **revisar + criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text=" ":::

1. Escolha **Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text=" ":::

## <a name="next-steps"></a>Próximas etapas

Se você quiser ver um exemplo de código que demonstra a capacidade diferencial de instantâneos incrementais, usando o .NET, consulte [copiar backups de Managed disks do Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
