---
title: Configurar a regra de limite de taxa waf para porta da frente - Azure PowerShell
description: Saiba como configurar uma regra de limite de taxa para um ponto final existente da Porta da Frente.
author: vhorne
ms.service: web-application-firewall
ms.topic: article
services: web-application-firewall
ms.date: 02/26/2020
ms.author: victorh
ms.openlocfilehash: b034159c3d12927f6425b3dc3c5b5609af9b0b76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649357"
---
# <a name="configure-a-web-application-firewall-rate-limit-rule-using-azure-powershell"></a>Configure uma regra de limite de taxa de firewall de aplicativos da Web usando o Azure PowerShell
A regra de limite de taxa do Azure Web Application Firewall (WAF) para o Azure Front Door controla o número de solicitações permitidas pelos clientes durante uma duração de um minuto.
Este artigo mostra como configurar uma regra de limite de taxa waf que controla o número de solicitações permitidas dos clientes para um aplicativo web que contém */promo* na URL usando o Azure PowerShell.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar a configurar uma política de limite de taxa, configure seu ambiente PowerShell e crie um perfil da Porta da Frente.
### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell
O Azure PowerShell fornece um conjunto de cmdlets que usam o modelo [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página, faça login com suas credenciais do Azure e instale o módulo Az PowerShell.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Conecte-se ao Azure com um diálogo interativo para login
```
Connect-AzAccount

```
Antes de instalar o módulo Front Door, certifique-se de que a versão atual do PowerShellGet esteja instalada. Execute o seguinte comando e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar o módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de Front Door
Crie um perfil da porta da frente seguindo as instruções descritas no [Quickstart: Crie um perfil da porta da frente](../../frontdoor/quickstart-create-front-door.md)

## <a name="define-url-match-conditions"></a>Definir as condições de correspondência de url
Defina uma condição de correspondência de URL (URL contém /promo) usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject).
O exemplo a seguir corresponde */promo* como o valor da variável *RequestUri:*

```powershell-interactive
   $promoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
     -MatchVariable RequestUri `
     -OperatorProperty Contains `
     -MatchValue "/promo"
```
## <a name="create-a-custom-rate-limit-rule"></a>Crie uma regra de limite de taxa personalizada
Defina um limite de taxa usando [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject). No exemplo a seguir, o limite é definido como 1000. As solicitações de qualquer cliente para a página promocional superior a 1000 durante um minuto são bloqueadas até o minuto seguinte começar.

```powershell-interactive
   $promoRateLimitRule = New-AzFrontDoorWafCustomRuleObject `
     -Name "rateLimitRule" `
     -RuleType RateLimitRule `
     -MatchCondition $promoMatchCondition `
     -RateLimitThreshold 1000 `
     -Action Block -Priority 1
```


## <a name="configure-a-security-policy"></a>Configure uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzureRmResourceGroup`. Em seguida, configure uma diretiva de segurança com uma regra de limite de taxa personalizada usando [new-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil Front Door.

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* com a suposição de que você criou o perfil da Porta da Frente usando instruções fornecidas no [artigo Quickstart: Create a Front Door.](../../frontdoor/quickstart-create-front-door.md)

 usando [new-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell-interactive
   $ratePolicy = New-AzFrontDoorWafPolicy `
     -Name "RateLimitPolicyExamplePS" `
     -resourceGroupName myResourceGroupFD1 `
     -Customrule $promoRateLimitRule `
     -Mode Prevention `
     -EnabledState Enabled
```
## <a name="link-policy-to-a-front-door-front-end-host"></a>Vincular a política a um host front-end da Porta da frente
Vincule o objeto de política de segurança a um host front-end existente e atualize as propriedades do Front Door. Primeiro recupere o objeto Front Door usando o comando [Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor)
Em seguida, defina a propriedade *Front-end WebApplicationFirewallPolicyLink* para o *recursoId* do "$ratePolicy" criado na etapa anterior usando o comando [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) 

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* com a suposição de que você criou o perfil da Porta da Frente usando instruções fornecidas no [artigo Quickstart: Create a Front Door.](../../frontdoor/quickstart-create-front-door.md) Além disso, no exemplo abaixo, substitua $frontDoorName com o nome do seu perfil da Porta da Frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $ratePolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Você só precisa definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para vincular uma política de segurança a um front-end da Porta da Frente. As atualizações de diretiva subseqüentes são aplicadas automaticamente ao front-end.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [a Porta da Frente](../../frontdoor/front-door-overview.md). 


