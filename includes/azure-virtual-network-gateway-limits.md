---
title: arquivo de inclusão
description: incluir arquivo
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88854069"
---
| Recurso                                | Limite        |
|-----------------------------------------|------------------------------|
| Prefixos de endereço de VNet                   | 600 por gateway de VPN          |
| Rotas BGP de agregação                    | 4.000 por gateway de VPN        |
| Prefixos de endereço de gateway de rede local  | 1000 por gateway de rede local               |
| Conexões S2S                         | [Depende do SKU do gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| Conexões P2S                         | [Depende do SKU do gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| Limite de rotas P2S-IKEv2                 | 256 para não Windows **/** 25 para Windows           |
| Limite de rotas P2S-OpenVPN               | 1000                         |
| Máx. flows                              | 100 mil para VpnGw1/AZ  **/**  512K para VpnGw2-4/AZ|