---
title: Como consultar logs do Monitor Azure para VMs
description: A solução Azure Monitor for VMs coleta métricas e dados de registro para e este artigo descreve os registros e inclui consultas de amostra.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 71258b04bad9a7aec4e86564d51d1d6f3f8cac76
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283796"
---
# <a name="how-to-query-logs-from-azure-monitor-for-vms"></a>Como consultar logs do Azure Monitor para VMs

O Azure Monitor for VMs coleta métricas de desempenho e conexão, dados de inventário de computadores e processos e informações sobre o estado de saúde e os encaminha para o espaço de trabalho log analytics no Azure Monitor.  Esses dados estão disponíveis [para consulta no](../../azure-monitor/log-query/log-query-overview.md) Azure Monitor. Você pode aplicar esses dados a cenários que incluem planejamento de migração, análise de capacidade, descoberta e solução de problemas de desempenho sob demanda.

## <a name="map-records"></a>Registros do mapa

Um registro é gerado por hora para cada computador e processo exclusivo, além dos registros que são gerados quando um processo ou computador inicia ou é integrado ao Azure Monitor para o recurso de Mapeamento de VMs. Esses registros têm as propriedades descritas nas tabelas a seguir. Os campos e valores nos eventos ServiceMapComputer_CL mapeiam para campos do recurso do Computador na API do ServiceMap do Azure Resource Manager. Os campos e valores nos eventos ServiceMapProcess_CL mapeiam para os campos do recurso do Processo na API do ServiceMap do Azure Resource Manager. O campo ResourceName_s corresponde ao campo de nome no recurso do Gerenciador de Recursos correspondente. 

Há propriedades geradas internamente que você pode usar para identificar computadores e processos exclusivos:

- Computador: use *ResourceId* ou *ResourceName_s* para identificar exclusivamente um computador em um espaço de trabalho do Log Analytics.
- Processo: use *ResourceId* para identificar exclusivamente um processo em um espaço de trabalho do Log Analytics. O *ResourceName_s* é exclusivo no contexto do computador no qual o processo está em execução (MachineResourceName_s) 

Como vários registros podem existir para um processo e computador específicos em um intervalo de tempo específico, as consultas podem retornar mais de um registro para o mesmo computador ou processo. Para incluir apenas o registro `| summarize arg_max(TimeGenerated, *) by ResourceId` mais recente, adicione à consulta.

### <a name="connections-and-ports"></a>Conexões e portas

O recurso Connection Metrics introduz duas novas tabelas nos logs do Monitor Azure - VMConnection e VMBoundPort. Essas tabelas fornecem informações sobre as conexões de uma máquina (entrada e saída), bem como as portas do servidor que estão abertas/ativas nelas. Conexões As métricas também são expostas via APIs que fornecem os meios para obter uma métrica específica durante uma janela de tempo. As conexões TCP resultantes da *aceitação* em um soquete de escuta estão a entrar, enquanto as criadas *pela conexão* a um determinado IP e porta são de saída. A direção de uma conexão é representada pela propriedade Direction, o que pode ser definida para **entrada** ou **saída**. 

Os registros nessas tabelas são gerados a partir de dados relatados pelo Agente de Dependência. Cada registro representa uma observação em um intervalo de tempo de 1 minuto. A propriedade TimeGenerated indica o início do intervalo de tempo. Cada registro contém informações para identificar a respectiva entidade, ou seja, conexão ou porta, bem como as métricas associadas àquela entidade. Atualmente, apenas as atividades de rede que usam TCP via IPv4 são relatadas. 

#### <a name="common-fields-and-conventions"></a>Campos e convenções comuns 

Os seguintes campos e convenções se aplicam tanto ao VMConnection quanto ao VMBoundPort: 

- Computador: Nome de domínio totalmente qualificado da máquina de relatórios 
- AgentId: O identificador exclusivo para uma máquina com o agente Log Analytics  
- Máquina: Nome do recurso Azure Resource Manager para a máquina exposta pelo ServiceMap. É do formulário *m-{GUID}*, onde *guid* é o mesmo GUID que AgentId  
- Processo: Nome do recurso Do Azure Resource Manager para o processo exposto pelo ServiceMap. É da forma *p-{hex string}*. O processo é único dentro de um escopo de máquina e para gerar um ID de processo único entre máquinas, combinar campos de Máquina e Processo. 
- Nome do processo: nome executável do processo de emissão de relatórios.
- Todos os endereços IP são strings no formato canônico IPv4, por exemplo *13.107.3.160* 

