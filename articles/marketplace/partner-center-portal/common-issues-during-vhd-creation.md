---
title: Problemas comuns durante a criação do VHD (Perguntas Frequentes)
description: Perguntas frequentes sobre problemas comuns ao criar um disco rígido virtual (VHD).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266260"
---
# <a name="common-issues-during-vhd-creation"></a>Problemas comuns durante a criação de VHD

> [!NOTE]
> Estamos mudando o gerenciamento de suas ofertas de VM do Portal de Parceiros na Nuvem para o Partner Center. Até que suas ofertas sejam migradas, continue a seguir as instruções em [problemas comuns durante a criação de VHD (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) no Cloud Partner Portal para gerenciar suas ofertas.

Essas perguntas freqüentes (FAQ) cobrem problemas comuns que você pode encontrar ao criar um disco rígido virtual (VHD) para sua oferta da Azure Virtual Machine.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Como criar uma VM do portal Azure usando um VHD em armazenamento premium?

Atualmente, o Azure Marketplace não oferece a criação de ofertas de VM a partir de imagens no armazenamento gerenciado ou no Azure Premium Storage. Para obter detalhes, consulte [visão geral dos discos gerenciados do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Posso usar vms da Geração 2 para ofertas?

Não, apenas VHDs de geração 1 são suportados. No entanto, estamos atualmente trabalhando com a equipe de plataforma do Microsoft Azure para investigar o suporte para VMs da Geração 2. Para mais [detalhes, veja Devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Como posso alterar o nome do host?

Não é possível fazer isso. Depois que uma VM é criada, os usuários (incluindo proprietários) não podem atualizar o nome do host.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como redefinir o serviço de desktop remoto ou sua senha de login?

Esses artigos explicam como executar redefinições RDS para VMs baseadas em Windows e Linux:

* [Como redefinir o serviço Área de Trabalho Remota ou sua senha de logon em uma VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Como redefinir uma senha de VM do Linux ou chave SSH, corrigir a configuração de SSH e verificar a consistência de disco usando a extensão VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Como gerar novos certificados SSH?

A geração de certificados é explicada na [certificação de imagem Azure VM](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configuro uma rede virtual privada (VPN) para trabalhar com minhas VMs?

Se você estiver usando o modelo de implantação do Azure Resource Manager, você tem três opções:

* [Criar um gateway de VPN baseado em rotas usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Crie um gateway VPN baseado em rota usando o Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Criar um Gateway de VPN baseado em rota usando CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para a execução de software de servidor Microsoft em VMs do Azure?

Você pode encontrar detalhes no [suporte de software do microsoft server para máquinas virtuais Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores exclusivos associados a eles?

Sim, se hospedados no Azure. O Azure atribui um identificador exclusivo, chamado [Azure Virtual Machine Unique ID](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), a cada novo recurso VM criado. Para obter detalhes, consulte Azure Virtual Machine Unique ID. Você também pode obter este identificador através da [API lista](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Em uma VM, como faço para gerenciar a extensão de script personalizada na tarefa de inicialização?

Para obter detalhes sobre o uso da extensão de script personalizada usando o módulo Azure PowerShell, modelos do Azure Resource Manager e etapas de solução de problemas em sistemas Windows, consulte [Extensão de script personalizada para Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Aplicativos ou serviços de 32 bits são suportados no Azure Marketplace?

Em geral, não. Os serviços padrão para VMs do Azure e sistemas operacionais com suporte são 64-bit. Embora a maioria dos sistemas operacionais de 64 bits suporte mera versões de aplicativos de 32 bits para compatibilidade retrógrada, o uso de aplicativos de 32 bits como parte de sua solução VM não é suportado e altamente desencorajado. Recrie seu aplicativo como um projeto de 64 bits.

Para obter mais informações, consulte estes artigos:

* [Aplicativos de 32 bits em execução](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: O VHD já está registrado no repositório de imagens como recurso

Toda vez que tento criar uma imagem dos meus VHDs, recebo o erro "VHD já está registrado com repositório de imagem como recurso" no Azure PowerShell. Eu não criei nenhuma imagem antes nem encontrei nenhuma imagem com esse nome no Azure. Como resolver isso?

Esse problema geralmente aparece se você criou uma VM de um VHD que tem um bloqueio nele. Confirme se não há VM alocado a partir deste VHD e, em seguida, tente novamente a operação. Se esse problema continuar, abra um bilhete de suporte. Consulte [Suporte para Centro de Parceiros](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
