---
title: Unir o runtime de integração do Azure-SSIS a uma rede virtual
description: Saiba como participar de um tempo de execução de integração Azure-SSIS em uma rede virtual Do Zure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 4819eaf2a65cf542029cf36f262d0cea5be75f2e
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521954"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Unir o runtime de integração do Azure-SSIS a uma rede virtual

Ao usar o SSIS (SSIS) SQL Server Integration Services (SSIS) na Fábrica de Dados Do Azure, você deve juntar o tempo de execução de integração Do Azure-SSIS (IR) a uma rede virtual do Azure nos seguintes cenários:

- Você deseja se conectar a armazenamentos de dados locais a partir de pacotes SSIS que são executados em seu IR Azure-SSIS sem configurar ou gerenciar um IR auto-hospedado como proxy. 

- Você deseja hospedar o Banco de Dados de Catálogo SSIS (SSISDB) em um banco de dados SQL do Azure com regras de firewall IP/pontos finais de serviço de rede virtual ou uma instância gerenciada com ponto final privado. 

- Você deseja se conectar aos recursos do Azure configurados com pontos finais de serviço de rede virtual a partir de pacotes SSIS que são executados em seu IR Azure-SSIS.

- Você deseja se conectar a armazenamentos/recursos de dados configurados com regras de firewall IP de pacotes SSIS que são executados em seu IR Azure-SSIS.

A Fábrica de Dados permite que você junte seu IR Azure-SSIS a uma rede virtual criada através do modelo clássico de implantação ou do modelo de implantação do Azure Resource Manager.

> [!IMPORTANT]
> A rede virtual clássica está sendo depreciada, então use a rede virtual Do Azure Resource Manager.  Se você já usa a rede virtual clássica, mude para a rede virtual do Azure Resource Manager o mais rápido possível.

A [configuração de um Tempo de execução de integração de servidores Azure-SQL (SSIS) para participar de um](tutorial-deploy-ssis-virtual-network.md) tutorial de rede virtual mostra os passos mínimos através do portal Azure. Este artigo expande no tutorial e descreve todas as tarefas opcionais:

- Se você estiver usando rede virtual (clássica).
- Se você trouxer seus próprios endereços IP públicos para o Azure-SSIS IR.
- Se você usar seu próprio servidor DNS (Domain Name System, sistema de nome de domínio).
- Se você usar um grupo de segurança de rede (NSG) na sub-rede.
- Se você usar o Azure ExpressRoute ou uma rota definida pelo usuário (UDR).
- Se você usar o Azure-SSIS IR personalizado.
- Se você usar o provisionamento Azure Powershell.

## <a name="access-to-on-premises-data-stores"></a>Acessar armazenamentos de dados locais

