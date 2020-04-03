---
title: Implantar o Azure Data Explorer em sua rede virtual
description: Saiba como implantar o Azure Data Explorer em sua rede virtual
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b0530ddada68cc9d07753a3b8ab30bff642e26dd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618657"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Implantar o cluster Azure Data Explorer em sua rede virtual

Este artigo explica os recursos presentes quando você implanta um cluster Azure Data Explorer em uma rede virtual personalizada do Azure. Essas informações ajudarão você a implantar um cluster em uma sub-rede em sua Rede Virtual (VNet). Para obter mais informações sobre redes virtuais do Azure, consulte [O que é a Rede Virtual Do Azure?](/azure/virtual-network/virtual-networks-overview)

   ![diagrama vnet](media/vnet-deployment/vnet-diagram.png)

O Azure Data Explorer suporta a implantação de um cluster em uma sub-rede em sua Rede Virtual (VNet). Esse recurso permite que você:

* Impor regras do Grupo [de Segurança de Rede](/azure/virtual-network/security-overview) (NSG) sobre o tráfego de cluster do Azure Data Explorer.
* Conecte sua rede local à sub-rede do azure Data Explorer.
* Proteja suas fontes de conexão de dados[(Event Hub](/azure/event-hubs/event-hubs-about) e [Event Grid)](/azure/event-grid/overview)com [pontos finais de serviço.](/azure/virtual-network/virtual-network-service-endpoints-overview)

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Acesse seu cluster Azure Data Explorer em seu VNet

Você pode acessar o cluster do Azure Data Explorer usando os seguintes endereços IP para cada serviço (serviços de gerenciamento de dados e mecanismos):

* **IP privado**: Usado para acessar o cluster dentro do VNet.
* **IP público**: Usado para acessar o cluster de fora do VNet para gerenciamento e monitoramento, e como endereço de origem para conexões de saída iniciadaa a partir do cluster.

Os seguintes registros de DNS são criados para acessar o serviço: 

* `[clustername].[geo-region].kusto.windows.net`(motor) `ingest-[clustername].[geo-region].kusto.windows.net` (gerenciamento de dados) são mapeados para o IP público para cada serviço. 

