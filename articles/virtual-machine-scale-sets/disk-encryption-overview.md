---
title: Habilite a criptografia de disco do Azure para conjuntos de escala de máquinavirtual
description: Este artigo fornece instruções sobre como ativar a criptografia de disco do Microsoft Azure para conjuntos de escala de máquinavirtual
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278983"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Criptografia de disco azure para conjuntos de escala de máquinavirtual

O Azure Disk Encryption fornece criptografia de volume para o sistema operacional e discos de dados de suas máquinas virtuais, ajudando a proteger e proteger seus dados para cumprir os compromissos de segurança organizacional e conformidade. Para saber mais, consulte [Azure Disk Encryption: Linux VMs](../virtual-machines/linux/disk-encryption-overview.md) e [Azure Disk Encryption: Windows VMs](../virtual-machines/windows/disk-encryption-overview.md)  

A Criptografia de disco do Azure também pode ser aplicada aos conjuntos de escala de máquinas virtuais do Windows e Linux, nesses casos:
- Conjuntos de escala criados com discos gerenciados. A criptografia de disco do Azure não é suportada para conjuntos de escala de disco nativos (ou não gerenciados).
- Os volumes de sO e dados nos conjuntos de escala do Windows.
- Volumes de dados em conjuntos de escala Linux. A criptografia de disco do SISTEMA OPERACIONAL NÃO é suportada no momento para conjuntos de escala Linux.

Você pode aprender os fundamentos da Criptografia de Disco Do Azure para conjuntos de escala de máquinas virtuais em apenas alguns minutos com os [conjuntos de escala de máquina virtual usando o Azure CLI](disk-encryption-cli.md) ou os [conjuntos de escala de máquina virtual do Azure usando os tutoriais do Azure PowerShell.](disk-encryption-powershell.md)

## <a name="next-steps"></a>Próximas etapas

- [Criptografe um conjunto de escala de máquina virtual usando o Azure Resource Manager](disk-encryption-azure-resource-manager.md)
- [Criar e configurar um cofre de chaves para o Azure Disk Encryption](disk-encryption-key-vault.md)
- [Usar o Azure Disk Encryption com o sequenciamento de extensão do conjunto de dimensionamento de máquinas virtuais](disk-encryption-extension-sequencing.md)
