---
title: Criando e configurando um cofre de chaves para Azure Disk Encryption
description: Este artigo fornece etapas para criar e configurar um cofre de chaves para uso com criptografia de disco Azure
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72245234"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption

A Criptografia de Disco do Azure usa o Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco.  Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../../key-vault/key-vault-get-started.md) e [Proteja seu cofre de chaves](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Se você já usou a Criptografia de Disco Do Azure com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [Criar e configurar um cofre de chaves para criptografia de disco Azure com o Azure AD (versão anterior)](disk-encryption-key-vault-aad.md) para obter detalhes.

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption envolve três etapas:

1. Criando um grupo de recursos, se necessário.
2. Criando um cofre de chaves. 
3. Definindo políticas avançadas de acesso do cofre de chaves.

Estas etapas são ilustradas nas seguintes partidas rápidas:

- [Criar e criptografar uma VM do Windows com a CLI do Azure](disk-encryption-cli-quickstart.md)
- [Criar e criptografar uma VM do Windows com o Azure PowerShell](disk-encryption-cli-quickstart.md)

Você também pode, se desejar, gerar ou importar uma chave de criptografia (KEK).

> [!Note]
> As etapas deste artigo são automatizadas nos pré-requisitos de criptografia de disco do [Azure, script CLI](https://github.com/ejarvi/ade-cli-getting-started) e [pré-requisitos do Azure Disk Encryption do PowerShell.](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)

## <a name="install-tools-and-connect-to-azure"></a>Instale ferramentas e conecte-se ao Azure

As etapas deste artigo podem ser completadas com o [Azure CLI](/cli/azure/), o [módulo Azure PowerShell Az](/powershell/azure/overview)ou o [portal Azure](https://portal.azure.com).

Embora o portal esteja acessível através do seu navegador, o Azure CLI e o Azure PowerShell exigem instalação local; consulte [Azure Disk Encryption for Windows: instale ferramentas](disk-encryption-windows.md#install-tools-and-connect-to-azure) para detalhes.

### <a name="connect-to-your-azure-account"></a>Conectar-se à sua conta do Azure

Antes de usar o Azure CLI ou Azure PowerShell, você deve primeiro conectar-se à sua assinatura do Azure. Você faz isso [fazendo login com a Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest), fazendo login com o [Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)ou fornecendo suas credenciais para o portal Azure quando solicitado.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Próximas etapas

- [Pré-requisitos de criptografia de disco azure cli script](https://github.com/ejarvi/ade-cli-getting-started)
- [Pré-requisitos de criptografia de disco do Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Conheça [os cenários de criptografia de disco do Azure em VMs do Windows](disk-encryption-windows.md)
- Saiba como [solucionar problemas na criptografia de disco do Azure](disk-encryption-troubleshooting.md)
- Leia os scripts de [exemplo de criptografia de disco do Azure](disk-encryption-sample-scripts.md)
