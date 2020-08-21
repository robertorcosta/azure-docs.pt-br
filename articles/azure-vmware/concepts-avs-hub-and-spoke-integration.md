---
title: Conceito – integrar uma implantação de solução do Azure VMware em uma arquitetura de Hub e spoke
description: Saiba mais sobre as recomendações para integrar uma implantação de solução do Azure VMware a uma arquitetura de Hub e spoke existente ou nova no Azure.
ms.topic: conceptual
ms.date: 08/20/2020
ms.openlocfilehash: deb2756f7e83250ff58836098dc4954ec482fbda
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684466"
---
# <a name="integrate-azure-vmware-solution-in-a-hub-and-spoke-architecture"></a>Integrar a solução do Azure VMware em uma arquitetura de Hub e spoke

Neste artigo, fornecemos recomendações para integrar uma implantação de solução do Azure VMware em uma [arquitetura de Hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/shared-services) existente ou nova no Azure. 

O cenário Hub e spoke assumem um ambiente de nuvem híbrida com cargas de trabalho em:

* Azure nativo usando serviços IaaS ou PaaS
* Solução VMware no Azure 
* vSphere local

## <a name="architecture"></a>Arquitetura

O *Hub* é uma rede virtual do Azure que atua como um ponto central de conectividade com a nuvem privada da solução local e do Azure VMware. Os *spokes* são redes virtuais emparelhadas com o Hub para habilitar a comunicação entre redes virtuais.

O tráfego entre o datacenter local, a nuvem privada da solução Azure VMware e o Hub passam por conexões do ExpressRoute. As redes virtuais spoke geralmente contêm cargas de trabalho baseadas em IaaS, mas podem ter serviços de PaaS como [ambiente do serviço de aplicativo](../app-service/environment/intro.md), que tem integração direta com a rede virtual ou outros serviços de PaaS com o [link privado do Azure](../private-link/index.yml) habilitado. 

O diagrama mostra um exemplo de implantação de Hub e spoke no Azure conectado à solução local e do Azure VMware por meio do ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Implantação do hub de solução do Azure VMware e integração de spoke" border="false":::

A arquitetura tem os seguintes componentes principais:

-   **Site local:** Datacenters locais do cliente conectados ao Azure por meio de uma conexão do ExpressRoute.

-   **Nuvem privada da solução Azure VMware:** Solução do Azure VMware SDDC formada por um ou mais clusters vSphere, cada um com um máximo de 16 nós.

-   **Gateway de ExpressRoute:** Habilita a comunicação entre a nuvem privada da solução do Azure VMware, a rede local, os serviços compartilhados na rede virtual do Hub e as cargas de trabalho em execução nas redes virtuais do spoke.

    > [!NOTE]
    > **Considerações sobre VPN S2S:** Para implantações de produção da solução do Azure VMware, não há suporte para o S2S do Azure devido aos requisitos de rede do HCX. No entanto, para uma implantação de PoC ou de não produção que não requer HCX, ela pode ser usada.

-   **Rede virtual do Hub:** Atua como o ponto central de conectividade para sua rede local e a nuvem privada da solução Azure VMware.

-   **Rede virtual spoke**

    -   **Spoke de IaaS:** Um IaaS spoke hospeda cargas de trabalho baseadas no Azure IaaS, incluindo conjuntos de disponibilidade de VM e conjuntos de dimensionamento de máquinas virtuais e os componentes de rede correspondentes.

    -   **Spoke de PaaS:** Um spoke de PaaS hospeda serviços de PaaS do Azure usando o endereçamento privado graças ao [ponto de extremidade privado](../private-link/private-endpoint-overview.md) e ao [link privado](../private-link/private-link-overview.md).

-   **Firewall do Azure:** Atua como a peça central para segmentar o tráfego entre os spokes, o local e a solução do Azure VMware.

-   **Gateway de aplicativo:** Expõe e protege aplicativos Web que são executados no Azure IaaS/PaaS ou em VMs (máquinas virtuais) da solução do Azure VMware. Ele se integra a outros serviços como o gerenciamento de API.

## <a name="network-and-security-considerations"></a>Considerações sobre rede e segurança

As conexões do ExpressRoute permitem que o tráfego flua entre o local, a solução do Azure VMware e a malha de rede do Azure. A solução Azure VMware usa o [ExpressRoute alcance global](../expressroute/expressroute-global-reach.md) para implementar essa conectividade.

A conectividade local também pode usar o ExpressRoute Alcance Global, mas não é obrigatório.

* **Fluxo de tráfego de solução local para o Azure VMware**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Fluxo de tráfego de solução local para o Azure VMware" border="false":::


* **Solução do Azure VMware para o fluxo de tráfego de VNET do Hub**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Solução do Azure VMware para o fluxo de tráfego de rede virtual do Hub" border="false":::


Você pode encontrar mais detalhes sobre os conceitos de rede e interconectividade da solução do Azure VMware na [documentação do produto da solução Azure VMware](./concepts-networking.md).

### <a name="traffic-segmentation"></a>Segmentação de tráfego

O [Firewall do Azure](../firewall/index.yml) é a parte central da topologia hub e spoke, implantada na rede virtual do Hub. Use o Firewall do Azure ou outra solução de virtualização de rede com suporte do Azure para estabelecer regras de tráfego e segmentar a comunicação entre os diferentes spokes, locais e cargas de trabalho de solução do Azure VMware.

