---
title: Reescrever cabeçalhos HTTP no Gateway de Aplicativo do Azure
description: Este artigo fornece informações sobre como reescrever cabeçalhos HTTP no Azure Application Gateway usando o Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 47fe6a5247622e3ad3b3720955068580e0329913
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "64947200"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>Reescreva os cabeçalhos de solicitação e resposta HTTP com o Gateway de aplicativo Do Azure - Azure PowerShell

Este artigo descreve como usar o Azure PowerShell para configurar uma instância [SKU do Application Gateway v2](<https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant>) para reescrever os cabeçalhos HTTP em solicitações e respostas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="before-you-begin"></a>Antes de começar

- Você precisa executar o Azure PowerShell localmente para concluir as etapas deste artigo. Você também precisa ter a versão 1.0.0 do módulo AZ ou posterior. Execute `Import-Module Az` e, em seguida, `Get-Module Az` para determinar a versão que você instalou. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Depois de verificar a versão do PowerShell, execute `Login-AzAccount` para criar uma conexão com o Azure.
- Você precisa ter uma instância SKU do Application Gateway v2. A reescrita de cabeçalhos não é suportada no V1 SKU. Se você não tiver o V2 SKU, crie uma instância [SKU do Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/tutorial-autoscale-ps) antes de começar.

## <a name="create-required-objects"></a>Criar objetos necessários

Para configurar a reescrita do cabeçalho HTTP, você precisa concluir essas etapas.

1. Crie os objetos necessários para reescrever o cabeçalho HTTP:

   - **Configuração requestHeader :** Usado para especificar os campos de cabeçalho de solicitação que você pretende reescrever e o novo valor para os cabeçalhos.

   - **ResponseHeaderConfiguration**: Usado para especificar os campos de cabeçalho de resposta que você pretende reescrever e o novo valor para os cabeçalhos.

   - **ActionSet**: Contém as configurações dos cabeçalhos de solicitação e resposta especificados anteriormente.

   - **Condição:** Uma configuração opcional. As condições de regravação avaliam o conteúdo das solicitações e respostas HTTP(S). A ação de reescrita ocorrerá se a solicitação ou resposta HTTP(S) corresponder à condição de reescrita.

     Se você associar mais de uma condição a uma ação, a ação só ocorre quando todas as condições são atendidas. Em outras palavras, a operação é uma operação lógica e.

   - **ReescreverRegra**: Contém várias combinações de ação de regravação/ reescrita.

   - **Sequência de regras**: Uma configuração opcional que ajuda a determinar a ordem na qual as regras de reescrita são executadas. Essa configuração é útil quando você tem várias regras de reescrita em um conjunto de reescrita. Uma regra de reescrita que tem um valor de seqüência de regras mais baixo é executada primeiro. Se você atribuir o mesmo valor de seqüência de regras a duas regras de reescrita, a ordem de execução não será determinista.

     Se você não especificar explicitamente a Seqüência de Regras, um valor padrão de 100 será definido.

   - **ReescreverRuleSet**: Contém várias regras de reescrita que estarão associadas a uma regra de roteamento de solicitação.

2. Anexe o RewriteRuleSet a uma regra de roteamento. A configuração de reescrita é anexada ao ouvinte de origem através da regra de roteamento. Quando você usa uma regra básica de roteamento, a configuração de regravação de cabeçalho está associada a um ouvinte de origem e é uma reescrita global de cabeçalho. Quando você usa uma regra de roteamento baseada em caminho, a configuração de regravação de cabeçalho é definida no mapa de caminho da URL. Nesse caso, aplica-se apenas à área de caminho específica de um site.

Você pode criar vários conjuntos de reescrita de cabeçalho HTTP e aplicar cada conjunto de regravação a vários ouvintes. Mas você pode aplicar apenas uma reescrita definida a um ouvinte específico.

## <a name="sign-in-to-azure"></a>Entrar no Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>Especifique a configuração de regra de regravação do cabeçalho HTTP

Neste exemplo, modificaremos uma URL de redirecionamento reescrevendo o cabeçalho de localização na resposta HTTP sempre que o cabeçalho de localização contiver uma referência a azurewebsites.net. Para fazer isso, adicionaremos uma condição para avaliar se o cabeçalho de localização na resposta contém azurewebsites.net. Usaremos o `(https?):\/\/.*azurewebsites\.net(.*)$`padrão. E vamos usar `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` como o valor do cabeçalho. Este valor substituirá *azurewebsites.net* por *contoso.com* no cabeçalho de localização.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>Recupere a configuração do gateway do aplicativo

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>Recupere a configuração da regra de roteamento da solicitação

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>Atualize o gateway do aplicativo com a configuração para reescrever cabeçalhos HTTP

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Excluir uma regra de regravação

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como configurar alguns casos de uso comum, consulte [cenários comuns de reescrita de cabeçalho](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).