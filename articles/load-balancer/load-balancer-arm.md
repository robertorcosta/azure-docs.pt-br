---
title: Suporte de Azure Resource Manager para Load Balancer
description: Neste artigo, use Azure PowerShell e modelos com Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: b22b89334fbb55e594ac2b27b486cf5d0bd26f03
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74196100"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Suporte Azure Resource Manager com Azure Load Balancer



O Azure Resource Manager é a estrutura de gerenciamento preferida dos serviços no Azure. O Azure Load Balancer pode ser gerenciado usando ferramentas e APIs baseadas no Azure Resource Manager.

## <a name="concepts"></a>Conceitos

Com Resource Manager, o Azure Load Balancer contém os recursos filho a seguir:

* Configuração de IP de front-end – um balanceador de carga pode incluir um ou mais endereços IP de front-ends, também conhecidos como VIPs (IPs virtuais). Esses endereços IP servem como entrada para o tráfego.
* Pool de endereços de back-end – esse pool é uma coleção de endereços IP associados à NIC (placa de interface de rede) da máquina virtual na qual a carga é distribuída.
* Regras de balanceamento de carga – uma propriedade de regra mapeia um determinado IP de front-end e uma combinação de porta para um conjunto de endereços IP de back-end e combinação de porta. Um único balanceador de carga pode ter várias regras de balanceamento de carga. Cada regra é uma combinação de um IP de front-end e uma porta e um IP de back-end e uma porta associada às VMs.
* Investigações – as investigações permitem que você controle a integridade das instâncias VM. Se um teste de integridade falhar, a instância VM será retirada automaticamente do rodízio.
* Regras de NAT de entrada – regras de NAT que definem o tráfego de entrada que flui através do IP de front-end e distribuído para o IP de back-end.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de início rápido

O Gerenciador de Recursos do Azure permite que você provisione seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo durante cada estágio do ciclo de vida do aplicativo.

Os modelos podem incluir definições para:
* **Máquinas virtuais**
* **Redes virtuais**
* **Conjuntos de disponibilidade**
* **Adaptadores de rede (NICs)**
* **Contas de armazenamento**
* **Balanceadores de carga**
* **Grupos de segurança de rede**
* **IPs públicos.** 

Com os modelos, você pode criar tudo o que precisa para um aplicativo complexo. O arquivo de modelo pode ser inserido no sistema de gerenciamento de conteúdo para controle de versão e colaboração.

[Saiba mais sobre modelos](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Saiba mais sobre recursos de rede](../networking/networking-overview.md)

Para modelos de início rápido usando Azure Load Balancer, consulte o [repositório GitHub](https://github.com/Azure/azure-quickstart-templates) que hospeda um conjunto de modelos gerados pela Comunidade.

Exemplos de modelos:

* [2 VMs em um balanceador de carga e regras de balanceamento de carga](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs em uma VNET com um Load Balancer interno e regras de balanceador de carga](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs em um balanceador de carga e configurar regras de NAT no LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurando o balanceador de carga do Azure com PowerShell ou CLI

Introdução aos cmdlets Azure Resource Manager, ferramentas de linha de comando e APIs REST

* [Cmdlets de rede do Azure](https://docs.microsoft.com/powershell/module/az.network#networking) podem ser usados para criar um balanceador de carga.
* [Como criar um balanceador de carga usando o gerenciador de recursos do Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Usando a CLI do Azure com o gerenciamento de recursos do Azure](../xplat-cli-azure-resource-manager.md)
* [APIs REST do balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Próximas etapas

[Comece a criar um balanceador de carga](load-balancer-get-started-internet-arm-ps.md) para a Internet e configure o tipo de [modo de distribuição](load-balancer-distribution-mode.md) para o comportamento de tráfego de rede específico.

Saiba como gerenciar [tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). Essas configurações são importantes quando seu aplicativo precisa manter conexões ativas para servidores atrás de um balanceador de carga.
