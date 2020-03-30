---
title: Esquema de análise de tráfego do Azure | Microsoft Docs
description: Entenda o esquema do Traffic Analytics para analisar os registros de fluxo do grupo de segurança da rede Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666368"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Esquema e agregação de dados no Traffic Analytics

Análise de Tráfego é uma solução baseada em nuvem, que oferece visibilidade sobre atividade de usuário e aplicativo nas redes em nuvem. Análise de Tráfego analisa os logs de fluxo de grupo de segurança da rede (NSG) do Observador de Rede para fornecer informações sobre o fluxo de tráfego em sua nuvem do Azure. Com a Análise de Tráfego, você pode:

- Visualizar a atividade de rede nas assinaturas do Azure e identificar pontos de acesso.
- Identificar ameaças à segurança e proteger sua rede com informações como portas abertas, aplicativos tentando acesso à internet e máquinas virtuais (VM) conectando-se a redes não autorizadas.
- Compreender os padrões de fluxo de tráfego entre regiões do Azure e a internet para otimizar a implantação de rede para desempenho e capacidade.
- Identificar problemas de configuração de rede originando conexões com falha em sua rede.
- Conheça o uso da rede em bytes, pacotes ou fluxos.

### <a name="data-aggregation"></a>Agregação de dados

1. Todos os registros de fluxo em um NSG entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t" são capturados em intervalos de um minuto na conta de armazenamento como blobs antes de serem processados pelo Traffic Analytics.
2. O intervalo de processamento padrão do Traffic Analytics é de 60 minutos. Isso significa que a cada 60 mins o Traffic Analytics escolhe bolhas do armazenamento para agregação. Se o intervalo de processamento escolhido for de 10 minutos, o Traffic Analytics escolherá blobs da conta de armazenamento após cada 10 minutos.
3. Fluxos que possuem o mesmo IP de origem, IP de destino, porta de destino, nome NSG, regra NSG, direção de fluxo e protocolo de camada de transporte (TCP ou UDP) (Nota: A porta de origem está excluída para agregação) são incorporadas a um único fluxo pelo Traffic Analytics
4. Este único registro é decorado (Detalhes na seção abaixo) e ingerido no Log Analytics pela Traffic Analytics.Este processo pode levar até 1 hora no máximo.
5. FlowStartTime_t campo indica a primeira ocorrência de tal fluxo agregado (mesmo quatro tuplos) no intervalo de processamento do registro de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
6. Para qualquer recurso em TA, os fluxos indicados na ida e volta são fluxos totais vistos pelo NSG, mas no Log Analytics o usuário verá apenas o registro único e reduzido. Para ver todos os fluxos, use o campo blob_id, que pode ser referenciado a partir do Armazenamento. A contagem total de fluxo para esse registro corresponderá aos fluxos individuais vistos na bolha.

A consulta abaixo ajuda você a olhar todos os registros de fluxo de on-premises nos últimos 30 dias.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Para visualizar o caminho blob para os fluxos na consulta acima mencionada, use a consulta abaixo:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

A consulta acima constrói uma URL para acessar a bolha diretamente. A URL com porta-espaços está abaixo:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Campos usados no esquema de Análise de Tráfego
  > [!IMPORTANT]
  > O Esquema de Análise de Tráfego foi atualizado em 22 de agosto de 2019. O novo esquema fornece IPs de origem e destino removendo separadamente a necessidade de analisar o campo FlowDirection tornando as consultas mais simples. </br>
  > FASchemaVersion_s atualizada de 1 para 2. </br>
  > Campos depreciados: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Novos campos: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Os campos deprecados estarão disponíveis até o dia 22 de novembro de 2019.

O Traffic Analytics é construído em cima do Log Analytics, para que você possa executar consultas personalizadas sobre dados decorados pelo Traffic Analytics e definir alertas no mesmo.

Listados abaixo estão os campos no esquema e o que eles significam

