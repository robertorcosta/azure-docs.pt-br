---
title: Perguntas frequentes do Firewall do Azure
description: Perguntas frequentes para o Firewall Azure. Um serviço de segurança de rede gerenciado baseado em nuvem que protege os recursos da Rede Virtual do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 45276884d59ac8d1d876e2225ac02bb51c3f74fc
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437720"
---
# <a name="azure-firewall-faq"></a>Perguntas frequentes do Firewall do Azure

## <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall como serviço totalmente com estado com alta disponibilidade interna e escalabilidade de nuvem irrestrita. É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>Quais recursos têm suporte no Firewall do Azure?

* Firewall como serviço com estado
* Alta disponibilidade interna com escalabilidade de nuvem irrestrita
* Filtragem de FQDN
* Marcas de FQDN
* Regras de filtragem de tráfego de rede
* Suporte a SNAT de saída
* Suporte a DNAT de entrada
* É possível criar, impor e registrar em log centralmente políticas de conectividade de rede e de aplicativo em VNETs e assinaturas do Azure
* Totalmente integrado ao Azure Monitor para registro em log e análise

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>Qual é o modelo de implantação típico para o Firewall do Azure?

É possível implantar o Firewall do Azure em qualquer rede virtual, mas os clientes normalmente o implantam em uma rede virtual central e emparelham outras redes virtuais a ele em um modelo de hub e spoke. Você pode definir a rota padrão de redes virtuais emparelhadas para apontar a essa rede virtual de firewall central. O peering Global VNet é suportado, mas não é recomendado devido a potenciais problemas de desempenho e latência em todas as regiões. Para obter o melhor desempenho, implante um firewall por região.

A vantagem desse modelo é a capacidade de exercer controle central sobre várias VNETs spoke entre assinaturas diferentes. Também há economia de custo, pois você não precisa implantar um firewall em cada VNET separadamente. A economia de custo deve ser medida em comparação com o custo de emparelhamento associado com base nos padrões de tráfego do cliente.

## <a name="how-can-i-install-the-azure-firewall"></a>Como eu instalo o Firewall do Azure?

O Firewall do Azure pode ser configurado usando o portal do Azure, PowerShell, API REST ou usando modelos. Veja o [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md) para obter instruções passo a passo.

## <a name="what-are-some-azure-firewall-concepts"></a>Quais são alguns conceitos do Firewall do Azure?

O Firewall do Azure é compatível com regras e coleções de regras. Uma coleção de regras é um conjunto de regras que compartilham a mesma ordem e a mesma prioridade. Coleções de regras são executadas na ordem de prioridade. Coleções de regras de rede têm prioridade maior do que as coleções de regras de aplicativo, sendo que todas as regras são de terminação.

Existem três tipos de coleções de regras:

* *Regras do aplicativo*: Configure nomes de domínio totalmente qualificados (FQDNs) que podem ser acessados a partir de uma sub-rede.
* *Regras da rede*: Configure regras que contenham endereços de origem, protocolos, portas de destino e endereços de destino.
* *Regras nat*: Configure regras de DNAT para permitir conexões de Internet recebidas.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>O Firewall do Azure dá suporte à filtragem de tráfego de entrada?

O Firewall do Azure dá suporte à filtragem de entrada e saída. A proteção de entrada é normalmente usada para protocolos não-HTTP/S. Por exemplo, protocolos RDP, SSH e FTP. Para obter a melhor proteção HTTP/S de entrada, use um firewall de aplicativos web, como [o WAF (Azure Web Application Firewall, firewall](../web-application-firewall/overview.md)de aplicativos da Web) .

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>Quais serviços de registro em log e análise têm suporte do Firewall do Azure?

