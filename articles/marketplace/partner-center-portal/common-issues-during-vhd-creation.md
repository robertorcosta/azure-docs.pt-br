---
title: Problemas comuns durante a criação do VHD (Perguntas Frequentes)
description: Perguntas frequentes sobre problemas comuns ao criar um VHD (disco rígido virtual).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 04/09/2020
ms.openlocfilehash: f4d30cdb931d6523eba3aac003caeee38a8c024d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653521"
---
# <a name="common-issues-during-vhd-creation"></a>Problemas comuns durante a criação do VHD

Essas perguntas frequentes abordam os problemas comuns que você pode encontrar ao criar um VHD (disco rígido virtual) para sua oferta de Máquina Virtual do Azure.

## <a name="how-do-i-create-a-vm-from-the-azure-portal-using-a-vhd-in-premium-storage"></a>Como criar uma VM do portal do Azure usando um VHD no armazenamento Premium?

O Azure Marketplace atualmente não dá suporte para a criação de ofertas de VM de imagens no armazenamento gerenciado ou de Armazenamento Premium do Azure. Para detalhes, confira [Visão geral do Azure Managed Disks](../../virtual-machines/managed-disks-overview.md).

## <a name="can-i-use-generation-2-vms-for-offers"></a>É possível usar VMs de Geração 2 para as ofertas?

Não, somente VHDs da Geração 1 são compatíveis. No entanto, estamos atualmente trabalhando com a Equipe da Plataforma Microsoft Azure para investigar o suporte para VMs de Geração 2. Para mais detalhes, confira [Devo criar uma máquina virtual de geração 1 ou 2 no Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="how-do-i-change-the-name-of-the-host"></a>Como posso alterar o nome do host?

Você não pode. Depois que uma VM é criada, os usuários (incluindo os proprietários) não podem atualizar o nome do host.

## <a name="how-do-i-reset-the-remote-desktop-service-or-its-sign-in-password"></a>Como redefinir o serviço de Área de Trabalho Remota ou sua senha de entrada?

Os seguintes artigos explicam como executar redefinições RDS para VMs baseadas em Windows e Linux:

* [Como redefinir o serviço Área de Trabalho Remota ou sua senha de logon em uma VM do Windows](/azure/virtual-machines/troubleshooting/reset-rdp)
* [Como redefinir uma senha de VM do Linux ou chave SSH, corrigir a configuração de SSH e verificar a consistência de disco usando a extensão VMAccess](/azure/virtual-machines/extensions/vmaccess)

## <a name="how-do-i-generate-new-ssh-certificates"></a>Como posso gerar novos certificados SSH?

A geração de certificados é explicada na [certificação de imagem de VM do Azure](https://aks.ms/CertifyVMimage).

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma VPN (rede virtual privada) para trabalhar com minhas VMs?

Se você está usando o Modelo de implantação do Azure Resource Manager, há três opções:

* [Criar um gateway de VPN baseado em rotas usando o portal do Azure](../../vpn-gateway/create-routebased-vpn-gateway-portal.md)
* [Criar um gateway de VPN baseado em rotas usando o Azure PowerShell](../../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
* [Criar um Gateway de VPN baseado em rota usando CLI](../../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para a execução de software de servidor Microsoft em VMs do Azure?

Encontre detalhes em [Suporte de software para servidores da Microsoft para Máquinas Virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="do-virtual-machines-have-unique-identifiers-associated-with-them"></a>As máquinas virtuais têm identificadores exclusivos associados a eles?

Sim, se hospedados no Azure. O Azure atribui um identificador exclusivo, chamado de [ID exclusiva da Máquina Virtual do Azure](https://blogs.msdn.microsoft.com/wasimbloch/2016/10/20/azure-virtual-machine-unique-id/) para cada recurso de VM que é criado. Para obter detalhes, confira ID exclusiva da Máquina Virtual do Azure. Você também pode obter esse identificador por meio da [API de lista](https://docs.microsoft.com/rest/api/compute/virtualmachines/list).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Em uma VM, como gerencio a extensão de script personalizado na tarefa de inicialização?

Para detalhes sobre como usar a extensão de script personalizado usando o módulo do Azure PowerShell, os modelos do Azure Resource Manager e as etapas em sistemas Windows para solucionar problemas, confira [Extensão de script personalizado para Windows](/azure/virtual-machines/extensions/custom-script-windows).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>São aplicativos de 32 bits ou serviços com suporte no Azure Marketplace?

Em geral, não. Os serviços padrão para VMs do Azure e sistemas operacionais com suporte são 64-bit. Embora a maioria dos sistemas operacionais de 64 bits dê suporte a versões de 32 bits de aplicativos para compatibilidade com versões anteriores, não há suporte para o uso de aplicativos de 32 bits como parte de sua solução de VM e isso não é recomendável. Crie novamente seu aplicativo como um projeto de 64 bits.

Para obter mais informações, consulte estes artigos:

* [Aplicativos de 32 bits em execução](https://docs.microsoft.com/windows/desktop/WinProg64/running-32-bit-applications)
* [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
* [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: o VHD já está registrado com o repositório de imagens como o recurso

Sempre que estou tento criar uma imagem de minhas VHDs, recebo o erro "O VHD já está registrado no repositório de imagens como o recurso" no Azure PowerShell. Eu não criei uma imagem antes, nem encontrei uma imagem com esse nome no Azure. Como resolver isso?

Esse problema geralmente aparece quando você cria uma VM de um VHD que tem um bloqueio. Confirme se não há VM alocada desse VHD e, em seguida, repita a operação. Se o problema persistir, abra um tíquete de suporte. Confira [Suporte do Partner Center](support.md).
