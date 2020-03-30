---
title: Criptografia de disco azure com Azure AD (versão anterior)
description: Este artigo fornece os pré-requisitos para usar o Microsoft Azure Disk Encryption para VMs IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 33b257e9d344fc31df072509f105d2e8fd1bd29b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72245169"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Criptografia de disco azure com Azure AD (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo Azure AD para habilitar a criptografia de disco VM. Com a nova versão, você não é mais obrigado a fornecer credenciais Azure AD durante a etapa de criptografia de habilitação. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo Azure AD usando a nova versão. Para exibir instruções para ativar a criptografia de disco VM usando a nova versão, consulte [Azure Disk Encryption for Windows VMs](disk-encryption-overview.md). As VMs que já estavam criptografadas com os parâmetros de aplicação do Azure AD ainda são suportadas e devem continuar a ser mantidas com a sintaxe AAD.**

Este artigo complementa [a criptografia de disco azure para VMs Windows](disk-encryption-overview.md) com requisitos adicionais e pré-requisitos para criptografia de disco Azure com Azure AD (versão anterior). A seção [VMs e sistemas operacionais suportados](disk-encryption-overview.md#supported-vms-and-operating-systems) permanece a mesma.

## <a name="networking-and-group-policy"></a> Sistema de rede e a diretiva de grupo

**Para habilitar o recurso de Azure Disk Encryption usando a sintaxe do parâmetro AAD, as VMs IaaS devem atender os requisitos de configuração do ponto de extremidade da rede a seguir:** 
  - Para obter um token para se conectar ao seu cofre de chaves, a VM IaaS deve ser capaz de se conectar a um ponto de extremidade do Azure Active Directory, \[login.microsoftonline.com\].
  - Para gravar as chaves de criptografia no cofre de chaves, a VM IaaS deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM IaaS deve ser capaz de se conectar a um ponto de extremidade do armazenamento do Azure que hospeda o repositório de extensão do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se a política de segurança limita o acesso de VMs do Azure à Internet, você pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - No Windows, se o TLS 1.0 foi desabilitado explicitamente e a versão do .NET não foi atualizada para 4.6 ou superior, a seguinte alteração do registro permitirá ADE selecionar a versão mais recente do TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Política do grupo:**
 - A solução de Azure Disk Encryption usa o protetor de chave externa BitLocker para VMs IaaS do Windows. Para VMs ingressado no domínio, não envie por push todas as políticas de grupo que imponham protetores TPM. Para obter informações sobre a política de grupo "Permitir BitLocker sem um TPM compatível", confira [Referência de política de grupo do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A política do BitLocker sobre máquinas virtuais de domínio com a política de grupo personalizado deve incluir a seguinte configuração: Configure o [armazenamento do usuário das informações de recuperação do BitLocker -> Permitir a chave de recuperação de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). O Azure Disk Encryption falha quando as configurações da política de grupo personalizada para o BitLocker são incompatíveis. Em computadores que não tinham a configuração de política correta, aplique a nova política, force a atualização da nova política (gpupdate.exe /force) e, em seguida, pode ser necessário reiniciar.  

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de criptografia  

A Criptografia de disco do Azure requer um Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. Seu cofre-chave e VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [Criar e configurar um cofre de chaves para criptografia de disco Azure com a Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Próximas etapas

- [Criação e configuração de um cofre de chaves para criptografia de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Habilite a criptografia de disco do Azure com o Azure AD em VMs Windows (versão anterior)](disk-encryption-windows-aad.md)
- [Pré-requisitos de criptografia de disco azure cli script](https://github.com/ejarvi/ade-cli-getting-started)
- [Pré-requisitos de criptografia de disco do Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)