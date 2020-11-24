---
title: Planejando a implantação da Solução VMware no Azure
description: Este artigo descreve um fluxo de trabalho de implantação da Solução VMware no Azure.  O resultado final é um ambiente pronto para a criação e a migração da VM (máquina virtual).
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 08a15e6f8cad4068415cec3353544829f2218fb0
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888973"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planejando a implantação da Solução VMware no Azure

Este artigo oferece o processo de planejamento para identificar e coletar os dados usados durante a implantação. Ao planejar sua implantação, lembre-se de documentar as informações coletadas para facilitar a referência durante a implantação.

Os processos deste início rápido resultam em um ambiente pronto para produção para a criação e migração de VMs (máquinas virtuais). 

>[!IMPORTANT]
>Antes de criar o recurso da Solução VMware no Azure, siga o artigo [Como habilitar o recurso da Solução VMware no Azure](enable-azure-vmware-solution.md) para enviar um tíquete de suporte para alocar seus hosts. Depois que a equipe de suporte receber sua solicitação, levará até cinco dias úteis para confirmar a solicitação e alocar os hosts. Se você tiver uma nuvem privada da Solução VMware no Azure existente e quiser mais hosts alocados, deverá passar pelo mesmo processo. 


## <a name="subscription"></a>Subscription

Identifique a assinatura que você planeja usar para implantar a Solução VMware no Azure.  Você pode criar uma assinatura ou reutilizar uma existente.

>[!NOTE]
>A assinatura deve ser associada a um Contrato Enterprise da Microsoft.

## <a name="resource-group"></a>Resource group

Identifique o grupo de recursos que você deseja usar para a Solução VMware no Azure.  Em geral, um grupo de recursos é criado especificamente para a Solução VMware no Azure, mas você pode usar um grupo de recursos existente.

## <a name="region"></a>Região

Identifique a região que você deseja que a Solução VMware no Azure seja implantada.  Para obter mais informações, veja o [Guia de produtos do Azure disponíveis por região](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware).

## <a name="resource-name"></a>Nome do recurso

Defina o nome do recurso que você usará durante a implantação.  O nome do recurso é um nome amigável e descritivo que você dará à nuvem privada da Solução VMware no Azure.

>[!IMPORTANT]
>O nome não pode ultrapassar 40 caracteres. Se o nome exceder esse limite, você não poderá criar endereços IP públicos para uso com a nuvem privada. 

## <a name="size-hosts"></a>Dimensionar hosts

