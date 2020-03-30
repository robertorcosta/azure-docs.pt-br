---
title: Use o Gateway de aplicativo Do Azure com máquinas virtuais VMware
description: Descreve como usar o gateway de aplicativo Do Zure para gerenciar o tráfego web de entrada para servidores web em execução em máquinas virtuais VMware, ganhe o ambiente CloudSimple Private Cloud
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cbfdd358fdfd5403c677c067376142169cdc6bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77015449"
---
# <a name="use-azure-application-gateway-with-vmware-virtual-machines-in-the-cloudsimple-private-cloud-environment"></a>Use o Gateway de aplicativo Do Azure com máquinas virtuais VMware no ambiente CloudSimple Private Cloud

Você pode usar o Azure Application Gateway para gerenciar o tráfego web de entrada para seus servidores web em execução em máquinas virtuais VMware dentro do ambiente CloudSimple Private Cloud.

Ao aproveitar o Azure Application Gateway em uma implantação híbrida público-privada, você pode gerenciar o tráfego da Web para seus aplicativos, fornecer um front-end seguro e descarregar o processamento ssl para seus serviços em execução no ambiente VMware. O Azure Application Gateway direciona o tráfego da Web para instâncias de pool de backend residentes em ambientes VMware de acordo com regras e testes de saúde configurados.

Esta solução do Azure Application Gateway exige que você:

* Tenho uma assinatura do Azure.
* Crie e configure uma rede virtual Azure e uma sub-rede dentro da rede virtual.
* Crie e configure as regras do NSG e pares seu vNet usando expressRoute para sua CloudSimple Private Cloud.
* Crie & Configure sua nuvem privada.
* Criar & Configure o gateway de aplicativo do Azure.

## <a name="azure-application-gateway-deployment-scenario"></a>Cenário de implantação do Azure Application Gateway

Neste cenário, o Gateway de aplicativos do Azure é executado em sua rede virtual Azure. A rede virtual está conectada à sua Nuvem Privada em um circuito ExpressRoute. Todas as sub-redes da Nuvem Privada são acessíveis ip a partir das sub-redes de rede virtuais.

![Balanceador de carga azure na rede virtual Azure](media/load-balancer-use-case.png)

## <a name="how-to-deploy-the-solution"></a>Como implantar a solução

O processo de implantação consiste nas seguintes tarefas:

1. [Verifique se os pré-requisitos são atendidos](#1-verify-prerequisites)
2. [Conecte sua conexão virtual do Azure à Nuvem Privada](#2-connect-your-azure-virtual-network-to-your-private-cloud)
3. [Implantar um gateway de aplicativo Do Zure](#3-deploy-an-azure-application-gateway)
4. [Criar e configurar o pool de VM do Servidor Web em sua nuvem privada](#4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud)

## <a name="1-verify-prerequisites"></a>1. Verifique os pré-requisitos

Verifique se esses pré-requisitos são cumpridos:

* Um Gerenciador de Recursos do Azure e uma rede virtual já estão criados.
* Uma sub-rede dedicada (para gateway de aplicativos) dentro de sua rede virtual Azure já está criada.
* Um CloudSimple Private Cloud já foi criado.
* Não há conflito de IP entre sub-redes IP na rede virtual e sub-redes na Nuvem Privada.

## <a name="2-connect-your-azure-virtual-network-to-your-private-cloud"></a>2. Conecte sua rede virtual Do Azure à sua Nuvem Privada

Para conectar sua rede virtual Do Azure à sua Nuvem Privada, siga esse processo.

1. [No portal CloudSimple, copie as informações de peering do ExpressRoute](virtual-network-connection.md).

2. [Configure um gateway de rede virtual para sua rede virtual Azure](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).

3. [Vincule sua rede virtual ao circuito CloudSimple ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md#connect-a-vnet-to-a-circuit---different-subscription).

4. [Use as informações de peering que você copiou para vincular sua rede virtual ao circuito ExpressRoute](virtual-network-connection.md).

## <a name="3-deploy-an-azure-application-gateway"></a>3. Implante um gateway de aplicativo Azure

As instruções detalhadas para isso estão disponíveis em [Criar um gateway de aplicativo com regras de roteamento baseadas em caminho usando o portal Azure](../application-gateway/create-url-route-portal.md). Aqui está um resumo das etapas necessárias:

1. Crie uma rede virtual em seu grupo de assinatura e recursos.
2. Crie uma sub-rede (para ser usada como sub-rede dedicada) dentro de sua rede virtual.
3. Crie um Gateway de aplicativo padrão (habilitar opcionalmente o WAF): Na página inicial do portal Azure, clique no **Gateway** > de aplicativo**de rede** > de**recursos** do lado superior esquerdo da página. Selecione o SKU padrão e o tamanho e forneça informações sobre assinatura, grupo de recursos e localização do Azure. Se necessário, crie um novo IP público para este gateway de aplicativo e forneça detalhes sobre a rede virtual e a sub-rede dedicada para o gateway do aplicativo.
4. Adicione um pool de back-end com máquinas virtuais e adicione-o ao gateway de aplicativo.

## <a name="4-create-and-configure-a-web-server-vm-pool-in-your-private-cloud"></a>4. Crie e configure um pool de VM de servidor web em sua Nuvem Privada

No vCenter, crie VMs com o SISTEMA OPERACIONAL e o servidor web de sua escolha (como Windows/IIS ou Linux/Apache). Escolha uma sub-rede/VLAN designada para o nível web em sua Nuvem Privada. Verifique se pelo menos um vNIC das VMs do servidor web está na sub-rede de nível web.
