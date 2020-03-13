---
title: Adicionar, alterar ou excluir uma sub-rede de rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual no Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2018
ms.author: kumud
ms.openlocfilehash: de80094c3fd2df7d2f8b32d1e968e9bebea847a1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245038"
---
# <a name="add-change-or-delete-a-virtual-network-subnet"></a>Adicionar, alterar ou excluir uma sub-rede da rede virtual

Saiba como adicionar, alterar ou excluir uma sub-rede de rede virtual. Todos os recursos do Azure implantados em uma rede virtual são implantados em uma sub-rede dentro de uma rede virtual. Se você é novo em redes virtuais, aprenda mais sobre eles no [visão geral de Rede Virtual](virtual-networks-overview.md) ou executando um [tutorial](quick-create-portal.md). Para criar, alterar ou excluir uma rede virtual, consulte [Gerenciar uma rede virtual](manage-virtual-network.md).

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando o Portal, abra https://portal.azure.com e faça logon com sua conta do Azure.
- Se usar os comandos do PowerShell para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/powershell) ou então executando o PowerShell do computador. O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo. Ele tem ferramentas do Azure instaladas e configuradas para usar com sua conta. Este tutorial exige o módulo do Azure PowerShell versão 1.0.0 ou posterior. Execute `Get-Module -ListAvailable Az` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.
- Se usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, execute os comandos no [Azure Cloud Shell](https://shell.azure.com/bash) ou então executando a CLI do computador. Este tutorial requer a CLI do Azure versão 2.0.31 ou posterior. Execute `az --version` para localizar a versão instalada. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli). Se estiver executando a CLI do Azure localmente, você também precisará executar o `az login` para criar uma conexão com o Azure.

A conta em que você realizou o logon, ou se conectou ao Azure, deve estar atribuída à função do [contribuidor de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) que é atribuída a ações adequadas listadas em [Permissões](#permissions).

## <a name="add-a-subnet"></a>Adicionar uma sub-rede

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual à qual deseja adicionar uma sub-rede.
3. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
4. Selecione **+Sub-rede**.
5. Insira os valores dos seguintes parâmetros:
   - **Nome**: o nome deve ser exclusivo na rede virtual. Para obter máxima compatibilidade com outros serviços do Azure, é recomendável usar uma letra como o primeiro caractere do nome. Por exemplo, o Gateway de Aplicativo do Azure não implantará em uma sub-rede cujo nome começa com um número.
   - **Intervalo de endereços**: o intervalo deve ser exclusivo dentro do espaço de endereços para a rede virtual. O intervalo não pode sobrepor-se com outros intervalos de endereços de sub-rede dentro da rede virtual. O espaço de endereçamento deve ser especificado utilizando a notação CIDR (Roteamento Entre Domínios sem Classes). Por exemplo, em uma rede virtual com espaço de endereçamento 10.0.0.0/16, você pode definir um espaço de endereçamento de sub-rede de 10.0.0.0/24. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, definir uma sub-rede com um intervalo de endereços /29 resulta em três endereços IP utilizáveis na sub-rede. Caso pretenda conectar uma rede virtual a um gateway de VPN, será necessário criar uma sub-rede de gateway. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é adicionada. Para saber como alterar um intervalo de endereços de sub-rede, consulte [Alterar as configurações de sub-rede](#change-subnet-settings).
   - **Grupo de segurança de rede**: é possível associar zero ou um grupo de segurança de rede existente à sub-rede para filtrar o tráfego de rede de entrada e saída para a sub-rede. O grupo de segurança da rede deve existir na mesma assinatura e local da rede virtual. Saiba mais sobre [grupos de segurança de rede](security-overview.md) e [como criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
   - **Tabela de rotas:** você pode associar zero ou uma tabela de rotas existente à sub-rede a controlar o roteamento de tráfego de rede para outras redes. A tabela de rotas deve existir na mesma assinatura e localização da rede virtual. Saiba mais sobre o [Roteamento do Azure](virtual-networks-udr-overview.md) e [como criar uma tabela de rotas](tutorial-create-route-table-portal.md)
   - **Pontos de extremidade de serviço:** uma sub-rede pode ter zero ou vários pontos de extremidade do serviço habilitados. Para habilitar um ponto de extremidade de serviço para um serviço, selecione o serviço ou serviços para os quais deseja habilitar os pontos de extremidade de serviço na lista **Serviços**. O local é configurado automaticamente para um ponto de extremidade. Por padrão, os pontos de extremidade de serviço são configurados para a região da rede virtual. No caso do Armazenamento do Azure, para dar suporte a cenários de failover regionais, os pontos de extremidade são automaticamente configurados para [Regiões emparelhadas do Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
   
       Para remover um ponto de extremidade de serviço, desmarque o serviço para o qual deseja remover o ponto de extremidade de serviço. Para saber mais sobre os pontos de extremidade do serviço e os serviços que podem ser habilitados, consulte [Visão geral dos pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md). Depois de habilitar um ponto de extremidade de serviço para um serviço, você também precisa habilitar o acesso à rede para a sub-rede para um recurso criado com o serviço. Por exemplo, se habilitar o ponto de extremidade de serviço para *Microsoft.Storage*, você também deverá habilitar o acesso à rede para todas as contas de Armazenamento do Azure a que deseja conceder acesso à rede. Para obter detalhes sobre como habilitar o acesso à rede para sub-redes para as quais um ponto de extremidade de serviço está habilitado, consulte a documentação do serviço individual para o qual você habilitou o ponto de extremidade de serviço.

     Para validar que um ponto de extremidade de serviço está habilitado para uma sub-rede, visualize as [rotas efetivas](diagnose-network-routing-problem.md) para qualquer interface de rede na sub-rede. Quando um ponto de extremidade estiver configurado, você verá uma rota*padrão* com os prefixos de endereço do serviço, e um nextHopType de **VirtualNetworkServiceEndpoint**. Para saber mais sobre roteamento, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md).
   - **Delegação de sub-rede:** uma sub-rede pode ter nenhuma ou várias delegações habilitadas para ela. A delegação de sub-rede dá permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede, usando um identificador exclusivo ao implantar o serviço. Para delegar para um serviço, selecione o serviço que você deseja delegar na lista **Serviços**.

6. Para adicionar a sub-rede à rede virtual que você selecionou, selecione **OK**.

**Comandos**

- CLI do Azure: [az network vnet subnet create](/cli/azure/network/vnet/subnet)
- PowerShell: [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig)

## <a name="change-subnet-settings"></a>Alterar as configurações de sub-rede

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede para a qual deseja alterar as configurações.
3. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
4. Na lista de sub-redes, selecione a sub-rede para a qual deseja alterar as configurações. É possível adicionar ou remover as seguintes configurações:

    - **Intervalo de endereços:** se nenhum recurso for implantado dentro da sub-rede, você poderá alterar o intervalo de endereços. Se houver recursos na sub-rede, você deverá mover os recursos para outra sub-rede ou excluí-los da sub-rede primeiro. As etapas necessárias para mover ou excluir um recurso variam de acordo com o recurso. Para saber como mover ou excluir os recursos que estão nas sub-redes, leia a documentação para cada tipo de recurso que deseja mover ou excluir. Consulte as restrições de **Intervalo de endereços** na etapa 5 de [Adicionar uma sub-rede](#add-a-subnet).
    - **Usuários**: é possível controlar o acesso à sub-rede utilizando funções internas ou suas próprias funções personalizadas. Para saber mais sobre como atribuir funções e usuários para acessar a sub-rede, consulte [Utilizar atribuição de função para gerenciar o acesso aos recursos do Azure](../role-based-access-control/role-assignments-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-role-assignment).
    - **Grupo de segurança de rede** e **Tabela de rotas**: Veja a etapa 5 de [Adicionar uma sub-rede](#add-a-subnet).
    - **Pontos de extremidade de serviço**: consulte pontos de extremidade de serviço na etapa 5 de [Adicionar uma sub-rede](#add-a-subnet). Ao habilitar um ponto de extremidade de serviço para uma sub-rede existente, certifique-se de que nenhuma tarefa crítica está executando qualquer recurso na sub-rede. Os pontos de extremidade de serviço alternam rotas em cada interface de rede na sub-rede do uso da rota padrão com o prefixo de endereço *0.0.0.0/0* e o tipo de próximo salto de *Internet*, usando uma nova rota com o endereço prefixos do serviço e um tipo de próximo salto de *VirtualNetworkServiceEndpoint*. Durante a troca, todas as conexões TCP abertas podem ser finalizadas. O ponto de extremidade de serviço não estará habilitado até que os fluxos de tráfego para o serviço para todas as interfaces de rede sejam atualizadas com a nova rota. Para saber mais sobre roteamento, consulte [Visão geral de roteamento](virtual-networks-udr-overview.md).
    - **Delegação de sub-rede**: consulte pontos de extremidade de serviço na etapa 5 de [Adicionar uma sub-rede](#add-a-subnet). A delegação de sub-rede pode ser modificada para ter nenhuma ou várias delegações habilitadas para ela. Se um recurso para um serviço já estiver implantado na sub-rede, a delegação de sub-rede não poderá ser adicionada ou removida até que todos os recursos do serviço sejam removidos. Para delegar para um serviço diferente, selecione o serviço que você deseja delegar na lista **Serviços**.
5. Clique em **Salvar**.

**Comandos**

- CLI do Azure: [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update)
- PowerShell: [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig)

## <a name="delete-a-subnet"></a>Excluir uma sub-rede

Uma sub-rede somente pode ser excluída se não houver recursos na sub-rede. Se houver recursos na sub-rede, será necessário excluir os recursos na sub-rede antes de excluí-la. As etapas necessárias para excluir um recurso variam de acordo com o recurso. Para saber como excluir os recursos que estão nas sub-redes, leia a documentação para cada tipo de recurso que deseja excluir.

1. Na caixa de pesquisa na parte superior do portal, digite *redes virtuais* na caixa de pesquisa. Quando **Redes virtuais** aparecer nos resultados da pesquisa, selecione essa opção.
2. Na lista de redes virtuais, selecione a rede virtual que contém a sub-rede que você deseja excluir.
3. Em **CONFIGURAÇÕES**, selecione **Sub-redes**.
4. Na lista de sub-redes, selecione **...** , à direita, para a sub-rede que deseja excluir
5. Selecione **Excluir** e, em seguida, selecione **Sim**.

**Comandos**

- CLI do Azure: [AZ Network vnet subnet Delete](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-delete)
- PowerShell: [Remove-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/remove-azvirtualnetworksubnetconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Permissões

Para executar tarefas em sub-redes, sua conta deve ser atribuída à função de [colaborador da rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) ou a uma função [personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) à qual são atribuídas as ações apropriadas listadas na tabela a seguir:

|Ação                                                                   |   Nome                                       |
|-----------------------------------------------------------------------  |   -----------------------------------------  |
|Microsoft.Network/virtualNetworks/subnets/read                           |   Ler uma sub-rede de rede virtual              |
|Microsoft.Network/virtualNetworks/subnets/write                          |   Criar ou atualizar uma sub-rede da rede virtual  |
|Microsoft.Network/virtualNetworks/subnets/delete                         |   Excluir uma sub-rede da rede virtual            |
|Microsoft.Network/virtualNetworks/subnets/join/action                    |   Unir uma rede virtual                     |
|Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action  |   Habilitar um ponto de extremidade de serviço para uma sub-rede     |
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read           |   Obter as máquinas virtuais em uma sub-rede       |

## <a name="next-steps"></a>Próximas etapas

- Criar uma rede virtual e sub-redes usando [PowerShell](powershell-samples.md) ou os scripts de exemplo da [CLI do Azure](cli-samples.md) ou usando os modelos do [Azure Resource Manager](template-samples.md)
- Criar e aplicar a [Política do Azure](policy-samples.md) para redes virtuais