Crie tabelas de rotas para direcionar o tráfego para o Firewall do Azure.  Para as redes virtuais spoke, crie uma rota que defina a rota padrão para a interface interna do firewall do Azure, dessa forma, quando uma carga de trabalho na rede virtual precisar acessar o espaço de endereço da solução VMware do Azure, o firewall poderá avaliá-la e aplicar a regra de tráfego correspondente para permitir ou negar.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Criar tabelas de rotas para direcionar o tráfego para o Firewall do Azure":::


> [!IMPORTANT]
> Não há suporte para uma rota com o prefixo de endereço 0.0.0.0/0 na configuração **GatewaySubnet** .

Defina rotas para redes específicas na tabela de rotas correspondente. Por exemplo, as rotas para alcançar o gerenciamento de soluções do Azure VMware e os prefixos de IP de cargas de trabalho do local e vice-versa, roteando todo o tráfego do local para a nuvem privada da solução do Azure VMware por meio do firewall do Azure.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Definir rotas para redes específicas na tabela de rotas correspondente":::

Um segundo nível de segmentação de tráfego usando os grupos de segurança de rede dentro dos spokes e o Hub para criar uma política de tráfego mais granular. 


### <a name="application-gateway"></a>Gateway de Aplicativo

Aplicativo Azure gateway v1 e v2 foram testados com aplicativos Web que são executados em VMs de solução do Azure VMware como um pool de back-end. Atualmente, o gateway de aplicativo é o único método com suporte para expor aplicativos Web em execução em VMs de solução do Azure VMware para a Internet. Ele também pode expor os aplicativos a usuários internos com segurança.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Segundo nível de segmentação de tráfego usando os grupos de segurança de rede" border="false":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox e bastiões do Azure

Acesse o ambiente de solução do Azure VMware com Jumpbox, que é uma VM do Windows 10 ou do Windows Server implantada na sub-rede do serviço compartilhado dentro da rede virtual do Hub.

Como prática recomendada de segurança, implante [Microsoft Azure](../bastion/index.yml) serviço de bastiões na rede virtual do Hub. A bastiões do Azure fornece acesso contínuo de RDP e SSH a VMs implantadas no Azure sem a necessidade de provisionar endereços IP públicos para esses recursos. Depois de provisionar o serviço de bastiões do Azure, você pode acessar a VM selecionada no portal do Azure. Depois de estabelecer a conexão, uma nova guia é aberta, mostrando a área de trabalho Jumpbox e, a partir dessa área de trabalho, você pode acessar o plano de gerenciamento de nuvem privada da solução Azure VMware.

> [!IMPORTANT]
> Não forneça um endereço IP público para a VM Jumpbox ou expor a porta 3389/TCP para a Internet pública. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Rede virtual do hub de bastiões do Azure" border="false":::


## <a name="azure-dns-resolution-considerations"></a>Considerações sobre a resolução de DNS do Azure

Para a resolução de DNS do Azure, há duas opções disponíveis:

-   Use os controladores de domínio Azure Active Directory (AD do Azure) implantados no Hub (descrito em [considerações de identidade](#identity-considerations)) como servidores de nomes.

-   Implantar e configurar uma zona privada de DNS do Azure.

A melhor abordagem é combinar ambos para fornecer resolução de nomes confiável para a solução do Azure VMware, no local e no Azure.

Como recomendação de design geral, use a infraestrutura de DNS do Azure existente (nesse caso, o DNS Active Directory integrado) implantado em pelo menos duas VMs do Azure implantadas na rede virtual do Hub e configuradas nas redes virtuais do spoke para usar esses servidores DNS do Azure nas configurações de DNS.

O Azure DNS privado ainda pode ser usado onde a zona de DNS privado do Azure está vinculada às redes virtuais, e os servidores DNS são usados como resolvedores híbridos com encaminhamento condicional para a solução local/Azure VMware que executa o DNS, aproveitando a infraestrutura de DNS privado do Azure do cliente.

Há várias considerações a serem consideradas para as zonas privadas do DNS do Azure:

* O registro automático deve ser habilitado para que o DNS do Azure gerencie automaticamente o ciclo de vida dos registros DNS para as VMs implantadas em redes virtuais spoke.
* O número máximo de zonas DNS privadas às quais uma rede virtual pode ser vinculada com o registro automática habilitado é apenas uma.
* O número máximo de zonas DNS privadas às quais uma rede virtual pode ser vinculada é 1000 sem o registro automática habilitado.

Os servidores de solução local e do Azure VMware podem ser configurados com encaminhadores condicionais para resolver VMs no Azure para a zona de DNS privado do Azure.

## <a name="identity-considerations"></a>Considerações sobre identidade

Para fins de identidade, a melhor abordagem é implantar pelo menos um controlador de domínio do AD no Hub, usando a sub-rede do serviço compartilhado, idealmente dois deles no modo distribuído por zona ou em um conjunto de disponibilidade de VM. Consulte [centro de arquitetura do Azure](/azure/architecture/reference-architectures/identity/adds-extend-domain) para estender seu domínio do AD local para o Azure.

Além disso, implante outro controlador de domínio no lado da solução do Azure VMware para atuar como identidade e origem do DNS no ambiente vSphere.

Para o vCenter e o SSO, defina uma origem de identidade no portal do Azure, em **Gerenciar \> fontes de \> identidade de identidade**.

Como prática recomendada, integre o [domínio do AD com o Azure Active Directory](/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: /azure/architecture/

[Hub & Spoke topology]: /azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: ../networking/index.yml

<!-- LINKS - internal -->
