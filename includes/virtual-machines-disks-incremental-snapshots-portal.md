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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80628411"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="portal"></a>Portal


1. Entre no [portal do Azure](https://portal.azure.com/) e navegue até o disco do qual você deseja fazer o instantâneo.
1. Em seu disco, selecione **criar um instantâneo**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-snapshot-button-incremental.png" alt-text="Captura. A folha do disco, com * * + criar instantâneo * * realçado, como é o que você deve selecionar.":::

1. Selecione o grupo de recursos que você deseja usar e insira um nome.
1. Selecione **incremental** e selecione **revisar + criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/incremental-snapshot-create-snapshot-blade.png" alt-text="Captura. Crie uma folha de instantâneo, preencha o nome e selecione incremental e, em seguida, crie o instantâneo.":::

1. Escolha **Criar**

    :::image type="content" source="media/virtual-machines-disks-incremental-snapshots-portal/create-incremental-snapshot-validation.png" alt-text="Captura. Página de validação para seu instantâneo, confirme suas seleções e crie o instantâneo.":::

## <a name="next-steps"></a>Próximas etapas

Se você quiser ver um exemplo de código que demonstra a capacidade diferencial de instantâneos incrementais, usando o .NET, consulte [copiar backups de Managed disks do Azure para outra região com capacidade diferencial de instantâneos incrementais](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).