O Firewall do Azure é integrado ao Azure Monitor para exibir e analisar logs de firewall. Os logs podem ser enviados para o Log Analytics, Armazenamento do Azure ou para Hubs de Eventos. Eles podem ser analisados no Log Analytics ou por diferentes ferramentas, como Excel e Power BI. Para obter mais informações, consulte [Tutorial: Monitorar logs do Firewall do Azure](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>Como o Firewall do Azure funciona diferentemente dos serviços existentes, como NVAs no marketplace?

O Firewall do Azure é um serviço de firewall básico que pode atender a determinados cenários de cliente. Espera-se que você tenha uma mistura de NVAs de terceiros e Firewall Azure. Trabalhar melhor juntos é uma prioridade principal.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>Qual é a diferença entre o WAF do Gateway de Aplicativo e o Firewall do Azure?

O WAF (Firewall de Aplicativo Web) é um recurso do Gateway de Aplicativo que fornece proteção de entrada centralizada de seus aplicativos Web contra vulnerabilidades e explorações comuns. O Firewall do Azure fornece proteção de entrada para protocolos não HTTP / S (por exemplo, RDP, SSH, FTP), proteção de nível de rede de saída para todas as portas e protocolos e proteção no nível do aplicativo para HTTP / S de saída.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>Qual é a diferença entre NSGs (Grupos de Segurança de Rede) e o Firewall do Azure?

O serviço de Firewall do Azure complementa a funcionalidade de grupo de segurança de rede. Juntos, eles fornecem uma melhor segurança de rede de "defesa em profundidade". Os grupo de segurança de rede fornecem filtragem de tráfego de camada de rede distribuída para limitar o tráfego para recursos dentro de redes virtuais em cada assinatura. O Firewall do Azure é um firewall como serviço de rede centralizado totalmente com estado, que fornece proteção no nível de rede e de aplicativo em diferentes assinaturas e redes virtuais.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>Os NSGs (Network Security Groups, grupos de segurança de rede) são suportados na sub-rede do Firewall Azure?

O Azure Firewall é um serviço gerenciado com várias camadas de proteção, incluindo proteção de plataforma com NSGs de nível NIC (não visível).  Os NSGs de nível de sub-rede não são necessários na sub-rede do Firewall Do Azure e são desativados para garantir que não haja interrupção do serviço.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>Como configurar o Firewall do Azure com meus pontos de extremidade de serviço?

Para obter acesso seguro aos serviços de PaaS, recomendamos ponto de extremidade de serviço. Você pode optar por habilitar pontos de extremidade de serviço na sub-rede de Firewall do Azure e desabilitá-los nas redes virtuais spoke conectadas. Desta forma, você se beneficia de ambos os recursos: segurança de ponto final de serviço e registro central para todo o tráfego.

## <a name="what-is-the-pricing-for-azure-firewall"></a>Qual é o preço para o Firewall do Azure?

Consulte [os preços do Firewall Do Azure](https://azure.microsoft.com/pricing/details/azure-firewall/).

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

Para os limites de serviço do Azure Firewall, consulte os limites de assinatura e serviço do [Azure, cotas e restrições](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>O Firewall do Azure em uma rede virtual de hub pode encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke?

Sim, você pode usar o Firewall do Azure em uma rede virtual de hub para encaminhar e filtrar o tráfego de rede entre duas redes virtuais de spoke. As sub-redes em cada uma das redes virtuais faladas devem ter um UDR apontando para o Firewall Do Azure como um gateway padrão para que este cenário funcione corretamente.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>O Firewall do Azure pode encaminhar e filtrar o tráfego de rede entre sub-redes na mesma rede virtual ou em redes virtuais emparelhadas?

Sim. No entanto, configurar os UDRs para redirecionar o tráfego entre sub-redes no mesmo VNET requer atenção adicional. Embora o uso do intervalo de endereços de VNET como um prefixo de destino para a UDR seja suficiente, isso também roteia todo o tráfego de um computador para outro na mesma sub-rede por meio da instância do Firewall do Azure. Para evitar isso, inclua uma rota para a sub-rede na UDR com um tipo **VNET** de próximo salto. O gerenciamento dessas rotas pode ser complicado e passível de erros. O método recomendado para segmentação interna de rede é usar Grupos de Segurança de Rede, que não requerem UDRs.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>O Azure Firewall sai do SNAT entre redes privadas?

O Azure Firewall não snat quando o endereço IP de destino é um intervalo IP privado por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Se sua organização usar um intervalo de endereçoIP público para redes privadas, o Azure Firewall SNATs será o tráfego para um dos endereços IP privados de firewall no AzureFirewallSubnet. Você pode configurar o Firewall Azure para **não** SNAT sua faixa pública de endereçoIP. Para obter mais informações, consulte [as faixas de endereçoIP privada snat do Azure Firewall](snat-private-range.md).

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>O tunelamento/acorrentamento forçado a um aparelho virtual de rede é suportado?

Túnel forçado é suportado. Para obter mais informações, consulte [o túnel forçado do Azure Firewall (visualização)](forced-tunneling.md). 

O Firewall do Azure deve ter conectividade direta com a Internet. Se seu AzureFirewallSubnet aprender uma rota padrão para sua rede local via BGP, você deve substituir isso por um UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** para manter a conectividade direta com a Internet.

Se a configuração exigir o túnel forçado para uma rede local e você puder determinar os prefixos de IP de destino para os destinos da Internet, você poderá configurar esses intervalos com a rede local como o próximo salto por meio de uma rota definida pelo usuário no AzureFirewallSubnet. Ou você pode usar o BGP para definir essas rotas.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>Há qualquer firewall restrições no grupo de recursos?

Sim. O firewall, o VNet e o endereço IP público devem estar no mesmo grupo de recursos.

## <a name="when-configuring-dnat-for-inbound-internet-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Ao configurar o DNAT para tráfego de rede de internet de entrada, também preciso configurar uma regra de rede correspondente para permitir esse tráfego?

Não. As regras DNAT adicionam implicitamente uma regra de rede correspondente para permitir o tráfego convertido. Você pode substituir esse comportamento adicionando explicitamente uma coleção de regras de rede com regras de negação que correspondem ao tráfego convertido. Para saber mais sobre a lógica de processamento de regra do Firewall do Azure, confira [Lógica de processamento de regra do Firewall no Azure](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>Como os curingas funcionam em uma regra de aplicativo que visa o FQDN?

Se você configurar ***.contoso.com**, ele permite *qualquer valor*.contoso.com, mas não contoso.com (o ápice de domínio). Se você quiser permitir o ápice do domínio, você deve configurá-lo explicitamente como um FQDN de destino.

## <a name="what-does-provisioning-state-failed-mean"></a>O que *significa provisionamento: Falha?*

Sempre que uma alteração de configuração é aplicada, o Azure Firewall tenta atualizar todas as suas instâncias de backend subjacentes. Em casos raros, uma dessas instâncias de back-end pode não ser atualizada com a nova configuração e o processo de atualização pára com um estado de provisionamento com falha. O Firewall do Azure ainda está operacional, mas a configuração aplicada pode estar em um estado inconsistente, onde algumas instâncias têm a configuração anterior onde outras têm o conjunto de regras atualizado. Se isso acontecer, tente atualizar sua configuração mais uma vez até que a operação seja bem sucedida e seu Firewall esteja em um estado de provisionamento *bem sucedido.*

## <a name="how-does-azure-firewall-handle-planned-maintenance-and-unplanned-failures"></a>Como o Azure Firewall lida com manutenção planejada e falhas não planejadas?
O Azure Firewall consiste em vários álos de backend em uma configuração ativa.  Para qualquer manutenção planejada, temos lógica de drenagem de conexão para atualizar graciosamente nós.  As atualizações são planejadas durante o horário comercial para cada uma das regiões do Azure para limitar ainda mais o risco de interrupção.  Para problemas não planejados, instanciamos um novo nó para substituir o nó falho.  A conectividade com o novo nó é normalmente restabelecida dentro de 10 segundos a partir do momento da falha.

## <a name="how-does-connection-draining-work"></a>Como funciona a drenagem de conexões?

Para qualquer manutenção planejada, a lógica de drenagem de conexão atualiza graciosamente os nós backend. O Firewall Azure espera 90 segundos para que as conexões existentes se fechem. Se necessário, os clientes podem restabelecer automaticamente a conectividade a outro nó backend.

## <a name="is-there-a-character-limit-for-a-firewall-name"></a>Existe um limite de caracteres para um nome de firewall?

Sim. Há um limite de 50 caracteres para um nome de firewall.

## <a name="why-does-azure-firewall-need-a-26-subnet-size"></a>Por que o Azure Firewall precisa de um tamanho de sub-rede de 26?

O Azure Firewall deve provisão mais instâncias de máquinas virtuais à medida que ele é dimensionado. Um espaço de endereço /26 garante que o firewall tenha endereços IP suficientes disponíveis para acomodar o dimensionamento.

## <a name="does-the-firewall-subnet-size-need-to-change-as-the-service-scales"></a>O tamanho da sub-rede de firewall precisa mudar à medida que as escalas de serviço são dimensionadas?

Não. O Firewall Azure não precisa de uma sub-rede maior que a 26.

## <a name="how-can-i-increase-my-firewall-throughput"></a>Como posso aumentar minha passagem de firewall?

A capacidade inicial de throughput do Azure Firewall é de 2,5 - 3 Gbps e ele é dimensionado para 30 Gbps. Ele é dimensionado com base no uso e throughput da CPU. Suporte de contato para aumentar a capacidade de throughput do firewall.

## <a name="how-long-does-it-take-for-azure-firewall-to-scale-out"></a>Quanto tempo leva para o Azure Firewall escalar?

Leva de cinco a sete minutos para o Firewall Azure escalar. Suporte de contato para aumentar a capacidade inicial de throughput do firewall se você tiver rajadas que requerem uma escala automática mais rápida.

Os seguintes pontos devem ser levados em conta quando você testa a escala automática de firewall:

- O desempenho de fluxo TCP único é limitado a 1,4 Gbps. Assim, um teste de desempenho precisa estabelecer múltiplos fluxos TCP.
- As ferramentas de desempenho devem estabelecer continuamente novas conexões para que elas se conectem com as instâncias de firewall de backup em escala. Se o teste estabelecer conexões uma vez no início, então elas só se conectarão com as instâncias iniciais de backend. Mesmo que o firewall seja dimensionado, você não verá nenhum desempenho aumentado porque as conexões estão associadas às instâncias iniciais.


## <a name="does-azure-firewall-allow-access-to-active-directory-by-default"></a>O Azure Firewall permite o acesso ao Active Directory por padrão?

Não. O Azure Firewall bloqueia o acesso ao Active Directory por padrão. Para permitir o acesso, configure a tag de serviço AzureActiveDirectory. Para obter mais informações, consulte [as tags de serviço do Azure Firewall](service-tags.md).

## <a name="can-i-exclude-a-fqdn-or-an-ip-address-from-azure-firewall-threat-intelligence-based-filtering"></a>Posso excluir um FQDN ou um endereço IP da filtragem baseada em Ameaças do Firewall Do Azure?

Sim, você pode usar o Azure PowerShell para fazê-lo:

```azurepowershell
# Add a Threat Intelligence Whitelist to an Existing Azure Firewall

## Create the Whitelist with both FQDN and IPAddresses

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist = New-AzFirewallThreatIntelWhitelist `
   -FQDN @("fqdn1", "fqdn2", …) -IpAddress @("ip1", "ip2", …)

## Or Update FQDNs and IpAddresses separately

$fw = Get-AzFirewall -Name "Name_of_Firewall" -ResourceGroupName "Name_of_ResourceGroup"
$fw.ThreatIntelWhitelist.FQDNs = @("fqdn1", "fqdn2", …)
$fw.ThreatIntelWhitelist.IpAddress = @("ip1", "ip2", …)

Set-AzFirewall -AzureFirewall $fw
```

## <a name="why-can-a-tcp-ping-and-similar-tools-successfully-connect-to-a-target-fqdn-even-when-no-rule-on-azure-firewall-allows-that-traffic"></a>Por que um ping TCP e ferramentas similares podem se conectar com sucesso a um FQDN de destino mesmo quando nenhuma regra no Azure Firewall permite esse tráfego?

Um ping TCP não está realmente se conectando ao FQDN de destino. Isso acontece porque o proxy transparente do Azure Firewall ouve na porta 80/443 para tráfego de saída. O ping TCP estabelece uma conexão com o firewall, que então derruba o pacote e registra a conexão. Esse comportamento não tem nenhum impacto na segurança. No entanto, para evitar confusão estamos investigando possíveis mudanças nesse comportamento. 