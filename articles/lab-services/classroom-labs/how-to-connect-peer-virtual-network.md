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
ms.date: 03/31/2020
ms.author: spelluru
ms.openlocfilehash: 9e53b6bdb041bfac5a82ed607b75b25ab0513f57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187997"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Conecte a rede do seu laboratório a uma rede virtual de mesmo nível no Azure Lab Services

Este artigo fornece informações sobre como emparelhar sua rede de laboratórios com outra rede.

## <a name="overview"></a>Visão geral

O emparelhamento de rede virtual permite que você conecte diretamente as redes virtuais do Azure. Uma vez emparelhadas, as redes virtuais aparecerão como uma para fins de conectividade. O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas será roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre as máquinas virtuais na mesma rede virtual somente por endereços IP privados. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Talvez seja necessário conectar a rede do laboratório a uma rede virtual de mesmo nível em alguns cenários, incluindo as seguintes:

- As máquinas virtuais no laboratório têm software que se conecta a servidores de licença locais para adquirir licença.
- As máquinas virtuais no laboratório precisam de acesso a conjuntos de dados (ou qualquer outro arquivo) nos compartilhamentos de rede da Universidade.

Determinadas redes locais são conectadas à rede virtual do Azure por meio do [ExpressRoute](../../expressroute/expressroute-introduction.md) ou do [Gateway de rede virtual](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Esses serviços devem ser configurados fora do Azure Lab Services. Para saber mais sobre como conectar uma rede local ao Azure usando o ExpressRoute, consulte [visão geral do expressroute](../../expressroute/expressroute-introduction.md). Para a conectividade local usando um gateway de rede virtual, o gateway, a rede virtual especificada e a conta de laboratório devem estar na mesma região.

> [!NOTE]
> Ao criar uma rede virtual do Azure que será emparelhada com uma conta de laboratório, é importante entender como a região da rede virtual afeta onde os laboratórios de sala de aula são criados.  Para obter mais informações, consulte a seção do guia do administrador em [regions\locations](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations).

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta do laboratório

Durante a criação da nova [conta de laboratório](tutorial-setup-lab-account.md), você pode escolher uma rede virtual existente que é mostrada na lista suspensa **rede virtual par** na guia **avançado** .  A lista só mostrará redes virtuais na mesma região que a conta do laboratório. A rede virtual selecionada está conectada (emparelhada) a laboratórios criados na conta do laboratório.  Todas as máquinas virtuais em laboratórios criados após a realização dessa alteração terão acesso aos recursos na rede virtual emparelhada.

![Selecionar VNet para emparelhar](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Intervalo de endereços

Também há uma opção para fornecer um **intervalo de endereços** para máquinas virtuais para os laboratórios.  A propriedade **intervalo de endereços** se aplica somente se a **rede virtual par** estiver habilitada para o laboratório.  Se o intervalo de endereços for fornecido, todas as máquinas virtuais nos laboratórios na conta do laboratório serão criadas nesse intervalo de endereços. O intervalo de endereços deve estar na notação CIDR (por exemplo, 10.20.0.0/20) e não se sobreponha a nenhum intervalo de endereços existente.  Ao fornecer um intervalo de endereços, é importante pensar no número de *laboratórios* que serão criados e fornecer um intervalo de endereços para acomodá-lo. Os serviços de laboratório assumem um máximo de 512 máquinas virtuais por laboratório.  Por exemplo, um intervalo de IP com '/23 ' pode criar apenas um laboratório.  Um intervalo com um '/21 ' permitirá a criação de quatro laboratórios.

Se o **intervalo de endereços** não for especificado, os serviços de laboratório usarão o intervalo de endereços padrão fornecido a ele pelo Azure ao criar a rede virtual a ser emparelhada com sua rede virtual.  O intervalo geralmente é algo como 10. x. 0,0/16.  Isso pode levar à sobreposição de intervalo de IP, portanto, certifique-se de especificar e endereçar o intervalo nas configurações do laboratório ou verificar o intervalo de endereços de sua rede virtual que está sendo emparelhado.

## <a name="configure-after-the-lab-is-created"></a>Configurar após a criação do laboratório

A mesma propriedade pode ser habilitada na guia **configuração de laboratórios** da página de conta do **laboratório** se você não tiver configurado uma rede de mesmo nível no momento da criação da conta do laboratório. As alterações feitas nessa configuração aplicam-se somente aos laboratórios criados após a alteração. Como você pode ver na imagem, você pode habilitar ou desabilitar a **rede virtual par** para laboratórios na conta do laboratório.

![Habilitar ou desabilitar o emparelhamento VNet após a criação do laboratório](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Quando você seleciona uma rede virtual para o campo **rede virtual par** , a opção **permitir que o criador do laboratório selecione o local do laboratório** está desabilitada. Isso ocorre porque os laboratórios na conta do laboratório devem estar na mesma região que a conta do laboratório para que eles se conectem aos recursos na rede virtual do par.

> [!IMPORTANT]
> A configuração de rede virtual emparelhada aplica-se somente a laboratórios criados após a alteração, e não para os laboratórios existentes.

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicionar um usuário como um proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Definir outras configurações para um laboratório](how-to-configure-lab-accounts.md)
