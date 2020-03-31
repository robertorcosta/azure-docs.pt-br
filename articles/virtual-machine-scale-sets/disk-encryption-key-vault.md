---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com criptografia de disco Azure
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279010"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption

A Criptografia de Disco do Azure usa o Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco.  Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md) e [Proteja seu cofre de chaves](../key-vault/key-vault-secure-your-key-vault.md).

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption envolve três etapas:

1. Criando um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definindo políticas avançadas de acesso do cofre de chaves.

Estas etapas são ilustradas nas seguintes partidas rápidas:

Você também pode, se desejar, gerar ou importar uma chave de criptografia (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e conecte-se ao Azure

As etapas deste artigo podem ser completadas com o [Azure CLI](/cli/azure/), o [módulo Azure PowerShell Az](/powershell/azure/overview)ou o [portal Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Antes de usar o Azure CLI ou Azure PowerShell, você deve primeiro conectar-se à sua assinatura do Azure. Você faz isso [fazendo login com a Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), fazendo login com o [Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo suas credenciais para o portal Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Próximas etapas

- [Visão geral da criptografia de disco do Azure](disk-encryption-overview.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando a CLI do Azure](disk-encryption-cli.md)
- [Criptografar um conjunto de dimensionamento de máquinas virtuais usando o Azure PowerShell](disk-encryption-powershell.md)
