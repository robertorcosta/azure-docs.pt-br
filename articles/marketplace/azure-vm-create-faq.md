---
title: Perguntas comuns sobre a VM no Azure Marketplace
description: Perguntas comuns encontradas ao criar uma máquina virtual no Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: guide
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 10/15/2020
ms.openlocfilehash: d045af3b170d585b4bf1f8c57b7ba924c6b30695
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489779"
---
# <a name="common-questions-about-vm-in-azure-marketplace"></a>Perguntas comuns sobre a VM no Azure Marketplace

Essas perguntas frequentes abordam os problemas comuns que você pode encontrar ao criar uma oferta de VM (máquina virtual) no Azure Marketplace.

## <a name="how-do-i-configure-a-virtual-private-network-vpn-to-work-with-my-vms"></a>Como configurar uma VPN (rede virtual privada) para trabalhar com minhas VMs?

Se você está usando o Modelo de implantação do Azure Resource Manager, há três opções:

- [Criar um gateway de VPN baseado em rotas usando o portal do Azure](../vpn-gateway/tutorial-create-gateway-portal.md)
- [Criar um gateway de VPN baseado em rotas usando o Azure PowerShell](../vpn-gateway/create-routebased-vpn-gateway-powershell.md)
- [Criar um Gateway de VPN baseado em rota usando CLI](../vpn-gateway/create-routebased-vpn-gateway-cli.md)

## <a name="what-are-microsoft-support-policies-for-running-microsoft-server-software-on-azure-based-vms"></a>Quais são as políticas de suporte da Microsoft para a execução de software de servidor Microsoft em VMs do Azure?

Encontre detalhes em [Suporte de software para servidores da Microsoft para Máquinas Virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

## <a name="in-a-vm-how-do-i-manage-the-custom-script-extension-in-the-startup-task"></a>Em uma VM, como gerencio a extensão de script personalizado na tarefa de inicialização?

Para detalhes sobre como usar a extensão de script personalizado usando o módulo do Azure PowerShell, os modelos do Azure Resource Manager e as etapas em sistemas Windows para solucionar problemas, confira [Extensão de script personalizado para Windows](../virtual-machines/extensions/custom-script-windows.md).

## <a name="are-32-bit-applications-or-services-supported-in-azure-marketplace"></a>São aplicativos de 32 bits ou serviços com suporte no Azure Marketplace?

Não. Os serviços padrão para VMs do Azure e sistemas operacionais com suporte são 64-bit. Embora a maioria dos sistemas operacionais de 64 bits dê suporte a versões de 32 bits de aplicativos para compatibilidade com versões anteriores, não há suporte para o uso de aplicativos de 32 bits como parte de sua solução de VM e isso não é recomendável. Crie novamente seu aplicativo como um projeto de 64 bits.

Para obter mais informações, consulte estes artigos:

- [Aplicativos de 32 bits em execução](/windows/desktop/WinProg64/running-32-bit-applications)
- [Suporte para sistemas operacionais de 32 bits em máquinas virtuais do Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)
- [Suporte de software de servidor da Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)

## <a name="error-vhd-is-already-registered-with-image-repository-as-the-resource"></a>Erro: o VHD já está registrado com o repositório de imagens como o recurso

Sempre que estou tento criar uma imagem de minhas VHDs, recebo o erro "O VHD já está registrado no repositório de imagens como o recurso" no Azure PowerShell. Eu não criei uma imagem antes, nem encontrei uma imagem com esse nome no Azure. Como resolver isso?

Esse problema geralmente aparece quando você cria uma VM de um VHD que tem um bloqueio. Confirme se não há VM alocada desse VHD e, em seguida, repita a operação. Se o problema persistir, abra um tíquete de suporte. Confira [Suporte do Partner Center](support.md).

## <a name="how-do-i-test-a-hidden-preview-image"></a>Como fazer testar uma imagem de visualização oculta?

Você pode implantar imagens de visualização ocultas usando modelos de início rápido.
Para implantar uma imagem de visualização do Linux, 
1. Ir para este [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux), selecione "implantar no Azure". Isso deve levá-lo a portal do Azure
2. Em portal do Azure, selecione "Editar modelo".
3. No modelo JSON, pesquise imageReference e atualize o PublisherID, o OfferId, o SkuID e a versão da imagem. Para testar a imagem de visualização, acrescente "-PREVIEW" à OfferId.
 ![imagem](https://user-images.githubusercontent.com/79274470/110191995-71c7d500-7de0-11eb-9f3c-6a42f55d8f03.png)
4. Clique em Salvar
5. Preencha o restante dos detalhes. Examinar e criar



## <a name="next-steps"></a>Próximas etapas

- [Solução de problemas de certificação de VM](azure-vm-create-certification-faq.md)
