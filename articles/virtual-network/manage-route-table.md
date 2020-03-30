---
title: Criar, alterar ou excluir uma tabela de rotas do Azure
titlesuffix: Azure Virtual Network
description: Aprenda a criar, alterar ou excluir uma tabela de rotas.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: kumud
ms.openlocfilehash: 79310ddf121d6ada10755b198b515fdc9c1114d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247054"
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou excluir uma tabela de rotas

O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você desejar alterar qualquer roteamento padrão do Azure, poderá criar uma tabela de rotas para fazer isso. Se você é novo no roteamento em redes virtuais, você pode aprender mais sobre isso no [roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md) ou completando um [tutorial](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Se você não tiver uma, configure uma conta do Azure com uma assinatura ativa. [Crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Em seguida, complete uma dessas tarefas antes de iniciar etapas em qualquer seção deste artigo:

- **Usuários do portal**: Faça login no [portal Azure](https://portal.azure.com) com sua conta do Azure.

- **Usuários do PowerShell**: Execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell)ou execute o PowerShell a partir do seu computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Na guia do navegador Azure Cloud Shell, encontre a lista de itens de lista de itens de lista de itens de **isento do ambiente Select** e escolha **o PowerShell** se ele ainda não estiver selecionado.

    Se estiver executando o PowerShell localmente, use a versão 1.0.0 do módulo Azure PowerShell ou posterior. Execute `Get-Module -ListAvailable Az.Network` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Execute `Connect-AzAccount` também para criar uma conexão com o Azure.

- **Usuários da interface de linha de comando do Azure (CLI):** Execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash)ou execute o CLI a partir do seu computador. Use a versão 2.0.31 do Azure CLI ou posterior se estiver executando o Azure CLI localmente. Execute `az --version` para localizar a versão instalada. Se você precisar instalar ou atualizar, consulte [Install Azure CLI](/cli/azure/install-azure-cli). Execute `az login` também para criar uma conexão com o Azure.

A conta com a qual você faz login ou se conecta ao Azure deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) da Rede ou a uma função [Personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha atribuído as ações [apropriadas listadas](#permissions)nas Permissões .

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Há um limite para quantas tabelas de rota você pode criar por localização e assinatura do Azure. Para obter detalhes, consulte [os limites de rede - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No menu do [portal Azure](https://portal.azure.com) ou na **página inicial,** selecione **Criar um recurso**.

1. Na caixa de pesquisa, insira *Tabela de rotas*. Quando a **Tabela de rotas** é exibida nos resultados da pesquisa, selecione-a.

1. Na página **Tabela de rotas**, selecione **Criar**.

1. Na caixa de diálogo **de tabela de tabela** de rota criar:

    1. Digite um **nome** para a tabela de rotas.
    1. Escolha sua **Assinatura**.
    1. Escolha um **grupo de recursos** existente ou selecione **Criar um novo** para criar um novo grupo de recursos.
    1. Escolha um **local**.
    1. Se você planeja associar a tabela de rotas a uma sub-rede em uma rede virtual conectada à sua rede local por meio de um gateway VPN, e não quiser propagar suas rotas locais para as interfaces de rede na sub-rede, defina a **propagação** de gateway de rede virtual para **Desativado**.

1. Selecione **Criar** para criar sua nova tabela de rotas.

### <a name="create-route-table---commands"></a>Criar tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network route-table create](/cli/azure/network/route-table#az-network-route-table-create) |
| PowerShell | [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) |

## <a name="view-route-tables"></a>Exibir tabelas de rotas

Vá ao [portal Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise e selecione **tabelas de rota**. As tabelas de rotas que existem na sua assinatura estão listadas.

### <a name="view-route-table---commands"></a>Exibir tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [lista de tabela de rota de rede az](/cli/azure/network/route-table#az-network-route-table-list) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="view-details-of-a-route-table"></a>Exibir detalhes de uma tabela de rotas

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas para a qual deseja exibir detalhes.

1. Na página da tabela de rotas, em **Configurações,** veja as **Rotas** na tabela de rota ou nas **Subredes** a que a tabela de rotas está associada.

Para saber mais sobre configurações comuns do Azure, veja as seguintes informações:

- [Registro de atividades](../azure-monitor/platform/platform-logs-overview.md)
- [Controle de acesso (IAM)](../role-based-access-control/overview.md)
- [Marcas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Fechaduras](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Script de automação](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Ver detalhes da tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-tabela show](/cli/azure/network/route-table#az-network-route-table-show) |
| PowerShell | [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable) |

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que deseja alterar.

As mudanças mais comuns são [adicionar](#create-a-route) rotas, [remover](#delete-a-route) rotas, [associar](#associate-a-route-table-to-a-subnet) tabelas de rotas a sub-redes ou [dissociar](#dissociate-a-route-table-from-a-subnet) tabelas de rotas de sub-redes.

### <a name="change-a-route-table---commands"></a>Alterar uma tabela de rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [atualização de tabela de rota de rede az](/cli/azure/network/route-table#az-network-route-table-update) |
| PowerShell | [Set-AzRouteTable](/powershell/module/az.network/set-azroutetable) |

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Você pode associar opcionalmente uma tabela de rota a uma sub-rede. Uma tabela de rota pode ser associada a zero ou mais sub-redes. Como as tabelas de rota não estão associadas a redes virtuais, você deve associar uma tabela de rota a cada sub-rede à qual deseja a tabela de rotas associada. O Azure roteia todo o tráfego saindo da sub-rede com base nas rotas criadas dentro de tabelas de rotas, [rotas padrão](virtual-networks-udr-overview.md#default)e rotas propagadas a partir de uma rede local, se a rede virtual estiver conectada a um gateway de rede virtual do Azure (ExpressRoute ou VPN). Você só pode associar uma tabela de rotas a sub-redes em redes virtuais que existem no mesmo local e assinatura do Azure que a tabela de rotas.

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise e selecione **redes virtuais**.

1. Na lista de rede virtual, escolha a rede virtual que contém a sub-rede à qual deseja associar uma tabela de rotas.

1. Na barra de menu de rede virtual, escolha **Subnets**.

1. Selecione a sub-rede à qual você deseja associar a tabela de rotas.

1. Na **tabela Rota,** escolha a tabela de rotas que deseja associar à sub-rede.

1. Selecione **Salvar**.

Se sua rede virtual estiver conectada a um gateway Azure VPN, não associe uma tabela de rota à [sub-rede gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que inclua uma rota com destino de *0.0.0.0/0*. Isso pode impedir que o gateway funcione corretamente. Para obter mais informações sobre como usar *0.0.0.0/0* em uma rota, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associar uma tabela de rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar uma tabela de rotas de uma sub-rede

Quando você desassocia uma tabela de rota de uma sub-rede, o Azure roteia o tráfego com base em suas [rotas padrão](virtual-networks-udr-overview.md#default).

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar sua rede virtual. Pesquise e selecione **redes virtuais**.

1. Na lista de rede virtual, escolha a rede virtual que contém a sub-rede da qual deseja dissociar uma tabela de rota.

1. Na barra de menu de rede virtual, escolha **Subnets**.

1. Selecione a sub-rede da qual você deseja desassociar a tabela de rotas.

1. Na **tabela Rota,** escolha **Nenhum**.

1. Selecione **Salvar**.

### <a name="dissociate-a-route-table---commands"></a>Dissociar uma tabela de rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) |
| PowerShell | [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) |

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

Você não pode excluir uma tabela de rota que está associada a quaisquer sub-redes. [Desassocie](#dissociate-a-route-table-from-a-subnet) uma tabela de rota de todas as sub-redes antes de tentar excluí-la.

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rota, escolha a tabela de rotas que deseja excluir.

1. Selecione **Excluir**e selecione **Sim** na caixa de diálogo de confirmação.

### <a name="delete-a-route-table---commands"></a>Excluir uma tabela de rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede de tabela de rota excluir](/cli/azure/network/route-table#az-network-route-table-delete) |
| PowerShell | [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable) |

## <a name="create-a-route"></a>Criar uma rota

Há um limite para quantas rotas por tabela de rota pode criar por localização e assinatura do Azure. Para obter detalhes, consulte [os limites de rede - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas para a qual deseja adicionar uma rota.

1. Na barra de menu da tabela de rotas, escolha **'Adicionar** > **rotas'.**

1. Digite um **nome de rota** exclusivo para a rota dentro da tabela de rotas.

1. Digite o **prefixo Endereço**, na notação DE ROTEAmento Interdomínio (CIDR) sem classe, para a qual deseja direcionar o tráfego. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Por exemplo, se você definiu *10.0.0.0/16* como um prefixo em uma rota, você ainda pode definir outra rota com o prefixo de endereço *10.0.0.0/22.* O Azure seleciona uma rota para o tráfego com base na correspondência de prefixo mais longa. Para saber mais, veja [como o Azure seleciona uma rota](virtual-networks-udr-overview.md#how-azure-selects-a-route).

1. Escolha um **tipo de salto seguinte**. Para saber mais sobre os próximos tipos de hop, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).

1. Se você escolheu um dispositivo **virtual**de **hop Next,** digite um endereço IP para **endereço de salto Next**.

1. Selecione **OK**.

### <a name="create-a-route---commands"></a>Criar uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network route-table route create](/cli/azure/network/route-table/route#az-network-route-table-route-create) |
| PowerShell | [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig) |

## <a name="view-routes"></a>Exibir rotas

Uma tabela de rotas contém zero ou mais rotas. Para saber mais sobre as informações listadas ao visualizar rotas, consulte [Roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas para a qual deseja ver as rotas.

1. Na barra de menus da tabela de rotas, escolha **Rotas** para ver a lista de rotas.

### <a name="view-routes---commands"></a>Exibir rotas - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [lista de rotas de rota de rede az](/cli/azure/network/route-table/route#az-network-route-table-route-list) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="view-details-of-a-route"></a>Exibir detalhes de uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que contém a rota para a qual deseja ver detalhes.

1. Na barra de menus da tabela de rotas, escolha **Rotas** para ver a lista de rotas.

1. Selecione a rota cujos detalhes você deseja exibir.

### <a name="view-details-of-a-route---commands"></a>Exibir detalhes de uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-tabela show](/cli/azure/network/route-table/route#az-network-route-table-route-show) |
| PowerShell | [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig) |

## <a name="change-a-route"></a>Alterar uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rotas, escolha a tabela de rotas que contém a rota que deseja alterar.

1. Na barra de menus da tabela de rotas, escolha **Rotas** para ver a lista de rotas.

1. Escolha a rota que deseja mudar.

1. Altere as configurações existentes para suas novas configurações e selecione **Salvar**.

### <a name="change-a-route---commands"></a>Alterar uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-tabela atualização de rota](/cli/azure/network/route-table/route#az-network-route-table-route-update) |
| PowerShell | [Set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig) |

## <a name="delete-a-route"></a>Excluir uma rota

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar suas tabelas de rotas. Pesquise e selecione **tabelas de rota**.

1. Na lista de tabelas de rota, escolha a tabela de rota que contém a rota que deseja excluir.

1. Na barra de menus da tabela de rotas, escolha **Rotas** para ver a lista de rotas.

1. Escolha a rota que deseja excluir.

1. Selecione **Excluir**e **selecione Sim** na caixa de diálogo de confirmação.

### <a name="delete-a-route---commands"></a>Excluir uma rota - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede rota-tabela de exclusão](/cli/azure/network/route-table/route#az-network-route-table-route-delete) |
| PowerShell | [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig) |

## <a name="view-effective-routes"></a>Exibir rotas efetivas

As rotas eficazes para cada interface de rede conectada à VM são uma combinação de tabelas de rotas que você criou, as rotas padrão do Azure e quaisquer rotas propagadas a partir de redes locais através do Border Gateway Protocol (BGP) através de uma rede virtual do Azure Gateway. Entender as rotas em vigor para um adaptador de rede é útil ao solucionar problemas de roteamento. Você pode visualizar as rotas eficazes para qualquer interface de rede que esteja conectada a uma VM em execução.

1. Vá para o [portal Azure](https://portal.azure.com) para gerenciar suas VMs. Procure e selecione **máquinas virtuais**.

1. Na lista de máquinas virtuais, escolha a VM para a qual deseja visualizar rotas eficazes.

1. Na barra de menus VM, escolha **Rede**.

1. Selecione o nome de um adaptador de rede.

1. Na barra de menu de interface de rede, selecione **Rotas eficazes**.

1. Revise a lista de rotas eficazes para ver se existe a rota correta para onde você deseja direcionar o tráfego. Saiba mais sobre os próximos tipos de hop que você vê nesta lista no [roteamento de tráfego de rede Virtual](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Exibir rotas eficazes - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az rede nic show-efetivo-route-table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) |
| PowerShell | [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable) |

## <a name="validate-routing-between-two-endpoints"></a>Validar o roteamento entre dois pontos de extremidade

Você pode determinar o tipo de próximo salto entre uma máquina virtual e o endereço IP de outro recurso do Azure, um recurso local ou um recurso na Internet. Determinar o roteamento do Azure é útil ao solucionar problemas de roteamento. Para concluir esta tarefa, você deve ter um observador de rede existente. Se você não tiver um observador de rede existente, crie um completando as etapas em [Criar uma instância de Observador de rede](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Vá ao [portal Azure](https://portal.azure.com) para gerenciar seus observadores de rede. Procure e selecione **O Observador de Rede**.

1. Na barra de menu sinuosa do observador de rede, escolha **Next hop**.

1. No Observador da **Rede | Próxima** página de salto:

    1. Escolha sua **Assinatura** e o **grupo Recurso** da VM de origem da qual deseja validar o roteamento.

    1. Escolha a **máquina Virtual** e a interface **de rede** anexada à VM.
    
    1. Digite um **endereço IP de origem** atribuído à interface de rede da a que você deseja validar o roteamento.

    1. Digite um **endereço IP de destino** para o que você deseja validar o roteamento.

1. Selecione **Próximo salto**.

Depois de uma pequena espera, o Azure informa o próximo tipo de salto e o ID da rota que encaminhou o tráfego. Saiba mais sobre os próximos tipos de salto que você vê retornado no [roteamento de tráfego de rede virtual](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Validar o roteamento entre dois pontos finais - comandos

| Ferramenta | Comando |
| ---- | ------- |
| CLI do Azure | [az network watcher show-next-hop](/cli/azure/network/watcher#az-network-watcher-show-next-hop) |
| PowerShell | [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop) |

## <a name="permissions"></a>Permissões

Para fazer tarefas em tabelas de rotas e rotas, sua conta deve ser atribuída à [função de contribuinte](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) da Rede ou a uma função [Personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que tenha as ações apropriadas listadas na tabela a seguir:

| Ação                                                          |   Nome                                                  |
|--------------------------------------------------------------   |   -------------------------------------------           |
| Microsoft.Network/routeTables/read                              |   Ler uma tabela de rotas                                    |
| Microsoft.Network/routeTables/write                             |   Criar ou atualizar tabela de rotas                        |
| Microsoft.Network/routeTables/delete                            |   Excluir uma tabela de rotas                                  |
| Microsoft.Network/routeTables/join/action                       |   Associar uma tabela de rotas a uma sub-rede                   |
| Microsoft.Network/routeTables/routes/read                       |   Ler uma rota                                          |
| Microsoft.Network/routeTables/routes/write                      |   Criar ou atualizar uma rota                              |
| Microsoft.Network/routeTables/routes/delete                     |   Excluir uma rota                                        |
| Microsoft.Network/networkInterfaces/effectiveRouteTable/action  |   Obter tabela de rotas em vigor para uma interface de rede |
| Microsoft.Network/networkWatchers/nextHop/action                |   Obtém o próximo salto de uma VM                           |

## <a name="next-steps"></a>Próximas etapas

- Crie uma tabela de rotas usando scripts de exemplo [PowerShell](powershell-samples.md) ou [Azure CLI](cli-samples.md) ou [modelos](template-samples.md) do Azure Resource Manager
- Criar e aplicar a [Política do Azure](policy-samples.md) para redes virtuais
