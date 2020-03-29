---
title: Criptografia de disco azure com pré-requisitos de aplicativos Azure AD (versão anterior)
description: Este artigo fornece os pré-requisitos para usar o Microsoft Azure Disk Encryption para VMs IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970575"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Criptografia de disco azure com Azure AD (versão anterior)

A nova versão do Azure Disk Encryption elimina a exigência de fornecer um parâmetro de aplicativo Azure Active Directory (Azure AD) para permitir a criptografia de disco VM. Com a nova versão, não é mais necessário fornecer credenciais do Azure AD durante a etapa para habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo Azure AD usando a nova versão. Para obter instruções sobre como ativar a criptografia de disco VM usando a nova versão, consulte [Azure Disk Encryption para VMs Linux](disk-encryption-overview.md). As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.

Este artigo fornece suplementos para o [Azure Disk Encryption para VMs Linux](disk-encryption-overview.md) com requisitos adicionais e pré-requisitos para criptografia de disco Azure com Azure AD (versão anterior).

As informações nestas seções permanecem as mesmas:

- [VMs e sistemas operacionais suportados](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisitos adicionais de VM](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a> Sistema de rede e a diretiva de grupo

Para habilitar o recurso Azure Disk Encryption usando a sintaxe de parâmetros AAD mais antiga, as VMs de infra-estrutura como serviço (IaaS) devem atender aos seguintes requisitos de configuração de ponto final de rede: 
  - Para obter um token para se conectar ao seu cofre principal, a VM IaaS \[deve\]ser capaz de se conectar a um ponto final do Azure AD, login.microsoftonline.com .
  - Para gravar as chaves de criptografia no cofre de chaves, a VM IaaS deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM IaaS deve ser capaz de se conectar a um ponto de extremidade do armazenamento do Azure que hospeda o repositório de extensão do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se sua política de segurança limitar o acesso das VMs do Azure à internet, você poderá resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída aos IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - No Windows, se o TLS 1.0 estiver explicitamente desativado e a versão .NET não for atualizada para 4,6 ou mais, a seguinte alteração de registro permite que o Azure Disk Encryption selecione a versão TLS mais recente:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Política de Grupo
 - A solução de Azure Disk Encryption usa o protetor de chave externa BitLocker para VMs IaaS do Windows. Para VMs com domínio, não pressione nenhuma Política de Grupo que imponha protetores TPM. Para obter informações sobre a Diretiva de Grupo para a opção **Permitir bitLocker sem um TPM compatível,** consulte [a referência de diretiva do grupo BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- A política do BitLocker em máquinas virtuais com adesão ao domínio com uma diretiva de grupo personalizada deve incluir a seguinte configuração: Configure o [armazenamento do usuário das informações de recuperação do BitLocker - > Permitir a chave de recuperação de 256 bits](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). A criptografia de disco do Azure falha quando as configurações personalizadas da diretiva de grupo para o BitLocker são incompatíveis. Em máquinas que não têm a configuração correta de diretiva, aplique a nova diretiva, force a nova diretiva a atualizar (gpupdate.exe /force) e, em seguida, reinicie se for necessário. 

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de criptografia 

A criptografia de disco do Azure requer que o Azure Key Vault controle e gerencie chaves e segredos de criptografia de disco. Seu cofre-chave e VMs devem residir na mesma região e assinatura do Azure.

Para obter mais informações, consulte [Criar e configurar um cofre de chaves para criptografia de disco Azure com a Azure AD (versão anterior)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Próximas etapas

- [Criação e configuração de um cofre de chaves para criptografia de disco Azure com Azure AD (versão anterior)](disk-encryption-key-vault-aad.md)
- [Habilite a criptografia de disco do Azure com o Azure AD em VMs Linux (versão anterior)](disk-encryption-linux-aad.md)
- [Pré-requisitos de criptografia de disco azure cli script](https://github.com/ejarvi/ade-cli-getting-started)
- [Pré-requisitos de criptografia de disco do Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)