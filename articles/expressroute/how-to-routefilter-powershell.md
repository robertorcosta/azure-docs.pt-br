---
title: 'ExpressRoute: filtros de rota- Peering da Microsoft:Azure PowerShell'
description: Este artigo descreve como configurar filtros de rota para o Emparelhamento da Microsoft usando o PowerShell
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3fa53258321b22e1683122edca1816f6d4c291b5
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618611"
---
# <a name="configure-route-filters-for-microsoft-peering-powershell"></a>Configurar os filtros de rota para o emparelhamento da Microsoft: PowerShell
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [CLI do Azure](how-to-routefilter-cli.md)
> 

Filtros de rota são uma maneira de consumir um subconjunto de serviços com suporte por meio do emparelhamento da Microsoft. As etapas neste artigo lhe ajudam a configurar e gerenciar filtros de rota para circuitos de ExpressRoute.

Serviços do Office 365, como Exchange Online, SharePoint Online e Skype for Business, e serviços públicos do Azure, como armazenamento e SQL DB, são acessíveis através do peering da Microsoft. Os serviços públicos do Azure são selecionáveis por região e não podem ser definidos por serviço público.

Quando o emparelhamento da Microsoft é configurado em um circuito do ExpressRoute e um filtro de rota é anexado, todos os prefixos selecionados para esses serviços são anunciados por meio das sessões BGP estabelecidas. Um valor de comunidade BGP é anexado a cada prefixo, pelo qual se pode identificar o serviço que é oferecido. Para obter uma lista dos valores de comunidade BGP e os serviços para os quais eles são mapeados, consulte [comunidades BGP](expressroute-routing.md#bgp).

Se você precisa de conectividade para todos os serviços, um grande número de prefixos é anunciado através do BGP. Isso aumenta consideravelmente o tamanho das tabelas de rotas mantidas por roteadores em sua rede. Se você planeja consumir apenas um subconjunto de serviços oferecidos pelo emparelhamento da Microsoft, você pode reduzir o tamanho de suas tabelas de rotas de duas maneiras. Você pode:

- Filtre prefixos indesejados aplicando filtros de rota nas comunidades do BGP. Essa é uma prática de rede padrão e é usada frequentemente em várias redes.

- Defina filtros de rota e aplique-as ao circuito de ExpressRoute. Um filtro de rota é um novo recurso que permite a você selecionar a lista de serviços que você planeja consumir por meio do emparelhamento da Microsoft. Roteadores do ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o peering da Microsoft é configurado no circuito ExpressRoute, os roteadores de borda de rede da Microsoft estabelecem um par de sessões de BGP com os roteadores de borda (seus ou do seu provedor de conectividade). Nenhuma rota é anunciada para sua rede. Para habilitar os anúncios de rota para sua rede, você deve associar um filtro de rota.

Um filtro de rota permite identificar os serviços que você deseja consumir por meio de emparelhamento da Microsoft do seu circuito de ExpressRoute. É essencialmente uma lista de permitir todos os valores da comunidade BGP. Depois que um recurso de filtro de rota é definido e anexado a um circuito de ExpressRoute, todos os prefixos que são mapeados para os valores de comunidade do BGP são anunciados para sua rede.

Para que seja possível anexar filtros de rota que incluam serviços do Office 365, você deve ter autorização para consumir serviços do Office 365 por meio do ExpressRoute. Se você não está autorizado a consumir serviços do Office 365 por meio do ExpressRoute, a operação de anexação de filtros de rota falhará. Para obter mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos de ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para poder se conectar aos serviços por meio do emparelhamento da Microsoft, você deve concluir as etapas de configuração a seguir:

- Você deve ter um circuito de ExpressRoute ativado que tenha o Microsoft emparelhamento provisionado. Você pode usar as instruções a seguir para realizar estas tarefas:
  - [Crie um circuito de ExpressRoute](expressroute-howto-circuit-arm.md) e habilite-o pelo provedor de conectividade antes de prosseguir. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.
  - [Crie o emparelhamento da Microsoft](expressroute-circuit-peerings.md) se você gerenciar a sessão de BGP diretamente. Ou então, faça com que seu provedor de conectividade provisione emparelhamento da Microsoft para o circuito.

-  Você deve criar e configurar um filtro de rota.
    - Identifique os serviços que você deseja consumir por meio de emparelhamento da Microsoft
    - Identificar a lista de valores de comunidade BGP associados aos serviços
    - Criar uma regra para permitir a lista de prefixos que correspondem aos valores de comunidade BGP

-  Você deve anexar o filtro de rota ao circuito de ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, verifique se você satisfaz os seguintes critérios:

 - Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

 - Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-arm.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.

 - Você deve ter um emparelhamento da Microsoft ativo. Siga as instruções no artigo [Criar e modificar a configuração de emparelhamento](expressroute-circuit-peerings.md).


### <a name="working-with-azure-powershell"></a>Trabalhando com o Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="log-in-to-your-azure-account"></a>Faça logon na sua conta do Azure

Antes de iniciar essa configuração, você deve fazer logon na sua conta do Azure. O cmdlet solicita as credenciais de logon para sua conta do Azure. Depois de entrar, ele baixa as configurações da conta para que elas estejam disponíveis para o Azure PowerShell.

Abra o console do PowerShell com privilégios elevados e conecte-se à sua conta. Use o exemplo a seguir para ajudá-lo a se conectar. Se você estiver usando o Azure Cloud Shell, não precisará executar este cmdlet, pois você será automaticamente conectado.

```azurepowershell
Connect-AzAccount
```

Se você tiver várias assinaturas do Azure, verifique as assinaturas para a conta.

```azurepowershell-interactive
Get-AzSubscription
```

Especifique a assinatura que você deseja usar.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Etapa 1: obter uma lista de prefixos e valores de comunidade BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obtenha uma lista de valores da comunidade BGP

Use o cmdlet a seguir para obter a lista de valores de comunidade BGP associados aos serviços acessíveis por meio do emparelhamento da Microsoft, junto com a lista de prefixos associados a eles:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```
### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que você deseja usar

Faça uma lista de valores de comunidade BGP que você deseja usar no filtro de rota.

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Etapa 2: criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, que deve ser do tipo 'Permitir'. Essa regra pode ter uma lista de valores de comunidade BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Crie um filtro de rota

Primeiro, crie o filtro de rota. O comando 'New-AzRouteFilter' só cria um recurso de filtro de rota. Depois de criar o recurso, você deve criar uma regra e anexá-la ao objeto de filtro de rota. Execute o comando a seguir para criar um recurso de filtro de rota:

```azurepowershell-interactive
New-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup" -Location "West US"
```

### <a name="2-create-a-filter-rule"></a>2. Crie uma regra de filtro

Você pode especificar um conjunto de comunidades BGP como uma lista separada por vírgulas, conforme mostrado no exemplo. Execute o comando a seguir para criar uma nova regra:
 
```azurepowershell-interactive
$rule = New-AzRouteFilterRuleConfig -Name "Allow-EXO-D365" -Access Allow -RouteFilterRuleType Community -CommunityList 12076:5010,12076:5040
```

### <a name="3-add-the-rule-to-the-route-filter"></a>3. Adicione a regra ao filtro de rota

Execute o comando a seguir para adicionar uma regra ao filtro de rota:
 
```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.Rules.Add($rule)
Set-AzRouteFilter -RouteFilter $routefilter
```

## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Etapa 3: anexar o filtro de rota a um circuito de ExpressRoute

Execute o seguinte comando para anexar o filtro de rota ao circuito ExpressRoute, supondo que você tenha apenas emparelhamento da Microsoft:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
$ckt.Peerings[0].RouteFilter = $routefilter 
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Para obter as propriedades de um filtro de rota, use as seguintes etapas:

1. Execute o comando a seguir para obter o recurso de filtro de rota:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   ```
2. Obtenha as regras de filtro de rota para o recurso de filtro de rota, executando o seguinte comando:

   ```azurepowershell-interactive
   $routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
   $rule = $routefilter.Rules[0]
   ```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Se o filtro de rota já está anexado a um circuito, as atualizações à lista de comunidade BGP propagam automaticamente as alterações de anúncio de prefixo apropriadas por meio das sessões de BGP estabelecidas. Você pode atualizar a lista de comunidades BGP de seu filtro de rota usando o seguinte comando:

```azurepowershell-interactive
$routefilter = Get-AzRouteFilter -Name "RouteFilterName" -ResourceGroupName "ExpressRouteResourceGroupName"
$routefilter.rules[0].Communities = "12076:5030", "12076:5040"
Set-AzRouteFilter -RouteFilter $routefilter
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para desanexar um filtro de rota de um circuito de ExpressRoute

Depois que um filtro de rota é desanexado do circuito de ExpressRoute, nenhum prefixo é anunciado através da sessão de BGP. Você pode desanexar um filtro de rota de um circuito de ExpressRoute usando o seguinte comando:
  
```azurepowershell-interactive
$ckt.Peerings[0].RouteFilter = $null
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Para excluir um filtro de rota

Você só pode excluir um filtro de rota se ele não está anexado a nenhum circuito. Verifique se o filtro de rota não está anexado a algum circuito antes de tentar excluí-lo. Você pode excluir um filtro de rota usando o seguinte comando:

```azurepowershell-interactive
Remove-AzRouteFilter -Name "MyRouteFilter" -ResourceGroupName "MyResourceGroup"
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o ExpressRoute, consulte o [FAQ ExpressRoute](expressroute-faqs.md).
