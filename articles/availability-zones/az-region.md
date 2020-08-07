---
title: Regiões que dão suporte a Zonas de Disponibilidade no Azure
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade oferecem locais fisicamente separados que você pode usar para executar seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/05/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: cf1fc81ea63db21d2e864c00e1987eec3d376b59
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87853153"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regiões que dão suporte a Zonas de Disponibilidade no Azure

Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. Para obter mais informações sobre Zonas de Disponibilidade, consulte [regiões e zonas de disponibilidade no Azure](az-overview.md).

Esta seção lista os serviços e regiões do Azure que dão suporte a Zonas de Disponibilidade.

Os serviços que estão disponíveis em cada região, juntamente com o próximo roteiro para disponibilidade, podem ser encontrados em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).

## <a name="americas"></a>Américas

| Serviço | Centro dos EUA | Leste dos EUA | Leste dos EUA 2 | Oeste dos EUA 2 |
| --- | :---: | :---: | :---: | :---: |
| **Computação** |  |  |  |  |
| Máquinas Virtuais do Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Máquinas Virtuais do Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Conjuntos de Dimensionamento de Máquinas Virtuais         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB ambientes de serviço de Azure App | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Serviço de Kubernetes do Azure           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Armazenamento com redundância de zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |
| Endereço IP padrão                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway do ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de aplicativo (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Firewall do Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Banco de Dados SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | : heavy_check_mark: (visualização) |
| Cache Redis do Azure              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |
| Hubs de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |  |
| Barramento de Serviço (somente Camada Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Grade de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identidade** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="europe"></a>Europa

| Serviço | França Central | Norte da Europa | Sul do Reino Unido | Europa Ocidental |
| --- | :---: | :---: | :---: | :---: |
| **Computação** |  |  |  |  |
| Máquinas Virtuais do Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Máquinas Virtuais do Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Conjuntos de Dimensionamento de Máquinas Virtuais         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB ambientes de serviço de Azure App | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Serviço de Kubernetes do Azure           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Storage** |  |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Armazenamento com redundância de zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |  |
| Endereço IP padrão                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway do ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de aplicativo (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Firewall do Azure                     | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Bancos de dados** |  |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Banco de Dados SQL                       | :heavy_check_mark: | : heavy_check_mark: (visualização) | :heavy_check_mark: | :heavy_check_mark: |
| Cache Redis do Azure              | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |  |
| Hubs de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração**  |  |  |  |  |
| Barramento de Serviço (somente Camada Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Grade de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Identidade** |  |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |

## <a name="asia-pacific"></a>Pacífico Asiático

| Serviço | Japan East | Sudeste Asiático | Leste da Austrália |
| --- | :---: | :---: | :---: |
| **Computação** |  |  |  |
| Máquinas Virtuais do Linux             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Máquinas Virtuais do Windows           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Conjuntos de Dimensionamento de Máquinas Virtuais         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| ILB ambientes de serviço de Azure App | :heavy_check_mark: | :heavy_check_mark: |  |
| Serviço de Kubernetes do Azure           | :heavy_check_mark: | :heavy_check_mark: |  |
| **Storage** |  |  |  |
| Managed Disks                      | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Armazenamento com redundância de zona             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Rede** |  |  |  |
| Endereço IP padrão                | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Standard Load Balancer             | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de VPN                        | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway do ExpressRoute               | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Gateway de aplicativo (v2)           | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Firewall do Azure                     | :heavy_check_mark: | :heavy_check_mark: |  |
| **Bancos de dados** |  |  |  |
| Azure Data Explorer                | :heavy_check_mark: | :heavy_check_mark: |  |
| Banco de Dados SQL                       | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Cache Redis do Azure              | :heavy_check_mark: | :heavy_check_mark: |  |
| Azure Cosmos DB                    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Análise** |  |  |  |
| Hubs de Eventos                         | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| **Integração** |  |  |  |
| Barramento de Serviço (somente Camada Premium)    | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Grade de Eventos                         | :heavy_check_mark: | :heavy_check_mark: |  |
| **Identidade** |  |  |  |
| Azure AD Domain Services           | :heavy_check_mark: | :heavy_check_mark: |  |

## <a name="other"></a>Outros

O Azure também oferece suporte Zonas de Disponibilidade nas seguintes regiões:

- Gov. dos EUA – Virgínia
- Norte da África do Sul
- Centro-Sul dos Estados Unidos
- Canadá Central

Para saber mais sobre o suporte a Zonas de Disponibilidade nessas quatro regiões, entre em contato com seu representante do cliente ou vendas da Microsoft ou abra uma solicitação de suporte técnico.

## <a name="next-steps"></a>Próximas etapas

- [Regiões e zonas de disponibilidade no Azure](az-overview.md)
