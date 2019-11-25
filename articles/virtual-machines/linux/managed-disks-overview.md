---
title: Visão geral do disco gerenciado pelo Armazenamento em Disco do Azure para VMs Linux
description: Visão geral dos discos gerenciados do Azure, que lida com as contas de armazenamento ao usar VMs do Linux
author: roygara
ms.service: virtual-machines-linux
ms.topic: overview
ms.date: 11/06/2019
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e9a5e1b1e1b0c879a1264573e7073e70a1e95fc
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035859"
---
# <a name="introduction-to-azure-managed-disks"></a>Introdução aos discos gerenciados do Azure

Um disco gerenciado do Azure é um disco rígido virtual (VHD). Você pode pensar nisso como um disco físico em um servidor local, mas virtualizado. Os discos gerenciados do Azure são armazenados como blobs de página, que são um objeto de armazenamento de E/S aleatório no Azure. Chamamos um disco gerenciado de “gerenciado” porque é uma abstração sobre blobs de páginas, contêineres de blob e contas de armazenamento do Azure. Com discos gerenciados, tudo o que você precisa fazer é provisionar o disco e o Azure cuidará do resto.

Ao escolher usar discos gerenciados do Azure com suas cargas de trabalho, o Azure cria e gerencia o disco para você. Os tipos disponíveis de discos são discos Ultra, SSD (unidade de estado sólido) Premium, SSD Standard e HD (unidade de disco rígido) Standard. Para saber mais sobre cada tipo de disco individual, confira [Selecionar um tipo de disco para VMs IaaS](disks-types.md).

[!INCLUDE [virtual-machines-managed-disks-overview.md](../../../includes/virtual-machines-managed-disks-overview.md)]

> [!div class="nextstepaction"]
> [Selecionar um tipo de disco para VMs de IaaS](disks-types.md)
