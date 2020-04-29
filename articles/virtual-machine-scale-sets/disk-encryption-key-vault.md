---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 30fa6b910c0241621c2b2cdae9bb9a164f27cedb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81454536"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption

Azure Disk Encryption usa Azure Key Vault para controlar e gerenciar chaves de criptografia de disco e segredos.  Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md) e [Proteja seu cofre de chaves](../key-vault/general/secure-your-key-vault.md).

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption envolve três etapas:

1. Criar um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definindo políticas de acesso avançado do cofre de chaves.

Essas etapas são ilustradas nos seguintes guias de início rápido:

Você também pode, se desejar, gerar ou importar uma chave de criptografia de chave (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instalar ferramentas e conectar-se ao Azure

As etapas neste artigo podem ser concluídas com o [CLI do Azure](/cli/azure/), o [módulo Azure PowerShell Az](/powershell/azure/overview)ou o [portal do Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Antes de usar o CLI do Azure ou Azure PowerShell, você deve primeiro se conectar à sua assinatura do Azure. Você faz isso [entrando com CLI do Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), [entrando com o Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo suas credenciais para o portal do Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Próximas etapas

- [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](disk-encryption-cli.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando o Azure PowerShell](disk-encryption-powershell.md)
