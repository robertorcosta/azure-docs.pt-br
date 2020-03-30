---
title: 'ExpressRoute: Filtros de rota - Peering microsoft:portal Azure'
description: Este artigo descreve como configurar filtros de rota para o Emparelhamento da Microsoft usando o portal do Azure.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 07/01/2019
ms.author: ganesr
ms.custom: seodec18
ms.openlocfilehash: 0b8e06ad5688374e5ab4aaa72d8485e6da797afe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037451"
---
# <a name="configure-route-filters-for-microsoft-peering-azure-portal"></a>Configurar os filtros de rota para o emparelhamento da Microsoft: Portal do Azure
> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure CLI](how-to-routefilter-cli.md)
> 

Filtros de rota são uma maneira de consumir um subconjunto de serviços com suporte por meio do emparelhamento da Microsoft. As etapas neste artigo lhe ajudam a configurar e gerenciar filtros de rota para circuitos de ExpressRoute.

Serviços do Office 365, como Exchange Online, SharePoint Online e Skype for Business, e serviços do Azure, como armazenamento e SQL DB, são acessíveis através do peering da Microsoft. Quando emparelhamento da Microsoft é configurado em um circuito de ExpressRoute, todos os prefixos relacionados a esses serviços são publicados por meio das sessões de BGP que são estabelecidas. Um valor de comunidade BGP é anexado a cada prefixo, pelo qual se pode identificar o serviço que é oferecido. Para obter uma lista dos valores de comunidade BGP e os serviços para os quais eles são mapeados, consulte [comunidades BGP](expressroute-routing.md#bgp).

Se você precisa de conectividade para todos os serviços, um grande número de prefixos é anunciado através do BGP. Isso aumenta consideravelmente o tamanho das tabelas de rotas mantidas por roteadores em sua rede. Se você planeja consumir apenas um subconjunto de serviços oferecidos pelo emparelhamento da Microsoft, você pode reduzir o tamanho de suas tabelas de rotas de duas maneiras. Você pode:

- Filtre prefixos indesejados aplicando filtros de rota nas comunidades do BGP. Essa é uma prática de rede padrão e é usada frequentemente em várias redes.

- Defina filtros de rota e aplique-as ao circuito de ExpressRoute. Um filtro de rota é um novo recurso que permite a você selecionar a lista de serviços que você planeja consumir por meio do emparelhamento da Microsoft. Roteadores do ExpressRoute enviam apenas a lista de prefixos que pertencem aos serviços identificados no filtro de rota.

### <a name="about-route-filters"></a><a name="about"></a>Sobre filtros de rota

Quando o emparelhamento da Microsoft é configurado no seu circuito de ExpressRoute, os roteadores de borda da Microsoft estabelecem um par de sessões de BGP com os roteadores de borda (seus ou de seu provedor de conectividade). Nenhuma rota é anunciada para sua rede. Para habilitar os anúncios de rota para sua rede, você deve associar um filtro de rota.

Um filtro de rota permite identificar os serviços que você deseja consumir por meio de emparelhamento da Microsoft do seu circuito de ExpressRoute. É essencialmente uma lista de todos os valores da comunidade BGP que você deseja permitir. Depois que um recurso de filtro de rota é definido e anexado a um circuito de ExpressRoute, todos os prefixos que são mapeados para os valores de comunidade do BGP são anunciados para sua rede.

Para que seja possível anexar filtros de rota que incluam serviços do Office 365, você deve ter autorização para consumir serviços do Office 365 por meio do ExpressRoute. Se você não está autorizado a consumir serviços do Office 365 por meio do ExpressRoute, a operação de anexação de filtros de rota falhará. Para obter mais informações sobre o processo de autorização, consulte [Azure ExpressRoute para Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd).

> [!IMPORTANT]
> O emparelhamento da Microsoft de circuitos de ExpressRoute configurados antes de 1º de agosto de 2017 terá todos os prefixos de serviço anunciados através do emparelhamento da Microsoft, mesmo que os filtros de rota não estejam definidos. O emparelhamento da Microsoft de circuitos de ExpressRoute configurados em ou após 1º de agosto de 2017 não terá nenhum prefixo anunciado até que um filtro de rota seja anexado ao circuito.
> 
> 

### <a name="workflow"></a><a name="workflow"></a>Fluxo de trabalho

Para poder se conectar aos serviços por meio do emparelhamento da Microsoft, você deve concluir as etapas de configuração a seguir:

- Você deve ter um circuito de ExpressRoute ativado que tenha o Microsoft emparelhamento provisionado. Você pode usar as instruções a seguir para realizar estas tarefas:
  - [Crie um circuito de ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e habilite-o pelo provedor de conectividade antes de prosseguir. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.
  - [Crie o emparelhamento da Microsoft](expressroute-howto-routing-portal-resource-manager.md) se você gerenciar a sessão de BGP diretamente. Ou então, faça com que seu provedor de conectividade provisione emparelhamento da Microsoft para o circuito.

-  Você deve criar e configurar um filtro de rota.
    - Identifique os serviços que você deseja consumir por meio de emparelhamento da Microsoft
    - Identificar a lista de valores de comunidade BGP associados aos serviços
    - Criar uma regra para permitir a lista de prefixos que correspondem aos valores de comunidade BGP

-  Você deve anexar o filtro de rota ao circuito de ExpressRoute.

## <a name="before-you-begin"></a>Antes de começar

Antes de começar a configuração, verifique se você satisfaz os seguintes critérios:

 - Examine os [pré-requisitos](expressroute-prerequisites.md) e os [fluxos de trabalho](expressroute-workflows.md) antes de começar a configuração.

 - Você deve ter um circuito do ExpressRoute ativo. Antes de continuar, siga as instruções para [criar um circuito do ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) e para que o circuito seja habilitado pelo provedor de conectividade. O circuito de ExpressRoute deve estar em um estado habilitado e provisionado.

 - Você deve ter um emparelhamento da Microsoft ativo. Siga as instruções em [Criar e modificar a configuração de emparelhamento](expressroute-howto-routing-portal-resource-manager.md)


## <a name="step-1-get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Etapa 1: obter uma lista de prefixos e valores de comunidade BGP

### <a name="1-get-a-list-of-bgp-community-values"></a>1. Obtenha uma lista de valores da comunidade BGP

Os valores de comunidade BGP associados aos serviços acessíveis por meio de emparelhamento da Microsoft está disponível na página [Requisitos de roteamento do ExpressRoute](expressroute-routing.md).

### <a name="2-make-a-list-of-the-values-that-you-want-to-use"></a>2. Faça uma lista dos valores que você deseja usar

Faça uma lista dos valores da [comunidade BGP](expressroute-routing.md#bgp) que você deseja usar no filtro de rota. 

## <a name="step-2-create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Etapa 2: criar um filtro de rota e uma regra de filtro

Um filtro de rota pode ter apenas uma regra, que deve ser do tipo 'Permitir'. Essa regra pode ter uma lista de valores de comunidade BGP associados a ela.

### <a name="1-create-a-route-filter"></a>1. Crie um filtro de rota
Você pode criar um circuito de rota selecionando a opção de criar um novo recurso. Clique **em Criar um recurso** > **Networking** > **RouteFilter**, como mostrado na imagem a seguir:

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter1.png)

Você deve colocar o filtro de rota em um grupo de recursos. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/CreateRouteFilter.png)

### <a name="2-create-a-filter-rule"></a>2. Crie uma regra de filtro

Você pode adicionar e atualizar regras selecionando a guia de regra de gerenciar para o filtro de rota.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)


