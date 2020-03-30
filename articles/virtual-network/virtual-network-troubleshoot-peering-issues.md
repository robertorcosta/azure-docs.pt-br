---
title: Solucionar problemas de emparelhamento de rede virtual
description: Etapas para ajudar a resolver a maioria dos problemas de peering de rede virtual.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796245"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Solucionar problemas de emparelhamento de rede virtual

Este guia de solução de problemas fornece etapas para ajudá-lo a resolver a maioria dos problemas [de peering de rede virtual.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

![Diagrama de peering de rede virtual](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Configurar peering de rede virtual entre duas redes virtuais

As redes virtuais estão na mesma assinatura ou em assinaturas diferentes?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>As redes virtuais estão na mesma assinatura

Para configurar o peering de rede virtual para as redes virtuais que estão na mesma assinatura, use os métodos nos seguintes artigos:

* Se as redes virtuais estiverem na *mesma região,* consulte [Criar um peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Se as redes virtuais estiverem nas *diferentes regiões,* consulte [Peering de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> A conectividade não funciona em rede virtual global para os seguintes recursos: 
>
> * Máquinas virtuais (VMs) por trás do SKU do balanceador de carga interna básico (ILB)
> * Cache Redis (usa Basic ILB SKU)
> * Gateway de aplicativo (usa Basic ILB SKU)
> * Conjuntos de escala de máquinas virtuais (usa Basic ILB SKU)
> * Clusters de malha de serviço azure (usa Basic ILB SKU)
> * SQL Server Always On (usa Basic ILB SKU)
> * Ambiente de serviço do aplicativo Azure para PowerApps (usa SKU Básico ILB)
> * Gerenciamento de API do Azure (usa SKU Básico iLB)
> * Azure Active Directory Domain Services (Azure AD DS) (usa SKU Básico do ILB)

Para obter mais informações, consulte os [requisitos e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) do peering global.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>As redes virtuais estão em diferentes assinaturas ou inquilinos do Active Directory

Para configurar o peering de rede virtual para redes virtuais em diferentes assinaturas ou inquilinos do Active Directory, consulte [Criar peering em diferentes assinaturas para o Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Para configurar o peering de rede, você deve ter permissões **de Contribuinte de Rede** em ambas as assinaturas. Para obter mais informações, consulte [permissões de peering](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Configure peering de rede virtual com topologia com fala de hub que usa recursos no local

![Diagrama de peering de rede virtual com fala no local](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Para uma conexão site-a-site ou uma conexão ExpressRoute

Siga as etapas em: [Configure o transporte de gateway VPN para peering de rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Para conexões ponto a ponto

1. Siga as etapas em: [Configure o transporte de gateway VPN para peering de rede virtual](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Depois que o peering de rede virtual for estabelecido ou alterado, baixe e reinstale o pacote ponto a ponto para que os clientes ponto a ponto obtenham as rotas atualizadas para a rede virtual falada.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Configure peering de rede virtual com rede virtual de topologia com fala hub

![Diagrama de peering de rede virtual com uma rede virtual falou](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>As redes virtuais estão na mesma região


1. Na rede virtual do hub, configure um aparelho virtual de rede (NVA).
1. Nas redes virtuais faladas, têm rotas definidas pelo usuário com o próximo tipo de hop "network virtual appliance" aplicado.

Para obter mais informações, consulte [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Se precisar de ajuda para configurar um NVA, [entre em contato com o fornecedor NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Para obter ajuda para solucionar problemas na configuração e roteamento do dispositivo NVA, consulte [Problemas de dispositivo virtual da Rede no Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>As redes virtuais estão em diferentes regiões

O trânsito sobre a rede virtual global agora é suportado. A conectividade não funciona em rede virtual global para os seguintes recursos:

* VMs por trás do Basic ILB SKU
* Cache Redis (usa Basic ILB SKU)
* Gateway de aplicativo (usa Basic ILB SKU)
* Conjuntos de escala (usa Basic ILB SKU)
* Clusters de malha de serviço (usa Basic ILB SKU)
* SQL Server Always On (usa Basic ILB SKU)
* Ambiente de serviço do aplicativo (usa SKU ILB básico)
* Gerenciamento de API (usa SKU Básico do ILB)
* Azure AD DS (usa Basic ILB SKU)

Para saber mais sobre os requisitos e restrições globais de peering, consulte [Peering de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Solucionar problemas de conectividade entre duas redes virtuais peered

Faça login no [portal Azure](https://portal.azure.com/) com uma conta que tenha as [funções e permissões necessárias.](virtual-network-manage-peering.md#permissions) Selecione a rede virtual, **selecione Peering**e, em seguida, verifique o **campo Status.** Qual é a situação?

### <a name="the-peering-status-is-connected"></a>O status de peering é "Conectado"

Para solucionar este problema:

1. Verifique os fluxos de tráfego da rede:

   Use a [verificação](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) de [problemas de conexão](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) e o fluxo ip da VM de origem para a VM de destino para determinar se há um NSG ou UDR que está causando interferência nos fluxos de tráfego.

   Se você estiver usando um firewall ou NVA: 
   1. Documente os parâmetros do UDR para que você possa restaurá-los após esta etapa ser concluída.
   2. Remova o UDR da sub-rede VM ou NIC de origem que aponta para o NVA como o próximo salto. Verifique a conectividade da VM de origem diretamente para o destino que está contornando o NVA. Se esta etapa não funcionar, consulte o [solucionador de problemas NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Faça um rastreamento de rede: 
   1. Inicie um rastreamento de rede na VM de destino. Para windows, você pode usar **Netsh**. Para Linux, use **TCPDump**.
   2. Execute **O TcpPing** ou **PsPing** da fonte para o IP de destino.

      Este é um exemplo de um comando **TcpPing:**`tcping64.exe -t <destination VM address> 3389`

   3. Depois que o **TcpPing** estiver concluído, pare o rastreamento da rede no destino.
   4. Se os pacotes chegarem da fonte, não haverá problema de rede. Examine o firewall VM e o aplicativo que escuta nessa porta para localizar o problema de configuração.

   > [!Note]
   > Você não pode se conectar aos seguintes tipos de recursos sobre peering de rede virtual global (redes virtuais em diferentes regiões):
   >
   > * VMs por trás do Basic ILB SKU
   > * Cache Redis (usa Basic ILB SKU)
   > * Gateway de aplicativo (usa Basic ILB SKU)
   > * Conjuntos de escala (usa Basic ILB SKU)
   > * Clusters de malha de serviço (usa Basic ILB SKU)
   > * SQL Server Always On (usa Basic ILB SKU)
   > * Ambiente de serviço do aplicativo (usa SKU ILB básico)
   > * Gerenciamento de API (usa SKU Básico do ILB)
   > * Azure AD DS (usa Basic ILB SKU)

Para obter mais informações, consulte os [requisitos e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) do peering global.

### <a name="the-peering-status-is-disconnected"></a>O status de peering é "Desconectado"

Para resolver esse problema, exclua o peering de ambas as redes virtuais e, em seguida, recrie-as.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Solucionaum um problema de conectividade entre uma rede virtual com fala de hub e um recurso local

Sua rede usa um portal NVA ou VPN de terceiros?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Minha rede usa um portal NVA ou VPN de terceiros

Para solucionar problemas de conectividade que afetam um portal NVA ou VPN de terceiros, consulte os seguintes artigos:

* [Solução de problemas NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Encadeamento de serviços](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Minha rede não usa um portal NVA ou VPN de terceiros

A rede virtual hub e a rede virtual falada têm um gateway VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Tanto a rede virtual do hub quanto a rede virtual falada têm um gateway VPN

O uso de um gateway remoto não é suportado.

Se a rede virtual falada já tiver um gateway VPN, a opção **Usar gateway remoto** não será suportada na rede virtual falada. Isso se deve a uma limitação de peering de rede virtual.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Tanto a rede virtual do hub quanto a rede virtual falada não têm um gateway VPN

Para obter conexões site-to-site ou Azure ExpressRoute, verifique as seguintes causas primárias de problemas de conectividade na rede virtual remota a partir de locais:

* Na rede virtual que possui um gateway, verifique se a caixa **de seleção de tráfego encaminhado** allow está selecionada.
* Na rede virtual que não tem um gateway, verifique se a caixa de seleção **de gateway remoto Use** está selecionada.
* Mande o administrador de rede verificar os dispositivos no local para verificar se todos eles têm o espaço remoto de endereço de rede virtual adicionado.

Para conexões ponto a ponto:

* Na rede virtual que possui um gateway, verifique se a caixa **de seleção de tráfego encaminhado** allow está selecionada.
* Na rede virtual que não tem um gateway, verifique se a caixa de seleção **de gateway remoto Use** está selecionada.
* Baixe e reinstale o pacote cliente ponto a ponto. As rotas de rede virtuais que são recentemente pesquisadas não adicionam automaticamente rotas a clientes ponto a ponto.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Solucionar problemas de conectividade de rede com fala sinuosa entre redes virtuais faladas na mesma região

Uma rede de hub deve incluir um NVA. Configure UDRs em raios que tenham um Conjunto NVA como o próximo salto e **habilite permitir** tráfego encaminhado na rede virtual do hub.

Para obter mais informações, consulte [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)e discuta esses requisitos com o [fornecedor NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) de sua escolha.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Solucionar problemas de conectividade de rede com fala sinuosa entre redes virtuais faladas em diferentes regiões

O trânsito sobre a rede virtual global agora é suportado. A conectividade não funciona em rede virtual global para os seguintes recursos:

* VMs por trás do Basic ILB SKU
* Cache Redis (usa Basic ILB SKU)
* Gateway de aplicativo (usa Basic ILB SKU)
* Conjuntos de escala (usa Basic ILB SKU)
* Clusters de malha de serviço (usa Basic ILB SKU)
* SQL Server Always On (usa Basic ILB SKU)
* Ambiente de serviço do aplicativo (usa SKU ILB básico)
* Gerenciamento de API (usa SKU Básico do ILB)
* Azure AD DS (usa Basic ILB SKU)

Para obter mais informações, consulte os [requisitos e restrições](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) do peering global e [diferentes topologias de VPN.](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/)

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Solucionando um problema de conectividade de rede com fala de hub entre um aplicativo web e a rede virtual falada

Para solucionar este problema:

1. Entre no portal do Azure. 
1. No aplicativo web, selecione **a rede**e selecione **a Integração VNet**.
1. Verifique se você pode ver a rede virtual remota. Digite manualmente o espaço de endereço de rede virtual remoto **(Sync Network** e **Add Routes).**

Para obter mais informações, consulte os seguintes artigos:

* [Integre seu aplicativo com uma rede virtual Do Zure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Sobre o roteamento VPN Ponto a Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Solucionar problemas com uma mensagem de erro de configuração de peering de rede virtual 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>O `<TENANT ID>` inquilino atual não está autorizado a acessar a assinatura vinculada

Para resolver esse problema, consulte [Criar peering - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="not-connected"></a>Não conectado

Para resolver esse problema, exclua o peering de ambas as redes virtuais e, em seguida, recrie-as.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Falha ao fazer parte de uma rede virtual Databricks

Para resolver esse problema, configure a rede virtual em **tijolos de dados do Azure**e, em seguida, especifique a rede virtual de destino usando **o ID de recurso**. Para obter mais informações, consulte [Peer a Databricks virtual para uma rede virtual remota](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Próximas etapas

* [Solucionar problemas de conectividade entre VMs do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
