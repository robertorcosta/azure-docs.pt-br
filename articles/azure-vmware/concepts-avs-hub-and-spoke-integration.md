---
title: Conceito – integrar uma implantação da AVS (solução do Azure VMware) em uma arquitetura de Hub e spoke
description: Saiba mais sobre as recomendações para a integração de uma implantação da solução de VMware Azure (AVS) em uma arquitetura de Hub e spoke existente ou nova no Azure.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 82937e04fc0a5101c353702b92b6b068d027d7ad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85374943"
---
# <a name="integrate-azure-vmware-solution-avs-in-a-hub-and-spoke-architecture"></a>Integrar a solução do Azure VMware (AVS) em uma arquitetura de Hub e spoke

Neste artigo, fornecemos recomendações para integrar uma implantação da AVS (solução do Azure VMware) em uma [arquitetura de Hub e spoke](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/shared-services) existente ou nova no Azure. 

O cenário Hub e spoke assumem um ambiente de nuvem híbrida com cargas de trabalho em:

* Azure nativo usando serviços IaaS ou PaaS
* SINCRONIZAÇÃO 
* vSphere local

## <a name="architecture"></a>Arquitetura

O *Hub* é uma rede virtual do Azure que atua como um ponto central de conectividade para sua nuvem privada local e AVS. Os *spokes* são redes virtuais emparelhadas com o Hub para habilitar a comunicação entre redes virtuais.