Para gerenciar o custo e a complexidade, os registros de conexão não representam as conexões de rede física individuais. Várias conexões de rede física são agrupadas em uma conexão lógica, o que é refletido na respectiva tabela.  Ou seja, os registros na tabela *VMConnection* representam um agrupamento lógico, não as conexões físicas individuais sendo observadas. As conexões de rede física que compartilham o mesmo valor para os atributos a seguir durante o intervalo especificado de um minuto são agregadas em um único registro lógico em *VMConnection*. 

| Propriedade | Descrição |
|:--|:--|
|Direção |Direção da conexão, o valor é *entrada* ou *saída* |
|Computador |O FQDN do computador |
|Processo |Identidade do processo ou grupos de processos, iniciando/aceitando a conexão |
|SourceIp |Endereço IP da origem |
|DestinationIp |Endereço IP do destino |
|DestinationPort |Número da porta de destino |
|Protocolo |Protocolo usado para a conexão.  O valor é *tcp*. |

Para levar em conta o impacto do agrupamento, são fornecidas informações sobre o número de conexões físicas agrupadas nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
|LinksEstablished |O número de conexões de rede física que foram estabelecidas durante o intervalo de tempo de geração de relatórios |
|LinksTerminated |O número de conexões de rede física que foram terminadas durante o intervalo de tempo de geração de relatórios |
|LinksFailed |O número de conexões de rede física que falharam durante o intervalo de tempo de geração de relatórios. Essa informação está disponível atualmente apenas para as conexões de saída. |
|LinksLive |O número de conexões de rede física que estavam abertas no final do intervalo de tempo de geração de relatórios|

#### <a name="metrics"></a>Métricas

Além das métricas de contagem de conexões, as informações sobre o volume de dados enviados e recebidos em determinada conexão lógica ou porta de rede também estão incluídas nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
|BytesSent |Número total de bytes enviados durante o intervalo de tempo de geração de relatórios |
|BytesReceived |Número total de bytes recebidos durante o intervalo de tempo de geração de relatórios |
|Respostas |O número de respostas observadas durante o intervalo de tempo de geração de relatórios. 
|ResponseTimeMax |O maior tempo de resposta (milissegundos) observado durante o intervalo de tempo de geração de relatórios. Se não houve valor, a propriedade ficará em branco.|
|ResponseTimeMin |O menor tempo de resposta (milissegundos) observado durante o intervalo de tempo de geração de relatórios. Se não houve valor, a propriedade ficará em branco.|
|ResponseTimeSum |A soma de todos os tempos de resposta (milissegundos) observados durante o intervalo de tempo de geração de relatórios. Se não houve valor, a propriedade ficará em branco.|

O terceiro tipo de dados relatado é o tempo de resposta: quanto tempo quem chama fica aguardando uma solicitação enviada em uma conexão para ser processada e respondida pelo ponto de extremidade remoto. O tempo de resposta relatado é uma estimativa do tempo real de resposta do protocolo do aplicativo subjacente. Ele é calculado usando uma heurística baseada na observação do fluxo de dados entre a origem e o destino final de uma conexão de rede física. Conceitualmente, é a diferença entre a hora em que o último byte de uma solicitação deixa o emissor e a hora em que o último byte da resposta volta para ele. Esses dois carimbos de data/hora são usados para delinear os eventos de solicitação e resposta em determinada conexão física. A diferença entre eles representa o tempo de resposta de uma única solicitação. 

Na primeira versão do recurso, nosso algoritmo é uma aproximação que pode funcionar com vários graus de sucesso, dependendo do protocolo do aplicativo real usado em determinada conexão de rede. Por exemplo, a abordagem atual funciona bem para os protocolos baseados em solicitação-resposta, como o HTTP(S), mas não funciona com protocolos baseados em fila de mensagens ou unidirecionais.

Aqui estão alguns pontos importantes a considerar:

