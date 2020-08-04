---
title: Regiões que dão suporte a Zonas de Disponibilidade no Azure
description: Para criar aplicativos altamente disponíveis e resilientes no Azure, as Zonas de Disponibilidade oferecem locais fisicamente separados que você pode usar para executar seus recursos.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 07/06/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: b031b2a26f81c34f1699e530c8d0bb8839454c89
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543870"
---
# <a name="regions-that-support-availability-zones-in-azure"></a>Regiões que dão suporte a Zonas de Disponibilidade no Azure

Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. Para obter mais informações sobre Zonas de Disponibilidade, consulte [regiões e zonas de disponibilidade no Azure](az-overview.md).

## <a name="services-support-by-region"></a>Suporte a serviços por região

Esta seção lista os serviços e regiões do Azure que dão suporte a Zonas de Disponibilidade.

Os serviços que estão disponíveis em cada região, juntamente com o próximo roteiro para disponibilidade, podem ser encontrados em [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/).

| Serviço |Américas: EUA Central|Américas: leste dos EUA|Américas: leste dos EUA 2|Américas: oeste dos EUA 2|Europa: França central|Europa: Europa Setentrional|Europa: Sul do Reino Unido|Europa: Europa Ocidental|Pacífico Asiático: leste do Japão|Pacífico Asiático: Sudeste Asiático|Pacífico Asiático: leste da Austrália|
|----------------------------|----------|----------|---------|---------|--------------|------------|--------|----------|----------|-------------|-------------|
| **Computação**                         |            |              |           |           |                |              |          |             |            |                |                |
| Máquinas Virtuais do Linux          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Máquinas Virtuais do Windows        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Conjuntos de Dimensionamento de Máquinas Virtuais      | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| ILB ambientes de serviço de Azure App | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| Serviço de Kubernetes do Azure        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       |                |
| **Storage**   |            |              |           |           |                |              |          |             |            |                |                |
| Managed Disks                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Armazenamento com redundância de zona          | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| **Rede**                     |            |              |           |           |                |              |          |             |            |                |                |
| Endereço IP padrão        | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Standard Load Balancer     | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;   | &#10003;       | &#10003;               |
| Gateway de VPN            | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Gateway do ExpressRoute   | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Gateway de aplicativo (v2)    | &#10003;   |  &#10003;    | &#10003;  | &#10003;  | &#10003;       | &#10003;     |  &#10003;  | &#10003;    |  &#10003;   | &#10003;       | &#10003;               |
| Firewall do Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| **Bancos de dados**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure Data Explorer                   | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    | &#10003;        | &#10003;       |                |
| Banco de Dados SQL                    | &#10003;   | &#10003;     | &#10003;  | &#10003; (versão prévia) | &#10003;       | &#10003; (versão prévia)     | &#10003; | &#10003;    | &#10003;       | &#10003;       |&#10003;        |
| Cache Redis do Azure           | &#10003;   | &#10003;     | &#10003;  | &#10003;  | &#10003;       | &#10003;     | &#10003; | &#10003;    |  &#10003;       | &#10003;       |                |
| Azure Cosmos DB      | &#10003;   |  &#10003;  |  &#10003; | &#10003; |  &#10003;  | &#10003;    | &#10003; |  &#10003;   |     &#10003;       | &#10003;    | &#10003;  |
| **Análise**                       |            |              |           |           |                |              |          |             |            |                |                |
| Hubs de Eventos                      | &#10003;   |   &#10003; | &#10003;  | &#10003;  | &#10003; | &#10003; | &#10003; | &#10003; | &#10003; | &#10003;       |  &#10003;              |
| **Integração**                     |            |              |           |           |                |              |          |             |            |                |                |
| Barramento de Serviço (somente Camada Premium) | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |  &#10003;              |
| Grade de Eventos | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |
| **Identidade**                     |            |              |           |           |                |              |          |             |            |                |                |
| Azure AD Domain Services | &#10003;   |  &#10003;  | &#10003;  | &#10003;  | &#10003;  | &#10003;     |&#10003;   | &#10003;    |&#10003;      | &#10003;       |                |

O Azure também oferece suporte Zonas de Disponibilidade nas seguintes regiões:

- Gov. dos EUA – Virgínia
- Leste da Austrália
- Norte da África do Sul
- Centro-Sul dos Estados Unidos
- Canadá Central

Para saber mais sobre o suporte a Zonas de Disponibilidade nessas cinco regiões, entre em contato com seu representante de vendas ou cliente da Microsoft ou abra uma solicitação de suporte técnico.

## <a name="next-steps"></a>Próximas etapas

- [Regiões e zonas de disponibilidade no Azure](az-overview.md)
