---
title: Perguntas frequentes-Azure Disk Encryption para VMs do Windows | Microsoft Docs
description: Este artigo fornece respostas para perguntas frequentes sobre a criptografia de disco Microsoft Azure para VMs IaaS do Windows.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: b6cd7e93451de9c5761db22024ac82abaaa0949e
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245260"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Perguntas frequentes sobre o Azure Disk Encryption para VMs do Windows

Este artigo fornece respostas para perguntas frequentes sobre Azure Disk Encryption para VMs do Windows. Para obter mais informações sobre esse serviço, consulte [Azure Disk Encryption visão geral](disk-encryption-overview.md).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Onde o Azure Disk Encryption está na GA (disponibilidade geral)?

Azure Disk Encryption está em disponibilidade geral em todas as regiões públicas do Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quais experiências de usuário estão disponíveis com o Azure Disk Encryption?

A GA do Azure Disk Encryption dá suporte a modelos do Azure Resource Manager, ao Azure PowerShell e à CLI do Azure. As diferentes experiências de usuário oferecem flexibilidade. Você tem três opções diferentes para habilitar a criptografia de disco para suas VMs. Para obter mais informações sobre a experiência do usuário e as diretrizes passo a passo disponíveis no Azure Disk Encryption, consulte [cenários de Azure Disk Encryption do Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto custa o Azure Disk Encryption?

Não há nenhum custo para criptografar discos de VM com Azure Disk Encryption, mas há encargos associados ao uso de Azure Key Vault. Para obter mais informações sobre os custos do Cofre de Chaves do Azure, consulte a página de preços [Cofre de chaves](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Como posso começar a usar o Azure Disk Encryption?

Para começar, leia as [visão geral do Azure Disk Encryption](disk-encryption-overview.md).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quais tamanhos de VM e sistemas operacionais dão suporte Azure Disk Encryption?

O artigo de [visão geral de Azure Disk Encryption](disk-encryption-overview.md) lista os [tamanhos de VM](disk-encryption-overview.md#supported-vm-sizes) e os [sistemas operacionais de vm](disk-encryption-overview.md#supported-operating-systems) que dão suporte a Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>Posso criptografar volumes de dados e de inicialização com o Azure Disk Encryption?

Você pode criptografar volumes de dados e de inicialização, mas não pode criptografar os dados sem primeiro criptografar o volume do sistema operacional.

Depois de criptografar o volume do sistema operacional, não há suporte para desabilitar a criptografia no volume do sistema operacional.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>Posso criptografar um volume não montado com Azure Disk Encryption?

Não, Azure Disk Encryption apenas criptografa volumes montados.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Como fazer girar segredos ou chaves de criptografia?

Para girar os segredos, basta chamar o mesmo comando usado originalmente para habilitar a criptografia de disco, especificando um Key Vault diferente. Para girar a chave de criptografia de chave, chame o mesmo comando usado originalmente para habilitar a criptografia de disco, especificando a nova criptografia de chave. 

>[!WARNING]
> - Se você tiver usado anteriormente [Azure Disk Encryption com o aplicativo do Azure ad](disk-encryption-windows-aad.md) especificando as credenciais do Azure ad para criptografar essa VM, será necessário continuar usando essa opção para criptografar sua VM. Você não pode usar Azure Disk Encryption nessa VM criptografada, pois esse não é um cenário com suporte, o que significa que não há suporte para a alternância do aplicativo AAD para essa VM criptografada ainda.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Como fazer adicionar ou remover uma chave de criptografia de chave se eu não utilizou originalmente uma?

Para adicionar uma chave de criptografia de chave, chame o comando Enable novamente passando o parâmetro Key Encryption Key. Para remover uma chave de criptografia de chave, chame o comando Enable novamente sem o parâmetro Key Encryption Key.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>O Azure Disk Encryption permite o recurso BYOK (Bring Your Own Key)?

Sim, você pode fornecer suas próprias chaves de criptografia de chave. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre os cenários de suporte a chaves de criptografia de chave, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>Posso usar uma chave de criptografia de chave criada pelo Azure?

Sim, você pode usar o Azure Key Vault para gerar uma chave de criptografia de chave para ser usada pela criptografia de disco do Azure. Essas chaves ficam protegidas no Azure Key Vault, que é o repositório de chaves do Azure Disk Encryption. Para obter mais informações sobre a chave de criptografia de chave, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>Posso usar um serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia?

Você não pode usar o serviço de gerenciamento de chaves local ou HSM para proteger as chaves de criptografia com o Azure Disk Encryption. Você só pode usar o serviço Azure Key Vault para proteger as chaves de criptografia. Para obter mais informações sobre os principais cenários de suporte de chave de criptografia, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption?

Há pré-requisitos para o Azure Disk Encryption. Consulte o artigo [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md) para criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso de criptografia de disco para habilitar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte de chave de criptografia, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quais são os pré-requisitos para configurar o Azure Disk Encryption com um aplicativo do Azure AD (versão anterior)?

Há pré-requisitos para o Azure Disk Encryption. Consulte o [Azure Disk Encryption com conteúdo do Azure ad](disk-encryption-windows-aad.md) para criar um aplicativo de Azure Active Directory, criar um novo cofre de chaves ou configurar um cofre de chaves existente para acesso de criptografia de disco para habilitar a criptografia e proteger segredos e chaves. Para obter mais informações sobre os principais cenários de suporte de chave de criptografia, consulte [criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure ad](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>Ainda há suporte para o Azure Disk Encryption usando um aplicativo do Azure AD (versão anterior)?
Sim. Ainda há suporte para criptografia de disco usando um aplicativo do Azure AD. No entanto, ao criptografar novas VMs, é recomendável que você use o novo método em vez de criptografar com um aplicativo do Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>Posso migrar VMs que foram criptografadas com um aplicativo do Azure AD para a criptografia sem um aplicativo do Azure AD?
  Atualmente, não há um caminho de migração direto para computadores que foram criptografados com um aplicativo do Azure AD para criptografia sem um aplicativo do Azure AD. Além disso, também não há um caminho direto da criptografia sem um aplicativo do Azure AD para a criptografia com o aplicativo do AD. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>À qual versão do Azure PowerShell o Azure Disk Encryption dá suporte?

Use a versão mais recente do SDK do Azure PowerShell para configurar o Azure Disk Encryption. Baixe a última versão do [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). O Azure Disk Encryption *não* é suportado pela versão 1.1.0 do SDK do Azure.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>O que é o disco "Volume Bek" ou "/mnt/azure_bek_disk"?

O "volume Bek" é um volume de dados local que armazena com segurança as chaves de criptografia para VMs do Azure criptografadas.

> [!NOTE]
> Não exclua ou edite nenhum conteúdo neste disco. Não desmonte o disco, uma vez que a presença da chave de criptografia é necessária para operações de criptografia na VM IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual método de criptografia é usado pela criptografia de disco do Azure?

Azure Disk Encryption usa o método de criptografia AES256 do BitLocker (AES256WithDiffuser em versões anteriores ao Windows Server 2012). 

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se eu usar EncryptFormatAll e especificar todos os tipos de volume, ele apagará os dados em unidades de dados que já tivermos criptografado?
Não, os dados não serão apagados de unidades de dados que já tiverem sido criptografadas usando o Azure Disk Encryption. Semelhante ao modo como o EncryptFormatAll não criptografou novamente a unidade do sistema operacional, ele não criptografará novamente a unidade de dados já criptografada. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>Posso fazer backup e restaurar uma VM criptografada? 

O backup do Azure fornece um mecanismo para fazer backup e restaurar VMs criptografadas na mesma assinatura e região.  Para obter instruções, consulte [fazer backup e restaurar máquinas virtuais criptografadas com o backup do Azure](../../backup/backup-azure-vms-encryption.md).  Atualmente, não há suporte para a restauração de uma VM criptografada em uma região diferente.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Onde posso fazer perguntas ou fornecer comentários?

Você pode fazer perguntas ou fornecer comentários no [Fórum do Azure Disk Encryption](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Próximas etapas
Neste documento, você aprendeu mais sobre as perguntas mais frequentes relativas ao Azure Disk Encryption. Para obter mais informações sobre esse serviço, veja os seguintes artigos:

- [Visão geral do Azure Disk Encryption](disk-encryption-overview.md)
- [Aplicar a criptografia de disco na Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Criptografia de dados em repouso do Azure](../../security/fundamentals/encryption-atrest.md)