1. Se um processo aceita conexões no mesmo endereço IP, mas em várias interfaces de rede, é relatado um registro separado para cada interface. 
2. Os registros com IP curinga não conterão nenhuma atividade. Eles são incluídos para representar o fato de que uma porta no computador está aberta para o tráfego de entrada.
3. Para reduzir o nível de detalhes e o volume de dados, os registros com IP curinga serão omitidos quando houver um registro correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registro IP curinga for omitido, a propriedade de registro IsWildcardBind com o endereço IP específico será definida para "True" para indicar que a porta está exposta em cada interface do computador que gera os relatórios.
4. As portas vinculadas apenas em uma interface específica têm IsWildcardBind definido como *False*.

#### <a name="naming-and-classification"></a>Nomenclatura e Classificação

Para sua conveniência, o endereço IP da extremidade remota de uma conexão é incluído na propriedade RemoteIp. No caso das conexões de entrada, RemoteIp é igual a SourceIp; já para conexões de saída, é igual a DestinationIp. A propriedade RemoteDnsCanonicalNames representa os nomes DNS aceitos relatados pelo computador para RemoteIp. As propriedades RemoteDnsQuestions e RemoteClassification são reservadas para uso futuro. 

#### <a name="geolocation"></a>Geolocalização

*VMConnection* também inclui informações de localização geográfica para a extremidade remota de cada registro de conexão nas seguintes propriedades do registro: 

| Propriedade | Descrição |
|:--|:--|
|RemoteCountry |O nome do país/região que hospeda O Controle Remoto.  Por exemplo: *Estados Unidos* |
|RemoteLatitude |A latitude da localização geográfica. Por exemplo: *47,68* |
|RemoteLongitude |A longitude da localização geográfica. Por exemplo: *-122,12* |

#### <a name="malicious-ip"></a>IP malicioso

Todas as propriedades RemoteIp na tabela *VMConnection* são verificadas em um conjunto de IPs com atividades maliciosas conhecidas. Se RemoteIp for identificado como malicioso, as propriedades a seguir serão preenchidas (elas ficam em branco quando o IP não é considerado malicioso) nas seguintes propriedades do registro:

| Propriedade | Descrição |
|:--|:--|
|MaliciousIP |Endereço de RemoteIp |
|IndicatorThreadType |O indicador de ameaça detectado é um dos seguintes valores, *Botnet*, *C2*, *CryptoMining*, *Darknet*, *DDos*, *MaliciousUrl*, *Malware*, *Phishing*, *Proxy*, *PUA*, *Watchlist*.   |
|Descrição |Descrição da ameaça observada. |
|TLPLevel |O TLP (Traffic Light Protocol) é um dos valores definidos, *Branco*, *Verde*, *Âmbar*, *Vermelho*. |
|Confiança |Os valores são *0 – 100*. |
|Severity |Os valores são *0 – 5*, onde *5* é o mais grave e *0* não é grave. O valor padrão é *3*.  |
|FirstReportedDateTime |A primeira vez que o provedor relatou o indicador. |
|LastReportedDateTime |A última vez que o indicador foi visto pelo Interflow. |
|IsActive |Indica que os indicadores estão desativados com o valor *Verdadeiro* ou *Falso*. |
|ReportReferenceLink |Links para relatórios relacionados a um dado observável. |
|AdditionalInformation |Fornece informações adicionais, se aplicáveis, sobre a ameaça observada. |

### <a name="ports"></a>Portas 

As portas em uma máquina que aceita ativamente tráfego de entrada ou podem potencialmente aceitar tráfego, mas estão ociosas durante a janela de tempo de relatório, são escritas na tabela VMBoundPort.  

Todos os registros no VMBoundPort são identificados pelos seguintes campos: 

| Propriedade | Descrição |
|:--|:--|
|Processo | Identidade de processo (ou grupos de processos) com os quais a porta está associada.|
|Ip | Endereço IP da porta (pode ser WILDCARD IP, *0.0.0.0*) |
|Porta |O número da porta |
|Protocolo | O protocolo.  Exemplo, *tcp* ou *udp* (apenas *tcp* é suportado atualmente).|
 
A identidade de uma porta é derivada dos cinco campos acima e está armazenada na propriedade PortId. Esta propriedade pode ser usada para encontrar rapidamente registros de uma porta específica ao longo do tempo. 

#### <a name="metrics"></a>Métricas 

