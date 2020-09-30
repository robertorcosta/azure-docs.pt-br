---
title: Rede e conectividade da Solução VMware no Azure
description: Descrição da conectividade e da rede da Solução VMware no Azure.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574442"
---
<!-- Used in introduction.md and concepts-networking.md -->

A Solução VMware no Azure oferece um ambiente de nuvem privada acessível de ambientes ou recursos locais e baseados no Azure. Serviços, como o Azure ExpressRoute e as conexões VPN, fornecem a conectividade. Esses serviços exigem intervalos de endereços de rede e portas de firewall específicos para habilitar os serviços.

Quando uma nuvem privada é implantada, as redes privadas para gerenciamento, provisionamento e vMotion são criadas. Use essas redes privadas para acessar o vCenter e o NSX-T Manager, bem como o vMotion ou a implantação da máquina virtual.  O Alcance Global do ExpressRoute é usado para conectar nuvens privadas aos ambientes locais. A conexão requer uma rede virtual com um circuito de ExpressRoute na assinatura.



>[!NOTE]
>O acesso à Internet e aos serviços do Azure são provisionados e fornecidos para consumir VMs em redes de produção durante a implantação de uma nuvem privada.  Por padrão, o acesso à Internet é desabilitado para novas nuvens privadas e pode ser habilitado ou desabilitado a qualquer momento.