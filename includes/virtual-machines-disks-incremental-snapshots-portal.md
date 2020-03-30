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
ms.openlocfilehash: 8f2e2081ee31e2ce7b21677bd261ed7eda705334
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79486085"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal

Para criar e configurar instantâneos incrementais no portal global do [https://aka.ms/incrementalsnapshots](https://aka.ms/incrementalsnapshots)Azure, você deve usar o seguinte link: . A criação incremental de instantâneos ainda não está disponível no portal global do Azure.

1. Entre no [portal do Azure](https://aka.ms/incrementalsnapshots) com o link fornecido e navegue até o disco que você deseja tirar o instantâneo.
1. Em seu disco, selecione **Criar um snapshot**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Screenshot. A lâmina do disco, com **+Create snapshot** destacada, pois é isso que você deve selecionar.":::

1. Selecione o grupo de recursos que deseja usar e digite um nome.
1. Selecione **Incremental** e selecione **Revisão + Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Screenshot. Crie uma lâmina de instantâneo, preencha o nome e selecione incremental e crie seu snapshot.":::

1. Selecione **Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Screenshot. Página de validação para o seu snapshot, confirme suas seleções e crie o snapshot.":::

## <a name="next-steps"></a>Próximas etapas

Se você quiser ver o código de amostra demonstrando a capacidade diferencial de instantâneos incrementais, usando o .NET, consulte backups do [Copy Azure Managed Disks para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