Os registros de portas incluem métricas que representam as conexões associadas a eles. Atualmente, as seguintes métricas são relatadas (os detalhes de cada métrica são descritos na seção anterior): 

- BytesSent e BytesReceived 
- Linksestabelecidos, LinksTerminados, LinksLive 
- ResposeTime, ResponseTimeMin, ResponseTimeMax, ResponseTimeSum 

Aqui estão alguns pontos importantes a considerar:

- Se um processo aceita conexões no mesmo endereço IP, mas em várias interfaces de rede, é relatado um registro separado para cada interface.  
- Os registros com IP curinga não conterão nenhuma atividade. Eles são incluídos para representar o fato de que uma porta no computador está aberta para o tráfego de entrada. 
- Para reduzir o nível de detalhes e o volume de dados, os registros com IP curinga serão omitidos quando houver um registro correspondente (para o mesmo processo, porta e protocolo) com um endereço IP específico. Quando um registro IP curinga for omitido, a propriedade *IsWildcardBind* para registro com o endereço IP específico será definida como *True*.  Isso indica que a porta está exposta em todas as interfaces da máquina de relatórios. 
- As portas vinculadas apenas em uma interface específica têm IsWildcardBind definido como *False*. 

### <a name="vmcomputer-records"></a>VMRegistros de computador

Registros com um tipo de *VMComputer* têm dados de inventário para servidores com o agente Dependency. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
|TenantId | O identificador exclusivo para o espaço de trabalho |
|SourceSystem | *Insights* | 
|TimeGenerated | Carimbo de data do registro (UTC) |
|Computador | O FQDN do computador | 
|AgentId | O ID exclusivo do agente Log Analytics |
|Computador | Nome do recurso Azure Resource Manager para a máquina exposta pelo ServiceMap. É do formulário *m-{GUID}*, onde *guid* é o mesmo GUID que AgentId. | 
|DisplayName | Nome de exibição | 
|FullDisplayName | Nome de exibição completo | 
|HostName | O nome da máquina sem nome de domínio |
|Tempo de inicialização | O tempo de inicialização da máquina (UTC) |
|timeZone | O fuso horário normalizado |
|VirtualizaçãoEstado | *virtual,* *hipervisor,* *físico* |
|Ipv4Endereços | Matriz de endereços IPv4 | 
|Ipv4SubnetMasks | Matriz de máscaras de sub-rede IPv4 (na mesma ordem que Ipv4Addresses). |
|Ipv4DefaultGateways | Matriz de gateways IPv4 | 
|Ipv6Endereços | Matriz de endereços IPv6 | 
|MacAddresses | Matriz de endereços MAC | 
|DnsNames | Matriz de nomes DNS associados à máquina. |
|DependencyAgentVersion | A versão do agente dependency em execução na máquina. | 
|OperatingSystemFamily | *Linux*, *Windows* |
|OperatingSystemFullName | O nome completo do sistema operacional | 
|PhysicalMemoryMB | A memória física em megabytes | 
|Cpus | O número de processadores | 
|Velocidade da cpu | A velocidade da CPU em MHz | 
|VirtualMachineType | *hyperv,* *vmware,* *xen* |
|VirtualMachineNativeId | A ID da VM conforme atribuída pelo seu hipervisor | 
|Nome nativo da virtualmáquina | O nome da VM |
|VirtualMachineHypervisorId | O identificador exclusivo do hipervisor hospedando o VM |
|HipervisorType | *Hyperv* |
|Hipervisoride | A identificação única do hipervisor | 
|Provedor de hospedagem | *Azure* |
|_ResourceId | O identificador exclusivo para um recurso Do Zure |
|AzureSubscriptionId | Um identificador globalmente único que identifica sua assinatura | 
|AzureResourceGroup | O nome do grupo de recursos Azure do que a máquina é membro. |
|Nome do AzureResource | O nome do recurso Azure |
|AzureLocalização | A localização do recurso Azure |
|AzureUpdateDomain | O nome do domínio de atualização do Azure |
|Domínio de falha do Azure | O nome do domínio de falha do Azure |
|AzureVmId | O identificador exclusivo da máquina virtual Do Zure |
|AzureSize | O tamanho da máquina virtual do Azure |
|AzureImagePublisher | O nome do editor do Azure VM |
|AzureImageOffering | O nome do tipo de oferta azure VM | 
|AzureImageSku | O SKU da imagem Azure VM | 
|AzureImageVersion | A versão da imagem Do Azure VM | 
|AzureCloudServiceName | O nome do serviço de nuvem DoZure |
|Implantação do AzureCloudService | ID de implantação para o Serviço de Nuvem |
|AzureCloudServiceRoleName | Nome da função serviço em nuvem |
|AzureCloudServiceRoleType | Tipo de função serviço na nuvem: *trabalhador* ou *web* |
|AzureCloudServiceInstanceId | ID de instância de função de serviço em nuvem |
|AzureVmScaleSetNome | O nome do conjunto de escala de máquina virtual |
|Implantação do AzureVmScaleSet | ID de implantação de conjunto de escala de máquina virtual |
|AzureVmScaleSetResourceId | O identificador exclusivo do recurso de conjunto de escala de máquina virtual.|
|AzureVmScaleSetInstanceId | O identificador exclusivo do conjunto de escalas de máquinavirtual |
|AzureServiceFabricClusterId | A identificação única do cluster Azure Service Fabric | 
|AzureServiceFabricClusterNome | O nome do cluster Azure Service Fabric |

