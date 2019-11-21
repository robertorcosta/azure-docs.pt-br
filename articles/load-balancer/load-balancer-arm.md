---
title: Azure Resource Manager support for Load Balancer
description: In this article, use Azure PowerShell and templates with Azure Load Balancer
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
ms.openlocfilehash: 57a60a65dee995695224aa1b451e848ea8768ab1
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215400"
---
# <a name="azure-resource-manager-support-with-azure-load-balancer"></a>Azure Resource Manager support with Azure Load Balancer



O Azure Resource Manager é a estrutura de gerenciamento preferida dos serviços no Azure. O Azure Load Balancer pode ser gerenciado usando ferramentas e APIs baseadas no Azure Resource Manager.

## <a name="concepts"></a>Conceitos

Com Resource Manager, o Azure Load Balancer contém os recursos filho a seguir:

* Front-end IP configuration – a load balancer can include one or more frontend IP addresses, otherwise known as a virtual IPs (VIPs). Esses endereços IP servem como entrada para o tráfego.
* Back-end address pool – This pool is a collection of IP addresses associated with the virtual machine Network Interface Card (NIC) to which load is distributed.
* Load-balancing rules – a rule property maps a given frontend IP and port combination to a set of back-end IP addresses and port combination. A single load balancer can have multiple load-balancing rules. Each rule is a combination of a frontend IP and port and back-end IP and port associated with VMs.
* Investigações – as investigações permitem que você controle a integridade das instâncias VM. Se um teste de integridade falhar, a instância VM será retirada automaticamente do rodízio.
* Inbound NAT rules – NAT rules defining the inbound traffic flowing through the frontend IP and distributed to the back-end IP.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Modelos de início rápido

O Azure Resource Manager permite provisionar seus aplicativos usando um modelo declarativo. Em um único modelo, você pode implantar diversos serviços, em conjunto com suas dependências. Você usa o mesmo modelo para implantar repetidamente seu aplicativo em todas as etapas do ciclo de vida do aplicativo.

Templates may include definitions for:
* **Máquinas virtuais**
* **Redes virtuais**
* **Conjuntos de disponibilidade**
* **Network interfaces (NICs)**
* **Contas de armazenamento**
* **Balanceadores de carga**
* **Grupos de segurança de rede**
* **Public IPs.** 

Com os modelos, você pode criar tudo o que precisa para um aplicativo complexo. O arquivo de modelo pode ser inserido no sistema de gerenciamento de conteúdo para controle de versão e colaboração.

[Saiba mais sobre modelos](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Saiba mais sobre recursos de rede](../networking/networking-overview.md)

For Quickstart templates using Azure Load Balancer, see the [GitHub repository](https://github.com/Azure/azure-quickstart-templates) that hosts a set of community-generated templates.

Exemplos de modelos:

* [2 VMs em um balanceador de carga e regras de balanceamento de carga](https://go.microsoft.com/fwlink/?LinkId=544799)
* [2 VMs in a VNET with an Internal Load Balancer and load balancer rules](https://go.microsoft.com/fwlink/?LinkId=544800)
* [2 VMs in a load balancer and configure NAT rules on the LB](https://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Configurando o balanceador de carga do Azure com PowerShell ou CLI

Get started with Azure Resource Manager cmdlets, command-line tools, and REST APIs

* [Cmdlets de rede do Azure](https://docs.microsoft.com/powershell/module/az.network#networking) podem ser usados para criar um balanceador de carga.
* [Como criar um balanceador de carga usando o gerenciador de recursos do Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Usando a CLI do Azure com o gerenciamento de recursos do Azure](../xplat-cli-azure-resource-manager.md)
* [APIs REST do balanceador de carga](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Próximos passos

[Get started creating an Internet facing load balancer](load-balancer-get-started-internet-arm-ps.md) and configure the type of [distribution mode](load-balancer-distribution-mode.md) for specific network traffic behavior.

Saiba como gerenciar [tempo limite de TCP ocioso para um balanceador de carga](load-balancer-tcp-idle-timeout.md). These settings are important when your application needs to keep connections alive for servers behind a load balancer.
