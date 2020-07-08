---
title: Atualizar uma WAN virtual do Azure do básico para o Standard-portal do Azure | Microsoft Docs
description: Você pode atualizar seu tipo de WAN virtual para obter mais funcionalidade.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 769aa6c0d546b7a9bcbe355136bad811fb4f47b3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84753317"
---
# <a name="upgrade-a-virtual-wan-from-basic-to-standard"></a>Atualizar uma WAN virtual do básico para o Standard

Este artigo ajuda você a atualizar uma WAN básica para uma WAN padrão. Um tipo de WAN "básico" cria todos os hubs dentro dele como hubs de SKU básicos. Em um hub básico, você está limitado apenas à funcionalidade de VPN site a site. Um tipo de WAN "Standard" cria todos os hubs dentro dele como hubs SKU padrão. Ao usar hubs padrão, você pode habilitar o ExpressRoute, a VPN de usuário (ponto a site), um hub de malha completo e um trânsito de VNet para VNet por meio dos hubs do Azure.

A tabela a seguir mostra as configurações disponíveis para cada tipo de WAN:

[!INCLUDE [Basic and Standard SKUs](../../includes/virtual-wan-standard-basic-include.md)]

## <a name="to-change-the-virtual-wan-type"></a>Para alterar o tipo de WAN virtual

1. Na página de sua WAN virtual, selecione **configuração** para abrir a página de configuração.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/1.png)
2. Para o tipo de WAN virtual, selecione **Standard** na lista suspensa.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/2.png)
3. Entenda que, se você atualizar para uma WAN virtual padrão, não poderá reverter de volta para uma WAN virtual básica. Selecione **confirmar** se você deseja atualizar.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/4.png)
4. Depois que a alteração tiver sido salva, sua página WAN virtual será semelhante a este exemplo.

   ![Diagrama de WAN virtual](./media/upgrade-virtual-wan/5.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).