### <a name="vmprocess-records"></a>Registros do VMProcess

Registros com um tipo de *VMProcess* têm dados de inventário para processos conectados ao TCP em servidores com o agente Dependency. Esses registros têm as propriedades descritas na tabela a seguir:

| Propriedade | Descrição |
|:--|:--|
|TenantId | O identificador exclusivo para o espaço de trabalho |
|SourceSystem | *Insights* | 
|TimeGenerated | Carimbo de data do registro (UTC) |
|Computador | O FQDN do computador | 
|AgentId | O ID exclusivo do agente Log Analytics |
|Computador | Nome do recurso Azure Resource Manager para a máquina exposta pelo ServiceMap. É do formulário *m-{GUID}*, onde *guid* é o mesmo GUID que AgentId. | 
|Processo | O identificador exclusivo do processo Mapa de Serviço. É na forma de *p-{GUID}*. 
|Nome executável | O nome do processo executável | 
|DisplayName | Nome de exibição do processo |
|Função | Função de processo: *servidor web,* *appServer,* *banco de dadosServer,* *ldapServer,* *smbServer* |
|Agrupar | Nome do grupo de processo. Processos no mesmo grupo estão logicamente relacionados, por exemplo, parte do mesmo produto ou componente do sistema. |
|StartTime | O tempo de início do pool de processos |
|FirstPid | O primeiro PID no pool de processos |
|Descrição | A descrição do processo |
|CompanyName | O nome da empresa |
|InternalName | O nome interno |
|ProductName | O nome do produto |
|ProductVersion | A versão do produto |
|FileVersion | A versão do arquivo |
|Executablepath |O caminho do executável |
|CommandLine | A linha de comando |
|WorkingDirectory | O diretório de trabalho |
|Serviços | Uma série de serviços os quais o processo está sendo executado |
|UserName | A conta sob a qual o processo está sendo executado |
|UserDomain | O domínio sob o qual o processo está sendo executado |
|_ResourceId | O identificador exclusivo para um processo dentro do workspace |


## <a name="sample-map-queries"></a>Consultas de mapa de amostra

### <a name="list-all-known-machines"></a>Lista todas as máquinas conhecidas

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="when-was-the-vm-last-rebooted"></a>Quando a VM foi reiniciada pela última vez

```kusto
let Today = now(); VMComputer | extend DaysSinceBoot = Today - BootTime | summarize by Computer, DaysSinceBoot, BootTime | sort by BootTime asc
```

### <a name="summary-of-azure-vms-by-image-location-and-sku"></a>Resumo das VMs do Azure por imagem, localização e SKU

