---
title: Associe uma política de firewall de aplicativos da Web com um Gateway de aplicativo Azure existente
description: Aprenda a associar uma política de Firewall de aplicativos da Web com um Gateway de aplicativo Azure existente.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083899"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associar uma política WAF a um gateway de aplicativo existente

Você pode usar o Azure PowerShell para [criar uma política WAF,](create-waf-policy-ag.md)mas você pode já ter um Gateway de aplicativo e só deseja associar uma política WAF a ela. Neste artigo, você faz exatamente isso; você cria uma política WAF e a associa a um Gateway de aplicativo já existente. 

1. Obtenha sua política de gateway de aplicativo e firewall. Se você não tiver uma Política de Firewall existente, consulte o passo 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Opcional) Crie uma política de firewall.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Se você está criando esta Diretiva WAF para a transição de uma configuração WAF para uma diretiva WAF, então a Diretiva precisa ser uma cópia exata da sua antiga Configuração. Isso significa que toda exclusão, regra aduaneira, grupo de regras desativado, etc. precisa ser exatamente o mesmo que é na Configuração WAF.
3. (Opcional) Você pode configurar a política waf para atender às suas necessidades. Isso inclui regras personalizadas, desabilitação de regras/grupos de regras, exclusões, definição de limites de upload de arquivos, etc. Se você pular esta etapa, todos os padrões serão selecionados. 
   
4. Salve a diretiva e anexe-a ao gateway de aplicativo. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre regras personalizadas.](configure-waf-custom-rules.md)
