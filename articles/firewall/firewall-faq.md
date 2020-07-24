---
title: Perguntas frequentes do Firewall do Azure
description: Perguntas frequentes sobre o Firewall do Azure. Um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos da Rede Virtual do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: victorh
ms.openlocfilehash: bd849a413d718ba5a25839c50c63ec2ad39be440
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87128090"
---
# <a name="azure-firewall-faq"></a>Perguntas frequentes do Firewall do Azure

## <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quais recursos têm suporte no Firewall do Azure?

Para saber mais sobre os recursos do Firewall do Azure, confira [Recursos do Firewall do Azure](features.md).

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo de implantação típico para o Firewall do Azure?

É possível implantar o Firewall do Azure em qualquer rede virtual, mas os clientes normalmente o implantam em uma rede virtual central e emparelham outras redes virtuais a ele em um modelo de hub e spoke. Você pode definir a rota padrão de redes virtuais emparelhadas para apontar a essa rede virtual de firewall central. O emparelhamento VNet global é compatível, mas não é recomendado devido a possíveis problemas de desempenho e latência entre regiões. Para obter o melhor desempenho, implante um firewall por região.

A vantagem desse modelo é a capacidade de exercer controle central sobre várias VNETs spoke entre assinaturas diferentes. Também há economia de custo, pois você não precisa implantar um firewall em cada VNET separadamente. A economia de custo deve ser medida em comparação com o custo de emparelhamento associado com base nos padrões de tráfego do cliente.

## <a name="how-can-i-install-the-azure-firewall"></a>Como eu instalo o Firewall do Azure?

