---
title: Atualize uma WAN Virtual do Azure de Basic para Standard - Portal Azure | Microsoft Docs
description: Você pode atualizar seu tipo de WAN virtual para maior funcionalidade.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 83fd5bafb5496908403c50dc0e000fd33a836c95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515805"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Atualize uma WAN virtual de Básico para Padrão

Este artigo ajuda você a atualizar uma WAN básica para uma WAN padrão. Um tipo WAN 'Básico' cria todos os hubs dentro dele como hubs SKU básicos. Em um hub Basic, você está limitado apenas à funcionalidade VPN de site para local. Um tipo WAN 'Padrão' cria todos os hubs dentro dele como hubs SKU padrão. Quando você usa hubs Standard, você pode ativar o ExpressRoute, user (Point-to-site) VPN, um hub de malha completo e o trânsito VNet-to-VNet através dos hubs do Azure.

A tabela a seguir mostra as configurações disponíveis para cada tipo de WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Para alterar o tipo de WAN virtual

1. Na página para sua WAN virtual, **selecione Configuração** para abrir a página Configuração.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/1.png)
2. Para o tipo DE WAN virtual, selecione **Padrão** na lista de paradas.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/2.png)
3. Entenda que se você atualizar para uma WAN virtual padrão, você não poderá reverter para uma WAN virtual básica. Selecione **Confirmar** se deseja atualizar.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/4.png)
4. Uma vez salva a alteração, sua página de WAN virtual se parece com este exemplo.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).