```kusto
VMComputer | where AzureLocation != "" | summarize by ComputerName, AzureImageOffering, AzureLocation, AzureImageSku
```

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>Liste a capacidade de memória física de todos os computadores gerenciados

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project PhysicalMemoryMB, Computer
```

### <a name="list-computer-name-dns-ip-and-os"></a>Liste o nome do computador, DNS, IP e OS

```kusto
VMComputer | summarize arg_max(TimeGenerated, *) by _ResourceId | project Computer, OperatingSystemFullName, DnsNames, Ipv4Addresses
```

### <a name="find-all-processes-with-sql-in-the-command-line"></a>Localizar todos os processos com "sql" na linha de comando

```kusto
VMComputer | where CommandLine contains_cs "sql" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>Localizar uma máquina (registro mais recente) por nome de recurso

```kusto
search in (VMComputer) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>Localizar um computador (registro mais recente) pelo endereço IP

```kusto
search in (VMComputer) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-known-processes-on-a-specified-machine"></a>Listar todos os processos conhecidos em um computador especificado

```kusto
VMProcess | where Machine == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by _ResourceId
```

### <a name="list-all-computers-running-sql-server"></a>Listar todos os computadores que executam o SQL Server

```kusto
VMComputer | where AzureResourceName in ((search in (VMProcess) "\*sql\*" | distinct Machine)) | distinct Computer
```

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>Listar todas as versões de produtos exclusivas de curl no meu datacenter

```kusto
VMProcess | where ExecutableName == "curl" | distinct ProductVersion
```

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>Criar um grupo de computadores de todos os computadores executando CentOS

```kusto
VMComputer | where OperatingSystemFullName contains_cs "CentOS" | distinct ComputerName
```

### <a name="bytes-sent-and-received-trends"></a>Os bytes enviaram e receberam tendências

```kusto
VMConnection | summarize sum(BytesSent), sum(BytesReceived) by bin(TimeGenerated,1hr), Computer | order by Computer desc | render timechart
```

### <a name="which-azure-vms-are-transmitting-the-most-bytes"></a>Quais VMs do Azure transmitem a maioria dos bytes

```kusto
VMConnection | join kind=fullouter(VMComputer) on $left.Computer == $right.Computer | summarize count(BytesSent) by Computer, AzureVMSize | sort by count_BytesSent desc
```

### <a name="link-status-trends"></a>Vincular tendências de status

```kusto
VMConnection | where TimeGenerated >= ago(24hr) | where Computer == "acme-demo" | summarize dcount(LinksEstablished), dcount(LinksLive), dcount(LinksFailed), dcount(LinksTerminated) by bin(TimeGenerated, 1h) | render timechart
```

### <a name="connection-failures-trend"></a>Tendência com falhas de conexão

```kusto
VMConnection | where Computer == "acme-demo" | extend bythehour = datetime_part("hour", TimeGenerated) | project bythehour, LinksFailed | summarize failCount = count() by bythehour | sort by bythehour asc | render timechart
```

### <a name="bound-ports"></a>Portos Vinculados

```kusto
VMBoundPort
| where TimeGenerated >= ago(24hr)
| where Computer == 'admdemo-appsvr'
| distinct Port, ProcessName
```