O Firewall do Azure pode ser configurado usando o portal do Azure, PowerShell, API REST ou usando modelos. Confira o [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

## <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos do Firewall do Azure?

O Firewall do Azure é compatível com regras e coleções de regras. Uma coleção de regras é um conjunto de regras que compartilham a mesma ordem e a mesma prioridade. Coleções de regras são executadas na ordem de prioridade. Coleções de regras de rede têm prioridade maior do que as coleções de regras de aplicativo, sendo que todas as regras são de terminação.

Há três tipos de coleções de regras:

* *Regras de aplicativo*: configuram os FQDNs (nomes de domínio totalmente qualificados) que podem ser acessados por uma sub-rede.
* *Regras de rede*: configuram regras que contenham os endereços de origem, protocolos, portas de destino e os endereços de destino.
* *Regras de NAT*: configuram regras de DNAT para permitir conexões de entrada na Internet.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure dá suporte à filtragem de tráfego de entrada?

O Firewall do Azure dá suporte à filtragem de entrada e saída. Geralmente, a proteção de entrada é nos protocolos não HTTP/S. Por exemplo, protocolos RDP, SSH e FTP. Para obter a melhor proteção de HTTP/S de entrada, use um firewall de aplicativo Web, como o [WAF (Firewall do Aplicativo Web) do Azure](../web-application-firewall/overview.md).

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quais serviços de registro em log e análise têm suporte do Firewall do Azure?

O Firewall do Azure é integrado ao Azure Monitor para exibir e analisar logs de firewall. Os logs podem ser enviados para o Log Analytics, Armazenamento do Azure ou para Hubs de Eventos. Eles podem ser analisados no Log Analytics ou por diferentes ferramentas, como Excel e Power BI. Para saber mais, confira [Tutorial: Monitorar os logs do Firewall do Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como o Firewall do Azure funciona diferentemente dos serviços existentes, como NVAs no marketplace?

O Firewall do Azure é um serviço de firewall básico que pode atender a determinados cenários de cliente. A expectativa é de que você tenha uma mistura de NVAs de terceiros e o Firewall do Azure. Trabalhar melhor juntos é uma prioridade principal.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre o WAF do Gateway de Aplicativo e o Firewall do Azure?

O WAF (Firewall de Aplicativo Web) é um recurso do Gateway de Aplicativo que fornece proteção de entrada centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. O Firewall do Azure fornece proteção de entrada para protocolos não HTTP / S (por exemplo, RDP, SSH, FTP), proteção de nível de rede de saída para todas as portas e protocolos e proteção no nível do aplicativo para HTTP / S de saída.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre NSGs (Grupos de Segurança de Rede) e o Firewall do Azure?

O serviço de Firewall do Azure complementa a funcionalidade de grupo de segurança de rede. Juntos, eles fornecem uma melhor segurança de rede de "defesa em profundidade". Os grupo de segurança de rede fornecem filtragem de tráfego de camada de rede distribuída para limitar o tráfego para recursos dentro de redes virtuais em cada assinatura. O Firewall do Azure é um firewall como serviço de rede centralizado totalmente com estado, que fornece proteção no nível de rede e de aplicativo em diferentes assinaturas e redes virtuais.

## <a name="are-network-security-groups-nsgs-supported-on-the-azurefirewallsubnet"></a>Os grupos de segurança de rede (NSGs) são compatíveis no AzureFirewallSubnet?

O Firewall do Azure é um serviço gerenciado com várias camadas de proteção, incluindo a proteção da plataforma com NSGs na NIC (não visível).  Os NSGs de sub-rede não são necessários no AzureFirewallSubnet e estão desabilitados para garantir que não haja interrupção do serviço.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como configurar o Firewall do Azure com meus pontos de extremidade de serviço?

Para obter acesso seguro aos serviços de PaaS, recomendamos ponto de extremidade de serviço. Você pode optar por habilitar pontos de extremidade de serviço na sub-rede de Firewall do Azure e desabilitá-los nas redes virtuais spoke conectadas. Dessa forma você aproveita ambos os recursos: segurança do ponto de extremidade do serviço e registro em log central para todo o tráfego.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço para o Firewall do Azure?

Confira [Preços do Firewall do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>Como fazer para parar e iniciar o Firewall do Azure?

Use os métodos *deallocate* e *allocate* do Azure PowerShell.

Por exemplo:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Você deve realocar um firewall e o IP público ao grupo de recursos e à assinatura originais.

## <a name="what-are-the-known-service-limits"></a>Quais são os limites de serviço conhecidos?

Para limites de serviço do Firewall do Azure, consulte [Limites, cotas e restrições de assinatura e serviço do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>O Firewall do Azure em uma rede virtual de hub pode encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke?

Sim, você pode usar o Firewall do Azure em uma rede virtual de hub para encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke. As sub-redes em cada uma das redes virtuais de spoke devem ter uma UDR apontando para o Firewall do Azure como um gateway padrão para que este cenário funcione corretamente.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>O Firewall do Azure pode encaminhar e filtrar o tráfego de rede entre sub-redes na mesma rede virtual ou em redes virtuais emparelhadas?

Sim. No entanto, a configuração das UDRs para redirecionar o tráfego entre sub-redes na mesma VNET exige mais atenção. Embora o uso do intervalo de endereços de VNET como um prefixo de destino para a UDR seja suficiente, isso também roteia todo o tráfego de um computador para outro na mesma sub-rede por meio da instância do Firewall do Azure. Para evitar isso, inclua uma rota para a sub-rede na UDR com um tipo **VNET** de próximo salto. O gerenciamento dessas rotas pode ser complicado e passível de erros. O método recomendado para segmentação de rede interna é usar Grupos de Segurança de Rede, o que não exige UDRs.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>O Firewall do Azure realiza SNAT de saída entre redes privadas?

O Firewall do Azure não realiza SNAT (conversão de endereço de rede seguro) quando o endereço IP de destino é um intervalo de endereços IP privados de acordo com o [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se a sua organização usa um intervalo de endereços IP públicos para redes privadas, o Firewall do Azure realiza SNAT do tráfego para um dos endereços IP privados do firewall no AzureFirewallSubnet. Você pode configurar o Firewall do Azure para **não** realizar SNAT de seu intervalo de endereços IP públicos. Para obter mais informações, confira [Intervalos de endereços IP privados do SNAT do Firewall do Azure](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>O túnel forçado/encadeamento é compatível com uma solução de virtualização da rede?

O túnel forçado é compatível quando se cria um novo firewall. Não é possível configurar um firewall existente para túnel forçado. Para obter mais informações, confira [Túnel forçado do Firewall do Azure](forced-tunneling.md). 

O Firewall do Azure deve ter conectividade direta com a Internet. Se seu AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você deve substituir isso por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet.

Se a configuração exigir o túnel forçado para uma rede local e você puder determinar os prefixos de IP de destino para os destinos da Internet, você poderá configurar esses intervalos com a rede local como o próximo salto por meio de uma rota definida pelo usuário no AzureFirewallSubnet. Ou você pode usar o BGP para definir essas rotas.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Há qualquer firewall restrições no grupo de recursos?

Sim. O firewall, a rede virtual (VNet) e o endereço IP público devem estar todos no mesmo grupo de recursos.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar DNAT para tráfego de entrada da rede de Internet, também preciso configurar uma regra de rede correspondente para permitir esse tráfego?

Não. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Para saber mais sobre a lógica de processamento de regra do Firewall do Azure, confira [Lógica de processamento de regra do Firewall no Azure](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Como os curingas funcionam em um FQDN de destino da regra de aplicativo?

Se você configurar * **.contoso.com**, ele permitirá *anyvalue*.contoso.com, mas não contoso.com (o início do domínio). Para permitir o início do domínio, você deve configurá-lo explicitamente como um FQDN de destino.

## <a name="what-does-provisioning-state-failed-mean"></a>O que significa o *estado de provisionamento: Com falha*?

Sempre que uma alteração de configuração é aplicada, o Firewall do Azure tenta atualizar todas as suas instâncias de back-end subjacentes. Em casos raros, uma dessas instâncias de back-end pode falhar ao atualizar com a nova configuração e o processo de atualização será interrompido com um estado de provisionamento com falha. O Firewall do Azure ainda estará funcionando, mas a configuração aplicada poderá estar em um estado inconsistente, com algumas instâncias com a configuração anterior e outras com o conjunto de regras atualizado. Se isso acontecer, tente atualizar sua configuração mais uma vez até que a operação seja bem-sucedida e o firewall esteja em um estado de provisionamento *Com êxito*.

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Como o Firewall do Azure processa manutenção planejada e falhas não planejadas?
O Firewall do Azure consiste em vários nós de back-end em uma configuração ativo-ativo.  Em qualquer manutenção planejada, temos a lógica de esvaziamento de conexão para atualizar os nós normalmente.  As atualizações são planejadas fora do horário comercial para cada uma das regiões do Azure a fim de limitar ainda mais o risco de interrupção.  Para problemas não planejados, criamos uma instância de um novo nó para substituir o nó com falha.  A conectividade com o novo nó é normalmente restabelecida dentro de 10 segundos a partir do momento da falha.

## <a name="how-does-connection-draining-work"></a>Como funciona o esvaziamento da conexão?

Em qualquer manutenção planejada, a lógica de esvaziamento da conexão atualiza os nós do back-end normalmente. O Firewall do Azure aguarda 90 segundos para que as conexões existentes sejam fechadas. Se necessário, os clientes podem restabelecer automaticamente a conectividade com outro nó do back-end.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Há um limite de caracteres para um nome de firewall?

Sim. Há um limite de 50 caracteres para um nome de firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Por que o Firewall do Azure precisa de um tamanho de sub-rede /26?

O Firewall do Azure deve provisionar mais instâncias de máquina virtual conforme sua escala aumenta. Um espaço de endereço /26 garante que o firewall tenha endereços IP suficientes disponíveis para acomodar o aumento de escala.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>O tamanho da sub-rede do firewall precisa ser alterado conforme a escala do serviço aumenta?

Não. O Firewall do Azure não precisa de uma sub-rede maior que /26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Como posso aumentar a taxa de transferência do meu firewall?

A capacidade inicial da taxa de transferência do Firewall do Azure é de 2,5 a 3 Gbps e pode escalar horizontalmente até 30 Gbps. A escala horizontal acontece automaticamente com base no uso da CPU e na taxa de transferência.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo leva para o Firewall do Azure escalar horizontalmente?

A escala do Firewall do Azure aumenta gradualmente quando a taxa de transferência média ou o consumo de CPU está em 60%. O aumento de escala horizontal leva de cinco a sete minutos. Ao testar o desempenho, certifique-se de testar por pelo menos 10 a 15 minutos e inicie novas conexões para aproveitar os nós de firewall recém-criados.

## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>O Firewall do Azure permite acesso ao Active Directory por padrão?

Não. O Firewall do Azure bloqueia o acesso ao Active Directory por padrão. Para permitir o acesso, configure a marca de serviço AzureActiveDirectory. Para obter mais informações, confira [Marcas de serviço do Firewall do Azure](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Posso excluir um FQDN ou um endereço IP da filtragem baseada em inteligência contra ameaças do Firewall do Azure?

Sim, você pode usar o Azure PowerShell para fazer isso:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name $firewallname -ResourceGroupName $RG
$fw.ThreatIntelWhitelist.IpAddresses = @($fw.ThreatIntelWhitelist.IpAddresses + $ipaddresses )
$fw.ThreatIntelWhitelist.fqdns = @($fw.ThreatIntelWhitelist.fqdns + $fqdns)


Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Por que um ping TCP e ferramentas semelhantes conseguem se conectar a um FQDN de destino mesmo quando nenhuma regra no Firewall do Azure permite esse tráfego?

Um ping de TCP não está realmente se conectando ao FQDN de destino. Isso acontece porque o proxy transparente do Firewall do Azure escuta na porta 80/443 do tráfego de saída. O ping de TCP estabelece uma conexão com o firewall, que então descarta o pacote e registra a conexão. Esse comportamento não tem nenhum impacto de segurança. No entanto, para evitar confusão, estamos investigando possíveis alterações nesse comportamento.

## <a name="are-there-limits-for-the-number-of-ip-addresses-supported-by-ip-groups"></a>Há limites para o número de endereços IP aceitos nos grupos de IPs?

Sim. Para saber mais, confira [Assinatura e limites de serviço, cotas e restrições do Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits)

## <a name="can-i-move-an-ip-group-to-another-resource-group"></a>Posso mover um grupo de IPS para outro grupo de recursos?

Não, não há suporte para a movimentação de um grupo de IPS para outro grupo de recursos no momento.

## <a name="what-is-the-tcp-idle-timeout-for-azure-firewall"></a>Qual é o tempo limite de ociosidade de TCP para o Firewall do Azure?

Um comportamento padrão de um firewall de rede é garantir que as conexões TCP sejam mantidas ativas e fechá-las imediatamente se não houver atividade. O tempo limite de ociosidade de TCP do firewall do Azure é de quatro minutos. Essa configuração não é configurável. Se um período de inatividade for maior do que o valor de tempo limite, não haverá garantia de que a sessão TCP ou HTTP seja mantida. Uma prática comum é usar um TCP keep alive. Essa prática mantém a conexão ativa por um período maior. Para obter mais informações, consulte os [exemplos do .net](https://docs.microsoft.com/dotnet/api/system.net.servicepoint.settcpkeepalive?redirectedfrom=MSDN&view=netcore-3.1#System_Net_ServicePoint_SetTcpKeepAlive_System_Boolean_System_Int32_System_Int32_).

## <a name="can-i-deploy-azure-firewall-without-a-public-ip-address"></a>Posso implantar o Firewall do Azure sem um endereço IP público?

Não, no momento, você deve implantar o Firewall do Azure com um endereço IP público.

## <a name="where-does-azure-firewall-store-customer-data"></a>Onde o Firewall do Azure armazena dados do cliente?

O Firewall do Azure não move nem armazena os dados do cliente fora da região em que ele está implantado.