| Campo | Formatar | Comentários |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela de dados do Traffic Analytics
| SubType_s | FlowLog | Subtipo para os registros de fluxo. Use apenas "FlowLog", outros valores de SubType_s são para o funcionamento interno do produto |
| FASchemaVersion_s |   2   | Versão do esquema. Não reflete a versão do NSG Flow Log |
| TimeProcessed_t   | Data e hora em UTC  | Tempo em que o Traffic Analytics processou os logs de fluxo bruto da conta de armazenamento |
| FlowIntervalStartTime_t | Data e hora em UTC |  Tempo de início do intervalo de processamento do log de fluxo. Este é o tempo a partir do qual o intervalo de fluxo é medido |
| FlowIntervalEndTime_t | Data e hora em UTC | Tempo de término do intervalo de processamento do log de fluxo |
| FlowStartTime_t | Data e hora em UTC |  Primeira ocorrência do fluxo (que será agregado) no intervalo de processamento do registro de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Esse fluxo é agregado com base na lógica de agregação |
| FlowEndTime_t | Data e hora em UTC | Última ocorrência do fluxo (que será agregado) no intervalo de processamento do registro de fluxo entre "FlowIntervalStartTime_t" e "FlowIntervalEndTime_t". Em termos de registro de fluxo v2, este campo contém o tempo em que o último fluxo com o mesmo quatro-tuple iniciado (marcado como "B" no registro de fluxo bruto) |
| FlowType_s |  * Intravnet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * Público externo <br> * Fluxo malicioso <br> * Privado Desconhecido <br> * Desconhecido | Definição em notas abaixo da tabela |
| SrcIP_s | Endereço IP de origem | Estará em branco no caso de fluxos AzurePublic e ExternalPublic |
| DestIP_s | Endereço IP de destino | Estará em branco no caso de fluxos AzurePublic e ExternalPublic |
| VMIP_s | IP da VM | Usado para fluxos AzurePublic e ExternalPublic |
| PublicIP_s | Endereços IP públicos | Usado para fluxos AzurePublic e ExternalPublic |
| DestPort_d | Porta de destino | Porto em que o tráfego está chegando |
| L4Protocol_s  | * T <br> * U  | Protocolo de Transporte. T = TCP <br> U = UDP |
| L7Protocol_s  | Nome do protocolo | Derivado do porto de destino |
| FlowDirection_s | * I = Entrada<br> * O = Saída | Direção do fluxo de entrada/saída do NSG conforme o registro de fluxo |
| FlowStatus_s  | * A = Permitido pela Regra NSG <br> * D = Negado pela Regra NSG  | Status do fluxo permitido/bloqueado pelo NSG conforme o registro de fluxo |
| NSGList_s | \<> NSG_NAME \/<>\/<>RESOURCEGROUP_NAME RESOURCEGROUP_NAME RESOURCEGROUP_NAME RESOURCEGROUP_NAME | Grupo de Segurança de Rede (NSG) associado ao fluxo |
| NSGRules_s | \<Valor do índice \| \<0)>\| \<NSG_RULENAME NSG_RULENAME \| \<direção \| \<do fluxo de fluxo>>>fluxo de fluxofluxocontagem processadapor regra> |  Regra do NSG que permitiu ou negou esse fluxo |
| NSGRule_s | NSG_RULENAME |  Regra do NSG que permitiu ou negou esse fluxo |
| NSGRuleType_s | * Usuário definido * Padrão |   O tipo de Regra NSG usada pelo fluxo |
| MACAddress_s | Endereço MAC | Endereço MAC da NIC em que o fluxo foi capturado |
| Subscription_s | A assinatura da rede virtual do Azure/ interface de rede/ máquina virtual é preenchida neste campo | Aplicável apenas para flowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate flow types (tipos de fluxo onde apenas um lado é azul) |
| Subscription1_s | ID da assinatura | ID de assinatura de rede virtual/ interface de rede/ máquina virtual à qual o IP de origem no fluxo pertence |
| Subscription2_s | ID da assinatura | ID de assinatura de rede virtual/ interface de rede/ máquina virtual à qual o IP de destino no fluxo pertence |
| Region_s | Região azure de rede virtual/ interface de rede/ máquina virtual à qual o IP no fluxo pertence | Aplicável apenas para flowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow e UnknownPrivate flow types (tipos de fluxo onde apenas um lado é azul) |
| Region1_s | Região do Azure | Região azure de rede virtual/ interface de rede/ máquina virtual à qual o IP de origem no fluxo pertence |
| Region2_s | Região do Azure | Região azure de rede virtual à qual o IP de destino no fluxo pertence |
| NIC_s | \<resourcegroup_Name \/ \<>> |  NIC associado ao envio ou recebimento do tráfego da VM |
| NIC1_s | <resourcegroup_Name\<>/> | NIC associado ao IP de origem no fluxo |
| NIC2_s | <resourcegroup_Name\<>/> | NIC associado ao IP de destino no fluxo |
| VM_s | > resourcegroup_Name \/ \<> de interface de>de <> | Máquina Virtual associada à interface de rede NIC_s |
| VM1_s | <>\<resourcegroup_Name/> de máquina virtual | Máquina Virtual associada ao IP de origem no fluxo |
| VM2_s | <>\<resourcegroup_Name/> de máquina virtual | Máquina Virtual associada ao IP de destino no fluxo |
| Subnet_s | <ResourceGroup_Name>/<\<> VNET_Name>/ sub-rede de <>/<> | Sub-rede associada ao NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<\<> VNET_Name>/ sub-rede de <>/<> | Sub-rede associada ao IP de origem no fluxo |
| Subnet2_s | <ResourceGroup_Name>/<\<> VNET_Name>/ sub-rede de <>/<>    | Sub-rede associada ao IP destino no fluxo |
| ApplicationGateway1_s | \<> de assinatura/\<ResourceGroupName\<>/ ApplicationGatewayName> | Gateway de aplicativo associado ao IP de origem no fluxo |
| ApplicationGateway2_s | \<> de assinatura/\<ResourceGroupName\<>/ ApplicationGatewayName> | Gateway de aplicativo associado ao IP de destino no fluxo |
| LoadBalancer1_s | \<> de assinatura/\<ResourceGroupName\<>/ LoadBalancerNome> | Balanceador de carga associado ao IP de origem no fluxo |
| LoadBalancer2_s | \<> de assinatura/\<ResourceGroupName\<>/ LoadBalancerNome> | Balanceador de carga associado ao IP de destino no fluxo |
| LocalNetworkGateway1_s | \<de>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado ao IP de origem no fluxo |
| LocalNetworkGateway2_s | \<de>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Gateway de rede local associado ao IP destino no fluxo |
| ConnectionType_s | Os valores possíveis são VNetPeering, VpnGateway e ExpressRoute |    Tipo de Conexão |
| ConnectionName_s | \<> nome de\<nome>\<de>/ResourceGroupde de>/Recurso> | Nome da conexão. Para flowtype P2S, isso será <gateway name>formatado como _<VPN Client IP> |
| ConnectingVNets_s | Lista separada do espaço de nomes de rede virtuais | No caso de topologia hub e spoke, as redes virtuais do hub serão preenchidas aqui |
| Country_s | Código de país de duas letras (ISO 3166-1 alfa-2) | Preenchido para o tipo de fluxo ExternalPublic. Todos os endereços IP no campo PublicIPs_s compartilharão o mesmo código de país |
| AzureRegion_s | Localizações da região do Azure | Preenchido para o tipo de fluxo AzurePublic. Todos os endereços IP no campo PublicIPs_s compartilharão a região do Azure |
| AllowedInFlows_d | | Contagem de fluxos de entrada que eram permitidos. Isso representa o número de fluxos que compartilharam a mesma entrada de quatro tuplos na interface de rede na qual o fluxo foi capturado |
| DeniedInFlows_d |  | Contagem de fluxos de entrada que foram negados. (Entrada na interface de rede na qual o fluxo foi capturado) |
| AllowedOutFlows_d | | Contagem de fluxos de saída que foram permitidos (Saída para a interface de rede na qual o fluxo foi capturado) |
| DeniedOutFlows_d  | | Contagem de fluxos de saída que foram negados (Saída para a interface de rede na qual o fluxo foi capturado) |
| FlowCount_d | Preterido. Fluxos totais que correspondem ao mesmo quatro tuplo. No caso dos tipos de fluxo ExternalPublic e AzurePublic, a contagem incluirá os fluxos de vários endereços PublicIP também.
| InboundPackets_d | Pacotes recebidos como capturados na interface de rede onde a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo NSG |
| OutboundPackets_d  | Pacotes enviados como capturados na interface de rede onde a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo NSG |
| InboundBytes_d |  Bytes recebidos como capturados na interface de rede onde a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo NSG |
| OutboundBytes_d | Bytes enviados como capturados na interface de rede onde a regra NSG foi aplicada | Isso é preenchido apenas para a versão 2 do esquema de log de fluxo NSG |
| CompletedFlows_d  |  | Isso é preenchido com valor não-zero apenas para a versão 2 do esquema de log de fluxo NSG |
| PublicIPs_s | <PUBLIC_IP \| \<PUBLIC_IP \| \<PUBLIC_IP \| \<INBOUND_BYTES \| \<>\| \< \| \<>INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT>PUBLIC_IP PUBLIC_IP PUBLIC_IP PUBLIC_IP PUBLIC_IP PUBLIC_IP PUBLIC_IP OUTBOUND_BYTES PUBLIC_IP PUBLIC_IP PUBLIC_IP OUTBOUND_BYTES OUTBOUND_BYTES PUBLIC_IP PUBLIC_IP PUBLIC_IP OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES OUTBOUND_BYTES> PUBLIC_IP PUBLIC_IP | Entradas separadas por barras |
| SrcPublicIPs_s | > INBOUND_BYTES \| \<>\| \< \| \< \| \< \<>\|INBOUND_PACKETS>OUTBOUND_PACKETS>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT>SOURCE_PUBLIC_IP <OUTBOUND_BYTES \| \< | Entradas separadas por barras |
| DestPublicIPs_s | > INBOUND_BYTES \| \<>\| \< \| \< \| \< \| \< \|>INBOUND_PACKETS OUTBOUND_PACKETS>FLOW_ENDED_COUNT>FLOW_STARTED_COUNT>DESTINATION_PUBLIC_IP <>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>\<> | Entradas separadas por barras |

