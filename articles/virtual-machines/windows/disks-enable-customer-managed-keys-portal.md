---
title: Usar o portal do Azure para habilitar chaves gerenciadas pelo cliente com discos gerenciados por SSE
description: Use o portal do Azure para habilitar a criptografia do lado do servidor usando chaves gerenciadas pelo cliente em seus discos gerenciados.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 4e3699e1fab928db5a466073c411b701ffc1a51d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235770"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Habilitar chaves gerenciadas pelo cliente com os discos gerenciados por criptografia do lado do servidor-Portal

Armazenamento em Disco do Azure permite que você gerencie suas próprias chaves ao usar a criptografia do lado do servidor (SSE) para discos gerenciados, se você escolher. Para obter informações conceituais sobre o SSE com chaves gerenciadas pelo cliente, bem como outros tipos de criptografia de disco gerenciado, consulte a seção [chaves gerenciadas pelo cliente](disk-encryption.md#customer-managed-keys) do nosso artigo sobre criptografia de disco.

## <a name="restrictions"></a>Restrições

Por enquanto, as chaves gerenciadas pelo cliente têm as seguintes restrições:

- Se esse recurso estiver habilitado para o disco, não é possível desabilitá-lo.
    Se uma solução alternativa for necessária, você deve [copiar todos os dados](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) para um disco gerenciado totalmente diferente que não use chaves gerenciadas pelo cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

As seções a seguir abordam como habilitar e usar chaves gerenciadas pelo cliente para Managed disks:

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente contam com as identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Ao configurar chaves gerenciadas pelo cliente, uma identidade gerenciada é atribuída automaticamente aos recursos nos bastidores. Se, subsequentemente, você mover a assinatura, o grupo de recursos ou o disco gerenciado de um diretório do Azure AD para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo locatário, portanto, as chaves gerenciadas pelo cliente poderão deixar de funcionar. Para obter mais informações, confira [Transferência de uma assinatura entre diretórios do Azure Active Directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>Próximas etapas

- [Conheça os modelos do Azure Resource Manager para criar discos criptografados com chaves gerenciadas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)
- [Replicar máquinas com discos habilitados para chaves gerenciadas pelo cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurar a recuperação de desastre de VMs VMware para o Azure usando o PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurar a recuperação de desastres para o Azure para máquinas virtuais do Hyper-V usando o PowerShell e o Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)