Se os pacotes SSIS acessarem armazenamentos de dados no local, você poderá se juntar ao seu IR Azure-SSIS a uma rede virtual conectada à rede local. Ou você pode configurar e gerenciar um IR auto-hospedado como proxy para o seu IR Azure-SSIS. Para obter mais informações, consulte [Configurar um IR auto-hospedado como um proxy para um IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

Ao juntar seu Ir Azure-SSIS a uma rede virtual, lembre-se desses pontos importantes: 

- Se nenhuma rede virtual estiver conectada à sua rede local, crie primeiro uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que seu IR Azure-SSIS se inscreva. Em seguida, configure uma [conexão](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) de gateway VPN de site para local ou conexão [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) a partir dessa rede virtual para sua rede local. 

- Se uma rede virtual do Azure Resource Manager já estiver conectada à sua rede local no mesmo local que o SEU IR Azure-SSIS, você poderá aderir ao IR dessa rede virtual. 

- Se uma rede virtual clássica já estiver conectada à sua rede local em um local diferente do seu IR Azure-SSIS, você poderá criar uma [rede virtual azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que seu IR Azure-SSIS se inscreva. Em seguida, configure uma conexão entre [rede virtual clássica e Azure Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md). 
 
- Se uma rede virtual do Azure Resource Manager já estiver conectada à sua rede local em um local diferente do seu IR Azure-SSIS, você poderá primeiro criar uma [rede virtual do Azure Resource Manager](../virtual-network/quick-create-portal.md#create-a-virtual-network) para que seu IR Azure-SSIS se junte. Em seguida, configure uma conexão de rede virtual do Azure Resource Manager to Azure Resource Manager. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>Hospedagem do catálogo SSIS no Banco de Dados SQL

Se você hospedar seu catálogo SSIS em um banco de dados SQL do Azure com pontos finais de serviço de rede virtual, certifique-se de juntar seu IR Azure-SSIS à mesma rede virtual e sub-rede.

Se você hospedar seu catálogo SSIS em uma instância gerenciada com ponto final privado, certifique-se de juntar seu IR Azure-SSIS à mesma rede virtual, mas em uma sub-rede diferente da instância gerenciada. Para se juntar ao seu IR Azure-SSIS a uma rede virtual diferente da instância gerenciada, recomendamos peering de rede virtual (que é limitado à mesma região) ou uma conexão de rede virtual para rede virtual. Para obter mais informações, consulte [Conecte seu aplicativo à instância gerenciada do Azure SQL Database](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="access-to-azure-services"></a>Acesso aos serviços do Azure

Se os pacotes do SSIS acessarem os recursos do Azure que suportam [pontos finais de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) de rede virtual e você quiser garantir acesso a esses recursos do Azure-SSIS IR, você poderá se juntar ao seu IR Azure-SSIS a uma sub-rede virtual configurada para pontos finais de serviço de rede virtual e, em seguida, adicionar uma regra de rede virtual aos recursos relevantes do Azure para permitir o acesso da mesma sub-rede.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>Acesso a fontes de dados protegidas pela regra de firewall IP

Se o seu SSIS empacota armazenamentos/recursos de acesso que permitem apenas endereços IP públicos estáticos específicos e você deseja garantir acesso a esses recursos do Azure-SSIS IR, você pode trazer seus próprios [endereços IP públicos](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address) para o Azure-SSIS IR enquanto se junta a uma rede virtual e, em seguida, adicionar uma regra de firewall IP aos recursos relevantes para permitir o acesso a partir desses endereços IP.

Em todos os casos, a rede virtual só pode ser implantada através do modelo de implantação do Azure Resource Manager.

As seções a seguir apresentam mais detalhes. 

## <a name="virtual-network-configuration"></a>Configuração de rede virtual

Configure sua rede virtual para atender a esses requisitos: 

- Certifique-se `Microsoft.Batch` de que é um provedor registrado sob a assinatura de sua sub-rede virtual que hospeda o IR Azure-SSIS. Se você usar uma rede `MicrosoftAzureBatch` virtual clássica, junte-se também à função de Contribuinte de Máquina Virtual Clássica para essa rede virtual. 

- Certifique-se de ter as permissões necessárias. Para obter mais informações, consulte [Configurar permissões](#perms).

- Selecione a sub-rede correta para hospedar o IV de SSIS do Azure. Para obter mais informações, consulte [Selecione a sub-rede](#subnet). 

- Se você trouxer seus próprios endereços IP públicos para o IR Azure-SSIS, consulte [Selecionar os endereços IP públicos estáticos](#publicIP)

- Se você usar o servidor DNS (Domain Name System, sistema de nomes de domínio) na rede virtual, consulte [Configurar o servidor DNS](#dns_server). 

- Se você usar um GRUPO DE SEGURANÇA de rede (NSG) na sub-rede, consulte [Configurar um NSG](#nsg). 

- Se você usar o Azure ExpressRoute ou uma rota definida pelo usuário (UDR), consulte [Use Azure ExpressRoute ou um UDR](#route). 

- Certifique-se de que o grupo de recursos da rede virtual (ou o grupo de recursos dos endereços IP públicos se você trouxer seus próprios endereços IP públicos) possa criar e excluir certos recursos de rede do Azure. Para obter mais informações, consulte [Configurar o grupo de recursos](#resource-group). 

- Se você personalizar seu IR Azure-SSIS conforme descrito na [configuração personalizada para o Azure-SSIS IR,](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)seus nós DE IR Azure-SSIS receberão endereços IP privados de uma faixa predefinida de 172.16.0.0 a 172.31.255.255. Portanto, certifique-se de que as faixas de endereçoIP privadas de suas redes virtuais ou locais não colidam com esse intervalo.

Este diagrama mostra as conexões necessárias para o seu IR Azure-SSIS:

![IR Azure-SSIS](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir.png)

### <a name="set-up-permissions"></a><a name="perms"></a>Configure permissões

O usuário que cria o IR Azure-SSIS deve ter as seguintes permissões:

- Se você ingressar no seu SSIS IR em uma rede virtual do Azure Resource Manager, terá duas opções:

  - Use a função interna de Colaborador de Rede. Essa função vem com a permissão _Microsoft.Network/\*_, que tem um escopo muito maior do que o necessário.

  - Crie uma função personalizada que inclua apenas a permissão _Microsoft.Network/virtualNetworks/\*/join/action_. Se você também quiser trazer seus próprios endereços IP públicos para o Azure-SSIS IR ao mesmo tempo em que se junta a uma rede virtual do Azure Resource Manager, inclua também a _permissão Microsoft.Network/publicIPAddresses/*/join/action_ na função.

- Se você estiver ingressando em seu SSIS IR em uma rede virtual clássica, recomendamos usar a função integrante do Virtual Machine interna. Caso contrário, você terá que definir uma função personalizada que inclua a permissão para ingressar na rede virtual.

### <a name="select-the-subnet"></a><a name="subnet"></a>Selecione a Sub-rede.

Ao escolher uma sub-rede: 

- Não selecione a GatewaySubnet para implantar um IR Azure-SSIS. É dedicado para gateways de rede virtuais. 

- Certifique-se de que a sub-rede selecionada tenha espaço de endereço disponível suficiente para o IR Azure-SSIS usar. Deixe endereços IP disponíveis por pelo menos duas vezes o número do nó IR. O Azure reserva alguns endereços IP em cada sub-rede. Esses endereços não podem ser usados. Os primeiros e últimos endereços IP das sub-redes são reservados para conformidade com o protocolo, e mais três endereços são usados para serviços do Azure. Para obter mais informações, consulte [Existem restrições quanto ao uso de endereços IP dentro dessas sub-redes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) 

- Não use uma sub-rede que seja ocupada exclusivamente por outros serviços do Azure (por exemplo, instância gerenciada do Banco de Dados SQL, Serviço de Aplicativo e assim por diante). 

### <a name="select-the-static-public-ip-addresses"></a><a name="publicIP"></a>Selecione os endereços IP públicos estáticos

Se você quiser trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR ao mesmo tempo em que se junta a uma rede virtual, certifique-se de que eles atendam aos seguintes requisitos:

- Exatamente dois não utilizados que ainda não estão associados a outros recursos do Azure devem ser fornecidos. O extra será usado quando atualizarmos periodicamente seu IR Azure-SSIS. Observe que um endereço IP público não pode ser compartilhado entre seus IRs Ativos Azure-SSIS.

- Ambos devem ser estáticos do tipo padrão. Consulte [sKUs de Endereço IP Público](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#sku) para obter mais detalhes.

- Ambos deveriam ter um nome DNS. Se você não forneceu um nome DNS ao criá-los, você pode fazê-lo no portal Azure.

![IR Azure-SSIS](media/ssis-integration-runtime-management-troubleshoot/setup-publicipdns-name.png)

- Eles e a rede virtual devem estar sob a mesma assinatura e na mesma região.

### <a name="set-up-the-dns-server"></a><a name="dns_server"></a>Configure o servidor DNS 
Se você precisar usar seu próprio servidor DNS em uma rede virtual acompanhada pelo seu IR Azure-SSIS para resolver seu nome de host privado, certifique-se de que ele também pode resolver nomes de host globais do Azure (por exemplo, um blob de armazenamento Azure chamado `<your storage account>.blob.core.windows.net`). 

Uma abordagem recomendada está abaixo: 

-   Configure o DNS personalizado para encaminhar solicitações ao Azure DNS. Você pode encaminhar registros DeDNS não resolvidos para o endereço IP do Azure recursivo (168.63.129.16) no seu próprio servidor DNS. 

Para obter mais informações, consulte [a resolução Nome que usa seu próprio servidor DNS](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). 

> [!NOTE]
> Por favor, use um nome de domínio totalmente qualificado (FQDN) `<your_private_server>.contoso.com` para `<your_private_server>`o nome do host privado, por exemplo, use em vez de , como O IR do Azure-SSIS não anexará automaticamente seu próprio sufixo DNS.

### <a name="set-up-an-nsg"></a><a name="nsg"></a>Configure um NSG
Se você precisar implementar um NSG para a sub-rede usada pelo seu IR Azure-SSIS, permita o tráfego de entrada e saída através das seguintes portas: 

-   **Exigência de entrada do Azure-SSIS IR**

| Direção | Protocolo de transporte | Fonte | Faixa de porta de origem | Destino | Faixa de porto de destino | Comentários |
|---|---|---|---|---|---|---|
| Entrada | TCP | Gerenciamento de batchnode | * | VirtualNetwork | 29876, 29877 (se você aderir ao IR para uma rede virtual do Gerenciador de Recursos) <br/><br/>10100, 20100, 30100 (se você unir o IR a uma rede virtual clássica)| O serviço Data Factory usa essas portas para se comunicar com os nós do seu IR Azure-SSIS na rede virtual. <br/><br/> Quer você crie ou não um NSG de nível de sub-rede, a Data Factory sempre configura um NSG no nível das placas de interface de rede (NICs) anexadas às máquinas virtuais que hospedam o IR Azure-SSIS. Somente o tráfego de entrada dos endereços IP do Data Factory nas portas especificadas é permitido pelo NSG no nível do adaptador de rede. Mesmo que você abra essas portas para o tráfego de internet no nível da sub-rede, o tráfego de endereços IP que não são endereços IP da Fábrica de Dados é bloqueado no nível NIC. |
| Entrada | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (Opcional) Essa regra só é necessária quando o apoiador da Microsoft pedir ao cliente para abrir para solução avançada de problemas, e pode ser fechada logo após a solução de problemas. A tag de serviço **CorpNetSaw** permite apenas a segurança das estações de trabalho de acesso na rede corporativa da Microsoft para usar desktop remoto. E esta tag de serviço não pode ser selecionada do portal e só está disponível via Azure PowerShell ou Azure CLI. <br/><br/> No nível NIC NSG, a porta 3389 está aberta por padrão e permitimos que você controle a porta 3389 no nível de sub-rede NSG, enquanto isso o Azure-SSIS IR desautorizou a saída da porta 3389 por padrão na regra de firewall do Windows em cada nó IR para proteção. |
||||||||

-   **Exigência de saída do Azure-SSIS IR**

| Direção | Protocolo de transporte | Fonte | Faixa de porta de origem | Destino | Faixa de porto de destino | Comentários |
|---|---|---|---|---|---|---|
| Saída | TCP | VirtualNetwork | * | AzureCloud | 443 | Os nós do seu Ir Azure-SSIS na rede virtual usam essa porta para acessar serviços do Azure, como o Azure Storage e o Azure Event Hubs. |
| Saída | TCP | VirtualNetwork | * | Internet | 80 | (Opcional) Os nós do seu Azure-SSIS IR na rede virtual usam esta porta para baixar uma lista de revogação de certificados da internet. Se você bloquear esse tráfego, você poderá sofrer o downgrade de desempenho ao iniciar o IR e perder a capacidade de verificar a lista de revogação de certificados para o uso do certificado. Se você quiser reduzir ainda mais o destino para certos FQDNs, consulte use a seção **Use Azure ExpressRoute ou UDR**|
| Saída | TCP | VirtualNetwork | * | Sql | 1433, 11000-11999 | (Opcional) Essa regra só é necessária quando os nós do seu IR Azure-SSIS na rede virtual acessam um SSISDB hospedado pelo seu servidor SQL Database. Se a política de conexão do servidor sql database estiver definida como **Proxy** em vez de **Redirecionar,** somente a porta 1433 será necessária. <br/><br/> Essa regra de segurança de saída não é aplicável a um SSISDB hospedado por sua instância gerenciada na rede virtual ou servidor de banco de dados Azure configurado com ponto final privado. |
| Saída | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 | (Opcional) Essa regra só é necessária quando os nós do seu IR Azure-SSIS na rede virtual acessam um SSISDB hospedado por sua instância gerenciada na rede virtual ou servidor do Banco de Dados Azure configurado com ponto final privado. Se a política de conexão do servidor sql database estiver definida como **Proxy** em vez de **Redirecionar,** somente a porta 1433 será necessária. |
| Saída | TCP | VirtualNetwork | * | Armazenamento | 445 | (Opcional) Essa regra só é necessária quando você deseja executar o pacote SSIS armazenado em Arquivos Azure. |
||||||||

### <a name="use-azure-expressroute-or-udr"></a><a name="route"></a>Use a Azure ExpressRoute ou UDR
Se você quiser inspecionar o tráfego de saída do Azure-SSIS IR, você pode direcionar o tráfego iniciado do Azure-SSIS IR para o dispositivo de firewall local via abanagem de força [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) (anunciando uma rota BGP, 0.0.0.0/0, para a rede virtual) ou para o Network Virtual Appliance (NVA) como um firewall ou [Firewall Azure](https://docs.microsoft.com/azure/firewall/) via [UDRs](../virtual-network/virtual-networks-udr-overview.md). 

![Cenário de NVA para Azure-SSIS IR](media/join-azure-ssis-integration-runtime-virtual-network/azure-ssis-ir-nva.png)

Você precisa fazer coisas abaixo para fazer todo o cenário funcionar
   -   O tráfego de entrada entre os serviços de gerenciamento do Azure Batch e o IR Azure-SSIS não pode ser roteado via aparelho de firewall.
   -   O aparelho de firewall permitirá o tráfego de saída exigido pelo Azure-SSIS IR.

Tráfego de entrada entre os serviços de gerenciamento do Azure Batch e o IR Azure-SSIS não pode ser encaminhado para o aparelho de firewall, caso contrário, o tráfego será quebrado devido a um problema de roteamento assimétrico. As rotas devem ser definidas para o tráfego de entrada para que o tráfego possa responder da mesma forma que entrou. Você pode definir UDRs específicos para direcionar o tráfego entre os serviços de gerenciamento do Azure Batch e o Ir Azure-SSIS com o próximo tipo de hop como **Internet**.

Por exemplo, se o seu IR Do Azure-SSIS estiver `UK South` localizado e quiser inspecionar o tráfego de saída através `BatchNodeManagement.UKSouth` do Firewall Azure, você obterá, em primeiro lugar, uma lista de faixa si de serviço da tag IP do link de [download da faixa](https://www.microsoft.com/download/details.aspx?id=56519) de serviço ou através da [API de detecção de marca de serviço.](https://aka.ms/discoveryapi) Em seguida, aplique os Seguintes UDRs de rotas de faixa ip relacionadas com o próximo tipo de salto como **Internet,** juntamente com a rota 0.0.0.0/0 com o próximo tipo de hop como **aparelho Virtual**.

![Configurações uDR do lote azure](media/join-azure-ssis-integration-runtime-virtual-network/azurebatch-udr-settings.png)

> [!NOTE]
> Esta abordagem incorre em um custo adicional de manutenção. Verifique regularmente o intervalo IP e adicione novas faixas IP em seu UDR para evitar quebrar o IR Azure-SSIS. Recomendamos verificar a faixa de IP mensalmente porque quando o novo IP aparecer na tag de serviço, o IP levará mais um mês para entrar em vigor. 

Para facilitar a configuração das regras do UDR, você pode executar seguindo o script do Powershell para adicionar regras de UDR para serviços de gerenciamento de lotes do Azure:
```powershell
$Location = "[location of your Azure-SSIS IR]"
$RouteTableResourceGroupName = "[name of Azure resource group that contains your Route Table]"
$RouteTableResourceName = "[resource name of your Azure Route Table ]"
$RouteTable = Get-AzRouteTable -ResourceGroupName $RouteTableResourceGroupName -Name $RouteTableResourceName
$ServiceTags = Get-AzNetworkServiceTag -Location $Location
$BatchServiceTagName = "BatchNodeManagement." + $Location
$UdrRulePrefixForBatch = $BatchServiceTagName
if ($ServiceTags -ne $null)
{
    $BatchIPRanges = $ServiceTags.Values | Where-Object { $_.Name -ieq $BatchServiceTagName }
    if ($BatchIPRanges -ne $null)
    {
        Write-Host "Start to add rule for your route table..."
        for ($i = 0; $i -lt $BatchIPRanges.Properties.AddressPrefixes.Count; $i++)
        {
            $UdrRuleName = "$($UdrRulePrefixForBatch)_$($i)"
            Add-AzRouteConfig -Name $UdrRuleName `
                -AddressPrefix $BatchIPRanges.Properties.AddressPrefixes[$i] `
                -NextHopType "Internet" `
                -RouteTable $RouteTable `
                | Out-Null
            Write-Host "Add rule $UdrRuleName to your route table..."
        }
        Set-AzRouteTable -RouteTable $RouteTable
    }
}
else
{
    Write-Host "Failed to fetch service tags, please confirm that your Location is valid."
}
```

Para que o aparelho de firewall permita o tráfego de saída, você precisa permitir que a saída para abaixo das portas seja igual à exigência nas regras de saída do NSG.
-   Port 443 com destino como serviços azure Cloud.

    Se você usar o Azure Firewall, você pode especificar a regra de rede com a tag de serviço do AzureCloud. Para firewall dos outros tipos, você pode simplesmente permitir o destino como tudo para a porta 443 ou permitir abaixo de FQDNs com base no tipo de seu ambiente Azure:
    | Azure Environment | Pontos de extremidade                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Público do Azure      | <ul><li><b>Fábrica de Dados Azure (Gerenciamento)</b></li><li style="list-style-type:none"><ul><li>\*Frontend.clouddatahub.net.</li></ul></li><li><b>Armazenamento azure (gerenciamento)</b></li><li style="list-style-type:none"><ul><li>\*.blob.core.windows.net</li><li>\*Table.core.windows.net</li></ul></li><li><b>Registro de contêineres do Azure (configuração personalizada)</b></li><li style="list-style-type:none"><ul><li>\*.azurecr.io</li></ul></li><li><b>Hub de Eventos (Registro)</b></li><li style="list-style-type:none"><ul><li>\*.servicebus.windows.net</li></ul></li><li><b>Serviço de registro microsoft (uso interno)</b></li><li style="list-style-type:none"><ul><li>gcs.prod.monitoring.core.windows.net</li><li>prod.warmpath.msftcloudes.com</li><li>azurewatsonanalysis-prod.core.windows.net</li></ul></li></ul> |
    | Azure Government  | <ul><li><b>Fábrica de Dados Azure (Gerenciamento)</b></li><li style="list-style-type:none"><ul><li>\*frontend.datamovement.azure.us</li></ul></li><li><b>Armazenamento azure (gerenciamento)</b></li><li style="list-style-type:none"><ul><li>\*.blob.core.usgovcloudapi.net</li><li>\*Table.core.usgovcloudapi.net</li></ul></li><li><b>Registro de contêineres do Azure (configuração personalizada)</b></li><li style="list-style-type:none"><ul><li>\*Azurecr.us</li></ul></li><li><b>Hub de Eventos (Registro)</b></li><li style="list-style-type:none"><ul><li>\*Servicebus.usgovcloudapi.net</li></ul></li><li><b>Serviço de registro microsoft (uso interno)</b></li><li style="list-style-type:none"><ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>azurewatsonanalysis.usgovcloudapp.net</li></ul></li></ul> |
    | Azure China 21Vianet     | <ul><li><b>Fábrica de Dados Azure (Gerenciamento)</b></li><li style="list-style-type:none"><ul><li>\*frontend.datamovement.azure.cn</li></ul></li><li><b>Armazenamento azure (gerenciamento)</b></li><li style="list-style-type:none"><ul><li>\*Blob.core.chinacloudapi.cn</li><li>\*Table.core.chinacloudapi.cn</li></ul></li><li><b>Registro de contêineres do Azure (configuração personalizada)</b></li><li style="list-style-type:none"><ul><li>\*Azurecr.cn.</li></ul></li><li><b>Hub de Eventos (Registro)</b></li><li style="list-style-type:none"><ul><li>\*Servicebus.chinacloudapi.cn</li></ul></li><li><b>Serviço de registro microsoft (uso interno)</b></li><li style="list-style-type:none"><ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>azurewatsonanalysis.chinacloudapp.cn</li></ul></li></ul>

    Quanto aos FQDNs do Azure Storage, Azure Container Registry e Event Hub, você também pode optar por ativar os seguintes pontos finais de serviço para sua rede virtual para que o tráfego de rede para esses pontos finais passe pela rede backbone do Azure em vez de ser roteado para o firewall do seu dispositivo:
    -  Microsoft.Storage
    -  Microsoft.ContainerRegistry
    -  Microsoft.EventHub


-   Porta 80 com destino como sites de download crl.

    Você deve permitir abaixo FQDNs que são usados como CRL (Lista de Revogação de Certificados) locais de download de certificados para o propósito de gerenciamento de IR do Azure-SSIS:
    -  crl.microsoft.com:80
    -  mscrl.microsoft.com:80
    -  crl3.digicert.com:80
    -  crl4.digicert.com:80
    -  ocsp.digicert.com:80
    -  cacerts.digicert.com:80
    
    Se você estiver usando certificados com CRL diferente, é sugerido incluí-los também. Você pode ler isso para entender mais na [Lista de Revogação de Certificados](https://social.technet.microsoft.com/wiki/contents/articles/2303.understanding-access-to-microsoft-certificate-revocation-list.aspx).

    Se você não permitir esse tráfego, você poderá sofrer o downgrade de desempenho ao iniciar o Azure-SSIS IR e perder a capacidade de verificar a lista de revogação de certificados para o uso do certificado, o que não é recomendado do ponto de vista de segurança.

-   Porta 1433, 11000-11999 com destino como Azure SQL (só necessário quando os nós do seu Azure-SSIS IR na rede virtual acessam um SSISDB hospedado pelo seu servidor SQL Database).

    Se você usar o Azure Firewall, você pode especificar a regra de rede com a tag de serviço SQL do Azure, caso contrário, você pode permitir o destino como url sql azure específico no aparelho de firewall.

-   Porta 445 com destino como Armazenamento Azure (apenas necessário quando você executa o pacote SSIS armazenado em Arquivos Azure).

    Se você usar o Azure Firewall, você pode especificar a regra de rede com a Tag de serviço de armazenamento, caso contrário, você pode permitir o destino como url de armazenamento de arquivos azure específico no dispositivo firewall.

> [!NOTE]
> Para o Azure SQL and Storage, se você configurar pontos finais de serviço da Rede Virtual em sua sub-rede, então o tráfego entre o Azure-SSIS IR e o Azure SQL na mesma região \ O armazenamento do Azure na mesma região ou região emparelhada será encaminhado diretamente para a rede backbone do Microsoft Azure em vez do seu dispositivo de firewall.

Se você não precisar de capacidade de inspecionar o tráfego de saída do Azure-SSIS IR, você pode simplesmente aplicar a rota para forçar todo o tráfego para a **Próxima Internet**tipo hop :

-   Em um cenário do Azure ExpressRoute, você pode aplicar uma rota 0.0.0.0/0 com o próximo tipo de salto como **Internet** na sub-rede que hospeda o IR Azure-SSIS. 
-   Em um cenário De NVA, você pode modificar a rota 0.0.0.0/0 existente aplicada na sub-rede que hospeda o Azure-SSIS IR do próximo tipo de salto como **aparelho virtual** para **internet**.

![Adicionar uma rota](media/join-azure-ssis-integration-runtime-virtual-network/add-route-for-vnet.png)

> [!NOTE]
> Especificar rota com o próximo tipo de **hop Internet** não significa que todo o tráfego passará pela Internet. Enquanto o endereço de destino é para um dos serviços do Azure, o Azure encaminha o tráfego diretamente para o serviço pela rede backbone do Azure, em vez de direcionar o tráfego para a Internet.

### <a name="set-up-the-resource-group"></a><a name="resource-group"></a>Configurar o grupo de recursos

O Azure-SSIS IR precisa criar determinados recursos de rede no mesmo grupo de recursos que a rede virtual. Esses recursos incluem:
- Um balanceador de carga Azure, com o nome * \<Guid>-azurebatch-cloudserviceloadbalancer*.
- Um endereço IP público do Azure, com o nome * \<Guid>-azurebatch-cloudservicepublicip*.
- Um grupo de segurança de trabalho de rede, com o nome * \<Guid>-azurebatch-cloudservicenetworksecuritygroup*. 

> [!NOTE]
> Agora você pode trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR. Neste cenário, criaremos apenas o balanceador de carga do Azure e o grupo de segurança de rede sob o mesmo grupo de recursos que seus endereços IP públicos estáticos em vez da rede virtual.

Esses recursos serão criados quando o seu IR Azure-SSIS for iniciado. Eles serão excluídos quando o seu Azure-SSIS IR parar. Se você trouxer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR, seus próprios endereços IP públicos estáticos não serão excluídos quando o IR do Azure-SSIS parar. Para evitar que o Azure-SSIS IR pare, não reutilize esses recursos de rede em seus outros recursos.

Certifique-se de que não tem bloqueio de recursos no grupo de recursos/assinatura ao qual a rede virtual/seus endereços IP públicos estáticos pertencem. Se você configurar um bloqueio somente leitura/exclusão, iniciar e interromper o Azure-SSIS IR falhará ou ele deixará de responder.

Certifique-se de que você não tem uma política do Azure que impeça a criação dos seguintes recursos sob o grupo de recursos/assinatura ao qual a rede virtual/seus endereços IP públicos estáticos pertencem: 
- Microsoft.Network/LoadBalancers 
- Microsoft.Network/NetworkSecurityGroups 
- Microsoft.Network/PublicIPAddresses 

Certifique-se de que a cota de recursos da sua assinatura é suficiente para os três recursos de rede acima. Especificamente, para cada IR Azure-SSIS criado em rede virtual, você precisa reservar duas cotas gratuitas para cada um dos três recursos de rede acima. A cota extra será usada quando atualizarmos periodicamente seu IR Azure-SSIS.

### <a name="faq"></a><a name="faq"></a> Perguntas frequentes

- Como posso proteger o endereço IP público exposto no meu Ir Azure-SSIS para conexão de entrada? É possível remover o endereço IP público?
 
  Agora, um endereço IP público será criado automaticamente quando o seu IR Azure-SSIS se juntar a uma rede virtual. Temos um NSG de nível NIC para permitir que apenas os serviços de gerenciamento de lotes do Azure se conectem ao seu IR Azure-SSIS. Você também pode especificar um NSG de nível de sub-rede para proteção de entrada.

  Se você não quiser que nenhum endereço IP público seja exposto, considere [configurar um IR auto-hospedado como proxy para o seu IR Azure-SSIS](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis) em vez de se juntar ao seu IR Azure-SSIS a uma rede virtual, se isso se aplica ao seu cenário.
 
- Posso adicionar o endereço IP público do meu Azure-SSIS IR à lista de permitir do firewall para minhas fontes de dados?

  Agora você pode trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR. Neste caso, você pode adicionar seus endereços IP à lista de permitir o firewall para suas fontes de dados. Você também pode considerar outras opções abaixo para garantir o acesso a dados do seu IR Azure-SSIS, dependendo do seu cenário:

  - Se sua fonte de dados estiver no local, depois de conectar uma rede virtual à sua rede local e se juntar ao SEU IR Azure-SSIS à sub-rede virtual, você poderá adicionar o intervalo de endereçoIP privado dessa sub-rede à lista de permitir o firewall para sua fonte de dados.
  - Se sua fonte de dados for um serviço Azure que suporta pontos finais de serviço de rede virtual, você pode configurar um ponto final de serviço de rede virtual em sua sub-rede virtual e juntar seu IR Azure-SSIS a essa sub-rede. Em seguida, você pode adicionar uma regra de rede virtual com essa sub-rede ao firewall para sua fonte de dados.
  - Se a sua fonte de dados for um serviço de nuvem não-Azure, você pode usar um UDR para direcionar o tráfego de saída do seu IR Azure-SSIS para um Firewall NVA/Azure através de um endereço IP público estático. Em seguida, você pode adicionar o endereço IP público estático do seu Firewall NVA/Azure à lista de permissão do firewall para sua fonte de dados.
  - Se nenhuma das opções acima atender às suas necessidades, considere [configurar um IR auto-hospedado como proxy para o seu Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). Em seguida, você pode adicionar o endereço IP público estático da máquina que hospeda seu RI auto-hospedado à lista de permitir o firewall para sua fonte de dados.

- Por que eu preciso fornecer dois endereços públicos estáticos se eu quero trazer o meu próprio para o Azure-SSIS IR?

  O Azure-SSIS IR é atualizado automaticamente regularmente. Novos nós são criados durante a atualização e os antigos serão excluídos. No entanto, para evitar o tempo de inatividade, os nós antigos não serão excluídos até que os novos estejam prontos. Assim, seu primeiro endereço IP público estático usado pelos nós antigos não pode ser liberado imediatamente e precisamos do seu segundo endereço IP público estático para criar os novos nós.

- Trouxe meus próprios endereços IP públicos estáticos para o Azure-SSIS IR, mas por que ele ainda não pode acessar minhas fontes de dados?

  - Confirme se os dois endereços IP públicos estáticos foram adicionados à lista de permitir o firewall para suas fontes de dados. Cada vez que seu IR Azure-SSIS é atualizado, seu endereço IP público estático é alternado entre os dois trazidos por você. Se você adicionar apenas um deles à lista de permitir, o acesso a dados para o seu Azure-SSIS IR será quebrado após sua atualização.
  - Se sua fonte de dados for um serviço Azure, verifique se você o configurou com pontos finais de serviço de rede virtual. Se esse for o caso, o tráfego do Azure-SSIS IR para sua fonte de dados mudará para usar os endereços IP privados gerenciados pelos serviços do Azure e adicionar seus próprios endereços IP públicos estáticos à lista de permitir o firewall para sua fonte de dados não terá efeito.

## <a name="azure-portal-data-factory-ui"></a>Portal do Azure (Interface do usuário do Data Factory)

Esta seção mostra como juntar um IR Azure-SSIS existente a uma rede virtual (classic ou Azure Resource Manager) usando o portal Azure e a UI data factory. 

Antes de aderir ao seu IR Azure-SSIS para a rede virtual, você precisa configurar adequadamente a rede virtual. Siga as etapas na seção que se aplica ao seu tipo de rede virtual (classicou Azure Resource Manager). Em seguida, siga as etapas da terceira seção para se juntar ao seu IR Azure-SSIS à rede virtual. 

### <a name="configure-an-azure-resource-manager-virtual-network"></a>Configure uma rede virtual do Azure Resource Manager

Use o portal para configurar uma rede virtual do Azure Resource Manager antes de tentar aderir a um IR Azure-SSIS para ele.

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas esses navegadores da Web suportam a interface do usuário da Fábrica de Dados. 

1. Entre no [portal do Azure](https://portal.azure.com). 

1. Selecione **Mais serviços**. Filtre e selecione **Redes virtuais**. 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **Rede virtual**, selecione **Propriedades**. 

1. Selecione o botão de cópia para que a **ID DO RECURSO** copie a ID do recurso da rede virtual para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. No menu esquerdo, selecione **Sub-redes**. Certifique-se de que o número de endereços disponíveis é maior do que os nós do seu IR Azure-SSIS. 

1. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou registre o provedor de lotes Do zure. Se você já tem uma conta do Azure Batch em sua assinatura, sua assinatura está registrada no Azure Batch. (Se você criar o IR do Azure-SSIS no portal do Azure Data Factory,  o provedor do lote do Microsoft Azure é registrado automaticamente para você.) 

   1. No portal Azure, no menu à esquerda, **selecione Assinaturas**. 

   1. Selecione sua assinatura. 

   1. À esquerda, selecione **provedores de**recursos e confirme se **o Microsoft.Batch** é um provedor registrado. 

   ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois. 

### <a name="configure-a-classic-virtual-network"></a>Configure uma rede virtual clássica

Use o portal para configurar uma rede virtual clássica antes de tentar aderir a um IR Azure-SSIS para ele. 

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas esses navegadores da Web suportam a interface do usuário da Fábrica de Dados. 

1. Entre no [portal do Azure](https://portal.azure.com). 

1. Selecione **Mais serviços**. Filtre e selecione **Redes virtuais (clássicas)**. 

1. Filtre e selecione sua rede virtual na lista. 

1. Na página **Rede virtual (clássica)**, selecione **Propriedades**. 

   ![ID do recurso de rede virtual clássica](media/join-azure-ssis-integration-runtime-virtual-network/classic-vnet-resource-id.png)

1. Selecione o botão de cópia para que **ID DO RECURSO** copie a ID do recurso da rede clássica para a área de transferência. Salve a ID da área de transferência no OneNote ou em um arquivo. 

1. No menu esquerdo, selecione **Sub-redes**. Certifique-se de que o número de endereços disponíveis é maior do que os nós do seu IR Azure-SSIS. 

   ![Número de endereços disponíveis na rede virtual](media/join-azure-ssis-integration-runtime-virtual-network/number-of-available-addresses.png)

1. Una **MicrosoftAzureBatch** à função **Colaborador da Máquina Virtual Clássica** para a rede virtual. 

   1. No menu à esquerda, selecione **Controle de acesso (IAM)** e selecione a guia **'Funções'.** 

       !["Controle de Acesso" e botões "Adicionar"](media/join-azure-ssis-integration-runtime-virtual-network/access-control-add.png)

   1. Selecione **Adicionar atribuição de função**.

   1. Na página **'Adicionar função',** para **Função,** selecione **Contribuinte clássico da máquina virtual**. Na caixa **Select,** cole **ddbf3205-c6bd-46ae-8127-60eb93363864**e selecione **Microsoft Azure Batch** na lista de resultados de pesquisa. 

       ![Resultados da pesquisa na página "Adicionar atribuição de função"](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-to-vm-contributor.png)

   1. Selecione **Salvar** para salvar as configurações e fechar a página. 

       ![Salvar as configurações de acesso](media/join-azure-ssis-integration-runtime-virtual-network/save-access-settings.png)

   1. Confirme se você visualiza o **Lote do Microsoft Azure** na lista de colaboradores. 

       ![Confirme o acesso ao lote do Azure](media/join-azure-ssis-integration-runtime-virtual-network/azure-batch-in-list.png)

1. Verifique se o provedor do Lote do Azure está registrado na assinatura do Azure que possui a rede virtual. Ou registre o provedor de lotes Do zure. Se você já tem uma conta do Azure Batch em sua assinatura, sua assinatura está registrada no Azure Batch. (Se você criar o IR do Azure-SSIS no portal do Azure Data Factory,  o provedor do lote do Microsoft Azure é registrado automaticamente para você.) 

   1. No portal Azure, no menu à esquerda, **selecione Assinaturas**. 

   1. Selecione sua assinatura. 

   1. À esquerda, selecione **provedores de**recursos e confirme se **o Microsoft.Batch** é um provedor registrado. 

   ![Confirmação do status "Registrado"](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   Se você não visualizar **Microsoft.Batch** na lista, para registrá-lo [crie uma conta do Lote do Azure vazia](../batch/batch-account-create-portal.md) na sua assinatura. Você pode excluí-lo depois. 

### <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Unir o IR do Azure-SSIS a uma rede virtual

Depois de configurar sua rede virtual do Azure Resource Manager ou uma rede virtual clássica, você pode se juntar ao IR Azure-SSIS à rede virtual:

1. Inicie o Microsoft Edge ou o Google Chrome. Atualmente, apenas esses navegadores da Web suportam a interface do usuário da Fábrica de Dados. 

1. No [portal Azure](https://portal.azure.com), no menu esquerdo, selecione **fábricas de Dados**. Se você não ver **fábricas de dados** no menu, selecione Mais **serviços**e, em seguida, na seção **INTELIGÊNCIA + ANALYTICS,** selecione **fábricas de dados**. 

   ![Lista de fábricas de dados](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. Selecione sua fábrica de dados com o IR Azure-SSIS na lista. Você verá a home page do seu data factory. Selecione o **bloco Autor & Monitor.** Você visualiza a interface do usuário do Data Factory em uma guia separada. 

   ![Página inicial do data factory](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. Na interface de usuário do Data Factory, alterne para a guia **Editar**, selecione **Conexões** e alterne para a guia **Runtimes de integração**. 

   ![Guia "runtimes de integração"](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Se o IR do Azure-SSIS estiver sendo executado, na lista **'Tempos de execução de integração',** na coluna **Ações',** selecione o botão **Parar** para o seu IR Azure-SSIS. Você não pode editar seu Azure-SSIS IR até pará-lo. 

   ![Parar o IR](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. Na lista **'Tempos de execução de integração',** na coluna **Ações,** selecione o botão **Editar** para o IR Do Azure-SSIS. 

   ![Editar o runtime de integração](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. No painel de configuração de tempo de execução de integração, avance nas seções **Configurações Gerais** e **Configurações SQL,** selecionando o botão **Seguinte.** 

1. Na seção **Configurações Avançadas:** 

   1. Selecione o **Select a VNet para o tempo de execução de integração Do Azure-SSIS para participar, permita que o ADF crie certos recursos de rede e, opcionalmente, traga sua própria caixa de seleção de endereços IP públicos estáticos.** 

   1. Para ** Assinatura **, selecione a assinatura do Azure que possui sua rede virtual.

   1. Para ** Local **, o mesmo local do seu runtime de integração é selecionado.

   1. Para **Tipo,** selecione o tipo de sua rede virtual: classic ou Azure Resource Manager. Recomendamos que você selecione uma rede virtual do Azure Resource Manager, pois as redes virtuais clássicas serão depreciadas em breve.

   1. Para ** Nome da VNet **, selecione o nome da sua rede virtual. Ele deve ser o mesmo usado para o servidor De banco de dados SQL do Azure com pontos finais de serviço de rede virtual ou instância gerenciada com ponto final privado para hospedar o SSISDB. Ou deve ser o mesmo conectado à sua rede local. Caso contrário, pode ser qualquer rede virtual para trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR.

   1. Para ** Nome da sub-rede **, selecione o nome da sub-rede da sua rede virtual. Ele deve ser o mesmo usado para o servidor De banco de dados SQL do Azure com pontos finais de serviço de rede virtual para hospedar o SSISDB. Ou deve ser uma sub-rede diferente da usada para sua instância gerenciada com ponto final privado para hospedar o SSISDB. Caso contrário, pode ser qualquer sub-rede para trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR.

   1. Selecione os endereços IP públicos estáticos para a caixa de seleção **de tempo de execução de integração Azure-SSIS** para escolher se você deseja trazer seus próprios endereços IP públicos estáticos para o Azure-SSIS IR, para que você possa permitir no firewall para suas fontes de dados.

      Se você selecionar a caixa de seleção, complete as seguintes etapas.

      1. Para **primeiro endereço IP público estático,** selecione o primeiro endereço IP público estático que [atenda aos requisitos do](#publicIP) seu IR Azure-SSIS. Se você não tiver nenhum, clique em **Criar novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que você possa selecioná-los.
      
      1. Para **segundo endereço IP público estático,** selecione o segundo endereço IP público estático que atende aos [requisitos do](#publicIP) seu IR Azure-SSIS. Se você não tiver nenhum, clique em **Criar novo** link para criar endereços IP públicos estáticos no portal Azure e, em seguida, clique no botão de atualização aqui, para que você possa selecioná-los.

   1. Selecione **validação VNet**. Se a validação for bem sucedida, selecione **Continuar**. 

   ![Configurações avançadas com uma rede virtual](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. Na seção **Resumo,** revise todas as configurações do seu IR Azure-SSIS. Em seguida, **selecione Atualizar**.

1. Inicie seu IR Azure-SSIS selecionando o botão **Iniciar** na coluna **Ações** para o SEU IR Azure-SSIS. Leva cerca de 20 a 30 minutos para iniciar o IR Azure-SSIS que se junta a uma rede virtual. 

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="define-the-variables"></a>Defina as variáveis

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"
```

### <a name="configure-a-virtual-network"></a>Configurar uma rede virtual

Antes de se juntar ao seu IR Azure-SSIS em uma rede virtual, você precisa configurar a rede virtual. Para configurar automaticamente permissões e configurações de rede virtual para que seu IR Azure-SSIS se junte à rede virtual, adicione o seguinte script:

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-an-azure-ssis-ir-and-join-it-to-a-virtual-network"></a>Criar um IR do Azure-SSIS e uni-lo a uma rede virtual

Você pode criar um IR do Azure-SSIS e uni-lo a uma rede virtual ao mesmo tempo. Para obter o script completo e as instruções, consulte [Criar um IR Azure-SSIS](create-azure-ssis-integration-runtime.md#use-azure-powershell-to-create-an-integration-runtime).

### <a name="join-an-existing-azure-ssis-ir-to-a-virtual-network"></a>Unir um IR do Azure-SSIS existente a uma rede virtual

O [artigo Criar um IR Azure-SSIS](create-azure-ssis-integration-runtime.md) mostra como criar um IR Azure-SSIS e se juntar a ele em uma rede virtual no mesmo script. Se você já possui um IR Azure-SSIS, siga estas etapas para aderir a ele à rede virtual: 
1. Interrompa o IR do Azure-SSIS. 
1. Configure o IR do Azure-SSIS para unir à rede virtual. 
1. Inicie o IR do Azure-SSIS. 

### <a name="stop-the-azure-ssis-ir"></a>Interrompa o IR do Azure-SSIS

Você tem que parar o Azure-SSIS IR antes de se juntar a ele em uma rede virtual. Esse comando libera todos os nós e para a cobrança:

```powershell
Stop-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force 
```

### <a name="configure-virtual-network-settings-for-the-azure-ssis-ir-to-join"></a>Configurar as configurações de rede virtual para o IR do Azure-SSIS a unir

Para configurar as configurações da rede virtual que o Azure-SSIS irá aderir, use este script: 

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs.
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
        Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="configure-the-azure-ssis-ir"></a>Configure o IR do Azure-SSIS

Para se juntar ao seu IR Azure-SSIS em uma rede virtual, execute o `Set-AzDataFactoryV2IntegrationRuntime` comando: 

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -VnetId $VnetId `
    -Subnet $SubnetName

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-azure-ssis-ir"></a>Inicie o IR do Azure-SSIS

Para iniciar o Ir Azure-SSIS, execute o seguinte comando: 

```powershell
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

Esse comando demora de 20 a 30 minutos para concluir.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure-SSIS IR, consulte os seguintes artigos: 
- [Azure-SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações conceituais gerais sobre IRs, incluindo o Azure-SSIS IR. 
- [Tutorial: Implante pacotes SSIS para o Azure](tutorial-create-azure-ssis-runtime-portal.md). Este tutorial fornece instruções passo-a-passo para criar seu IR Azure-SSIS. Ele usa o Banco de Dados SQL do Azure para hospedar o catálogo do SSIS. 
- [Criar um IR Azure-SSIS](create-azure-ssis-integration-runtime.md). Este artigo se expande no tutorial. Ele fornece instruções sobre o uso do Banco de Dados SQL do Azure com pontos finais de serviço de rede virtual ou instância gerenciada em uma rede virtual para hospedar o catálogo do SSIS. Ele mostra como juntar seu IR Azure-SSIS a uma rede virtual. 
- [Monitore um IR do Azure-SSIS](monitor-integration-runtime.md#azure-ssis-integration-runtime). Este artigo mostra como obter informações sobre o seu Azure-SSIS IR. Ele fornece descrições de status para as informações devolvidas. 
- [Gerencie um IR do Azure-SSIS](manage-azure-ssis-integration-runtime.md). Este artigo mostra como parar, iniciar ou excluir seu IR Azure-SSIS. Adicionalmente, mostra como escalar horizontalmente o IR do Azure-SSIS adicionando nós.