### <a name="notes"></a>Observações

1. No caso de fluxos AzurePublic e ExternalPublic, o IP do Azure VM de propriedade do cliente é preenchido em VMIP_s campo, enquanto os endereços IP públicos estão sendo preenchidos no campo PublicIPs_s. Para esses dois tipos de fluxo, devemos usar VMIP_s e PublicIPs_s em vez de campos de SrcIP_s e DestIP_s. Para endereços AzurePublic e ExternalPublicIP, agregamos ainda mais, de modo que o número de registros ingeridos no espaço de trabalho de análise de log do cliente é mínimo. (Este campo será preterido em breve e devemos usar SrcIP_ e DestIP_s dependendo se a VM azul foi a fonte ou o destino no fluxo)
1. Detalhes para tipos de fluxo: Com base nos endereços IP envolvidos no fluxo, categorizamos os fluxos para os seguintes tipos de fluxo:
1. IntraVNet – Ambos os endereços IP no fluxo residem na mesma Rede Virtual Azure.
1. InterVNet - Endereços IP no fluxo residem nas duas redes virtuais diferentes do Azure.
1. S2S – (Site para site) Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à rede de clientes (Site) conectada à Rede Virtual Azure através do gateway VPN ou do Express Route.
1. P2S - (Point To Site) Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à rede de clientes (Site) conectada à Rede Virtual Azure através do gateway VPN.
1. AzurePublic - Um dos endereços IP pertence à Azure Virtual Network, enquanto o outro endereço IP pertence aos endereços IP públicos internos do Azure de propriedade da Microsoft. Os endereços IP públicos de propriedade do cliente não farão parte desse tipo de fluxo. Por exemplo, qualquer VM de propriedade do cliente que enviasse tráfego para um serviço azure (ponto final de armazenamento) seria categorizado sob esse tipo de fluxo.
1. ExternalPublic - Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP é um IP público que não está no Azure, não é relatado como malicioso nos feeds ASC que o Traffic Analytics consome para o intervalo de processamento entre " FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. MaliciousFlow - Um dos endereços IP pertence à rede virtual azure, enquanto o outro endereço IP é um IP público que não está no Azure e é relatado como malicioso nos feeds ASC que o Traffic Analytics consome para o intervalo de processamento entre " FlowIntervalStartTime_t" e "FlowIntervalEndTime_t".
1. UnknownPrivate - Um dos endereços IP pertence à Rede Virtual Azure, enquanto o outro endereço IP pertence à gama de IP privada, conforme definido no RFC 1918 e não pode ser mapeado pelo Traffic Analytics para um site de propriedade do cliente ou Azure Virtual Network.
1. Desconhecido – Não é possível mapear qualquer um dos endereços IP nos fluxos com a topologia do cliente no Azure, bem como no local (site).
1. Alguns nomes de \_campo \_são anexados com s ou d. Estes NÃO significam origem e destino, mas indicam os tipos de dados string e decimal, respectivamente.

### <a name="next-steps"></a>Próximas etapas
Para obter respostas para perguntas frequentes, consulte perguntas frequentes sobre [análises de tráfego](traffic-analytics-faq.md) Para ver detalhes sobre funcionalidade, consulte a [documentação de análise de tráfego](traffic-analytics.md)
