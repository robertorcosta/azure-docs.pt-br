---
title: Regiões que dão suporte a Zonas de Disponibilidade no Azure
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade oferecem locais fisicamente separados que você pode usar para executar seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 10/07/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: 888365e04566f18e5a73454fea4c96c321f5ce52
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91858380"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regiões que dão suporte a Zonas de Disponibilidade no Azure

Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. Para obter mais informações sobre Zonas de Disponibilidade, consulte [regiões e zonas de disponibilidade no Azure](az-overview.md).

Esta seção lista os serviços e regiões do Azure que dão suporte a Zonas de Disponibilidade.

Os serviços que estão disponíveis em cada região, juntamente com o próximo roteiro para disponibilidade, podem ser encontrados em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="americas"></a>Américas

| **Produtos** | **Centro dos EUA** | **Leste dos EUA** | **Leste dos EUA 2** | **Oeste dos EUA 2** | **Canadá Central** |
|--|--|--|--|--|--|
| **Computação** |  |  |  |  |  |
| [Ambientes do serviço de aplicativo (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS (Serviço de Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de arquivos Premium](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |  |
| [Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall do Azure](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |  |  |
| [Cache Redis do Azure](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados SQL do Azure](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |  |
| [Hubs de Evento](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |  |
| [Grade de Eventos](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Barramento de Serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Segurança** |  |  |  |  |  |
| [Serviços de Domínio do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="europe"></a>Europa

| **Produtos** | **França Central** | **Norte da Europa** | **Sul do Reino Unido** | **Oeste da Europa** |
|--|--|--|--|--|
| **Computação** |  |  |  |  |
| [Ambientes do serviço de aplicativo (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS (Serviço de Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de arquivos Premium](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |  |
| [Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |
| [Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall do Azure](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |  |
| [Cache Redis do Azure](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados SQL do Azure](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |
| [Hubs de Evento](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |
| [Grade de Eventos](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Barramento de Serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Segurança** |  |  |  |  |
| [Serviços de Domínio do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="asia-pacific"></a>Pacífico Asiático



| **Produtos** | **Japan East** | **Sudeste Asiático** | **Leste da Austrália** |
|--|--|--|--|
| **Computação** |  |  |  |
| [Ambientes do serviço de aplicativo (ILB)](../app-service/environment/zone-redundancy.md#how-to-deploy-an-app-service-environment-in-an-availability-zone) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [AKS (Serviço de Kubernetes do Azure)](https://docs.microsoft.com/azure/aks/availability-zones) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Conjuntos de dimensionamento de máquinas virtuais](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones#:~:text=To%20protect%20your%20virtual%20machine%20scale%20sets%20from,their%20own%20independent%20power%20source%2C%20network%2C%20and%20cooling.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Máquinas virtuais](https://azure.microsoft.com/services/virtual-machines/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |
| [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de arquivos Premium](https://docs.microsoft.com/azure/storage/files/storage-files-planning) |  | :heavy_check_mark: | :heavy_check_mark: |
| [Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Managed Disks](https://azure.microsoft.com/en-gb/updates/azure-managed-snapshots-images-ga/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |
| [Gateway de aplicativo v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)  | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Firewall do Azure](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Endereço IP padrão](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Balanceador de Carga](../load-balancer/load-balancer-standard-availability-zones.md#concepts) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Rede Virtual](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [WAN Virtual](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-faq) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |
| [Cache Redis do Azure](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-overview) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Banco de dados SQL do Azure](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |
| [Hubs de Evento](https://docs.microsoft.com/azure/event-hubs/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |
| [Grade de Eventos](https://docs.microsoft.com/azure/event-grid/) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Barramento de Serviço](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications.) | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Segurança** |  |  |  |
| [Serviços de Domínio do Active Directory do Azure](https://docs.microsoft.com/azure/active-directory-domain-services/overview) | :heavy_check_mark: | :heavy_check_mark: |  |





## <a name="other"></a>Outros

O Azure também oferece suporte Zonas de Disponibilidade nas seguintes regiões: • US Gov-Virgínia • norte da África do Sul • Sul EUA Central

Para saber mais sobre o suporte a Zonas de Disponibilidade nessas três regiões, entre em contato com seu representante de vendas ou cliente da Microsoft.


## <a name="next-steps"></a>Próximas etapas

- [Regiões e zonas de disponibilidade no Azure](az-overview.md)