O tráfego entre o datacenter local, a nuvem privada de AVS e o Hub passam por conexões de ExpressRoute. As redes virtuais spoke geralmente contêm cargas de trabalho baseadas em IaaS, mas podem ter serviços de PaaS como [ambiente do serviço de aplicativo](../app-service/environment/intro.md), que tem integração direta com a rede virtual ou outros serviços de PaaS com o [link privado do Azure](https://docs.microsoft.com/azure/private-link/) habilitado. 

O diagrama mostra um exemplo de implantação de Hub e spoke no Azure conectado ao local e à AVS por meio do ExpressRoute.

:::image type="content" source="./media/hub-spoke/avs-hub-and-spoke-deployment.png" alt-text="Implantação de integração de Hub e spoke da AVS":::




A arquitetura tem os seguintes componentes principais:

-   **Site local:** Datacenters locais do cliente conectados ao Azure por meio de uma conexão de rota expressa.

-   **Nuvem privada de AVS:** AVS SDDC formado por um ou mais clusters vSphere, cada um com um máximo de 16 nós.

-   **Gateway de ExpressRoute:** Habilita a comunicação entre a nuvem privada da AVS, a rede local, os serviços compartilhados na rede virtual do Hub e as cargas de trabalho em execução em redes virtuais do spoke.

    > [!NOTE]
    > **Considerações sobre VPN S2S:** Para implantações de produção de AVS, não há suporte para o S2S do Azure devido aos requisitos de rede do HCX. No entanto, para uma implantação de PoC ou de não produção que não requer HCX, ela pode ser usada.

-   **Rede virtual do Hub:** Atua como o ponto central de conectividade para sua rede local e a nuvem privada de AVS.

-   **Rede virtual spoke**

    -   **Spoke de IaaS:** Um IaaS spoke hospedará cargas de trabalho baseadas em IaaS do Azure, incluindo conjuntos de disponibilidade de VM e conjuntos de dimensionamento de máquinas virtuais e os componentes de rede correspondentes.

    -   **Spoke de PaaS:** Um spoke de PaaS hospeda serviços de PaaS do Azure usando o endereçamento privado graças ao [ponto de extremidade privado](https://docs.microsoft.com/azure/private-link/private-endpoint-overview) e ao [link privado](https://docs.microsoft.com/azure/private-link/private-link-overview).

-   **Firewall do Azure:** Atua como a peça central para segmentar o tráfego entre os spokes, o local e a AVS.

-   **Gateway de aplicativo:** Expõe e protege os aplicativos Web que são executados em VMs IaaS/PaaS ou AVS (máquinas virtuais) de sincronização do Azure. Ele se integra a outros serviços como o gerenciamento de API.

## <a name="network-and-security-considerations"></a>Considerações sobre rede e segurança

As conexões do ExpressRoute permitem que o tráfego flua entre o local, a AVS e a malha de rede do Azure. O AVS usa o [ExpressRoute alcance global](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach) para implementar essa conectividade.

A conectividade local também pode usar o ExpressRoute Alcance Global, mas não é obrigatório.

* **Fluxo de tráfego de local para AVS**

  :::image type="content" source="media/hub-spoke/on-prem-to-avs-traffic-flow.png" alt-text="Fluxo de tráfego de local para AVS":::


* **Fluxo de tráfego da rede virtual AVS para o Hub**

  :::image type="content" source="media/hub-spoke/avs-to-hub-vnet-traffic-flow.png" alt-text="Fluxo de tráfego de rede virtual de AVS para Hub":::


Você pode encontrar mais detalhes sobre a rede AVS e conceitos de interconectividade na [documentação do produto AVS](https://docs.microsoft.com/azure/azure-vmware/concepts-networking).

### <a name="traffic-segmentation"></a>Segmentação de tráfego

O [Firewall do Azure](https://docs.microsoft.com/azure/firewall/) é a parte central da topologia hub e spoke, implantada na rede virtual do Hub. Use o Firewall do Azure ou outra solução de virtualização de rede com suporte do Azure para estabelecer regras de tráfego e segmentar a comunicação entre as cargas de trabalho diferentes de spokes, locais e AVS.

Crie tabelas de rotas para direcionar o tráfego para o Firewall do Azure.  Para as redes virtuais spoke, crie uma rota que defina a rota padrão para a interface interna do firewall do Azure, dessa forma, quando uma carga de trabalho na rede virtual precisar acessar o espaço de endereço AVS, o firewall poderá avaliá-lo e aplicar a regra de tráfego correspondente para permitir ou negar.  

:::image type="content" source="media/hub-spoke/create-route-table-to-direct-traffic.png" alt-text="Criar tabelas de rotas para direcionar o tráfego para o Firewall do Azure":::


> [!IMPORTANT]
> Não há suporte para uma rota com o prefixo de endereço 0.0.0.0/0 na configuração **GatewaySubnet** .

Defina rotas para redes específicas na tabela de rotas correspondente. Por exemplo, as rotas para alcançar o gerenciamento de AVS e os prefixos de IP de cargas de trabalho do local e vice-versa, roteando todo o tráfego de local para a nuvem privada de AVS por meio do firewall do Azure.

:::image type="content" source="media/hub-spoke/specify-gateway-subnet-for-route-table.png" alt-text="Definir rotas para redes específicas na tabela de rotas correspondente":::

Um segundo nível de segmentação de tráfego usando os grupos de segurança de rede dentro dos spokes e o Hub para criar uma política de tráfego mais granular. 


### <a name="application-gateway"></a>Gateway de Aplicativo

Aplicativo Azure gateway v1 e v2 foram testados com aplicativos Web que são executados em VMs AVS como um pool de back-end. Atualmente, o gateway de aplicativo é o único método com suporte para expor aplicativos Web em execução em VMs da AVS para a Internet. Ele também pode expor os aplicativos a usuários internos com segurança.

:::image type="content" source="media/hub-spoke/avs-second-level-traffic-segmentation.png" alt-text="Segundo nível de segmentação de tráfego usando os grupos de segurança de rede":::


### <a name="jumpbox-and-azure-bastion"></a>Jumpbox e bastiões do Azure

Acesse o ambiente AVS com Jumpbox, que é uma VM do Windows 10 ou do Windows Server implantada na sub-rede do serviço compartilhado dentro da rede virtual do Hub.

Como prática recomendada de segurança, implante [Microsoft Azure](https://docs.microsoft.com/azure/bastion/) serviço de bastiões na rede virtual do Hub. A bastiões do Azure fornece acesso contínuo de RDP e SSH a VMs implantadas no Azure sem a necessidade de provisionar endereços IP públicos para esses recursos. Depois de provisionar o serviço de bastiões do Azure, você pode acessar a VM selecionada no portal do Azure. Depois de estabelecer a conexão, uma nova guia é aberta, mostrando a área de trabalho Jumpbox e, a partir dessa área de trabalho, você pode acessar o plano de gerenciamento de nuvem privada da AVS.

> [!IMPORTANT]
> Não forneça um endereço IP público para a VM Jumpbox ou expor a porta 3389/TCP para a Internet pública. 


:::image type="content" source="media/hub-spoke/azure-bastion-hub-vnet.png" alt-text="Rede virtual do hub de bastiões do Azure":::


## <a name="azure-dns-resolution-considerations"></a>Considerações sobre a resolução de DNS do Azure

Para a resolução de DNS do Azure, há duas opções disponíveis:

-   Use os controladores de domínio Azure Active Directory (AD do Azure) implantados no Hub (descrito em [considerações de identidade](#identity-considerations)) como servidores de nomes.

-   Implantar e configurar uma zona privada de DNS do Azure.

A melhor abordagem é combinar ambos para fornecer resolução de nomes confiável para AVS, local e Azure.

Como recomendação de design geral, use a infraestrutura de DNS do Azure existente (nesse caso, o DNS Active Directory integrado) implantado em pelo menos duas VMs do Azure implantadas na rede virtual do Hub e configuradas nas redes virtuais do spoke para usar esses servidores DNS do Azure nas configurações de DNS.

O Azure DNS privado ainda pode ser usado onde a zona de DNS privado do Azure está vinculada às redes virtuais, e os servidores DNS são usados como resolvedores híbridos com encaminhamento condicional para o local/AVS executando nomes DNS utilizando a infraestrutura de DNS privado do Azure do cliente.

Há várias considerações a serem consideradas para as zonas privadas do DNS do Azure:

* O registro automático deve ser habilitado para que o DNS do Azure gerencie automaticamente o ciclo de vida dos registros DNS para as VMs implantadas em redes virtuais spoke.
* O número máximo de zonas DNS privadas às quais uma rede virtual pode ser vinculada com o registro automática habilitado é apenas uma.
* O número máximo de zonas DNS privadas às quais uma rede virtual pode ser vinculada é 1000 sem o registro automática habilitado.

Os servidores locais e AVS podem ser configurados com encaminhadores condicionais para resolver VMs no Azure para a zona de DNS privado do Azure.

## <a name="identity-considerations"></a>Considerações sobre identidade

Para fins de identidade, a melhor abordagem é implantar pelo menos um controlador de domínio do AD no Hub, usando a sub-rede do serviço compartilhado, idealmente dois deles no modo distribuído por zona ou em um conjunto de disponibilidade de VM. Consulte [centro de arquitetura do Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/adds-extend-domain) para estender seu domínio do AD local para o Azure.

Além disso, implante outro controlador de domínio no lado da AVS para atuar como identidade e origem do DNS no ambiente vSphere.

Para o vCenter e o SSO, defina uma origem de identidade no portal do Azure, em **Gerenciar \> fontes de \> identidade de identidade**.

Como prática recomendada, integre o [domínio do AD com o Azure Active Directory](https://docs.microsoft.com/azure/architecture/reference-architectures/identity/azure-ad).

<!-- LINKS - external -->
[Azure Architecture Center]: https://docs.microsoft.com/azure/architecture/

[Hub & Spoke topology]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke

[Azure networking documentation]: https://docs.microsoft.com/azure/networking/

<!-- LINKS - internal -->


