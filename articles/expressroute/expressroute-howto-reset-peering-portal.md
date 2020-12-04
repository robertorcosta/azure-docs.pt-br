---
title: 'Azure ExpressRoute: redefinir o emparelhamento de circuito usando o portal do Azure'
description: Saiba como desabilitar e habilitar emparelhamentos de um circuito do Azure ExpressRoute usando o portal do Azure.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: d4d6b0b0cce4f5304f7c5790ef2bda05633be52f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581522"
---
# <a name="reset-expressroute-circuit-peerings-use-the-azure-portal"></a>Redefinir emparelhamentos de circuito do ExpressRoute use o portal do Azure

Este artigo descreve como desabilitar e habilitar emparelhamentos de um circuito do ExpressRoute usando o portal do Azure. Quando você desabilita um emparelhamento, a sessão BGP para a conexão primária e a secundária do circuito do ExpressRoute serão desligadas. Você perderá a conectividade por meio desse emparelhamento para a Microsoft. Quando você habilita um emparelhamento, a sessão BGP no primário e a conexão secundária do circuito do ExpressRoute serão ativadas. Você reconhecerá a conectividade por meio desse emparelhamento para a Microsoft. Você pode habilitar e desabilitar o Microsoft Peering e o Azure Private Peering em um circuito do ExpressRoute de forma independente. Na primeira vez que você configurar os emparelhamentos no circuito do ExpressRoute, os emparelhamentos serão habilitados por padrão.

Há alguns cenários em que você pode achar útil redefinir seus peerings da ExpressRoute.
* Teste seu design de recuperação de desastres e implementação. Por exemplo, você tem dois circuitos ExpressRoute. Você pode desabilitar os peerings de um circuito e forçar o tráfego de sua rede a falhar no outro circuito.
* Habilite a detecção de encaminhamento bidirecional (BFD) no emparelhamento privado do Azure ou no emparelhamento da Microsoft do circuito do ExpressRoute. O BFD será habilitado por padrão no emparelhamento privado do Azure se o circuito do ExpressRoute for criado após 1 2018 de agosto e no emparelhamento da Microsoft. Se o circuito do ExpressRoute for criado após janeiro de 10 2020. Se o circuito foi criado antes dessa, BFD não foi habilitado. Você pode habilitar BFD desabilitando o emparelhamento e reabilitando a ele. 

### <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Em um navegador, acesse o [Portal do Azure](https://portal.azure.com) e entre com sua conta do Azure.

## <a name="reset-a-peering"></a>Redefinir um emparelhamento

1. Selecione o circuito para o qual você deseja fazer alterações de configuração de emparelhamento.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Lista de circuitos do ExpressRoute":::

1. Selecione a configuração de emparelhamento que você deseja habilitar ou desabilitar.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Visão geral do circuito do ExpressRoute":::

1. Desmarque **habilitar emparelhamento** e selecione **salvar** para desabilitar a configuração de emparelhamento.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Desabilitar emparelhamento privado":::

1. Você pode habilitar o emparelhamento novamente marcando **habilitar emparelhamento** e selecionar **salvar**.

## <a name="next-steps"></a>Próximas etapas
Se precisar de ajuda para solucionar um problema de ExpressRoute, confira os seguintes artigos:
* [Verificando a conectividade do ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* [Solucionando problemas de desempenho de rede](expressroute-troubleshooting-network-performance.md)
