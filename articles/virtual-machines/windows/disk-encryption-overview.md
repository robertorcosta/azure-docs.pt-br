---
title: Habilite a criptografia de disco do Azure para VMs windows
description: Este artigo fornece instruções sobre como ativar a criptografia de disco do Microsoft Azure para VMs windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: e00fee8841a2d5a817a00b942bfe0733a80b2cfc
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546333"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Criptografia de disco azure para VMs windows 

O Azure Disk Encryption ajuda a proteger seus dados e a atender aos compromissos de conformidade e segurança de sua organização. Ele usa o recurso [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) do Windows para fornecer criptografia de volume para o Sistema Operacional e discos de dados de máquinas virtuais (VMs) do Azure, e é integrado com [o Azure Key Vault](../../key-vault/index.yml) para ajudá-lo a controlar e gerenciar as chaves e segredos de criptografia de disco. 

Se você usar [o Azure Security Center,](../../security-center/index.yml)você será alertado se você tiver VMs que não estão criptografadas. Esses alertas são mostrados como Alta Gravidade e a recomendação é criptografar essas VMs.

![Alerta de criptografia de disco na Central de Segurança do Azure](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se você já usou a Criptografia de Disco Do Azure com o Azure AD para criptografar uma VM, você deve continuar usando essa opção para criptografar sua VM. Consulte [Azure Disk Encryption com Azure AD (versão anterior)](disk-encryption-overview-aad.md) para obter detalhes. 
> - Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou inscrição. Você deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte.

Você pode aprender os fundamentos da Criptografia de Disco Azure para Windows em apenas alguns minutos com o [Create e criptografar uma VM do Windows com o Azure CLI quickstart](disk-encryption-cli-quickstart.md) ou o [Create e criptografar uma VM do Windows com o Azure Powershell quickstart](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>VMs e sistemas operacionais suportados

### <a name="supported-vms"></a>VMs com suporte

As VMs do Windows estão disponíveis em uma [variedade de tamanhos](sizes-general.md). O Azure Disk Encryption não está disponível em [VMs básicos, série A,](https://azure.microsoft.com/pricing/details/virtual-machines/series/)ou em máquinas virtuais com menos de 2 GB de memória.

A Azure Disk Encryption também está disponível para VMs com armazenamento premium.

A Criptografia de Disco Azure não está disponível nas [VMs da Geração 2](generation-2.md#generation-1-vs-generation-2-capabilities)) e [nas VMs da série Lsv2](../lsv2-series.md)). Para obter mais exceções, consulte [Azure Disk Encryption: Cenários sem suporte](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemas operacionais compatíveis

- Cliente windows: Windows 8 e posterior.
- Windows Server: Windows Server 2008 R2 e posterior.  
 
> [!NOTE]
> O Windows Server 2008 R2 requer que o .NET Framework 4.5 seja instalado para criptografia; instalá-lo a partir do Windows Update com a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas baseados no Windows Server 2008 R2 x64[(KB2901983).](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)  
>  
> O Windows Server 2012 R2 Core e o Windows Server 2016 Core exigem que o componente bdehdcfg seja instalado na VM para criptografia.


## <a name="networking-requirements"></a>Requisitos de rede
Para habilitar a criptografia de disco do Azure, as VMs devem atender aos seguintes requisitos de configuração de ponto final de rede:
  - Para obter um token para se conectar ao cofre principal, o VM do Windows deve \[\]ser capaz de se conectar a um ponto final do Azure Active Directory, login.microsoftonline.com .
  - Para escrever as chaves de criptografia do cofre da chave, o Windows VM deve ser capaz de se conectar ao ponto final do cofre chave.
  - O Windows VM deve ser capaz de se conectar a um ponto final de armazenamento do Azure que hospeda o repositório de extensão Azure e uma conta de armazenamento Azure que hospeda os arquivos VHD.
  -  Se a política de segurança limita o acesso de VMs do Azure à Internet, você pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../../key-vault/key-vault-access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Requisitos de diretiva de grupo

A Criptografia de disco do Azure usa o protetor de chave externa BitLocker para VMs do Windows. Para VMs ingressado no domínio, não envie por push todas as políticas de grupo que imponham protetores TPM. Para obter informações sobre a política de grupo para "Permitir o BitLocker sem um TPM compatível", consulte [a referência de diretiva do grupo BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

A política do BitLocker sobre máquinas virtuais de domínio com a política de grupo personalizado deve incluir a seguinte configuração: Configure o [armazenamento do usuário das informações de recuperação do BitLocker -> Permitir a chave de recuperação de 256 bits](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). O Azure Disk Encryption falha quando as configurações da política de grupo personalizada para o BitLocker são incompatíveis. Em computadores que não tinham a configuração de política correta, aplique a nova política, force a atualização da nova política (gpupdate.exe /force) e, em seguida, pode ser necessário reiniciar.

A criptografia de disco do Azure falhará se a política de grupo de nível de domínio bloquear o algoritmo AES-CBC, que é usado pelo BitLocker.

## <a name="encryption-key-storage-requirements"></a>Requisitos de armazenamento de chaves de criptografia  

A Criptografia de disco do Azure requer um Azure Key Vault para controlar e gerenciar chaves e segredos de criptografia de disco. Seu cofre-chave e VMs devem residir na mesma região e assinatura do Azure.

Para obter detalhes, consulte [Criar e configurar um cofre de chaves para criptografia de disco Do Zure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
A tabela a seguir define alguns dos termos comuns usados na documentação de criptografia de disco do Azure:

| Terminologia | Definição |
| --- | --- |
| Cofre de Chave do Azure | Key Vault é um serviço de gerenciamento de chaves criptográfico baseado em módulos de segurança de hardware validados pelo FIPS (Federal Information Processing Standards). Esses padrões ajudam a proteger as chaves criptográficas e os segredos confidenciais. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [a criação e configuração de um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md). |
| CLI do Azure | [O Azure CLI](/cli/azure/install-azure-cli) é otimizado para gerenciar e administrar os recursos do Azure a partir da linha de comando.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) é uma tecnologia de criptografia de volume do Windows reconhecida pelo setor que é usada para permitir criptografia de disco em VMs windows. |
| Chave de criptografia de chave (KEK) | A chave assimétrica (RSA 2048) que você pode usar para proteger ou envolver o segredo. É possível fornecer uma chave protegida por HSM (módulos de segurança de hardware) ou uma chave protegida por software. Para obter mais informações, consulte a documentação do [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [a criação e configuração de um cofre de chaves para criptografia de disco Azure](disk-encryption-key-vault.md). |
| Cmdlets do PowerShell | Para obter mais informações, confira [cmdlets do Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Próximas etapas

- [Quickstart - Crie e criptografe uma VM do Windows com o Azure CLI](disk-encryption-cli-quickstart.md)
- [Quickstart - Crie e criptografe um VM do Windows com o Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Cenários de Azure Disk Encryption em VMs Windows](disk-encryption-windows.md)
- [Pré-requisitos de criptografia de disco azure cli script](https://github.com/ejarvi/ade-cli-getting-started)
- [Pré-requisitos de criptografia de disco do Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md)


