---
title: Conectar-se a uma rede de mesmo nível no Azure Lab Services | Microsoft Docs
description: Saiba como conectar sua rede de laboratório a outra rede como um par. Por exemplo, conecte sua rede de estudante/Universidade local com a rede virtual do laboratório no Azure.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 5e013011f81542aa279ba9276a6a1aac01eb9e41
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77443169"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Conecte a rede do seu laboratório a uma rede virtual de mesmo nível no Azure Lab Services 
Este artigo fornece informações sobre como emparelhar sua rede de laboratórios com outra rede. 

## <a name="overview"></a>Visão geral
O emparelhamento de rede virtual permite que você conecte diretamente as redes virtuais do Azure. Uma vez emparelhadas, as redes virtuais aparecerão como uma para fins de conectividade. O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas é roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre máquinas virtuais na mesma rede virtual, somente por meio de endereços IP privados. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Talvez seja necessário conectar a rede do laboratório a uma rede virtual de mesmo nível em alguns cenários, incluindo as seguintes:

- As máquinas virtuais no laboratório têm software que se conecta a servidores de licença locais para adquirir licença
- As máquinas virtuais no laboratório precisam de acesso a conjuntos de dados (ou qualquer outro arquivo) nos compartilhamentos de rede da Universidade. 

Determinadas redes locais são conectadas à rede virtual do Azure por meio do [ExpressRoute](../../expressroute/expressroute-introduction.md) ou do [Gateway de rede virtual](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Esses serviços devem ser configurados fora do Azure Lab Services. Para saber mais sobre como conectar uma rede local ao Azure usando o ExpressRoute, consulte [visão geral do expressroute](../../expressroute/expressroute-introduction.md). Para a conectividade local usando um gateway de rede virtual, o gateway, a rede virtual especificada e a conta de laboratório devem estar na mesma região.

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta do laboratório
Durante a criação da nova conta de laboratório, você pode escolher uma rede virtual existente que é mostrada na lista suspensa **rede virtual par** na guia **avançado** . A rede virtual selecionada está conectada (emparelhada) a laboratórios criados na conta do laboratório. Todas as máquinas virtuais em laboratórios que são criadas depois de fazer essa alteração teriam acesso aos recursos na rede virtual emparelhada. 

![Selecionar VNet para emparelhar](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

> [!NOTE]
> Para obter instruções passo a passo detalhadas para criar uma conta de laboratório, consulte [Configurar uma conta de laboratório](tutorial-setup-lab-account.md)


## <a name="configure-after-the-lab-is-created"></a>Configurar após a criação do laboratório
A mesma propriedade pode ser habilitada na guia **configuração de laboratórios** da página de conta do **laboratório** se você não tiver configurado uma rede de mesmo nível no momento da criação da conta do laboratório. As alterações feitas nessa configuração aplicam-se somente aos laboratórios criados após a alteração. Como você pode ver na imagem, você pode habilitar ou desabilitar a **rede virtual par** para laboratórios na conta do laboratório. 

![Habilitar ou desabilitar o emparelhamento VNet após a criação do laboratório](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png) 

Quando você seleciona uma rede virtual para o campo **rede virtual par** , a opção **permitir que o criador do laboratório selecione o local do laboratório** está desabilitada. É porque os laboratórios na conta do laboratório devem estar na mesma região que a conta do laboratório para que eles se conectem aos recursos na rede virtual do par. 

> [!IMPORTANT]
> Essa alteração de configuração aplica-se somente a laboratórios criados depois que a alteração é feita, não aos laboratórios existentes. 


## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Permitir que o criador de laboratório escolha o local do laboratório](allow-lab-creator-pick-lab-location.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicionar um usuário como um proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Definir outras configurações para um laboratório](how-to-configure-lab-accounts.md)