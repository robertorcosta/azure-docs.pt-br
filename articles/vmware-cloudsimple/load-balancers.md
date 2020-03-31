---
title: Solução Azure VMware by CloudSimple - Escolha uma solução de balanceamento de carga para CloudSimple Private Clouds
description: Descreve as opções de balanceamento de carga implantando um aplicativo em uma nuvem privada
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6c98b699b1d3aba15ce69c519d35d7ce3e90d123
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014871"
---
# <a name="choose-a-load-balancing-solution-for-cloudsimple-private-clouds"></a>Escolha uma solução de balanceamento de carga para CloudSimple Private Clouds

Ao implantar um aplicativo em uma Nuvem Privada Cloud, você pode escolher qualquer uma das várias opções para balanceamento de carga.

Você pode escolher um balanceador de carga virtual ou baseado em software em sua nuvem privada CloudSimple ou até mesmo usar o balanceador de carga Azure L7 em execução em sua assinatura do Azure para front-end suas VMs de nível web em execução na Nuvem Privada Cloud. Aqui, listamos algumas opções:

## <a name="virtual-load-balancers"></a>Balanceadores de carga virtuais

Você pode implantar aparelhos de balanceador de carga virtuais em seu ambiente VMware através da interface vCenter e configurá-los para front-end seu tráfego de aplicativos.

Alguns fornecedores populares são: NginX: http://nginx.org/en/docs/http/load_balancing.html F5- BigIP - Traffic Manager: https://www.f5.com/products/big-ip-services/virtual-editions Citrix ADC:https://www.citrix.com/products/citrix-adc/

## <a name="azure-l7-load-balancer"></a>Balanceador de carga Azure L7

Quando você usa o Azure Application Gateway como um balanceador de carga L7 para o seu aplicativo em execução em uma Nuvem Privada, você não precisa gerenciar o software balanceador de carga. O software balanceador de carga é gerenciado pelo Azure. Todas as VMs de nível web na Nuvem Privada usam endereços IP privados e não exigem regras adicionais de NAT ou endereços de IPs públicos para resolver nomes. As VMs de nível da Web se comunicam com o Gateway de aplicativos do Azure por uma conexão privada, de baixa latência e de alta largura de banda.

Para saber mais sobre como configurar essa solução, consulte o guia de soluções sobre o Uso do Gateway de Aplicativos Azure como um balanceador de carga L7.

## <a name="azure-internal-load-balancer"></a>Balanceador de carga interna do Azure

Se você optar por executar seu aplicativo em uma implantação híbrida onde o nível front-end da Web está sendo executado dentro de um azure vNet em sua assinatura do Azure e o nível DB do aplicativo está sendo executado em VMs VMware no CloudSimple Private Cloud, você pode usar a carga interna do Azure balanceador (balanceador de carga L4) em frente às VMs de nível DB para gerenciamento de tráfego.

Para saber mais, consulte a documentação [do Balancer de Carga Interna](../load-balancer/concepts-limitations.md#internalloadbalancer) do Azure.

## <a name="global-server-load-balancer"></a>Balanceador global de carga de servidor

Se você está procurando um balanceador de carga baseado em DNS, então você pode usar soluções de terceiros disponíveis no Azure Marketplace ou ir com a solução nativa Do Azure.

O Azure Traffic Manager é um balanceador de carga de tráfego baseado em DNS que permite distribuir tráfego de forma ideal para serviços em regiões globais do Azure e no local, ao mesmo tempo em que oferece alta disponibilidade e capacidade de resposta. Para saber mais, consulte a documentação [do Azure Traffic Manager.](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)
