---
title: 'Configure a política de IPsec personalizada para Wan Virtual do Azure: Portal | Microsoft Docs'
description: Saiba como configurar a política de IPsec personalizada para wan virtual do Azure usando o portal.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: cherylmc
ms.openlocfilehash: f37d7f3bfac37253339aab3493fb2c444900e099
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73515740"
---
# <a name="configure-a-custom-ipsec-policy-for-virtual-wan-using-the-portal"></a>Configure uma política de IPsec personalizada para WAN Virtual usando o portal

Você pode configurar a diretiva IPsec personalizada para WAN Virtual no portal Azure. As políticas personalizadas são úteis quando você deseja que ambos os lados (no local e no gateway Azure VPN) usem as mesmas configurações para a Fase 1 e a Fase 2 do IKE.

## <a name="working-with-custom-policies"></a>Trabalhando com políticas personalizadas

[!INCLUDE [IPsec](../../includes/virtual-wan-ipsec-custom-include.md)]

## <a name="configure-a-policy"></a>Configurar uma política

1. **Localize o hub virtual**. Em um navegador, acesse o [Portal do Azure](https://aka.ms/azurevirtualwanpreviewfeatures) e entre com sua conta do Azure. Localize o hub virtual do seu site.
2. **Selecione o site vpn**. Na página do hub, selecione o site VPN para o qual você deseja configurar uma política personalizada.

   ![select](./media/virtual-wan-custom-ipsec-portal/locate.png)
3. **Edite a conexão VPN**. No **menu Contexto** **...**, selecione **Editar conexão VPN**.

   ![editar](./media/virtual-wan-custom-ipsec-portal/contextmenu.png)
4. **Configure as configurações**. Na página **editar conexão VPN,** configure as configurações das configurações. Selecione **Salvar** para salvar suas configurações.

   ![configurar e salvar](./media/virtual-wan-custom-ipsec-portal/edit.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a WAN Virtual, consulte a página [Visão geral de WAN Virtual](virtual-wan-about.md).