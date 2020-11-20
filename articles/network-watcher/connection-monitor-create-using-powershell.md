---
title: Criar monitor de conexão-PowerShell
titleSuffix: Azure Network Watcher
description: Saiba como criar um monitor de conexão usando o PowerShell.
services: network-watcher
documentationcenter: na
author: vinigam
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/30/2020
ms.author: vinigam
ms.openlocfilehash: fa8b2d967a336343d23c5f6aa4477ebcf2396407
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949030"
---
# <a name="create-a-connection-monitor-using-powershell"></a>Criar um monitor de conexão usando o PowerShell

Saiba como criar um monitor de conexão para monitorar a comunicação entre seus recursos usando o PowerShell.

## <a name="before-you-begin"></a>Antes de começar 

Nos monitores de conexão criados no monitor de conexão, você pode adicionar máquinas locais e VMs do Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade com pontos de extremidade. Os pontos de extremidade podem estar no Azure ou qualquer outra URL ou IP.

O monitor de conexão inclui as seguintes entidades:

* **Recurso de monitor de conexão** – um recurso do Azure específico da região. Todas as entidades a seguir são propriedades de um recurso de monitor de conexão.
* **Ponto de extremidade** – uma origem ou um destino que participa das verificações de conectividade. Os exemplos de pontos de extremidade incluem VMs do Azure, agentes locais, URLs e IPs.
* **Configuração de teste** – uma configuração específica de protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros.
* **Grupo de teste** – o grupo que contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Teste** – a combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o RTT (tempo de ida e volta).

    ![Diagrama mostrando um monitor de conexão, definindo a relação entre os grupos de teste e os testes](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-powershell"></a>Etapas para criar com o PowerShell

Use os comandos a seguir para criar um monitor de conexão usando o PowerShell.

```powershell

//Connect to your Azure account with the subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId <your-subscription>
//Select region
$nw = "NetworkWatcher_centraluseuap"
//Declare endpoints like Azure VM below. You can also give VNET,Subnet,Log Analytics workspace
$sourcevmid1 = New-AzNetworkWatcherConnectionMonitorEndpointObject -Name MyAzureVm -ResourceID /subscriptions/<your-subscription>/resourceGroups/<your resourceGroup>/providers/Microsoft.Compute/virtualMachines/<vm-name>
//Declare endpoints like URL, IPs
$bingEndpoint = New-AzNetworkWatcherConnectionMonitorEndpointObject -name Bing -Address www.bing.com # Destination URL
//Create test configuration.Choose Protocol and parametersSample configs below.

$IcmpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -IcmpProtocol
$TcpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -TcpProtocol -Port 80
$httpProtocolConfiguration = New-AzNetworkWatcherConnectionMonitorProtocolConfigurationObject -HttpProtocol -Port 443 -Method GET -RequestHeader @{Allow = "GET"} -ValidStatusCodeRange 2xx, 300-308 -PreferHTTPS
$httpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name http-tc -TestFrequencySec 60 -ProtocolConfiguration $httpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
$icmpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name icmp-tc -TestFrequencySec 30 -ProtocolConfiguration $icmpProtocolConfiguration -SuccessThresholdChecksFailedPercent 5 -SuccessThresholdRoundTripTimeMs 500
$tcpTestConfiguration = New-AzNetworkWatcherConnectionMonitorTestConfigurationObject -Name tcp-tc -TestFrequencySec 60 -ProtocolConfiguration $TcpProtocolConfiguration -SuccessThresholdChecksFailedPercent 20 -SuccessThresholdRoundTripTimeMs 30
//Create Test Group
$testGroup1 = New-AzNetworkWatcherConnectionMonitorTestGroupObject -Name testGroup1 -TestConfiguration $httpTestConfiguration, $tcpTestConfiguration, $icmpTestConfiguration -Source $sourcevmid1 -Destination $bingEndpoint,
$testname = "cmtest9"
//Create Connection Monitor
New-AzNetworkWatcherConnectionMonitor -NetworkWatcherName $nw -ResourceGroupName NetworkWatcherRG -Name $testname -TestGroup $testGroup1

```

## <a name="description-of-properties"></a>Descrição das propriedades

* connectionMonitorName-nome do recurso de monitor de conexão

* ID da subassinatura da assinatura na qual você deseja criar o monitor de conexão

* NW-ID do recurso do observador de rede no qual CM será criado 

* local-região na qual o monitor de conexão será criado

* Pontos de extremidade
    * nome – nome exclusivo para cada ponto de extremidade
    * ResourceId – para pontos de extremidade do Azure, a ID de recurso refere-se à ID de recurso Azure Resource Manager para máquinas virtuais. Para pontos de extremidade não Azure, a ID de recurso refere-se à ID de recurso do Azure Resource Manager para o espaço de trabalho Log Analytics vinculado a agentes não Azure.
    * Endereço – aplicável somente quando a ID do recurso não é especificada ou se a ID do recurso é Log Analytics espaço de trabalho. Se usado com Log Analytics ID de recurso, isso se refere ao FQDN do agente que pode ser usado para monitoramento. Se usado sem a ID do recurso, pode ser a URL ou o IP de qualquer ponto de extremidade público.
    * filtro – para pontos de extremidade não Azure, use filtrar para selecionar agentes de Log Analytics espaço de trabalho que será usado para monitoramento no recurso de monitor de conexão. Se os filtros não estiverem definidos, todos os agentes pertencentes ao espaço de trabalho Log Analytics poderão ser usados para monitoramento
        * tipo – defina o tipo como "endereço do agente"
        * Endereço – defina o endereço como o FQDN do agente local

* Grupos de teste
    * nome-nome do seu grupo de teste.
    * testConfigurations-testar configurações com base em quais pontos de extremidade de origem se conectam aos pontos de extremidade de destino
    * fontes – escolha um dos pontos de extremidade criados acima. Os pontos de extremidade de origem baseados no Azure precisam ter a extensão do observador de rede do Azure instalada e os pontos de extremidade de origem não baseados no Azure precisam haveAzure o agente Log Analytics instalado. Para instalar um agente para sua origem, consulte [instalar agentes de monitoramento](./connection-monitor-overview.md#install-monitoring-agents).
    * destinos – escolha os pontos de extremidade criados acima. Você pode monitorar a conectividade com as VMs do Azure ou qualquer ponto de extremidade (um IP público, uma URL ou um FQDN) especificando-os como destinos. Em um único grupo de teste, você pode adicionar VMs do Azure, URLs do Office 365, URLs do Dynamics 365 e pontos de extremidade personalizados.
    * desabilitar – Use esse campo para desabilitar o monitoramento de todas as origens e destinos que o grupo de teste especifica.

* Configurações de teste
    * nome-nome da configuração de teste.
    * testFrequencySec-especifique a frequência com que as fontes executarão ping no protocolo e na porta que você especificou. Você pode escolher 30 segundos, 1 minuto, 5 minutos, 15 minutos ou 30 minutos. As fontes testarão a conectividade com destinos com base no valor que você escolher. Por exemplo, se você selecionar 30 segundos, as fontes verificarão a conectividade com o destino pelo menos uma vez em um período de 30 segundos.
    * protocolo-você pode escolher TCP, ICMP, HTTP ou HTTPS. Dependendo do protocolo, você pode fazer algumas configurações específicas de protocolo
        * preferHTTPS-especifique se deseja usar HTTPS sobre HTTP
        * porta-especifique a porta de destino de sua escolha.
        * disableTraceRoute-isso se aplica a grupos de teste cujo protocolo é TCP ou ICMP. Ele interrompe fontes de descobrir a topologia e o RTT de salto a salto.
    * successThreshold-você pode definir limites nos seguintes parâmetros de rede:
        * checksFailedPercent-define a porcentagem de verificações que podem falhar quando as origens verificam a conectividade com os destinos usando os critérios que você especificou. Para o protocolo TCP ou ICMP, a porcentagem de verificações com falha pode ser igual à porcentagem de perda de pacotes. Para o protocolo HTTP, esse campo representa o percentual de solicitações HTTP que não receberam resposta.
        * roundTripTimeMs – defina o RTT em milissegundos para o tempo que as fontes podem levar para se conectar ao destino pela configuração de teste.

## <a name="scale-limits"></a> Limites de escala

Os monitores de conexão têm os seguintes limites de escala:

* Máximo de monitores de conexão por assinatura por região: 100
* Máximo de grupos de teste por monitor de conexão: 20
* Máximo de origens e destinos por monitor de conexão: 100
* Configurações de teste máximas por monitor de conexão: 20

## <a name="next-steps"></a>Próximas etapas

* Saiba [como analisar dados de monitoramento e definir alertas](./connection-monitor-overview.md#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas em sua rede](./connection-monitor-overview.md#diagnose-issues-in-your-network)