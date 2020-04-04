---
title: incluir arquivo
description: incluir arquivo
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/02/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: de0cf260207747f4acb02a377819a13de8b8ba22
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80628411"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal


1. Entre no [portal Azure](https://portal.azure.com/) e navegue até o disco que você deseja fazer um instantâneo.
1. Em seu disco, selecione **Criar um snapshot**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Screenshot. A lâmina do disco, com **+Create snapshot** destacada, pois é isso que você deve selecionar.":::

1. Selecione o grupo de recursos que deseja usar e digite um nome.
1. Selecione **Incremental** e selecione **Revisão + Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Screenshot. Crie uma lâmina de instantâneo, preencha o nome e selecione incremental e crie seu snapshot.":::

1. Selecione **Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Screenshot. Página de validação para o seu snapshot, confirme suas seleções e crie o snapshot.":::

## <a name="next-steps"></a>Próximas etapas

Se você quiser ver o código de amostra demonstrando a capacidade diferencial de instantâneos incrementais, usando o .NET, consulte backups do [Copy Azure Managed Disks para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
