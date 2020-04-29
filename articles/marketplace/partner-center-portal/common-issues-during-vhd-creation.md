---
title: Problemas comuns durante a criação do VHD (Perguntas Frequentes)
description: Perguntas frequentes sobre problemas comuns ao criar um VHD (disco rígido virtual).
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
ms.date: 04/09/2020
ms.openlocfilehash: a47bece4df7263dbc29ec19510c3ea422bb3f4cf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81266260"
---
# <a name="common-issues-during-vhd-creation"></a>Problemas comuns durante a criação do VHD

> [!NOTE]
> Estamos movendo o gerenciamento de suas ofertas de VM do Azure de Portal do Cloud Partner para o Partner Center. Até que suas ofertas sejam migradas, continue seguindo as instruções em [problemas comuns durante a criação do VHD (FAQ)](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-common-vhd-creation-issues) em portal do Cloud Partner para gerenciar suas ofertas.

Essas perguntas frequentes abordam os problemas comuns que você pode encontrar ao criar um VHD (disco rígido virtual) para sua oferta de máquina virtual do Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Como fazer criar uma VM do portal do Azure usando um VHD no armazenamento Premium?

Atualmente, o Azure Marketplace não dá suporte à criação de ofertas de VM de imagens no armazenamento gerenciado ou no armazenamento Premium do Azure. Para obter detalhes, consulte [visão geral do Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

## <a name="can-i-use-generation-2-vms-for-offers"></a>Posso usar VMs de geração 2 para ofertas?

Não, somente VHDs de geração 1 têm suporte. No entanto, estamos trabalhando atualmente com a equipe da plataforma Microsoft Azure para investigar o suporte para VMs de geração 2. Para obter detalhes, consulte [devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Como posso alterar o nome do host?

Não é possível fazer isso. Depois que uma VM é criada, os usuários (incluindo os proprietários) não podem atualizar o nome do host.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como fazer redefinir o serviço de Área de Trabalho Remota ou sua senha de entrada?

Estes artigos explicam como executar redefinições de RDS para VMs baseadas em Windows e Linux:

* [Como redefinir o serviço Área de Trabalho Remota ou sua senha de logon em uma VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-reset-rdp/)
* [Como redefinir uma senha de VM do Linux ou chave SSH, corrigir a configuração de SSH e verificar a consistência de disco usando a extensão VMAccess](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-classic-reset-access/)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Como fazer gerar novos certificados SSH?

A geração de certificados é explicada na [certificação de imagem de VM do Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como fazer configurar uma VPN (rede virtual privada) para trabalhar com minhas VMs?

Se você estiver usando o modelo de implantação Azure Resource Manager, terá três opções:

* [Criar um gateway de VPN baseado em rotas usando o portal do Azure](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-portal)
* [Criar um gateway de VPN baseado em rota usando Azure PowerShell](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-powershell)
* [Criar um Gateway de VPN baseado em rota usando CLI](https://docs.microsoft.com/azure/vpn-gateway/create-routebased-vpn-gateway-cli)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para a execução de software de servidor Microsoft em VMs do Azure?

Você pode encontrar detalhes no [suporte de software de servidor da Microsoft para máquinas virtuais Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores exclusivos associados a eles?

Sim, se hospedados no Azure. O Azure atribui um identificador exclusivo, chamado [ID exclusiva da máquina virtual do Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/), a cada novo recurso da VM criado. Para obter detalhes, consulte ID exclusiva da máquina virtual do Azure. Você também pode obter esse identificador por meio da [API de lista](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Em uma VM, como gerenciar a extensão de script personalizado na tarefa de inicialização?

Para obter detalhes sobre como usar a extensão de script personalizado usando o módulo Azure PowerShell, os modelos de Azure Resource Manager e as etapas de solução de problemas em sistemas Windows, consulte [extensão de script personalizado para Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-extensions-customscript/).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>Há suporte para aplicativos de 32 bits ou serviços no Azure Marketplace?

Em geral, não. Os serviços padrão para VMs do Azure e sistemas operacionais com suporte são 64-bit. Embora a maioria dos sistemas operacionais de 64 bits dê suporte a versões de 32 bits de aplicativos para compatibilidade com versões anteriores, usar aplicativos de 32 bits como parte de sua solução de VM não tem suporte e não é recomendável. Recrie seu aplicativo como um projeto de 64 bits.

Para obter mais informações, consulte estes artigos:

* [Aplicativos de 32 bits em execução](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: o VHD já está registrado com o repositório de imagens como o recurso

Toda vez que tento criar uma imagem de meus VHDs, obtenho o erro "o VHD já está registrado com o repositório de imagens como o recurso" em Azure PowerShell. Eu não criei nenhuma imagem antes nem encontrei nenhuma imagem com esse nome no Azure. Como resolver isso?

Esse problema geralmente aparece se você criou uma VM de um VHD que tem um bloqueio. Confirme se não há nenhuma VM alocada a partir deste VHD e repita a operação. Se esse problema continuar, abra um tíquete de suporte. Consulte [suporte para o Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/support).
