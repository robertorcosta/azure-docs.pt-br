---
title: Solucionar problemas de discos anexados às VMs do Azure | Microsoft Docs
description: Fornece links para os recursos de solução de problemas dos VHDs (discos rígidos virtuais) da máquina virtual do Azure.
services: storage
author: roygara
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: rogarana
ms.reviewer: wmgries
ms.openlocfilehash: b81c0947327dc8e84c5d3fbbecde3aa31313cc70
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061195"
---
# <a name="troubleshoot-disks-attached-to-azure-vms"></a>Solucionar problemas de discos anexados às VMs do Azure 

As Máquinas Virtuais (VMs) do Azure dependem de Discos Rígidos virtuais (VHDs) para o disco do sistema operacional e quaisquer discos de dados anexados. Os VHDs são armazenados como blobs de páginas em uma ou mais contas de Armazenamento do Microsoft Azure. Este artigo descreve como solucionar problemas de conteúdo para problemas comuns que podem surgir com VHDs. 

## <a name="troubleshoot-storage-deletion-errors-for-a-vm"></a>Solucionar problemas de erros de exclusão de armazenamento para uma VM

Em certos casos, você pode encontrar um erro enquanto a exclusão de um recurso de armazenamento quando uma VM em uma implantação do Gerenciador de Recursos contém VHDs anexados. Para obter ajuda na resolução desse problema, consulte um dos seguintes artigos: 

  * Em VMs do Linux: [Erros de exclusão de armazenamento na implantação do Gerenciador de Recursos](../../virtual-machines/linux/storage-resource-deletion-errors.md)  
  * Em VMs do Windows: [Erros de exclusão de armazenamento na implantação do Gerenciador de Recursos](../../virtual-machines/windows/storage-resource-deletion-errors.md)  

## <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solucionar problemas de reinicializações inesperadas de VMs com VHDs anexados

Se você encontrar reinicializações inesperadas de uma VM com um grande número de VHDs anexados, consulte um dos seguintes artigos:

  * Em VMs do Linux: [Reinicializações inesperadas de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
  * Em VMs do Windows: [Reinicializações inesperadas de VMs com VHDs anexados](../../virtual-machines/linux/unexpected-reboots-attached-vhds.md)
