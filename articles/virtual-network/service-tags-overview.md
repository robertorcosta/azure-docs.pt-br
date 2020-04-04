---
title: Visão geral das tags de serviço do Azure
titlesuffix: Azure Virtual Network
description: Saiba mais sobre as tags de serviço. As tags de serviço ajudam a minimizar a complexidade da criação de regras de segurança.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 7da20a0bf87b33f05ea7f1d457157c5b7ee2ec7b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631522"
---
# <a name="virtual-network-service-tags"></a>Tags de serviço de rede virtual
<a name="network-service-tags"></a>

Uma tag de serviço representa um grupo de prefixos de endereço IP de um determinado serviço Azure. A Microsoft gerencia os prefixos de endereço englobados pela tag de serviço e atualiza automaticamente a tag de serviço à medida que os endereços mudam, minimizando a complexidade das atualizações frequentes às regras de segurança da rede.

Você pode usar tags de serviço para definir controles de acesso à rede em [grupos](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) de segurança de rede ou [Firewall Azure](https://docs.microsoft.com/azure/firewall/service-tags). Use tags de serviço no lugar de endereços IP específicos quando criar regras de segurança. Ao especificar o nome da tag de serviço (por exemplo, **ApiManagement**) no campo de *origem* ou *destino* apropriado de uma regra, você pode permitir ou negar o tráfego para o serviço correspondente.

Você pode usar tags de serviço para alcançar o isolamento da rede e proteger seus recursos do Azure da Internet em geral, ao mesmo tempo em que acessa serviços do Azure que têm pontos finais públicos. Crie regras do grupo de segurança de rede de entrada/saída para negar tráfego de/para **internet** e permitir que o tráfego entre/e o **AzureCloud** ou [outras tags](#available-service-tags) de serviço disponíveis de serviços específicos do Azure.

## <a name="available-service-tags"></a>Tags de serviço disponíveis
A tabela a seguir inclui todas as tags de serviço disponíveis para uso nas regras [do grupo de segurança da rede.](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)

As colunas indicam se a tag:

- É adequado para regras que cobrem tráfego de entrada ou saída.
- Suporta escopo [regional.](https://azure.microsoft.com/regions)
- É utilizável nas regras [do Firewall Do Azure.](https://docs.microsoft.com/azure/firewall/service-tags)

Por padrão, as tags de serviço refletem as faixas para toda a nuvem. Algumas tags de serviço também permitem um controle mais granular restringindo as faixas ip correspondentes a uma região especificada. Por exemplo, a tag de serviço **Armazenamento** representa o Armazenamento Azure para toda a nuvem, mas o **Storage.WestUS** reduz o alcance apenas para o endereço IP de armazenamento da região do WestUS. A tabela a seguir indica se cada tag de serviço suporta esse escopo regional.  

| Marca | Finalidade | Pode usar entrada ou saída? | Pode ser regional? | Pode usar com o Firewall Do Azure? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Actiongroup** | Grupo de Ação. | Entrada | Não | Não |
| **ApiManagement** | Tráfego gerencial para implantações dedicadas ao gerenciamento de API do Azure. <br/><br/>*Nota:* Esta tag representa o ponto final de serviço de gerenciamento de API do Azure para plano de controle por região. Isso permite que os clientes realizem operações de gerenciamento nas APIs, Operações, Políticas, Valores Nomeados configurados no serviço de gerenciamento de API.  | Entrada | Sim | Sim |
| **ApplicationInsightsDisponibilidade** | Disponibilidade de insights de aplicativos. | Entrada | Não | Não |
| **Configuração do aplicativo** | Configuração do aplicativo. | Saída | Não | Não |
| **AppService**    | Serviço de Aplicativo do Azure. Esta tag é recomendada para regras de segurança de saída para front ends de aplicativos web. | Saída | Sim | Sim |
| **AppServiceManagement** | Tráfego gerencial para implantações dedicadas ao Ambiente de Serviço de Aplicativos. | Ambos | Não | Sim |
| **AzureActiveDirectory** | Azure Active Directory. | Saída | Não | Sim |
| **AzureActiveDirectoryDomainServices** | Tráfego de gerenciamento para implantações dedicadas aos Serviços de Domínio do Diretório Ativo do Azure. | Ambos | Não | Sim |
| **Proteção contra ameaças avançadas do Azure** | Proteção avançada de ameaças do Azure. | Saída | Não | Não |
| **AzureBackup** |Backup Azure.<br/><br/>*Nota:* Esta tag tem uma dependência das tags **Armazenamento** e **AzureActiveDirectory.** | Saída | Não | Sim |
| **AzureBotService** | Serviço de Bot do Azure. | Saída | Não | Não |
| **AzureCloud** | Todos os [endereços IP públicos do datacenter](https://www.microsoft.com/download/details.aspx?id=56519). | Saída | Sim | Sim |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Esta tag ou os endereços IP cobertos por esta tag podem ser usados para conceder aos indexadores acesso seguro às fontes de dados. Consulte a [documentação de conexão do indexador](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) para obter mais detalhes. <br/><br/> *Nota*: O IP do serviço de pesquisa não está incluído na lista de faixas IP para esta tag de serviço e **também precisa ser adicionado** ao firewall IP de fontes de dados. | Entrada | Não | Não |
| **AzureConnectors** | Conectores azure Logic Apps para conexões de teste/back-end. | Entrada | Sim | Sim |
| **Registro de contêineres do Azure** | Registro de Contêineres Azure. | Saída | Sim | Sim |
| **AzureCosmosDB** | Azure Cosmos DB. | Saída | Sim | Sim |
| **AzureDatabricks** | Azure Databricks. | Ambos | Não | Não |
| **AzureDataExplorerManagement** | Gerenciamento do Explorador de Dados do Azure. | Entrada | Não | Não |
| **Lago AzureData** | Azure Data Lake Storage Gen1. | Saída | Não | Sim |
| **AzureDevSpaces** | Espaços Azure Dev. | Saída | Não | Não |
| **AzureEventGrid** | Grade de Eventos do Azure. <br/><br/>*Nota:* Esta tag abrange os pontos finais da Azure Event Grid no Centro-Sul dos EUA, Leste dos EUA, Leste dos EUA 2, US West 2 e US Central apenas. | Ambos | Não | Não |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Porta da Frente Azure. | Ambos | Não | Não |
| **AzureInformationProtection** | Proteção de Informações Azure.<br/><br/>*Nota:* Esta tag tem uma dependência das tags **AzureActiveDirectory,** **AzureFrontDoor.Frontend** e **AzureFrontDoor.FirstParty.** | Saída | Não | Não |
| **AzureIoTHub** | Azure IoT Hub. | Saída | Não | Não |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Nota:* Esta tag tem uma dependência da tag **AzureActiveDirectory.** | Saída | Sim | Sim |
| **AzureLoadBalancer** | O balanceador de carga de infra-estrutura do Azure. A tag se traduz para o [endereço IP virtual do host](security-overview.md#azure-platform-considerations) (168.63.129.16) de onde as sondas de saúde do Azure se originam. Isso não inclui tráfego para o recurso Azure Load Balancer. Se você não estiver usando o Azure Load Balancer, você pode anular esta regra. | Ambos | Não | Não |
| **AzureMachineLearning** | Azure Machine Learning. | Ambos | Não | Sim |
| **AzureMonitor** | Análise de log, insights de aplicativos, AzMon e métricas personalizadas (pontos finais GiG).<br/><br/>*Nota:* Para o Log Analytics, essa tag tem uma dependência da tag **Armazenamento.** | Saída | Não | Sim |
| **Conjuntos AzureOpenData** | Conjuntos de dados abertos do Azure.<br/><br/>*Nota:* Esta tag tem uma dependência da tag **AzureFrontDoor.Frontend** e **Armazenamento.** | Saída | Não | Não |
| **AzurePlatformDNS** | O serviço básico de Infra-estrutura (padrão) DNS.<br/><br>Você pode usar esta tag para desativar o DNS padrão. Tenha cuidado ao usar esta etiqueta. Recomendamos que você leia [as considerações da plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Também recomendamos que você realize testes antes de usar esta tag. | Saída | Não | Não |
| **AzurePlatformIMDS** | O Azure Instance Metadata Service (IMDS), que é um serviço básico de infra-estrutura.<br/><br/>Você pode usar esta tag para desativar o IMDS padrão. Tenha cuidado ao usar esta etiqueta. Recomendamos que você leia [as considerações da plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) Também recomendamos que você realize testes antes de usar esta tag. | Saída | Não | Não |
| **AzurePlatformLKM** | Licenciamento do Windows ou serviço de gerenciamento de chaves.<br/><br/>Você pode usar esta tag para desativar os padrões de licenciamento. Tenha cuidado ao usar esta etiqueta. Recomendamos que você leia [as considerações da plataforma Azure.](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)  Também recomendamos que você realize testes antes de usar esta tag. | Saída | Não | Não |
| **AzureResourceManager** | Gerente de Recursos do Azure. | Saída | Não | Não |
| **AzureSignalR** | Azure SignalR. | Saída | Não | Não |
| **AzureSiteRecovery** | Recuperação do Site Do Azul.<br/><br/>*Nota:* Esta tag tem uma dependência das tags **AzureActiveDirectory,** **AzureKeyVault,** **EventHub,****GuestAndHybridManagement** e **Storage.** | Saída | Não | Não |
| **AzureTrafficManager** | O Azure Traffic Manager testa endereços IP.<br/><br/>Para obter mais informações sobre endereços IP do teste do Traffic Manager, consulte [FAQ do Gerenciador de tráfego do Azure](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Entrada | Não | Sim |  
| **Gerenciamento de batchnode** | Tráfego de gerenciamento para implantações dedicadas ao Azure Batch. | Ambos | Não | Sim |
| **CognitiveServicesManagement** | O endereço varia para tráfego para serviços cognitivos Azure. | Ambos | Não | Não |
| **Datafactory**  | Fábrica de dados do Azure | Ambos | Não | Não |
| **DataFactoryManagement** | Tráfego gerencial para a Fábrica de Dados Azure. | Saída | Não | Não |
| **Dinâmica365ForMarketingEmail** | O endereço varia para o serviço de e-mail de marketing da Dynamics 365. | Saída | Sim | Não |
| **ElasticafD** | Porta da Frente Do Azure Elástico. | Ambos | Não | Não |
| **EventHub** | Hubs de Eventos Azure. | Saída | Sim | Sim |
| **GatewayManager** | Tráfego de gerenciamento para implantações dedicadas ao Azure VPN Gateway e ao Application Gateway. | Entrada | Não | Não |
| **GuestandHybridManagement** | Automação do Azure e Configuração de Hóspedes. | Saída | Não | Sim |
| **HDInsight** | Azure HDInsight. | Entrada | Sim | Não |
| **Internet** | O espaço de endereço IP que está fora da rede virtual e acessível pela internet pública.<br/><br/>A gama de endereços inclui o espaço de endereço IP público de propriedade do [Azure.](https://www.microsoft.com/download/details.aspx?id=41653) | Ambos | Não | Não |
| **LogicApps** | Aplicativos lógicos. | Ambos | Não | Não |
| **LogicAppsManagement** | Tráfego de gerenciamento para aplicativos lógicos. | Entrada | Não | Não |
| **MicrosoftCloudAppSegurança** | Microsoft Cloud App Security. | Saída | Não | Não |
| **MicrosoftContainerRegistry** | Registro de contêiner para imagens de contêiner da Microsoft. <br/><br/>*Nota:* Esta tag tem uma dependência da tag **AzureFrontDoor.FirstParty.** | Saída | Sim | Sim |
| **PowerQueryOnline** | Consulta de energia online. | Ambos | Não | Não |
| **Servicebus** | Tráfego de ônibus de serviço do Azure que usa o nível de serviço Premium. | Saída | Sim | Sim |
| **ServiceFabric** | Tecido de serviço Azure.<br/><br/>*Nota:* Esta tag representa o ponto final de serviço service Fabric para plano de controle por região. Isso permite que os clientes realizem operações de gerenciamento para seus clusters de Malha de Serviço a partir de seu VNET (endpoint, por exemplo. westus.servicefabric.azure.com https://) | Ambos | Não | Não |
| **Sql** | Banco de dados Azure SQL, Banco de Dados Azure para MySQL, Banco de Dados Azure para PostgreSQL e Azure SQL Data Warehouse.<br/><br/>*Nota:* Esta tag representa o serviço, mas não instâncias específicas do serviço. Por exemplo, a marca representa o serviço Banco de Dados SQL do Azure, mas não um banco de dados ou servidor SQL específico. Esta tag não se aplica à instância gerenciada pelo SQL. | Saída | Sim | Sim |
| **SqlManagement** | Tráfego de gerenciamento para implantações dedicadas ao SQL. | Ambos | Não | Sim |
| **Armazenamento** | Armazenamento do Microsoft Azure. <br/><br/>*Nota:* Esta tag representa o serviço, mas não instâncias específicas do serviço. Por exemplo, a marca representa o serviço Armazenamento do Azure, mas não uma conta do Armazenamento do Azure específica. | Saída | Sim | Sim |
| **Serviço de sincronização de armazenamento** | Serviço de sincronização de armazenamento. | Ambos | Não | Não |
| **WindowsVirtualDesktop** | Windows Virtual Desktop. | Ambos | Não | Não |
| **Rede Virtual** | O espaço de endereço de rede virtual (todas as faixas de endereçoIP definidas para a rede virtual), todos os espaços de endereços on-premises conectados, redes virtuais [peered,](virtual-network-peering-overview.md) redes virtuais conectadas a um gateway de [rede virtual,](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)o [endereço IP virtual do host](security-overview.md#azure-platform-considerations)e prefixos de endereço usados em [rotas definidas pelo usuário.](virtual-networks-udr-overview.md) Essa tag também pode conter rotas padrão. | Ambos | Não | Não |

>[!NOTE]
>No modelo clássico de implantação (antes do Azure Resource Manager), um subconjunto das tags listadas na tabela anterior são suportados. Estas tags são escritas de forma diferente:
>
>| Ortografia clássica | Tag equivalente do Gerenciador de recursos |
>|---|---|
>| BALANCEADORDECARGA_AZURE | AzureLoadBalancer |
>| INTERNET | Internet |
>| REDE_VIRTUAL | VirtualNetwork |

> [!NOTE]
> As tags de serviço dos serviços do Azure denotam os prefixos de endereço da nuvem específica que está sendo usada. Por exemplo, as faixas IP subjacentes que correspondem ao valor da tag **Sql** na nuvem Azure Public serão diferentes das faixas subjacentes na nuvem do Azure China.

> [!NOTE]
> Se você implementar um [ponto de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md) em um serviço, como o Armazenamento do Azure ou o Banco de Dados SQL do Azure, o Azure adicionará uma [rota](virtual-networks-udr-overview.md#optional-default-routes) para uma sub-rede de rede virtual para o serviço. Os prefixos de endereço na rota são os mesmos prefixos de endereço, ou faixas CIDR, como os da tag de serviço correspondente.

## <a name="service-tags-on-premises"></a>Tags de serviço no local  
Você pode obter as informações atuais de tag de serviço e alcance para incluir como parte de suas configurações de firewall no local. Essas informações são a lista de pontos em tempo atual das faixas de IP que correspondem a cada tag de serviço. Você pode obter as informações programáticamente ou através de um download de arquivo JSON, conforme descrito nas seções a seguir.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>Use a API de detecção de marca de serviço (visualização pública)
Você pode recuperar programáticamente a lista atual de tags de serviço juntamente com detalhes da gama de endereços IP:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [CLI do Azure](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Enquanto estiver em visualização pública, a API do Discovery pode retornar informações menos atuais do que as informações devolvidas pelos downloads do JSON. (Veja a próxima seção.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Descubra tags de serviço usando arquivos JSON para download 
Você pode baixar arquivos JSON que contêm a lista atual de tags de serviço, juntamente com detalhes da gama de endereços IP. Estas listas são atualizadas e publicadas semanalmente. Os locais para cada nuvem são:

- [Público do Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Governo dos EUA para Azure](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Alemanha](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Um subconjunto dessas informações foi publicado em arquivos XML para [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)e [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). Esses downloads xml serão preteridos até 30 de junho de 2020 e não estarão mais disponíveis após essa data. Você deve migrar para usar os downloads de arquivos Discovery API ou JSON, conforme descrito nas seções anteriores.

### <a name="tips"></a>Dicas 
- Você pode detectar atualizações de uma publicação para outra, observando valores de número de *alterações* aumentados no arquivo JSON. Cada subseção (por exemplo, **Storage.WestUS**) tem seu próprio *changeNumber* que é incrementado à medida que as alterações ocorrem. O nível superior da *alteração do arquivoNúmero* é incrementado quando qualquer uma das subseções é alterada.
- Para exemplos de como analisar as informações da tag de serviço (por exemplo, obter todas as faixas de endereço para armazenamento no WestUS), consulte a documentação do [PowerShell do Service Tag Discovery.](https://aka.ms/discoveryapi_powershell)

## <a name="next-steps"></a>Próximas etapas
- Aprenda a [criar um grupo de segurança de rede](tutorial-filter-network-traffic.md).
