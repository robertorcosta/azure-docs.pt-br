---
title: Configure as regras personalizadas do WAF & conjunto de regras padrão para a porta frontal do Azure
description: Saiba como configurar uma política WAF composta por regras personalizadas e gerenciadas para um ponto final existente na Porta da Frente.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 09/05/2019
ms.author: victorh
ms.openlocfilehash: 493ed1a31a23366a90b80d3ab510218c8dce0e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186645"
---
# <a name="configure-a-web-application-firewall-policy-using-azure-powershell"></a>Configure uma política de firewall de aplicativos da Web usando o Azure PowerShell

A política waf (Azure Web Application Firewall, firewall de aplicativos da Web) define as inspeções necessárias quando uma solicitação chega ao Front Door.
Este artigo mostra como configurar uma diretiva WAF que consiste em algumas regras personalizadas e com o Conjunto de Regras padrão gerenciado pelo Azure ativado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar uma política de limite de taxa, configure seu ambiente PowerShell e crie um perfil da Porta da Frente.

### <a name="set-up-your-powershell-environment"></a>Configurar o ambiente do PowerShell

O Azure PowerShell fornece um conjunto de cmdlets que usam o modelo [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) para gerenciar os recursos do Azure. 

Você pode instalar o [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) no computador local e usá-lo em qualquer sessão do PowerShell. Siga as instruções na página, faça login com suas credenciais do Azure e instale o módulo Az PowerShell.

#### <a name="sign-in-to-azure"></a>Entrar no Azure

```
Connect-AzAccount

```
Antes de instalar o módulo Front Door, verifique se você tem a versão atual do PowerShellGet instalada. Execute o comando abaixo e reabra o PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

#### <a name="install-azfrontdoor-module"></a>Instalar o módulo Az.FrontDoor 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Criar um perfil de Front Door

Crie um perfil da porta da frente seguindo as instruções descritas no [Quickstart: Crie um perfil da porta da frente](../../frontdoor/quickstart-create-front-door.md)

## <a name="custom-rule-based-on-http-parameters"></a>Regra personalizada com base em parâmetros http

O exemplo a seguir mostra como configurar uma regra personalizada com duas condições de correspondência usando [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject). As solicitações são de um site especificado, conforme definido pelo referrer, e a seqüência de consultas não contém "senha". 

```powershell-interactive
$referer = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestHeader -OperatorProperty Equal -Selector "Referer" -MatchValue "www.mytrustedsites.com/referpage.html"
$password = New-AzFrontDoorWafMatchConditionObject -MatchVariable QueryString -OperatorProperty Contains -MatchValue "password"
$AllowFromTrustedSites = New-AzFrontDoorWafCustomRuleObject -Name "AllowFromTrustedSites" -RuleType MatchRule -MatchCondition $referer,$password -Action Allow -Priority 1
```

## <a name="custom-rule-based-on-http-request-method"></a>Regra personalizada baseada no método de solicitação http

Crie um método de bloqueio de regra "PUT" usando [new-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject) da seguinte forma:

```powershell-interactive
$put = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestMethod -OperatorProperty Equal -MatchValue PUT
$BlockPUT = New-AzFrontDoorWafCustomRuleObject -Name "BlockPUT" -RuleType MatchRule -MatchCondition $put -Action Block -Priority 2
```

## <a name="create-a-custom-rule-based-on-size-constraint"></a>Crie uma regra personalizada com base na restrição de tamanho

O exemplo a seguir cria uma regra de bloqueio de solicitações com Url com mais de 100 caracteres usando o Azure PowerShell:
```powershell-interactive
$url = New-AzFrontDoorWafMatchConditionObject -MatchVariable RequestUri -OperatorProperty GreaterThanOrEqual -MatchValue 100
$URLOver100 = New-AzFrontDoorWafCustomRuleObject -Name "URLOver100" -RuleType MatchRule -MatchCondition $url -Action Block -Priority 3
```
## <a name="add-managed-default-rule-set"></a>Adicionar conjunto de regras padrão gerenciado

O exemplo a seguir cria um conjunto de regras padrão gerenciado usando o Azure PowerShell:
```powershell-interactive
$managedRules =  New-AzFrontDoorWafManagedRuleObject -Type DefaultRuleSet -Version 1.0
```
## <a name="configure-a-security-policy"></a>Configure uma política de segurança

Encontre o nome do grupo de recursos que contém o perfil de Front Door usando `Get-AzResourceGroup`. Em seguida, configure uma diretiva de segurança com regras criadas nas etapas anteriores usando [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) no grupo de recursos especificado que contém o perfil Porta da Frente.

```powershell-interactive
$myWAFPolicy=New-AzFrontDoorWafPolicy -Name $policyName -ResourceGroupName $resourceGroupName -Customrule $AllowFromTrustedSites,$BlockPUT,$URLOver100 -ManagedRule $managedRules -EnabledState Enabled -Mode Prevention
```

## <a name="link-policy-to-a-front-door-front-end-host"></a>Vincular a política a um host front-end da Porta da frente

Vincule o objeto de política de segurança a um host front-end existente e atualize as propriedades do Front Door. Primeiro, recupere o objeto Porta da Frente usando [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor).
Em seguida, defina a propriedade *Front-end WebApplicationFirewallPolicyLink* para o *recursoId* do "$myWAFPolicy$" criado na etapa anterior usando [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor). 

O exemplo abaixo usa o nome do Grupo de Recursos *myResourceGroupFD1* com a suposição de que você criou o perfil da Porta da Frente usando instruções fornecidas no [artigo Quickstart: Create a Front Door.](../../frontdoor/quickstart-create-front-door.md) Além disso, no exemplo abaixo, substitua $frontDoorName com o nome do seu perfil da Porta da Frente. 

```powershell-interactive
   $FrontDoorObjectExample = Get-AzFrontDoor `
     -ResourceGroupName myResourceGroupFD1 `
     -Name $frontDoorName
   $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $myWAFPolicy.Id
   Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
 ```

> [!NOTE]
> Você só precisa definir a propriedade *WebApplicationFirewallPolicyLink* uma vez para vincular uma política de segurança a um front-end da Porta da Frente. As atualizações de diretiva subseqüentes são aplicadas automaticamente ao front-end.

## <a name="next-steps"></a>Próximas etapas

- Saiba mais sobre [a Porta da Frente](../../frontdoor/front-door-overview.md) 
- Saiba mais sobre [o WAF com a Porta da Frente](afds-overview.md)