* `private-[clustername].[geo-region].kusto.windows.net`(motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (gerenciamento de dados) são mapeados para o IP privado para cada serviço.

## <a name="plan-subnet-size-in-your-vnet"></a>Planeje o tamanho da sub-rede em seu VNet

O tamanho da sub-rede usada para hospedar um cluster Azure Data Explorer não pode ser alterado depois que a sub-rede for implantada. Em seu VNet, o Azure Data Explorer usa um endereço IP privado para cada VM e dois endereços IP privados para os balanceadores de carga interna (gerenciamento de dados e mecanismo). A rede Azure também usa cinco endereços IP para cada sub-rede. O Azure Data Explorer disponibiliza duas VMs para o serviço de gerenciamento de dados. As VMs de serviço do motor são provisionadas por capacidade de escala de configuração do usuário.

O número total de endereços IP:

| Use | Número de endereços |
| --- | --- |
| Serviço do motor | 1 por instância |
| Serviço de gerenciamento de dados | 2 |
| Balanceadores de carga internos | 2 |
| Endereços reservados do Azure | 5 |
| **Total** | **#engine_instances + 9** |

> [!IMPORTANT]
> O tamanho da sub-rede deve ser planejado com antecedência, pois não pode ser alterado após a implantação do Azure Data Explorer. Portanto, a reserva precisava do tamanho da sub-rede em conformidade.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Pontos finais de serviço para conexão ao Azure Data Explorer

[O Azure Service Endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview) permite que você proteja seus recursos multi-inquilinos do Azure para sua rede virtual.
A implantação do cluster Azure Data Explorer em sua sub-rede permite configurar conexões de dados com [o Event Hub](/azure/event-hubs/event-hubs-about) ou event [Grid,](/azure/event-grid/overview) restringindo os recursos subjacentes à sub-rede do Azure Data Explorer.

> [!NOTE]
> Ao usar a configuração eventGrid com [storage](/azure/storage/common/storage-introduction) e [Event Hub], a conta de armazenamento usada na assinatura pode ser bloqueada com pontos finais de serviço para a sub-rede do Azure Data Explorer, permitindo serviços confiáveis da plataforma Azure na [configuração de firewall,](/azure/storage/common/storage-network-security)mas o Event Hub não pode ativar o Service Endpoint, uma vez que não suporta serviços confiáveis da [plataforma Azure.](/azure/event-hubs/event-hubs-service-endpoints)

## <a name="dependencies-for-vnet-deployment"></a>Dependências para implantação de VNet

### <a name="network-security-groups-configuration"></a>Configuração de grupos de segurança de rede

[Os Grupos de Segurança de Rede (NSG)](/azure/virtual-network/security-overview) fornecem a capacidade de controlar o acesso à rede dentro de um VNet. O Azure Data Explorer pode ser acessado usando dois pontos finais: HTTPs (443) e TDS (1433). As seguintes regras do NSG devem ser configuradas para permitir o acesso a esses pontos finais para gerenciamento, monitoramento e operação adequada do seu cluster.

#### <a name="inbound-nsg-configuration"></a>Configuração de NSG de entrada

| **Usar**   | **De**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Gerenciamento  |[Endereços de gerenciamento do ADX](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | sub-rede ADX:443  | TCP  |
| Monitoramento da integridade  | [Endereços de monitoramento de saúde ADX](#health-monitoring-addresses)  | sub-rede ADX:443  | TCP  |
| Comunicação interna do ADX  | Sub-rede ADX: Todas as portas  | Sub-rede ADX:Todas as portas  | Todos  |
| Permitir entrada do balanceador de carga Do Zure (sonda de saúde)  | AzureLoadBalancer  | sub-rede ADX:80.443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configuração de NSG de saída

| **Usar**   | **De**   | **Para**   | **Protocolo**   |
| --- | --- | --- | --- |
| Dependência no Armazenamento do Microsoft Azure  | Sub-rede ADX  | Armazenamento:443  | TCP  |
| Dependência do Lago de Dados Azure  | Sub-rede ADX  | AzureDataLake:443  | TCP  |
| Inserção e monitoramento de serviços do EventHub  | Sub-rede ADX  | EventHub:443,5671  | TCP  |
| Publicar métricas  | Sub-rede ADX  | AzureMonitor:443 | TCP  |
| Download da configuração do Azure Monitor  | Sub-rede ADX  | [Endereços finais da configuração do Azure Monitor](#azure-monitor-configuration-endpoint-addresses):443 | TCP  |
| Diretório Ativo (se aplicável) | Sub-rede ADX | AzureActiveDirectory:443 | TCP |
| Autoridade de certificação | Sub-rede ADX | Internet:80 | TCP |
| Comunicação interna  | Sub-rede ADX  | Sub-rede ADX:Todas as portas  | Todos  |
| Portas usadas para `sql\_request` `http\_request` e plugins  | Sub-rede ADX  | Internet:Personalizado  | TCP  |

### <a name="relevant-ip-addresses"></a>Endereços IP relevantes

#### <a name="azure-data-explorer-management-ip-addresses"></a>Endereços IP de gerenciamento do Azure Data Explorer

| Região | Endereços |
| --- | --- |
| Austrália Central | 20.37.26.134 |
| Austrália Central2 | 20.39.99.177 |
| Leste da Austrália | 40.82.217.84 |
| Sudeste da Austrália | 20.40.161.39 |
| BrasilSul | 191.233.25.183 |
| Canadá Central | 40.82.188.208 |
| Leste do Canadá | 40.80.255.12 |
| Índia Central | 40.81.249.251, 104.211.98.159 |
| Centro dos EUA | 40.67.188.68 |
| EUA Central EUAP | 40.89.56.69 |
| Leste da Ásia | 20.189.74.103 |
| Leste dos EUA | 52.224.146.56 |
| Leste dos EUA 2 | 52.232.230.201 |
| Us2 Euap Oriental | 52.253.226.110 |
| França Central | 40.66.57.91 |
| Sul da França | 40.82.236.24 |
| Leste do Japão | 20.43.89.90 |
| Oeste do Japão | 40.81.184.86 |
| Coreia Central | 40.82.156.149 |
| Sul da Coreia | 40.80.234.9 |
| Centro-Norte dos EUA | 40.81.45.254 |
| Norte da Europa | 52.142.91.221 |
| Norte da África do Sul | 102.133.129.138 |
| África do Sul Ocidental | 102.133.0.97 |
| Centro-Sul dos Estados Unidos | 20.45.3.60 |
| Sudeste Asiático | 40.119.203.252 |
| Sul da Índia | 40.81.72.110, 104.211.224.189 |
| Sul do Reino Unido | 40.81.154.254 |
| Oeste do Reino Unido | 40.81.122.39 |
| Centro-Oeste dos EUA | 52.159.55.120 |
| Europa Ocidental | 51.145.176.215 |
| Oeste da Índia | 40.81.88.112, 104.211.160.120 |
| Oeste dos EUA | 13.64.38.225 |
| Oeste dos EUA 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Endereços de monitoramento de saúde

| Região | Endereços |
| --- | --- |
| Austrália Central | 191.239.64.128 |
| Austrália Central 2 | 191.239.64.128 |
| Leste da Austrália | 191.239.64.128 |
| Sudeste da Austrália | 191.239.160.47 |
| Sul do Brasil | 23.98.145.105 |
| Canadá Central | 168.61.212.201 |
| Leste do Canadá | 168.61.212.201 |
| Índia Central | 23.99.5.162 |
| Centro dos EUA | 168.61.212.201 |
| EUA Central EUAP | 168.61.212.201 |
| Leste da Ásia | 168.63.212.33 |
| Leste dos EUA | 137.116.81.189 |
| Leste dos EUA 2 | 137.116.81.189 |
| Leste dos EUA 2 EUAP | 137.116.81.189 |
| França Central | 23.97.212.5 |
| Sul da França | 23.97.212.5 |
| Leste do Japão | 138.91.19.129 |
| Oeste do Japão | 138.91.19.129 |
| Coreia Central | 138.91.19.129 |
| Sul da Coreia | 138.91.19.129 |
| Centro-Norte dos EUA | 23.96.212.108 |
| Norte da Europa | 191.235.212.69 
| Norte da África do Sul | 104.211.224.189 |
| África do Sul Ocidental | 104.211.224.189 |
| Centro-Sul dos Estados Unidos | 23.98.145.105 |
| Sul da Índia | 23.99.5.162 |
| Sudeste Asiático | 168.63.173.234 |
| Sul do Reino Unido | 23.97.212.5 |
| Oeste do Reino Unido | 23.97.212.5 |
| Centro-Oeste dos EUA | 168.61.212.201 |
| Europa Ocidental | 23.97.212.5 |
| Oeste da Índia | 23.99.5.162 |
| Oeste dos EUA | 23.99.5.162 |
| Oeste dos EUA 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Endereços de ponto final de configuração do Azure Monitor

| Região | Endereços |
| --- | --- |
| Austrália Central | 52.148.86.165 |
| Austrália Central 2 | 52.148.86.165 |
| Leste da Austrália | 52.148.86.165 |
| Austrália Sudeste | 52.148.86.165 |
| Sul do Brasil | 13.68.89.19 |
| Canadá Central | 13.90.43.231 |
| Leste do Canadá | 13.90.43.231 |
| Índia Central | 13.71.25.187 |
| Centro dos EUA | 52.173.95.68 |
| EUA Central EUAP | 13.90.43.231 |
| Ásia Oriental | 13.75.117.221 |
| Leste dos EUA | 13.90.43.231 |
| Leste dos EUA 2 | 13.68.89.19 |    
| Leste dos EUA 2 EUAP | 13.68.89.19 |
| França Central | 52.174.4.112 |
| Sul da França | 52.174.4.112 |
| Leste do Japão | 13.75.117.221 |
| Oeste do Japão | 13.75.117.221 |
| Coreia Central | 13.75.117.221 |
| Sul da Coreia | 13.75.117.221 |
| Centro-Norte dos EUA | 52.162.240.236 |
| Norte da Europa | 52.169.237.246 |
| Norte da África do Sul | 13.71.25.187 |
| África do Sul Ocidental | 13.71.25.187 |
| Centro-Sul dos EUA | 13.84.173.99 |
| Sul da Índia | 13.71.25.187 |
| Sudeste Asiático | 52.148.86.165 |
| Sul do Reino Unido | 52.174.4.112 |
| Oeste do Reino Unido | 52.169.237.246 |
| Centro-Oeste dos EUA | 52.161.31.69 |
| Europa Ocidental | 52.174.4.112 |
| Oeste da Índia | 13.71.25.187 |
| Oeste dos EUA | 40.78.70.148 |
| Oeste dos EUA 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>Configuração do ExpressRoute

Use o ExpressRoute para conectar na rede de instalações à Rede Virtual Azure. Uma configuração comum é anunciar a rota padrão (0.0.0.0/0) através da sessão BGP (Border Gateway Protocol). Isso força que o tráfego que sai da Rede Virtual seja encaminhado para a rede de premissas do cliente que pode diminuir o tráfego, fazendo com que os fluxos de saída se rompam. Para superar esse padrão, [a Rota Definida pelo Usuário (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) pode ser configurada e o próximo salto será a *Internet*. Uma vez que o UDR tem precedência sobre o BGP, o tráfego será destinado à Internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Protegendo o tráfego de saída com firewall

Se você quiser proteger o tráfego de saída usando [o Firewall Azure](/azure/firewall/overview) ou qualquer dispositivo virtual para limitar nomes de domínio, os seguintes Nomes de Domínio Totalmente Qualificados (FQDN) devem ser permitidos no firewall.

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

Você também precisa definir a [tabela de rotas](/azure/virtual-network/virtual-networks-udr-overview) na sub-rede com os [endereços de gerenciamento](#azure-data-explorer-management-ip-addresses) e [endereços de monitoramento de saúde](#health-monitoring-addresses) com a próxima *Internet* de salto para evitar problemas de rotas assimétricas.

Por exemplo, para a região **oeste dos EUA,** as seguintes UDRs devem ser definidas:

| Nome | Prefixo de Endereço | Próximo Salto |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Implantar o cluster Azure Data Explorer em seu VNet usando um modelo do Azure Resource Manager

Para implantar o cluster Azure Data Explorer em sua rede virtual, use o [cluster Deploy Azure Data Explorer no](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) modelo do VNet Azure Resource Manager.

Esse modelo cria o cluster, a rede virtual, a sub-rede, o grupo de segurança da rede e os endereços IP públicos.
