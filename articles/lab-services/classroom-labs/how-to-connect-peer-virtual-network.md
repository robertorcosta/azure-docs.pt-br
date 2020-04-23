---
title: Conecte-se a uma rede de pares no Azure Lab Services | Microsoft Docs
description: Aprenda a conectar sua rede de laboratório com outra rede como um par. Por exemplo, conecte sua rede de escolas/universidades no local com a rede virtual do Lab no Azure.
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
ms.openlocfilehash: 6ed0d743f9b9cdc136b8f52f4d9d02583fc63eb9
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870188"
---
# <a name="connect-your-labs-network-with-a-peer-virtual-network-in-azure-lab-services"></a>Conecte a rede do seu laboratório com uma rede virtual de pares no Azure Lab Services

Este artigo fornece informações sobre como espiar sua rede de laboratórios com outra rede.

## <a name="overview"></a>Visão geral

O peering de rede virtual permite conectar perfeitamente as redes virtuais do Azure. Uma vez emparelhadas, as redes virtuais aparecerão como uma para fins de conectividade. O tráfego entre as máquinas virtuais nas redes virtuais emparelhadas será roteado por meio da infraestrutura de backbone da Microsoft, assim como o tráfego é roteado entre as máquinas virtuais na mesma rede virtual somente por endereços IP privados. Para obter mais informações, consulte [Emparelhamento de rede virtual do Azure](../../virtual-network/virtual-network-peering-overview.md).

Você pode precisar conectar a rede do seu laboratório com uma rede virtual de pares em alguns cenários, incluindo os seguintes:

- As máquinas virtuais no laboratório têm software que se conecta a servidores de licença no local para adquirir licença
- As máquinas virtuais no laboratório precisam ter acesso a conjuntos de dados (ou quaisquer outros arquivos) nos compartilhamentos de rede da universidade.

Certas redes locais estão conectadas à Rede Virtual Azure, seja através do [ExpressRoute](../../expressroute/expressroute-introduction.md) ou [do Virtual Network Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md). Esses serviços devem ser configurados fora do Azure Lab Services. Para saber mais sobre como conectar uma rede local ao Azure usando o ExpressRoute, consulte [a visão geral do ExpressRoute](../../expressroute/expressroute-introduction.md). Para conectividade no local usando um Gateway de Rede Virtual, o gateway, a rede virtual especificada e a conta de laboratório devem estar todas na mesma região.

> [!NOTE]
> Ao criar uma Rede Virtual Azure que será acompanhada por uma conta de laboratório, é importante entender como a região da rede virtual impacta onde os laboratórios de sala de aula são criados.  Para obter mais informações, consulte a seção do guia do administrador sobre [regiões\locais.](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#regionslocations)

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configurar no momento da criação da conta de laboratório

Durante a criação da nova [conta de laboratório,](tutorial-setup-lab-account.md)você pode escolher uma rede virtual existente que aparece na lista de paradas de **rede virtual Peer** na guia **Avançado.**  A lista só mostrará redes virtuais na mesma região que a conta do laboratório. A rede virtual selecionada está conectada (peered) a laboratórios criados sob a conta do laboratório.  Todas as máquinas virtuais em laboratórios que são criadas após a realização dessa mudança terão acesso aos recursos na rede virtual peered.

![Selecione o VNet para peer](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer.png)

### <a name="address-range"></a>Intervalo de endereços

Há também uma opção para fornecer **linha de endereços** para máquinas virtuais para os laboratórios.  A propriedade **''''Linha endereço'** só se aplica se **a rede virtual Peer** estiver habilitada para o laboratório.  Se o intervalo de endereços for fornecido, todas as máquinas virtuais nos laboratórios sob a conta do laboratório serão criadas nessa faixa de endereços. O intervalo de endereços deve estar na notação CIDR (por exemplo, 10.20.0.0/20) e não se sobrepor a nenhuma faixa de endereço existente.  Ao fornecer uma gama de endereços, é importante pensar no número de laboratórios que serão *criados* e fornecer uma gama de endereços para acomodar isso. A Lab Services assume um máximo de 512 máquinas virtuais por laboratório.  Por exemplo, um intervalo ip com '/23' pode criar apenas um laboratório.  Um intervalo com um '/21' permitirá a criação de quatro laboratórios.

Se a **faixa de endereço** não for especificada, os Serviços de Laboratório usarão o intervalo de endereçopadrão dado a ele pelo Azure ao criar a rede virtual a ser acompanhada pela sua rede virtual.  O alcance é muitas vezes algo como 10.x.0.0/16.  Isso pode levar à sobreposição do intervalo ip, por isso certifique-se de especificar e abordar o intervalo nas configurações do laboratório ou verificar o intervalo de endereços da sua rede virtual sendo espiado.

## <a name="configure-after-the-lab-is-created"></a>Configure depois que o laboratório for criado

A mesma propriedade pode ser habilitada a partir da guia de **configuração do Labs** da página Conta do **Laboratório** se você não configurar uma rede de pares no momento da criação da conta do laboratório. A alteração feita nesta configuração aplica-se apenas aos laboratórios criados após a alteração. Como você pode ver na imagem, você pode ativar ou desativar **a rede virtual Peer** para laboratórios na conta de laboratório.

![Habilite ou desative o peering do VNet após a criação do laboratório](../media/how-to-connect-peer-virtual-network/select-vnet-to-peer-existing-lab.png)

Quando você seleciona uma rede virtual para o campo **de rede virtual Peer,** o criador do **laboratório permitirá que a** opção de localização do laboratório seja desativada. Isso porque os laboratórios na conta do laboratório devem estar na mesma região que o laboratório conta para que eles se conectem com recursos na rede virtual de pares.

> [!IMPORTANT]
> A configuração de rede virtual peered aplica-se apenas aos laboratórios que são criados após a alteração, não aos laboratórios existentes.

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Permitir que o criador do laboratório escolha a localização do laboratório](allow-lab-creator-pick-lab-location.md)
- [Anexar uma galeria de imagens compartilhadas a um laboratório](how-to-attach-detach-shared-image-gallery.md)
- [Adicione um usuário como proprietário de laboratório](how-to-add-user-lab-owner.md)
- [Exibir configurações de firewall para um laboratório](how-to-configure-firewall-settings.md)
- [Configure outras configurações para um laboratório](how-to-configure-lab-accounts.md)