Identifique o tamanho dos hosts que você deseja usar ao implantar a Solução VMware no Azure.  Para obter uma lista completa, confira a documentação [Nuvens privadas e clusters da Solução VMware no Azure](concepts-private-clouds-clusters.md#hosts).

## <a name="number-of-hosts"></a>Número de hosts

Defina o número de hosts que você deseja implantar na nuvem privada da Solução VMware no Azure.  É necessário no mínimo três nós e no máximo 16 por cluster.  Para obter mais informações, confira a documentação [Nuvem privada e clusters da Solução VMware no Azure](concepts-private-clouds-clusters.md#clusters).

Você sempre poderá estender o cluster mais tarde se precisar ir além do número de implantação inicial.

## <a name="vcenter-admin-password"></a>Senha de administrador do vCenter
Defina a senha de administrador do vCenter.  Durante a implantação, você criará uma senha de administrador do vCenter. A senha é para a conta do administrador cloudadmin@vsphere.local durante a criação do vCenter. Você a usará para entrar no vCenter.

## <a name="nsx-t-admin-password"></a>Senha de administrador do NSX-T
Defina a senha de administrador do NSX-T.  Durante a implantação, você criará uma senha de administrador do NSX-T. A senha é atribuída ao usuário administrador na conta do NSX durante a criação do NSX. Você a usará para entrar no NSX-T Manager.

## <a name="ip-address-segment"></a>Segmento de endereço IP

A primeira etapa do planejamento da implantação é a segmentação de IP.  A Solução VMware no Azure ingere uma rede /22 fornecida por você. Depois ela forma segmentos menores e usa esses segmentos de IP para o vCenter, o HCX da VMware, o NSX-T e o vMotion.

A Solução VMware no Azure conecta-se à sua Rede Virtual do Microsoft Azure por meio de um circuito interno do ExpressRoute. Na maioria dos casos, ela se conecta ao seu data center por meio do Alcance Global do ExpressRoute. 

A Solução VMware no Azure, seu ambiente existente do Azure e seu ambiente local fazem intercâmbio de rotas (normalmente). Nesse caso, o bloco de endereço de rede CIDR /22 que você define nesta etapa não deve sobrepor nada que você já tenha localmente ou no Azure.

**Exemplo:** 10.0.0.0/22

Para obter mais informações, confira a [Lista de verificação de planejamento de rede](tutorial-network-checklist.md#routing-and-subnet-considerations).

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificar – segmento de endereço IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segmento de endereço IP para cargas de trabalho de máquina virtual

Identifique um segmento de IP para criar sua primeira rede (segmento NSX) na nuvem privada.  Em outras palavras, você deseja criar um segmento de rede na Solução VMware no Azure para poder implantar VMs na Solução VMware no Azure.   

Mesmo se você só planejar estender redes L2, crie um segmento de rede que validará o ambiente.

Lembre-se que todos os segmentos IP criados devem ser exclusivos no Azure e no volume de memória local.  

**Exemplo:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificar – Segmento de endereço IP para cargas de trabalho de máquina virtual" border="false":::     

## <a name="optional-extend-networks"></a>(Opcional) Estender redes

Você pode estender segmentos de rede locais para a Solução VMware no Azure e, se quiser fazer isso, identifique essas redes agora.  

Tenha em mente que:

- Se você planeja estender redes locais, essas redes devem se conectar a um [vDS (vSphere Distributed Switch)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) no ambiente do VMware local.  
- Se as redes que você deseja estender estão em um [vSphere Standard Switch](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html), elas não podem ser estendidas.

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Rede Virtual do Azure para anexar a Solução VMware no Azure

Para acessar a nuvem privada da Solução VMware no Azure, o circuito do ExpressRoute, que vem com a Solução VMware no Azure, deve ser anexado a uma Rede Virtual do Azure.  Durante a implantação, você pode definir uma nova rede virtual ou escolher uma existente.

O circuito do ExpressRoute da Solução VMware no Azure conecta-se a um gateway do ExpressRoute na Rede Virtual do Azure que você define nesta etapa.  

>[!IMPORTANT]
>Você pode usar um Gateway de ExpressRoute existente para se conectar à Solução VMware no Azure, desde que ela não exceda o limite de quatro circuitos de ExpressRoute por rede virtual.  No entanto, para acessar a Solução VMware no Azure do local por meio do ExpressRoute, você precisa ter o Alcance Global do ExpressRoute, pois o gateway do ExpressRoute não fornece roteamento transitivo entre os circuitos conectados.  

Se você quiser conectar o circuito do ExpressRoute da Solução VMware no Azure a um gateway existente do ExpressRoute, faça isso após a implantação.  

Portanto, em resumo, você deseja conectar a Solução VMware no Azure a um Gateway do Express Route existente?  

* **Sim** = identifique a rede virtual que não é usada durante a implantação.
* **Não** = identifique uma rede virtual existente ou crie uma durante a implantação.

De qualquer forma, documente o que você deseja fazer nesta etapa.

>[!NOTE]
>Essa rede virtual é vista pelo seu ambiente local e pela Solução VMware no Azure, portanto, o segmento IP que você usa nessa rede virtual e nas sub-redes não deve se sobrepor.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identidade – Rede Virtual do Azure para anexar a Solução VMware no Azure" border="false":::

## <a name="vmware-hcx-network-segments"></a>Segmentos de rede do HCX da VMware

O HCX da VMware é uma tecnologia incluída na Solução VMware no Azure. Os principais casos de uso da VMware HCX são para migrações de carga de trabalho e recuperação de desastres. Se você planeja usar algum destes cenários, é melhor planejar a rede agora.   Caso contrário, ignore e continue na próxima etapa.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você reuniu e documentou as informações necessárias, passe para a próxima seção para criar a nuvem privada da Solução VMware no Azure.

> [!div class="nextstepaction"]
> [Implantar a Solução VMware no Azure](deploy-azure-vmware-solution.md)
