---
title: Solução de problemas Erros do Gateway - Gateway de aplicativo Do Azure
description: 'Saiba como solucionar problemas do aplicativo Gateway Server Error: 502 - O servidor Web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130477"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Solução de problemas de erros de gateway incorreto no Application Gateway

Saiba como solucionar problemas de gateway ruim (502) erros recebidos ao usar o Gateway de aplicativo do Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Visão geral

Depois de configurar um gateway de aplicativo, um dos erros que você pode ver é "Erro do servidor: 502 - o servidor da Web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy". Esse erro pode acontecer pelas seguintes razões principais:

* O NSG, UDR ou DNS personalizado está bloqueando o acesso a membros do pool backend.
* As VMs de back-end ou instâncias do conjunto de escala de máquina virtual não estão respondendo ao teste de saúde padrão.
* Configuração inválida ou incorreta de investigações de integridade personalizadas.
* O [pool de back-end](#empty-backendaddresspool)do Azure Application Gateway não está configurado ou vazio .
* Nenhuma das VMs ou instâncias no [conjunto de dimensionamento de máquinas virtuais é íntegra](#unhealthy-instances-in-backendaddresspool).
* [Solicite problemas de tempo ou conectividade](#request-time-out) com solicitações de usuários.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Problema com o Grupo de Segurança de Rede, com a Rota definida pelo usuário ou com o DNS personalizado

### <a name="cause"></a>Causa

Se o acesso ao backend for bloqueado por causa de um NSG, UDR ou DNS personalizado, as instâncias de gateway do aplicativo não poderão chegar ao pool de backend. Isso causa falhas no teste, resultando em 502 erros.

O NSG/UDR pode estar presente na sub-rede do gateway de aplicativo ou na sub-rede onde as VMs do aplicativo estão implantadas.

Da mesma forma, a presença de um DNS personalizado no VNet também pode causar problemas. Um FQDN usado para membros do pool backend pode não ser resolvido corretamente pelo servidor DNS configurado pelo usuário para o VNet.

### <a name="solution"></a>Solução

Valide a configuração de DNS, UDR e NSG realizando as seguintes etapas:

* Verifique os NSGs associados à sub-rede de gateway do aplicativo. Certifique-se de que a comunicação para back-end não está bloqueada.
* Verifique o UDR associado à sub-rede de gateway do aplicativo. Certifique-se de que o UDR não está direcionando o tráfego para longe da sub-rede backend. Por exemplo, verifique se há roteamento para dispositivos virtuais de rede ou rotas padrão que estão sendo anunciadas na sub-rede do gateway do aplicativo via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Verifique se o NSG e a rota com a VM de back-end estão funcionando corretamente

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Verifique se há um DNS personalizado na VNet. O DNS pode ser verificado examinando os detalhes das propriedades da VNet na saída.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Se estiver presente, certifique-se de que o servidor DNS possa resolver corretamente o FQDN do membro do pool de backend.

## <a name="problems-with-default-health-probe"></a>Problemas com a investigação de integridade padrão

### <a name="cause"></a>Causa

502 erros também podem ser indicadores frequentes de que o teste de saúde padrão não pode alcançar VMs back-end.

Quando uma instância de gateway de aplicativo é provisionada, ela configura automaticamente um teste de saúde padrão para cada BackendAddressPool usando propriedades do BackendHttpSetting. Nenhuma entrada do usuário é necessária para definir essa investigação. Especificamente, quando uma regra de balanceamento de carga é configurada, uma associação é feita entre um BackendHttpSetting e um BackendAddressPool. Um teste padrão é configurado para cada uma dessas associações e o gateway de aplicativo inicia uma conexão periódica de verificação de saúde para cada instância no BackendAddressPool na porta especificada no elemento BackendHttpSetting. 

A tabela a seguir lista os valores associados ao teste de saúde padrão:

| Propriedades da investigação | Valor | Descrição |
| --- | --- | --- |
| URL de investigação |`http://127.0.0.1/` |Caminho da URL |
| Intervalo |30 |Intervalo da investigação em segundos |
| Tempo limite |30 |Tempo limite da investigação em segundos |
| Limite não íntegro |3 |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

* Verifique se um site padrão está configurado e está escutando em 127.0.0.1.
* Se BackendHttpSetting especificar uma porta diferente de 80, o site padrão deverá ser configurado para escutar nessa porta.
* A chamada para `http://127.0.0.1:port` deve retornar um código de resultado de HTTP 200. Isso deve ser devolvido dentro do período de tempo de 30 segundos.
* Certifique-se de que a porta configurada está aberta e que não há regras de firewall ou Grupos de Segurança de Rede Do Azure, que bloqueiam o tráfego de entrada ou saída na porta configurada.
* Se as VMs clássicas do Azure ou o Serviço de Nuvem forem usados com um FQDN ou um IP público, certifique-se de que o [ponto final](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) correspondente seja aberto.
* Se a VM estiver configurada via Azure Resource Manager e estiver fora do VNet onde o gateway de aplicativo está implantado, um [Grupo de Segurança de Rede](../virtual-network/security-overview.md) deve ser configurado para permitir o acesso na porta desejada.

## <a name="problems-with-custom-health-probe"></a>Problemas com a investigação de integridade personalizada

### <a name="cause"></a>Causa

Investigações de integridade personalizadas oferecem flexibilidade adicional para o comportamento de investigação padrão. Quando você usa testes personalizados, você pode configurar o intervalo do teste, a URL, o caminho para testar e quantas respostas falhadas aceitar antes de marcar a instância de pool back-end como insalubre.

As seguintes propriedades adicionais são adicionadas:

| Propriedades da investigação | Descrição |
| --- | --- |
| Nome |O nome da investigação. Este é o nome usado para se referir à investigação nas configurações de HTTP de back-end. |
| Protocolo |O protocolo usado para enviar a investigação. A investigação usa o protocolo definido nas configurações de HTTP do back-end |
| Host |O nome do host para enviar a investigação. Aplicável somente quando vários sites forem configurados no gateway do aplicativo. Isso é diferente do nome de host de VM. |
| Caminho |O caminho relativo da investigação. Um caminho válido começa com '/'. A investigação é enviada para \<protocol\>://\<host\>:\<port\>\<path\> |
| Intervalo |Intervalo de investigação em segundos. Este é o intervalo de tempo entre duas investigações consecutivas. |
| Tempo limite |Tempo limite da investigação em segundos. Se uma resposta válida não for recebida dentro deste período de tempo, a sonda será marcada como falha. |
| Limite não íntegro |Contagem de repetições da investigação. O servidor de back-end é marcado após a contagem de falhas de investigação consecutivas atingir o limite de não íntegro. |

### <a name="solution"></a>Solução

Valide se a Investigação de Integridade Personalizada está configurada corretamente conforme a tabela anterior. Além das etapas de solução de problemas anteriores, também verifique o seguinte:

* Verifique se a investigação foi especificada corretamente, conforme o [guia](application-gateway-create-probe-ps.md).
* Se o gateway de aplicativo estiver configurado para um único site, por padrão, o nome Host deve ser especificado como `127.0.0.1`, a menos que configurado de outra forma no teste personalizado.
* Verifique se uma chamada para http://\<host\>:\<port\>\<path\> retorna um código de resultado HTTP 200.
* Certifique-se de que intervalo, tempo limite e limite de não cura estão dentro das faixas aceitáveis.
* Se usar uma investigação HTTPS, certifique-se de que o servidor de back-end não requer o SNI, configurando para isso um certificado fallback no próprio servidor de back-end.

## <a name="request-time-out"></a>Tempo limite de solicitação

### <a name="cause"></a>Causa

Quando uma solicitação de usuário é recebida, o gateway do aplicativo aplica as regras configuradas à solicitação e a encaminha para uma instância de pool back-end. Ele aguarda por um intervalo de tempo configurável para receber uma resposta da instância de back-end. Por padrão, este intervalo é **de 20** segundos. Se o gateway do aplicativo não receber uma resposta do aplicativo back-end neste intervalo, a solicitação do usuário receberá um erro de 502.

### <a name="solution"></a>Solução

O Application Gateway permite configurar essa configuração através do BackendHttpSetting, que pode ser aplicado em diferentes pools. Diferentes pools back-end podem ter backendhttpsetting diferente e um tempo de intervalo de solicitação diferente configurado.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>BackendAddressPool vazio

### <a name="cause"></a>Causa

Se o gateway de aplicativo não tiver VMs ou conjunto de escala de máquina virtual configurado no pool de endereços back-end, ele não poderá encaminhar nenhuma solicitação do cliente e enviar um erro de gateway ruim.

### <a name="solution"></a>Solução

Certifique-se de que o pool de endereços back-end não está vazio. Isso pode ser feito por meio do PowerShell, da CLI ou do portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

A saída do cmdlet anterior deve conter o pool de endereços de back-end não vazio. O exemplo a seguir mostra dois pools retornados que são configurados com um FQDN ou um endereço IP para as VMs back-end. O estado de provisionamento de BackendAddressPool deve ser 'Bem-sucedido'.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Instâncias não íntegras em BackendAddressPool

### <a name="cause"></a>Causa

Se todas as instâncias do BackendAddressPool não estiverem saudáveis, o gateway do aplicativo não terá nenhum back-end para solicitar o usuário de rota. Isso também pode ser o caso quando as instâncias back-end são saudáveis, mas não têm o aplicativo necessário implantado.

### <a name="solution"></a>Solução

Verifique se as instâncias estão íntegras e se o aplicativo está configurado corretamente. Verifique se as instâncias de back-end podem responder a um ping de outra VM no mesmo VNet. Se configurado com um ponto final público, certifique-se de que uma solicitação de navegador para o aplicativo web esteja em manutenção.

## <a name="next-steps"></a>Próximas etapas

Se as etapas anteriores não resolverem o problema, abra um [ticket de suporte](https://azure.microsoft.com/support/options/).