### <a name="number-of-open-ports-across-machines"></a>Número de portas abertas entre máquinas

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by Computer, Machine, Port, Protocol
| summarize OpenPorts=count() by Computer, Machine
| order by OpenPorts desc
```

### <a name="score-processes-in-your-workspace-by-the-number-of-ports-they-have-open"></a>Score processos em seu espaço de trabalho pelo número de portas que eles têm aberto

```kusto
VMBoundPort
| where Ip != "127.0.0.1"
| summarize by ProcessName, Port, Protocol
| summarize OpenPorts=count() by ProcessName
| order by OpenPorts desc
```

### <a name="aggregate-behavior-for-each-port"></a>Comportamento agregado para cada porta

Essa consulta pode então ser usada para marcar portas por atividade, por exemplo, portas com a maioria do tráfego de entrada/saída, portas com a maioria das conexões
```kusto
// 
VMBoundPort
| where Ip != "127.0.0.1"
| summarize BytesSent=sum(BytesSent), BytesReceived=sum(BytesReceived), LinksEstablished=sum(LinksEstablished), LinksTerminated=sum(LinksTerminated), arg_max(TimeGenerated, LinksLive) by Machine, Computer, ProcessName, Ip, Port, IsWildcardBind
| project-away TimeGenerated
| order by Machine, Computer, Port, Ip, ProcessName
```

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>Resumir as conexões de saída a partir de um grupo de computadores

```kusto
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(VMComputer
| summarize ips=makeset(todynamic(Ipv4Addresses)) by MonitoredMachine=AzureResourceName
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="performance-records"></a>Registros de desempenho
Registros com um tipo de *InsightsMetrics* têm dados de desempenho do sistema operacional convidado da máquina virtual. Esses registros têm as propriedades descritas na tabela a seguir:


| Propriedade | Descrição |
|:--|:--|
|TenantId | Identificador exclusivo para o espaço de trabalho |
|SourceSystem | *Insights* | 
|TimeGenerated | Tempo que o valor foi coletado (UTC) |
|Computador | O FQDN do computador | 
|Origem | *vm.azm.ms* |
|Namespace | Categoria do contador de desempenho | 
|Nome | Nome do contador de desempenho |
|Val | Valor coletado | 
|Marcas | Detalhes relacionados sobre o registro. Veja a tabela abaixo para tags usadas com diferentes tipos de registro.  |
|AgentId | Identificador exclusivo para o agente de cada computador |
|Type | *InsightsMetrics* |
|_Resourceid_ | ID de recursos da máquina virtual |

Os contadores de desempenho atualmente coletados na tabela *InsightsMetrics* estão listados na tabela a seguir:

| Namespace | Nome | Descrição | Unidade | Marcas |
|:---|:---|:---|:---|:---|
| Computador    | Pulsação             | Batimentocardíaco do computador                        | | |
| Memória      | DisponívelMB           | Bytes disponíveis de memória                    | Bytes          | memóriaTamanhoMB - Tamanho total da memória|
| Rede     | WriteBytesPerSecond   | Bytes de gravação de rede por segundo            | BytesPerSecond | NetworkDeviceId - Id do dispositivo<br>bytes - Total enviado bytes |
| Rede     | ReadBytesPerSecond    | Rede ler bytes por segundo             | BytesPerSecond | networkDeviceId - Id do dispositivo<br>bytes - Total de bytes recebidos |
| Processador   | Percentual de utilização | Porcentagem de utilização do processador          | Porcentagem        | totalCpus - CPUs totais |
| LogicalDisk | WritesPerSecond       | Disco lógico grava por segundo            | CountPerSecond | mountId - ID de montagem do dispositivo |
| LogicalDisk | WriteLatencyMs        | Citência de gravação de disco lógico milissegundo    | MilliSeconds   | mountId - ID de montagem do dispositivo |
| LogicalDisk | WriteBytesPerSecond   | Bytes de gravação de disco lógico por segundo       | BytesPerSecond | mountId - ID de montagem do dispositivo |
| LogicalDisk | TransfersPerSecond    | Transferências de disco lógico por segundo         | CountPerSecond | mountId - ID de montagem do dispositivo |
| LogicalDisk | TransferLatencyMs     | Latência de transferência de disco lógico milissegundo | MilliSeconds   | mountId - ID de montagem do dispositivo |
| LogicalDisk | ReadsPerSecond        | Leituras de disco lógico por segundo             | CountPerSecond | mountId - ID de montagem do dispositivo |
| LogicalDisk | ReadLatencyMs         | Latência de latência do disco lógico     | MilliSeconds   | mountId - ID de montagem do dispositivo |
| LogicalDisk | ReadBytesPerSecond    | Disco lógico ler bytes por segundo        | BytesPerSecond | mountId - ID de montagem do dispositivo |
| LogicalDisk | FreeSpacePercentage   | Porcentagem de espaço livre de disco lógico        | Porcentagem        | mountId - ID de montagem do dispositivo |
| LogicalDisk | Espaço LivreMB           | Bytes de espaço livre de disco lógico             | Bytes          | mountId - ID de montagem do dispositivo<br>diskSizeMB - Tamanho total do disco |
| LogicalDisk | BytesPerSecond        | Bytes de disco lógico por segundo             | BytesPerSecond | mountId - ID de montagem do dispositivo |


## <a name="next-steps"></a>Próximas etapas

* Se você for novo para escrever consultas de log no Azure Monitor, [analise como usar](../../azure-monitor/log-query/get-started-portal.md) o Log Analytics no portal Azure para escrever consultas de log.

* Saiba mais sobre [a redação de consultas de pesquisa](../../azure-monitor/log-query/search-queries.md).
