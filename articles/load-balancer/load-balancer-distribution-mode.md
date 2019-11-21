---
title: Configurar o modo de distribuição do Azure Load Balancer
titleSuffix: Azure Load Balancer
description: In this article, get started configuring the distribution mode for Azure Load Balancer to support source IP affinity.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: ddccd02e7157792d942309ae4f74933322f246f9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74225366"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configurar o modo de distribuição para o Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Modo de distribuição baseado em hash

The default distribution mode for Azure Load Balancer is a five-tuple hash. 

The tuple is composed of the:
* **Source IP**
* **Source port**
* **Destination IP**
* **Destination port**
* **Protocol type**

The hash is used to map traffic to the available servers. The algorithm provides stickiness only within a transport session. Packets that are in the same session are directed to the same datacenter IP behind the load-balanced endpoint. When the client starts a new session from the same source IP, the source port changes and causes the traffic to go to a different datacenter endpoint.

![Five-tuple hash-based distribution mode](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidade de IP de origem

The load balancer can also be configured by using the source IP affinity distribution mode. Esse modo de distribuição também é conhecido como afinidade de sessão ou afinidade do IP do cliente. The mode uses a two-tuple (source IP and destination IP) or three-tuple (source IP, destination IP, and protocol type) hash to map traffic to the available servers. By using source IP affinity, connections that are started from the same client computer go to the same datacenter endpoint.

The following figure illustrates a two-tuple configuration. Notice how the two-tuple runs through the load balancer to virtual machine 1 (VM1). O backup da VM1 é feito pela VM2 e VM3.

![Two-tuple session affinity distribution mode](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

A afinidade do IP de origem resolve uma incompatibilidade entre o Azure Load Balancer e o Gateway de Área de Trabalho Remota (Gateway de RD). Usando esse modo, é possível criar um farm de Gateway de Área de Trabalho Remota em um único serviço de nuvem.

Outro cenário de caso de uso é o upload de mídia. O upload de dados ocorre por meio de UDP, mas o plano de controle é obtido por meio de TCP:

* A client starts a TCP session to the load-balanced public address and is directed to a specific DIP. O canal permanece ativo para monitorar a integridade da conexão.
* A new UDP session from the same client computer is started to the same load-balanced public endpoint. A conexão é direcionada para o mesmo ponto de extremidade DIP que a conexão TCP anterior. O upload da mídia pode ser executado com alta taxa de transferência, mantendo um canal de controle por meio de TCP.

> [!NOTE]
> Quando um conjunto com balanceamento de carga for alterado, removendo ou adicionando uma máquina virtual, a distribuição de solicitações de cliente será recalculada. Não é possível depender de novas conexões de clientes existentes que terminam no mesmo servidor. Além disso, o uso do modo de distribuição de afinidade do IP de origem pode causar uma distribuição desigual de tráfego. Os clientes que executam atrás de proxies podem ser vistos como um aplicativo cliente exclusivo.

## <a name="configure-source-ip-affinity-settings"></a>Definir configurações de afinidade do IP de origem

### <a name="azure-portal"></a>Portal do Azure

You can change the configuration of the distribution mode by modifying the load-balancing rule in the portal.

1. Sign in to the Azure portal and locate the Resource Group containing the load balancer you wish to change by clicking on **Resource Groups**.
2. In the load balancer overview screen, click on **Load-balancing rules** under **Settings**.
3. In the load-balancing rules screen, click on the load-balancing rule that you wish to change the distribution mode.
4. Under the rule, the distribution mode is changed by changing the **Session persistence** drop down box.  As seguintes opções estão disponíveis:
    
    * **None (hash-based)** - Specifies that successive requests from the same client may be handled by any virtual machine.
    * **Client IP (source IP affinity 2-tuple)** - Specifies that successive requests from the same client IP address will be handled by the same virtual machine.
    * **Client IP and protocol (source IP affinity 3-tuple)** - Specifies that successive requests from the same client IP address and protocol combination will be handled by the same virtual machine.

5. Choose the distribution mode and then click **Save**.

### <a name="azure-powershell"></a>Azure PowerShell

For virtual machines deployed with Resource Manager, use PowerShell to change the load-balancer distribution settings on an existing load-balancing rule. The following command updates the distribution mode: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Para máquinas virtuais clássicas, use o Azure PowerShell para alterar as configurações de distribuição. Adicione um ponto de extremidade do Azure a uma máquina virtual e configure o modo de distribuição do balanceador de carga:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Set the value of the `LoadBalancerDistribution` element for the amount of load balancing required. Specify sourceIP for two-tuple (source IP and destination IP) load balancing. Specify sourceIPProtocol for three-tuple (source IP, destination IP, and protocol type) load balancing. Specify none for the default behavior of five-tuple load balancing.

Recupere uma configuração de modo de distribuição do balanceador de carga do ponto de extremidade usando estas configurações:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

When the `LoadBalancerDistribution` element isn't present, Azure Load Balancer uses the default five-tuple algorithm.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configurar o modo de distribuição no conjunto do ponto de extremidade com balanceamento de carga

Quando os pontos de extremidade formarem parte de um conjunto de pontos de extremidade com balanceamento de carga, o modo de distribuição deverá ser configurado no conjunto de pontos de extremidade com balanceamento de carga:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configurar o modo de distribuição para pontos de extremidade dos Serviços de Nuvem

Use o SDK do Azure para .NET 2.5 para atualizar seu serviço de nuvem. As configurações de ponto de extremidade para Serviços de Nuvem são feitas no arquivo .csdef. Para atualizar o modo de distribuição do balanceador de carga para uma implantação de Serviços de Nuvem, é necessária uma atualização da implantação.

Aqui está um exemplo de alterações .csdef para configurações do ponto de extremidade:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>Exemplo de API

O exemplo a seguir mostra como reconfigurar o modo de distribuição do balanceador de carga para um conjunto com balanceamento de carga especificado em uma implantação. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Alterar o modo de distribuição para o conjunto com balanceamento de carga implantado

Use o modelo de implantação clássico do Azure para alterar uma configuração de implantação existente. Adicione o cabeçalho `x-ms-version` e defina o valor para a versão 2014-09-01 ou posterior.

#### <a name="request"></a>Solicitação

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

As previously described, set the `LoadBalancerDistribution` element to sourceIP for two-tuple affinity, sourceIPProtocol for three-tuple affinity, or none for no affinity (five-tuple affinity).

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Próximos passos

* [Visão geral do Azure Load Balancer interno](load-balancer-internal-overview.md)
* [Introdução à configuração de um balanceador de carga para a Internet](load-balancer-get-started-internet-arm-ps.md)
* [Definir configurações de tempo limite de TCP ocioso para o balanceador de carga](load-balancer-tcp-idle-timeout.md)
