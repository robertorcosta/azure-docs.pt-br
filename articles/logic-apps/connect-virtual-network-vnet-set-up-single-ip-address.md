---
title: Configure um endereço IP de saída pública para ISEs
description: Saiba como configurar um único endereço IP de saída pública para ambientes de serviço de integração (ISEs) em Aplicativos de Lógica Do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: 619c68b84291bc35b8216194ac4534393fde454c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191454"
---
# <a name="set-up-a-single-ip-address-for-one-or-more-integration-service-environments-in-azure-logic-apps"></a>Configure um único endereço IP para um ou mais ambientes de serviço de integração em Aplicativos de Lógica do Azure

Quando você trabalha com o Azure Logic Apps, você pode configurar um [ *ambiente de serviço de integração* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) para aplicativos de lógica de hospedagem que precisam de acesso a recursos em uma [rede virtual Do Azure.](../virtual-network/virtual-networks-overview.md) Quando você tem várias instâncias ISE que precisam acessar outros pontos finais que possuem restrições de IP, implante um [Firewall Azure](../firewall/overview.md) ou um [dispositivo virtual](../virtual-network/virtual-networks-overview.md#filter-network-traffic) de rede em sua rede virtual e rode o tráfego de saída através desse firewall ou dispositivo virtual de rede. Em seguida, você pode ter todas as instâncias ISE em sua rede virtual usar um único endereço IP, público, estático e previsível para se comunicar com sistemas de destino. Dessa forma, você não precisa configurar aberturas adicionais de firewall nesses sistemas de destino para cada ISE.

Este tópico mostra como direcionar o tráfego de saída através de um Firewall Azure, mas você pode aplicar conceitos semelhantes a um dispositivo virtual de rede, como um firewall de terceiros do Azure Marketplace. Embora este tópico se concentre na configuração de várias instâncias ISE, você também pode usar essa abordagem para um único ISE quando seu cenário exige limitar o número de endereços IP que precisam de acesso. Considere se os custos adicionais para o firewall ou o aparelho de rede virtual fazem sentido para o seu cenário. Saiba mais sobre [os preços do Firewall Do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="prerequisites"></a>Pré-requisitos

* Um firewall Azure que é executado na mesma rede virtual que o seu ISE. Se você não tiver um firewall, primeiro adicione uma `AzureFirewallSubnet` [sub-rede](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet) nomeada à sua rede virtual. Em seguida, você pode [criar e implantar um firewall](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall) em sua rede virtual.

* Uma tabela de [rota](../virtual-network/manage-route-table.md)do Azure. Se você não tem um, primeiro [crie uma tabela de rotas.](../virtual-network/manage-route-table.md#create-a-route-table) Para obter mais informações sobre roteamento, consulte [Roteamento de tráfego de rede virtual](../virtual-network/virtual-networks-udr-overview.md).

## <a name="set-up-route-table"></a>Configurar tabela de rotas

1. No [portal Azure,](https://portal.azure.com)selecione a tabela de rotas, por exemplo:

   ![Selecione a tabela de rotas com a regra para direcionar o tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/select-route-table-for-virtual-network.png)

1. Para [adicionar uma nova rota,](../virtual-network/manage-route-table.md#create-a-route)no menu da tabela de rotas, selecione **'Adicionar rotas'.** **Routes** > 

   ![Adicionar rota para direcionar tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-route-to-route-table.png)

1. No **painel de rota Adicionar,** [configure a nova rota](../virtual-network/manage-route-table.md#create-a-route) com uma regra que especifica que todo o tráfego de saída para o sistema de destino segue esse comportamento:

   * Usa o [**aparelho Virtual**](../virtual-network/virtual-networks-udr-overview.md#user-defined) como o próximo tipo de salto.

   * Vai para o endereço IP privado para a instância de firewall como o próximo endereço de salto.

     Para encontrar esse endereço IP, no menu do firewall, selecione **Visão geral,** encontre o endereço em **endereço IP privado,** por exemplo:

     ![Encontre o endereço IP privado do firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/find-firewall-private-ip-address.png)

   Aqui está um exemplo que mostra como tal regra pode parecer:

   ![Configurar regra para direcionar tráfego de saída](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-rule-to-route-table.png)

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome da rota** | <*nome de rota única*> | Um nome único para a rota na tabela de rotas |
   | **Prefixo de endereço** | <*endereço de destino*> | O endereço do sistema de destino onde você quer que o tráfego vá. Certifique-se de usar a [notação CIDR (Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para este endereço. |
   | **Próximo tipo de salto** | **Aparelho virtual** | O tipo de [salto](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools) que é usado pelo tráfego de saída |
   | **Endereço do próximo salto** | <*firewall-private-IP-address*> | O endereço IP privado para o firewall |
   |||

## <a name="set-up-network-rule"></a>Configurar a regra de rede

1. No portal Azure, encontre e selecione seu firewall. No menu firewall, em **Configurações,** selecione **Regras**. No painel de regras, selecione **Coleção de regras de rede** > **Adicionar coleta de regras de rede**.

   ![Adicionar coleta de regras de rede ao firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/add-network-rule-collection.png)

1. Na coleta, adicione uma regra que permite o tráfego para o sistema de destino.

   Por exemplo, suponha que você tenha um aplicativo lógico que seja executado em um ISE e precise se comunicar com um sistema SFTP. Você cria uma coleção de `LogicApp_ISE_SFTP_Outbound`regras de rede nomeada `ISE_SFTP_Outbound`, que contém uma regra de rede chamada . Esta regra permite o tráfego a partir do endereço IP de qualquer sub-rede onde seu ISE é executado em sua rede virtual para o sistema SFTP de destino usando o endereço IP privado do seu firewall.

   ![Configurar a regra de rede para firewall](./media/connect-virtual-network-vnet-set-up-single-ip-address/set-up-network-rule-for-firewall.png)

   **Propriedades de coleta de regras de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*nome de coleção de regras de rede*> | O nome para sua coleção de regras de rede |
   | **Priority** | <*nível de prioridade*> | A ordem de prioridade para usar para executar a coleta de regras. Para obter mais informações, [veja Quais são alguns conceitos do Azure Firewall?](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts) |
   | **Ação** | **Permitir** | O tipo de ação a ser realizado para esta regra |
   |||

   **Propriedades de regra de rede**

   | Propriedade | Valor | Descrição |
   |----------|-------|-------------|
   | **Nome** | <*nome de regra de rede*> | O nome para sua regra de rede |
   | **Protocolo** | <*protocolos de conexão*> | Os protocolos de conexão para usar. Por exemplo, se você estiver usando as regras do NSG, selecione **TCP** e **UDP,** não apenas **TCP**. |
   | **Endereços de origem** | <*Endereços de sub-rede ISE*> | O IP da sub-rede aborda onde seu ISE é executado e onde o tráfego do seu aplicativo lógico se origina |
   | **Endereços de destino** | <*endereço IP de destino*> | O endereço IP do seu sistema de destino onde você deseja que o tráfego vá |
   | **Portas de destino** | <*destinos-portos*> | Quaisquer portas que seu sistema de destino use para comunicação de entrada |
   |||

   Para obter mais informações sobre as regras da rede, consulte estes artigos:

   * [Configurar uma regra de rede](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)
   * [Lógica de processamento de regra do Firewall do Azure](../firewall/rule-processing.md#network-rules-and-applications-rules)
   * [Perguntas frequentes do Firewall do Azure](../firewall/firewall-faq.md)
   * [Azure PowerShell: New-AzFirewallNetworkRule](https://docs.microsoft.com/powershell/module/az.network/new-azfirewallnetworkrule)
   * [Azure CLI: regra de rede de firewall de rede az](https://docs.microsoft.com/cli/azure/ext/azure-firewall/network/firewall/network-rule?view=azure-cli-latest#ext-azure-firewall-az-network-firewall-network-rule-create)

## <a name="next-steps"></a>Próximas etapas

* [Conectar-se a redes virtuais do Azure dos Aplicativos Lógicos do Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)