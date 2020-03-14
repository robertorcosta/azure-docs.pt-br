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
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: fe8ea4dfb4de45a1e09648ac51fe8d74f93a6b9e
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280268"
---
# <a name="create-change-or-delete-a-route-table"></a>Criar, alterar ou excluir uma tabela de rotas

O Azure roteia o tráfego automaticamente entre redes virtuais, redes locais e sub-redes do Azure. Se você desejar alterar qualquer roteamento padrão do Azure, poderá criar uma tabela de rotas para fazer isso. Se você é novo em rotear as redes virtuais, aprenda mais sobre elas na [visão geral de Rede Virtual](virtual-networks-udr-overview.md) ou executando um [tutorial](tutorial-create-route-table-portal.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

* Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).<br>
* Se estiver usando o portal, abra https://portal.azure.come entre com sua conta do Azure.<br>
* Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.<br>
* Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta na qual você entra ou se conecta ao Azure com, deve ser atribuída à função de [colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que recebe as ações apropriadas listadas em [permissões](#permissions).

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Há um limite para o número de tabelas de rotas que você pode criar por assinatura e local do Azure. Para obter detalhes, confira [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. No canto superior esquerdo do Portal, selecione **+ Criar um recurso**.
1. Selecione **Rede** e, em seguida, selecione **Tabela de rotas**.
1. Insira um **Nome** para a tabela de rotas, selecione sua **Assinatura**, crie um novo **Grupo de recursos** ou selecione um grupo de recursos existente, selecione um **Local** e, em seguida, selecione **Criar**. Se você planeja associar a tabela de rotas a uma sub-rede em uma rede virtual conectada à sua rede local por meio de um gateway de VPN e desabilitar a **propagação de rota de gateway de rede virtual**, suas rotas locais não são propagadas para as interfaces de rede na sub-rede.

### <a name="create-route-table---commands"></a>Criar tabela de rotas-comandos

* CLI do Azure: [az network route-table create](/cli/azure/network/route-table/route)<br>
* PowerShell: [New-AzRouteTable](/powershell/module/az.network/new-azroutetable)

## <a name="view-route-tables"></a>Exibir tabelas de rotas

Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção. As tabelas de rotas que existem na sua assinatura estão listadas.

### <a name="view-route-table---commands"></a>Exibir tabela de rotas-comandos

* CLI do Azure: [az network route-table list](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="view-details-of-a-route-table"></a>Exibir detalhes de uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione na lista a tabela de rotas cujos detalhes você deseja exibir. Em **CONFIGURAÇÕES**, você pode exibir as **Rotas** na tabela de rotas e as **Sub-redes** às quais a tabela de rotas está associada.
1. Para saber mais sobre configurações comuns do Azure, veja as seguintes informações:

    * [Log de atividade](../azure-monitor/platform/platform-logs-overview.md)<br>
    * [Controle de acesso (IAM)](../role-based-access-control/overview.md)<br>
    * [Marcas](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Bloqueios](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br>
    * [Script de automação](../azure-resource-manager/templates/export-template-portal.md)

### <a name="view-details-of-route-table---commands"></a>Exibir detalhes da tabela de rotas – comandos

* CLI do Azure: [az network route-table show](/cli/azure/network/route-table/route)<br>
* PowerShell: [Get-AzRouteTable](/powershell/module/az.network/get-azroutetable)

## <a name="change-a-route-table"></a>Alterar uma tabela de rotas

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione a tabela de rotas que você deseja alterar. As alterações mais comuns são a [adição](#create-a-route) ou [remoção](#delete-a-route) de rotas e a [associação](#associate-a-route-table-to-a-subnet) ou a [desassociação](#dissociate-a-route-table-from-a-subnet) de tabelas de rotas de sub-redes.

### <a name="change-a-route-table---commands"></a>Alterar uma tabela de rotas – comandos

* CLI do Azure: [az network route-table update](/cli/azure/network/route-table/route)<br>
* PowerShell: [set-AzRouteTable](/powershell/module/az.network/set-azroutetable)

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela. Uma tabela de rotas pode ser associada a zero ou várias sub-redes. Já que tabelas de rotas não são associadas a redes virtuais, você precisa associar uma tabela de rotas a cada sub-rede à qual você deseje associar a tabela de rotas. Todo o tráfego que sai da sub-rede é roteado com base nas rotas que você criou nas tabelas de rota, [rotas padrão](virtual-networks-udr-overview.md#default)e rotas propagadas de uma rede local, se a rede virtual estiver conectada a um gateway de rede virtual do Azure (EXPRESSROUTE ou VPN). Você só pode associar uma tabela de rotas a sub-redes em redes virtuais que existem no mesmo local e assinatura do Azure que a tabela de rotas.

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
1. Selecione a rede virtual na lista que contém a sub-rede à qual você deseja associar uma tabela de rotas.
1. Selecione **Sub-redes** em **CONFIGURAÇÕES**.
1. Selecione a sub-rede à qual você deseja associar a tabela de rotas.
1. Selecione **tabela de rotas**, selecione a tabela de rotas que você deseja associar à sub-rede e selecione **Salvar**.

Se sua rede virtual está conectada a um gateway de VPN do Azure, não associe a uma tabela de rota para o [sub-rede de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub) que inclui uma rota com um destino de 0.0.0.0/0. Isso pode impedir que o gateway funcione corretamente. Para obter mais informações sobre como usar 0.0.0.0/0 em uma rota, consulte [roteamento de tráfego de rede Virtual](virtual-networks-udr-overview.md#default-route).

### <a name="associate-a-route-table---commands"></a>Associar uma tabela de rotas – comandos

* CLI do Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="dissociate-a-route-table-from-a-subnet"></a>Desassociar uma tabela de rotas de uma sub-rede

Quando você desassocia uma tabela de rota de uma sub-rede, o Azure roteia o tráfego com base em suas [rotas padrão](virtual-networks-udr-overview.md#default).

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
1. Selecione a rede virtual na lista que contém a sub-rede da qual você deseja desassociar uma tabela de rotas.
1. Selecione **Sub-redes** em **CONFIGURAÇÕES**.
1. Selecione a sub-rede da qual você deseja desassociar a tabela de rotas.
1. Selecione **Tabela de rotas**, selecione **Nenhum** e, em seguida, selecione **Salvar**.

### <a name="dissociate-a-route-table---commands"></a>Dissociar uma tabela de rotas – comandos

* CLI do Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet?view=azure-cli-latest)<br>
* PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-route-table"></a>Excluir uma tabela de rotas

Se uma tabela de rotas está associada a alguma sub-rede, ela não pode ser excluída. [Desassocie](#dissociate-a-route-table-from-a-subnet) uma tabela de rota de todas as sub-redes antes de tentar excluí-la.

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione **...** no lado direito da tabela de rotas que você deseja excluir.
1. Selecione **Excluir** e, em seguida, selecione **Sim**.

### <a name="delete-a-route-table---commands"></a>Excluir uma tabela de rotas – comandos

* CLI do Azure: [az network route-table delete](/cli/azure/network/route-table/route)<br>
* PowerShell: [Remove-AzRouteTable](/powershell/module/az.network/remove-azroutetable)

## <a name="create-a-route"></a>Criar uma rota

Há um limite para o número de rotas por tabela de rotas que você pode criar por assinatura e local do Azure. Para obter detalhes, confira [Limites do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione da lista a tabela de rotas à qual você deseja adicionar uma rota.
1. Selecione **Rotas**, em **CONFIGURAÇÕES**.
1. Selecione **+ Adicionar**.
1. Insira um único **Nome** para a rota dentro da tabela de rotas.
1. Insira o **Prefixo de endereço**, em notação CIDR, para o qual você deseja rotear o tráfego. O prefixo não pode ser duplicado em mais de uma rota dentro da tabela de rotas, embora o prefixo possa estar dentro de outro prefixo. Por exemplo, se você definir 10.0.0.0/16 como um prefixo em uma rota, ainda poderá definir outra rota com o prefixo de endereço 10.0.0.0/24. O Azure seleciona uma rota para o tráfego com base na correspondência de prefixo mais longa. Para saber mais sobre como o Azure seleciona rotas, veja [Visão geral de roteamento](virtual-networks-udr-overview.md#how-azure-selects-a-route).
1. Selecione um **Tipo do próximo salto**. Para obter uma descrição detalhada de todos os tipos de próximo salto, veja [Visão geral de roteamento](virtual-networks-udr-overview.md).
1. Insira um endereço IP para **Endereço do próximo salto**. Só é possível inserir um endereço se você seleciona *Dispositivo virtual* para **Tipo do próximo salto**.
1. Selecione **OK**.

### <a name="create-a-route---commands"></a>Criar um comando de rota

* CLI do Azure: [az network route-table route create](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [New-AzRouteConfig](/powershell/module/az.network/new-azrouteconfig)

## <a name="view-routes"></a>Exibir rotas

Uma tabela de rotas contém zero ou várias rotas. Para saber mais sobre as informações listadas ao exibir rotas, veja a [Visão geral de roteamento](virtual-networks-udr-overview.md).

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione da lista a tabela de rotas cujas rotas você deseja exibir.
1. Selecione **Rotas**, em **CONFIGURAÇÕES**.

### <a name="view-routes---commands"></a>Exibir rotas-comandos

* CLI do Azure: [az network route-table route list](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="view-details-of-a-route"></a>Exibir detalhes de uma rota

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione a tabela de rotas que contém uma rota cujos detalhes você deseja exibir.
1. Selecione **Rotas**.
1. Selecione a rota cujos detalhes você deseja exibir.

### <a name="view-details-of-a-route---commands"></a>Exibir detalhes de um comando de rota

* CLI do Azure: [az network route-table route show](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Get-AzRouteConfig](/powershell/module/az.network/get-azrouteconfig)

## <a name="change-a-route"></a>Alterar uma rota

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione a tabela de rotas que contém uma rota que você deseja alterar.
1. Selecione **Rotas**.
1. Selecione a rota que você deseja alterar.
1. Altere as configurações existentes para suas novas configurações e selecione **Salvar**.

### <a name="change-a-route---commands"></a>Alterar um comando de rota

* CLI do Azure: [az network route-table route update](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [set-AzRouteConfig](/powershell/module/az.network/set-azrouteconfig)

## <a name="delete-a-route"></a>Excluir uma rota

1. Na caixa de pesquisa na parte superior do portal, digite *tabelas de rota* na caixa de pesquisa. Quando as **Tabelas de rota** aparecem nos resultados da pesquisa, selecione essa opção.
1. Selecione a tabela de rotas que contém uma rota que você deseja excluir.
1. Selecione **Rotas**.
1. Na lista de rotas, selecione **...** no lado direito da rota que você deseja excluir.
1. Selecione **Excluir** e, em seguida, selecione **Sim**.

### <a name="delete-a-route---commands"></a>Excluir um comando de rota

* CLI do Azure: [az network route-table route delete](/cli/azure/network/route-table/route?view=azure-cli-latest)<br>
* PowerShell: [Remove-AzRouteConfig](/powershell/module/az.network/remove-azrouteconfig)

## <a name="view-effective-routes"></a>Exibir rotas efetivas

As rotas efetivas para cada adaptador de rede anexado a uma máquina virtual são uma combinação de tabelas de rotas criadas por você, rotas padrão do Azure e todas as rotas propagadas de redes locais por meio do BGP através de um gateway de rede virtual do Azure. Entender as rotas em vigor para um adaptador de rede é útil ao solucionar problemas de roteamento. Você pode exibir as rotas em vigor para qualquer adaptador de rede anexado a uma máquina virtual em execução.

1. Na caixa de pesquisa na parte superior do portal, digite o nome de uma máquina virtual cujas rotas em vigor você deseja exibir. Se você não souber o nome de uma máquina virtual, insira *máquinas virtuais* na caixa de pesquisa. Quando **Máquinas virtuais** aparecer nos resultados da pesquisa, selecione essa opção e selecione uma máquina virtual da lista.
1. Selecione **Rede** em **CONFIGURAÇÕES**.
1. Selecione o nome de um adaptador de rede.
1. Selecione **Rotas em vigor** em **SUPORTE + SOLUÇÃO DE PROBLEMAS**.
1. Examine a lista de rotas em vigor para determinar se existe a rota correta para o local ao qual você deseja rotear o tráfego. Saiba mais sobre os tipos de próximo salto que você vê nessa lista em [Visão geral do roteamento](virtual-networks-udr-overview.md).

### <a name="view-effective-routes---commands"></a>Exibir rotas efetivas-comandos

* CLI do Azure: [az network nic show-effective-route-table](/cli/azure/network/nic?view=azure-cli-latest)<br>
* PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="validate-routing-between-two-endpoints"></a>Validar o roteamento entre dois pontos de extremidade

Você pode determinar o tipo de próximo salto entre uma máquina virtual e o endereço IP de outro recurso do Azure, um recurso local ou um recurso na Internet. Determinar o roteamento do Azure é útil ao solucionar problemas de roteamento. Para concluir essa tarefa, você deve ter um Observador de Rede existente. Se você não tiver um Observador de Rede existente, crie um completando as etapas em [Criar uma instância do Observador de Rede](../network-watcher/network-watcher-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

1. Na caixa de pesquisa na parte superior do portal, digite *observador de rede* na caixa de pesquisa. Quando os resultados da pesquisa exibirem **Observador de Rede**, selecione essa opção.
1. Selecione **Próximo salto** em **FERRAMENTAS DE DIAGNÓSTICO DE REDE**.
1. Selecione sua **Assinatura** e o **Grupo de recursos** da máquina virtual de origem da qual provém o roteamento que você deseja validar.
1. Selecione a **Máquina virtual** e o **Adaptador de rede** anexado à máquina virtual, bem como o **Endereço IP de origem** atribuído ao adaptador de rede do qual provém o roteamento que você deseja validar.
1. Insira o **endereço IP de destino** para o qual se destina o roteamento que você deseja validar.
1. Selecione **Próximo salto**.
1. Após uma breve espera, serão retornadas informações que dizem a você o tipo de próximo salto e a ID da rota utilizada no roteamento do tráfego. Saiba mais sobre os tipos de próximo salto que você vê retornado em [Visão geral do roteamento](virtual-networks-udr-overview.md).

### <a name="validate-routing-between-two-endpoints---commands"></a>Validar o roteamento entre dois pontos de extremidade-comandos

* CLI do Azure: [az network watcher show-next-hop](/cli/azure/network/watcher?view=azure-cli-latest)<br>
* PowerShell: [Get-AzNetworkWatcherNextHop](/powershell/module/az.network/get-aznetworkwatchernexthop)

## <a name="permissions"></a>Permissões

Para executar tarefas em tabelas de rotas e em rotas, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as permissões apropriadas listadas na tabela a seguir:

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

* Criar uma tabela usando o [PowerShell](powershell-samples.md) ou os scripts de exemplo da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)<br>
* Criar e aplicar a [Política do Azure](policy-samples.md) para redes virtuais
