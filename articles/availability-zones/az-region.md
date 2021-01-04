---
title: Serviços do Azure que dão suporte ao Zonas de Disponibilidade
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade oferecem locais fisicamente separados que você pode usar para executar seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 12/17/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 0365a60317538ba31f39928cd30a57e2c969c832
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723024"
---
# <a name="azure-services-that-support-availability-zones"></a>Serviços do Azure que dão suporte ao Zonas de Disponibilidade

Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege seus aplicativos e dados de falhas do datacenter. Para obter a lista de regiões existentes e futuras que dão suporte a Zonas de Disponibilidade, consulte [regiões e zonas de disponibilidade no Azure](az-overview.md).  

Esta seção lista os serviços do Azure que dão suporte ao Zonas de Disponibilidade. 

Os serviços que estão disponíveis em cada região, juntamente com o próximo roteiro para disponibilidade, podem ser encontrados em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).

Todos os serviços de gerenciamento do Azure são arquitetados para serem resilientes de falhas no nível de região. No espectro de falhas, uma ou mais falhas de zona de disponibilidade em uma região têm um raio de falha menor em comparação a uma falha de região inteira. O Azure pode se recuperar de uma falha no nível de zona dos serviços de gerenciamento dentro de uma região. O Azure realiza uma manutenção crítica em uma zona por vez dentro de uma região, para evitar falhas que afetem os recursos do cliente implantados em Zonas de Disponibilidade dentro de uma região.


![exibição conceitual de uma região do Azure com 3 zonas](./media/az-region/azure-region-availability-zones.png)


Os serviços do Azure que dão suporte a Zonas de Disponibilidade se enquadram em três categorias: serviços **zonas**, **com redundância de zona** e **não regionais** . As cargas de trabalho do cliente podem ser categorizadas para utilizar qualquer um desses cenários de arquitetura para atender à durabilidade e ao desempenho do aplicativo.

- **Serviços zonais** – um recurso pode ser implantado em uma zona de disponibilidade específica, selecionada automaticamente, para obter requisitos de desempenho ou latência mais rigorosos.  A resiliência é autoarquitetada ao replicar aplicativos e dados para uma ou mais zonas dentro da região.  Os recursos podem ser fixados em uma zona específica. Por exemplo, máquinas virtuais, discos gerenciados ou endereços IP padrão podem ser fixados em uma zona específica, o que permite maior resiliência por ter uma ou mais instâncias de recursos espalhadas entre zonas.

- **Serviços com redundância de zona** – a plataforma Azure replica o recurso e os dados entre zonas.  A Microsoft gerencia a entrega de alta disponibilidade, já que o Azure replica e distribui instâncias na região automaticamente.  ZRS, por exemplo, replica os dados entre três zonas para que uma falha de zona não afete a alta disponibilidade dos dados. 

- **Serviços não regionais** – serviços que não têm dependência em uma região específica do Azure, tornando-os resilientes a interrupções em toda a zona, bem como interrupções em toda a região.


Para obter uma continuidade de negócios abrangente no Azure, compile a arquitetura do aplicativo usando a combinação de Zonas de Disponibilidade com pares de regiões do Azure. É possível replicar os aplicativos e dados de maneira síncrona usando Zonas de Disponibilidade em uma região do Azure para alta disponibilidade e replicação assíncrona em regiões do Azure para proteção de recuperação de desastre. Para saber mais, leia [criando soluções para alta disponibilidade usando o zonas de disponibilidade](https://docs.microsoft.com/azure/architecture/high-availability/building-solutions-for-high-availability). 


### <a name="azure-services-supporting-availability-zones"></a>Serviços do Azure com suporte a Zonas de Disponibilidade

- As máquinas virtuais de geração mais antigas não estão listadas abaixo. Para obter mais informações, consulte [gerações anteriores de tamanhos de máquina virtual](../virtual-machines/sizes-previous-gen.md).

- Alguns serviços não são regionais, consulte [regiões e zonas de disponibilidade no Azure](az-overview.md) para obter mais informações. Esses serviços não têm dependência em uma região específica do Azure, tornando-os resilientes a interrupções de toda a zona e interrupções em toda a região.  A lista de serviços não regionais pode ser encontrada em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).



## <a name="americas"></a>Américas

