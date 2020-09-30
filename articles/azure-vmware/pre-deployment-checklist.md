---
title: Lista de verificação de pré-implantação da solução Azure VMware
description: Use esta lista de verificação como parte do processo de planejamento de pré-implantação.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579511"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Lista de verificação de pré-implantação da solução Azure VMware
Você usará essa lista de verificação de pré-implantação durante a [fase de planejamento](production-ready-deployment-steps.md).

## <a name="success-criteria"></a>Critérios de êxito
Defina quais testes você vai executar e o resultado esperado.

| Informações fundamentais necessárias | Sua resposta |
| ----------- | ------------- |
| Implantar o SDDC | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar rede virtual | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar caixa de salto | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar gateway de rede virtual | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Conectar Alcance Global do ExpressRoute | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Faça logon no NSX Manager e no vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Informações principais necessárias | Sua resposta |
| --------| --------------|
| Criar servidor DHCP | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar segmentos de rede | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Dimensionar (adicionar ou excluir nós) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Implantar o VMware HCX | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar máquinas virtuais (VMs) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Habilitar Internet | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Migração de VM do local para a nuvem privada | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Informações adicionais necessárias | Sua resposta |
| --------| --------------|
| Operações de instantâneo de VM | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Implantar o balanceador de carga do NSX-T | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Integração do Azure<br><ul><li>Biblioteca de conteúdo compartilhada</li><li>Integração de segurança</li><li>Carregar ISO</li><li>Compilar a partir do ISO</li><li>Serviço de Backup do Azure</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Micro segmentação | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Roteamento | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Informações da solução Azure VMware

#### <a name="azure-subscription"></a>Assinatura do Azure
Forneça o nome da assinatura e a ID da assinatura para a solução do Azure VMware. A assinatura pode ser uma assinatura nova ou existente. Não use uma assinatura de desenvolvimento/teste.

| Informações necessárias  | Sua resposta |
| ------------| ------------- |
| Assinatura e ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Assinatura com um EA | ☐ Sim &nbsp; &nbsp; ☐ não  |
| Nome do Grupo de Recursos | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Local | Leste dos EUA ☒ |
| Administrador do Azure<br><br>Forneça o nome e o contato do administrador<br>atribuído para habilitar o serviço do Marketplace.<br>Colaborador é a função mínima necessária para <br>[Registre o provedor de recursos da solução VMware do Azure](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>Informações do VMware local

| Informações necessárias  | Sua resposta |
| ------------| --------------|
| Versão do vSphere | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| versão do vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| administrador do vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Extensão L2<br><br>Se estiver estendendo redes de L2 com o VMware HCX,<br>forneça a rede local que será estendida. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>Forneça o tipo de vSwitch usado em todo o ambiente. | ☐ &nbsp; &nbsp; ☐ Padrão distribuído<br><br>Se estiver usando Standard, o VMware HCX não estará disponível. |
| DNS e DHCP<br><br>Uma infraestrutura apropriada de DNS e DHCP é necessária. <br>É recomendável usar o serviço DHCP interno do <br>NSX ou usar um servidor DHCP local na nuvem privada <br>em vez de rotear tráfego DHCP de difusão no <br>WAN de volta para o local. Para mais informações, <br>consulte [como criar e gerenciar o DHCP na solução VMware do Azure](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Rede – infraestrutura de solução do Azure VMware 
Os dados necessários são para ajudá-lo a atender às necessidades de rede da solução VMware do Azure para o teste de rede inicial e de configuração. 

| Informações necessárias | Sua resposta |
| ----------- | ------------- |
|CIDR da solução VMware do Azure<br><br>Necessário para hosts vSphere, vSAN e gerenciamento <br>redes na solução VMware do Azure. Para obter mais <br>informações, consulte [considerações de roteamento e sub-rede](tutorial-network-checklist.md#routing-and-subnet-considerations). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| CIDR de carga de trabalho da solução VMware do Azure (opcional)<br><br>Atribuir uma rede a ser usada no Azure VMware<br>Solução para teste inicial. Máquinas virtuais<br>será implantado para validar a conectividade de rede <br>de/para a solução do Azure VMware. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Rede – conectar a solução VMware do Azure à rede virtual do Azure
Os dados necessários para o depois que o cluster da solução do Azure VMware é acima podem ser conectados ao Azure por meio de um circuito do ExpressRoute, que faz parte do serviço de solução do Azure VMware.

| Informações necessárias | Sua resposta |
| ------------------ | ------------- |
| Caixa de Jump-Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar rede virtual | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar sub-rede de gateway | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Criar gateway de rede virtual<br><br>Para obter mais informações, consulte [criar o gateway de rede virtual](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Rede – conectar a solução VMware do Azure a um datacenter local

| Informações necessárias | Sua resposta |
| ------------------ | ------------- |
| CIDR de emparelhamento do ExpressRoute<br><br>O `/29` bloco de endereço CIDR. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Chave de autorização e ID de recurso do ExpressRoute<br><br>Forneça uma chave de autorização e uma ID de recurso, <br>que é gerado a partir do ExpressRoute atual <br>circuito que se conecta ao datacenter local.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Direção da rota padrão<br><br>Devem ser as máquinas virtuais na solução VMware do Azure <br>acessar a Internet por meio da solução VMware do Azure fornecida <br>Internet ou volte pelo circuito do ExpressRoute para <br>local para a rota padrão? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Portas de rede necessárias para comunicação com o serviço<br><br>Para obter mais informações, consulte [portas de rede necessárias](tutorial-network-checklist.md#required-network-ports). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Rede – VMware HCX

| Informações necessárias | Sua resposta |
| ------------------ | ------------- |
| Portas de rede<br><br>Se houver um firewall, verifique se as portas de rede necessárias <br>são abertos entre a solução local e o Azure VMware. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Para obter informações sobre como configurar o DNS, <br>consulte [rede – infraestrutura de solução do Azure VMware](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| CIDRs do VMware HCX<br><br>Você precisa `/29` de dois blocos CIDR, que são usados para <br>os componentes do VMware HCX Infrastructure no local.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

