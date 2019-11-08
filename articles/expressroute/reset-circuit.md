---
title: 'Redefinir um circuito com falha-ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artigo ajuda a redefinir um circuito de ExpressRoute em um estado de falha.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748113"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Redefinir um circuito de ExpressRoute com falha

Quando uma operação em um circuito do Microsoft Azure ExpressRoute não for concluída, o circuito pode entrar em um estado 'Com falha'. Este artigo ajuda a redefinir um circuito da rota expressa com falha.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Redefinir um circuito

1. Instale a versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Para obter mais informações, consulte [Instalar e configurar o Azure PowerShell](/powershell/azure/install-az-ps).

2. Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudar a se conectar:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Especifique a assinatura que você quer usar.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Execute os seguintes comandos para redefinir um circuito em um estado com falha:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Agora o circuito deve ser íntegro. Abra um tíquete de suporte com [o suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se o circuito ainda estiver em um estado de falha.

## <a name="next-steps"></a>Próximas etapas

Abra um tíquete de suporte com o [suporte da Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.
