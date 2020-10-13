---
title: Criptografia do lado do servidor dos discos gerenciados do Azure - PowerShell
description: O Armazenamento do Microsoft Azure protege os dados com criptografia em repouso, antes de enviá-los para clusters de armazenamento. Você pode contar com chaves gerenciadas pela Microsoft para a criptografia dos discos gerenciados ou usar chaves gerenciadas pelo cliente para gerenciar a criptografia com suas próprias chaves.
author: roygara
ms.date: 09/23/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 49898f2215139c4460c639dc8caa3d2764082e34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91274768"
---
# <a name="server-side-encryption-of-azure-disk-storage-for-powershell"></a>Criptografia do Armazenamento em Disco do Azure no lado do servidor para o PowerShell

A criptografia do lado do servidor (SSE) ajuda a proteger os dados e atender aos compromissos de conformidade e segurança de sua organização. A SSE criptografa automaticamente os dados armazenados nos discos gerenciados do Azure (so e discos de dados) em repouso por padrão, ao mantê-los na nuvem. 

Os dados nos discos gerenciados são criptografados de maneira transparente usando a [criptografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) de 256 bits, uma das codificações de bloco mais fortes disponíveis, e são compatíveis com o FIPS 140-2. Para obter mais informações sobre os módulos criptográficos subjacentes aos discos gerenciados do Azure, veja [API de Criptografia: Próxima geração](/windows/desktop/seccng/cng-portal)

A criptografia do lado do servidor não afeta o desempenho dos discos gerenciados e não há nenhum custo adicional. 

> [!NOTE]
> Os discos temporários não são discos gerenciados e não são criptografados pelo SSE, a menos que você habilite a criptografia no host.

## <a name="about-encryption-key-management"></a>Sobre o gerenciamento de chaves de criptografia

Você pode contar com chaves gerenciadas pela plataforma para a criptografia do disco gerenciado ou gerenciar a criptografia usando suas próprias chaves. Se você optar por gerenciar a criptografia com suas próprias chaves, pode especificar uma *chave gerenciada pelo cliente* a ser usada para criptografar e descriptografar todos os dados nos discos gerenciados. 

As seções a seguir descrevem mais detalhadamente cada uma das opções de gerenciamento de chaves.

### <a name="platform-managed-keys"></a>Chaves gerenciadas pela plataforma

Por padrão, os discos gerenciados usam chaves de criptografia gerenciadas pela plataforma. Todos os discos gerenciados, instantâneos, imagens e dados gravados em discos gerenciados existentes são automaticamente criptografados em repouso com chaves gerenciadas pela plataforma.

### <a name="customer-managed-keys"></a>Chaves gerenciadas pelo cliente

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Restrições

Por enquanto, as chaves gerenciadas pelo cliente têm as seguintes restrições:

- Se esse recurso estiver habilitado para o disco, não é possível desabilitá-lo.
    Se uma solução alternativa for necessária, você deve [copiar todos os dados](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) para um disco gerenciado totalmente diferente que não use chaves gerenciadas pelo cliente.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

#### <a name="supported-regions"></a>Regiões com suporte

As chaves gerenciadas pelo cliente estão disponíveis em todas as regiões em que os discos gerenciados estão disponíveis.

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Criptografia em criptografia de host de ponta a ponta para os dados da VM

A criptografia de ponta a ponta inicia do host da VM, o servidor do Azure ao qual sua VM está alocada. Os dados em seus discos temporários, discos do sistema operacional efêmero e caches persistentes de disco de dados/sistema operacional são armazenados nesse host de VM. Quando você habilita a criptografia de ponta a ponta, todos esses dados são criptografados em repouso e os fluxos são criptografados para o serviço de armazenamento, onde são persistidos. A criptografia de ponta a ponta não usa a CPU da VM e não afeta o desempenho da VM. 

Discos temporários e discos do sistema operacional efêmero são criptografados em repouso com chaves gerenciadas pela plataforma quando você habilita a criptografia de ponta a ponta. Os caches do sistema operacional e dos dados são criptografados em repouso com chaves gerenciadas pelo cliente ou gerenciadas por plataforma, dependendo do tipo de criptografia. Por exemplo, se um disco for criptografado com chaves gerenciadas pelo cliente, o cache do disco será criptografado com chaves gerenciadas pelo cliente e, se um disco for criptografado com chaves gerenciadas pela plataforma, o cache do disco será criptografado com chaves gerenciadas pela plataforma.

### <a name="restrictions"></a>Restrições

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Regiões com suporte

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Tamanhos de VM com suporte

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Criptografia dupla em repouso

Clientes confidenciais de alta segurança que se preocupam com o risco associado a qualquer algoritmo de criptografia, implementação ou chave em particular comprometidos agora podem optar por uma camada adicional de criptografia usando um algoritmo/modo de criptografia diferente na camada de infraestrutura usando chaves de criptografia gerenciadas pela plataforma. Essa nova camada pode ser aplicada ao sistema operacional persistente e discos de dados, instantâneos e imagens, todos os quais serão criptografados em repouso com criptografia dupla.

### <a name="supported-regions"></a>Regiões com suporte

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Criptografia do lado do servidor versus criptografia de disco do Azure

O [Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) aproveita o recurso [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) do Windows para criptografar discos gerenciados com chaves gerenciadas pelo cliente na VM convidada. A criptografia do lado do servidor com chaves gerenciadas pelo cliente aprimora o ADE, permitindo usar quaisquer tipos de sistema operacional e imagens para as VMs, criptografando dados no serviço de armazenamento.

> [!IMPORTANT]
> As chaves gerenciadas pelo cliente contam com as identidades gerenciadas para recursos do Azure, um recurso do Azure Active Directory (Azure AD). Ao configurar chaves gerenciadas pelo cliente, uma identidade gerenciada é atribuída automaticamente aos recursos nos bastidores. Se, em seguida, você migrar a assinatura, o grupo de recursos ou o disco gerenciado de um diretório do Azure Active Directory para outro, a identidade gerenciada associada aos discos gerenciados não será transferida para o novo locatário. Portanto, as chaves gerenciadas pelo cliente podem deixar de funcionar. Para obter mais informações, confira [Transferência de uma assinatura entre diretórios do Azure Active Directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).


## <a name="next-steps"></a>Próximas etapas

- Habilite a criptografia de ponta a ponta usando a criptografia no host com o [PowerShell](disks-enable-host-based-encryption-powershell.md) ou o [portal do Azure](../disks-enable-host-based-encryption-portal.md).
- Habilite a criptografia dupla em repouso para discos gerenciados com o [PowerShell](disks-enable-double-encryption-at-rest-powershell.md) ou o [portal do Azure](../disks-enable-double-encryption-at-rest-portal.md).
- Habilite chaves gerenciadas pelo cliente para discos gerenciados com o [PowerShell](disks-enable-customer-managed-keys-powershell.md) ou o [portal do Azure](../disks-enable-customer-managed-keys-portal.md).
- [Conheça os modelos do Azure Resource Manager para criar discos criptografados com chaves gerenciadas pelo cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [O que é o Cofre da Chave do Azure?](../../key-vault/general/overview.md)
