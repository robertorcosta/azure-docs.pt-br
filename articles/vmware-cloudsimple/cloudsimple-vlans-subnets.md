---
title: VLANs e sub-redes na solução Azure VMware por CloudSimple
description: Conheça VLANs e sub-redes no CloudSimple Private Cloud
author: sharaths-cs
ms.author: dikamath
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2451fbb69636624db354006df2a7925ef9e75459
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024884"
---
# <a name="vlans-and-subnets-overview"></a>VLANs e sub-redes visão geral

O CloudSimple fornece uma rede por região onde seu serviço CloudSimple é implantado.  A rede é um único espaço de endereço TCP Layer 3 com roteamento ativado por padrão.  Todas as nuvens privadas e sub-redes criadas nesta região podem se comunicar entre si sem qualquer configuração adicional.  Você pode criar grupos de portas distribuídas no vCenter usando as VLANs.

![Topologia de rede Simple Cloud](media/cloudsimple-network-topology.png)

## <a name="vlans"></a>VLANs

Uma VLAN (rede de camada 2) é criada para cada Nuvem Privada.  O tráfego da Camada 2 permanece dentro do limite de uma Nuvem Privada, permitindo que você isole o tráfego local dentro da Nuvem Privada.  Uma VLAN criada na Nuvem Privada pode ser usada para criar grupos de portas distribuídas apenas nessa Nuvem Privada.  Uma VLAN criada em uma Nuvem Privada é configurada automaticamente em todos os switches conectados aos hosts de uma Nuvem Privada.

## <a name="subnets"></a>Sub-redes

Você pode criar uma sub-rede ao criar uma VLAN definindo o espaço de endereço da sub-rede. Um endereço IP do espaço de endereço é atribuído como um gateway de sub-rede. Um único espaço de endereço privado da Camada 3 é atribuído por cliente e região. Você pode configurar qualquer espaço de endereço não sobreposto RFC 1918, com sua rede local ou rede virtual Azure, na sua região de rede.

Todas as sub-redes podem se comunicar entre si por padrão, reduzindo a sobrecarga de configuração para roteamento entre nuvens privadas. Os dados leste-oeste em PCs na mesma região permanecem na mesma rede de Camada 3 e são transferidos pela infra-estrutura de rede local na região. Não é necessário egresso para a comunicação entre nuvens privadas em uma região. Essa abordagem elimina qualquer penalidade de desempenho wan/egresso na implantação de diferentes cargas de trabalho em diferentes nuvens privadas.

## <a name="vspherevsan-subnets-cidr-range"></a>gama de sub-redes vSphere/vSAN CIDR

Um Nuvem Privado é criado como um ambiente isolado de VMware stack (hosts ESXi, vCenter, vSAN e NSX) gerenciado por um servidor vCenter.  Os componentes de gerenciamento são implantados na rede selecionada para as sub-redes vSphere/vSAN CIDR.  O intervalo CIDR da rede é dividido em diferentes sub-redes durante a implantação.

* Prefixo mínimo da faixa de vSphere/vSAN CIDR: **/24**
* Prefixo máximo da faixa de vSphere/vSAN CIDR: **/21**

> [!CAUTION]
> Os endereços IP na gama vSphere/vSAN CIDR são reservados para uso pela infra-estrutura Private Cloud.  Não use o endereço IP neste intervalo em nenhuma máquina virtual.

### <a name="vspherevsan-subnets-cidr-range-limits"></a>limites de intervalo de sub-redes vSphere/vSAN CIDR

Selecionar o tamanho da gama de sub-redes vSphere/vSAN CIDR tem um impacto no tamanho da sua Nuvem Privada.  A tabela a seguir mostra o número máximo de nós que você pode ter com base no tamanho das sub-redes vSphere/vSAN CIDR.

| Comprimento do prefixo das sub-redes vSphere/vSAN CIDR | Número máximo de nódulos |
|---------------------------------------------------|-------------------------|
| /24 | 26 |
| /23 | 58 |
| /22 | 118 |
| /21 | 220 |

### <a name="management-subnets-created-on-a-private-cloud"></a>Sub-redes de gerenciamento criadas em uma Nuvem Privada

As seguintes sub-redes de gerenciamento são criadas quando você cria uma Nuvem Privada.

* **Gestão do sistema**. VLAN e sub-rede para rede de gerenciamento de hosts ESXi, servidor DNS, servidor vCenter.
* **VMotion**. VLAN e sub-rede para a rede vMotion dos hosts ESXi.
* **VSAN**. VLAN e sub-rede para a rede vSAN dos hosts ESXi.
* **NsxtEdgeUplink1**. VLAN e sub-rede para uplinks VLAN para uma rede externa.
* **NsxtEdgeUplink2**. VLAN e sub-rede para uplinks VLAN para uma rede externa.
* **NsxtEdgeTransport**. VLAN e sub-rede para zonas de transporte controlam o alcance das redes de camada 2 no NSX-T.
* **NsxtHostTransport**. VLAN e sub-rede para zona de transporte de hospedeiro.

### <a name="management-network-cidr-range-breakdown"></a>Divisão do intervalo cidr da rede de gerenciamento

o intervalo de sub-redes vSphere/vSAN CIDR especificado é dividido em várias sub-redes.  A tabela a seguir mostra um exemplo da quebra para prefixos permitidos.  O exemplo usa 192.168.0.0 como faixa CIDR.

Exemplo:

| Sub-redes vSphere/vSAN especificadas CIDR/prefixo | 192.168.0.0/21 | 192.168.0.0/22 | 192.168.0.0/23 | 192.168.0.0/24 |
|---------------------------------|----------------|----------------|----------------|----------------|
| Gerenciamento do sistema | 192.168.0.0/24 | 192.168.0.0/24 | 192.168.0.0/25 | 192.168.0.0/26 |
| Vmotion | 192.168.1.0/24 | 192.168.1.0/25 | 192.168.0.128/26 | 192.168.0.64/27 |
| vSAN | 192.168.2.0/24 | 192.168.1.128/25 | 192.168.0.192/26 | 192.168.0.96/27 |
| Transporte de host NSX-T | 192.168.4.0/23 | 192.168.2.0/24 | 192.168.1.0/25 | 192.168.0.128/26 |
| Transporte de borda NSX-T | 192.168.7.208/28 | 192.168.3.208/28 | 192.168.1.208/28 | 192.168.0.208/28 |
| NNsX-T Edge Uplink1 | 192.168.7.224/28 | 192.168.3.224/28 | 192.168.1.224/28 | 192.168.0.224/28 |
| Relink de borda NSX-T2 | 192.168.7.240/28 | 192.168.3.240/28 | 192.168.1.240/28 | 192.168.0.240/28 |

## <a name="next-steps"></a>Próximas etapas

* [Criar e gerenciar VLANs e sub-redes](create-vlan-subnet.md)