| **Produtos** | **Centro dos EUA** | **Leste dos EUA** | **Leste dos EUA 2** | **Oeste dos EUA 2** | **Canadá Central** |
|--|--|--|--|--|--|
| **Computação** |  |  |  |  |  |
| [Ambientes do serviço de aplicativo (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | 
| **Contêineres** |  |  |  |
| [AKS (Serviço de Kubernetes do Azure)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Registro de Contêiner](../container-registry/zone-redundancy.md) |  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  | 
| **Armazenamento** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de arquivos Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de Blobs](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |  |
| [Gateway de aplicativo v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall do Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT de Rede Virtual](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |  |  |
| [Cache Redis do Azure](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados do Azure para MySQL-servidor flexível](../mysql/flexible-server/concepts-high-availability.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Servidor Flexível do Banco de Dados do Azure para PostgreSQL](../postgresql/flexible-server/overview.md) | :x: | :x: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| [Banco de dados SQL do Azure (Uso Geral camada)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | : heavy_check_mark: (visualização) | : heavy_check_mark: (visualização) | : heavy_check_mark: (visualização) | :x: |
| [Banco de dados SQL do Azure (Premium & camadas de Comercialmente Crítico)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |  |
| [Hubs de Evento](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |  |
| [Grade de Eventos](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Barramento de Serviço](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gerenciamento e Governança** |  |  |  |  |  |
| [Observador de Rede](../network-watcher/frequently-asked-questions.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :x: |
| **Segurança** |  |  |  |  |  |
| [Serviços de Domínio do Active Directory do Azure](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europa

| **Produtos** | **França Central** | **Norte da Europa** | **Sul do Reino Unido** | **Oeste da Europa** |
|--|--|--|--|--|
| **Computação** |  |  |  |  |
| [Ambientes do serviço de aplicativo (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS (Serviço de Kubernetes do Azure)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Armazenamento** |  |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de arquivos Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento de Blobs](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |
| [Gateway de aplicativo v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall do Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT de Rede Virtual](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |  |
| [Cache Redis do Azure](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados do Azure para MySQL-servidor flexível](../mysql/flexible-server/concepts-high-availability.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Servidor Flexível do Banco de Dados do Azure para PostgreSQL](../postgresql/flexible-server/overview.md) | :x: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados SQL do Azure (Uso Geral camada)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | : heavy_check_mark: (visualização) | :x: | : heavy_check_mark: (visualização) |
| [Banco de dados SQL do Azure (Premium & camadas de Comercialmente Crítico)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |
| [Hubs de Evento](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |
| [Grade de Eventos](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Barramento de Serviço](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gerenciamento e Governança** |  |  |  |  |
| [Observador de Rede](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :heavy_check_mark: | :x: | :heavy_check_mark: |
| **Segurança** |  |  |  |  |
| [Serviços de Domínio do Active Directory do Azure](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Pacífico Asiático



| **Produtos** | **Leste do Japão** | **Sudeste Asiático** | **Leste da Austrália** |
|--|--|--|--|
| **Computação** |  |  |  |
| [Ambientes do serviço de aplicativo (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS (Serviço de Kubernetes do Azure)](../aks/availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de dimensionamento de máquinas virtuais](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Armazenamento** |  |  |  |
| [Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de arquivos Premium](../storage/files/storage-files-planning.md) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de Blobs](../storage/blobs/storage-blobs-introduction.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |
| [Gateway de aplicativo v2](../application-gateway/application-gateway-autoscaling-zone-redundant.md)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](../expressroute/designing-for-high-availability-with-expressroute.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall do Azure](../firewall/deploy-availability-zone-powershell.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](../virtual-network/public-ip-addresses.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](../vpn-gateway/create-zone-redundant-vnet-gateway.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [NAT de Rede Virtual](../virtual-network/nat-gateway-resource.md#availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](../virtual-wan/virtual-wan-faq.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](../vpn-gateway/about-zone-redundant-vnet-gateways.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |
| [Cache Redis do Azure](../azure-cache-for-redis/cache-overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados do Azure para MySQL-servidor flexível](../mysql/flexible-server/concepts-high-availability.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Servidor Flexível do Banco de Dados do Azure para PostgreSQL](../postgresql/flexible-server/overview.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados SQL do Azure (Uso Geral camada)](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview) | :x: | : heavy_check_mark: (visualização) | : heavy_check_mark: (visualização) |
| [Banco de dados SQL do Azure (Premium & camadas de Comercialmente Crítico)](../azure-sql/database/high-availability-sla.md#premium-and-business-critical-service-tier-zone-redundant-availability) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |
| [Hubs de Evento](../event-hubs/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |
| [Grade de Eventos](../event-grid/index.yml) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Barramento de Serviço](../service-fabric/service-fabric-cross-availability-zones.md) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Gerenciamento e Governança** |  |  |  |
| [Observador de Rede](../network-watcher/frequently-asked-questions.md) | :heavy_check_mark: | :x: | :x: |
| **Segurança** |  |  |  |
| [Serviços de Domínio do Active Directory do Azure](../active-directory-domain-services/overview.md) | :heavy_check_mark: | :heavy_check_mark: |  |


## <a name="upcoming-availability-zones"></a>Futuro Zonas de Disponibilidade 

O Azure oferece suporte Zonas de Disponibilidade nas seguintes regiões:
- Gov. dos EUA – Virgínia
- Norte da África do Sul
- Centro-Sul dos Estados Unidos
- Centro-Oeste da Alemanha

A lista de regiões existentes e futuras que dão suporte a Zonas de Disponibilidade pode ser encontrada [aqui](https://azure.microsoft.com/global-infrastructure/geographies/).    

Para saber mais sobre o suporte a Zonas de Disponibilidade nessas regiões, entre em contato com seu representante de vendas ou cliente da Microsoft.


## <a name="pricing-for-vms-in-availability-zones"></a>Preços para VMs no Zonas de Disponibilidade

Não há custo adicional para máquinas virtuais implantadas em uma Zona de Disponibilidade. Para obter mais informações, consulte a [página de preços de largura de banda](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Introdução às Zonas de Disponibilidade

- [Criar uma máquina virtual](../virtual-machines/windows/create-portal-availability-zone.md)
- [Adicionar um Disco Gerenciado usando o PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Criar um conjunto de dimensionamento de máquinas virtuais com redundância de zona](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Balancear carga de VMs entre zonas usando um Load Balancer Standard com um front-end com redundância de zona](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Balancear carga de VMs em uma zona usando um Load Balancer Standard com um front-end zonal](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-cli?tabs=option-1-create-load-balancer-standard)
- [Armazenamento com redundância de zona](../storage/common/storage-redundancy.md)
- [Camada de uso geral do banco de dados SQL](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Recuperação de desastre geográfico dos Hubs de Eventos](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Recuperação de desastre geográfico do Barramento de Serviço](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Criar um gateway de rede virtual com redundância de zona](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Adicionar região com redundância de zona para Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Introdução o cache do Azure para Redis Zonas de Disponibilidade](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Criar uma instância do Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)
- [Criar um cluster AKS (serviço de kubernetes do Azure) que usa Zonas de Disponibilidade](../aks/availability-zones.md)


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Regiões e zonas de disponibilidade no Azure](az-overview.md)