Você pode selecionar os serviços aos quais deseja se conectar a partir da lista de paradas e salvar a regra quando feito.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png)


## <a name="step-3-attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Etapa 3: anexar o filtro de rota a um circuito de ExpressRoute

Você pode anexar o filtro de rota a um circuito selecionando o botão "adicionar circuito" e selecionando o circuito ExpressRoute na lista de paradas.

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddCktToRouteFilter.png)

Se o provedor de conectividade configura o emparelhamento do seu circuito do ExpressRoute atualize o circuito da folha do circuito do ExpressRoute antes de selecionar o botão "Adicionar circuito".

![Criar um filtro de rota](./media/how-to-routefilter-portal/RefreshExpressRouteCircuit.png)

## <a name="common-tasks"></a><a name="tasks"></a>Tarefas comuns

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>Para obter as propriedades de um filtro de rota

Você pode exibir as propriedades de um filtro de rota quando abre o recurso no portal.

![Criar um filtro de rota](./media/how-to-routefilter-portal/ViewRouteFilter.png)


### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>Para atualizar as propriedades de um filtro de rota

Você pode atualizar a lista de valores de comunidade BGP anexada a um circuito ao selecionar o botão "Gerenciar regra".


![Criar um filtro de rota](./media/how-to-routefilter-portal/ManageRouteFilter.png)

![Criar um filtro de rota](./media/how-to-routefilter-portal/AddRouteFilterRule.png) 


### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Para desanexar um filtro de rota de um circuito de ExpressRoute

Para separar um circuito do filtro de rota, clique com o botão direito do mouse no circuito e clique em "desassociar".

![Criar um filtro de rota](./media/how-to-routefilter-portal/DetachRouteFilter.png) 


### <a name="to-delete-a-route-filter"></a><a name="delete"></a>Para excluir um filtro de rota

Você pode excluir um filtro de rota selecionando o botão de exclusão. 

![Criar um filtro de rota](./media/how-to-routefilter-portal/DeleteRouteFilter.png) 

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre o ExpressRoute, consulte o [FAQ ExpressRoute](expressroute-faqs.md).

* Para obter informações sobre amostras de configuração do roteador, consulte [amostras de configuração do Roteador para configurar e gerenciar o roteamento](expressroute-config-samples-routing.md). 
