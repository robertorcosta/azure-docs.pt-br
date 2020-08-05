---
title: Criar monitor de conexão Preview-ARMClient
titleSuffix: Azure Network Watcher
description: Saiba como criar um monitor de conexão (versão prévia) usando o ARMClient.
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
ms.openlocfilehash: 7d35799cd73ff4d065cb58189f2325dc4dac6840
ms.sourcegitcommit: 97a0d868b9d36072ec5e872b3c77fa33b9ce7194
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/04/2020
ms.locfileid: "87567861"
---
# <a name="create-a-connection-monitor-preview-using-the-armclient"></a>Criar um monitor de conexão (versão prévia) usando o ARMClient

Saiba como criar um monitor de conexão (versão prévia) para monitorar a comunicação entre os recursos usando o ARMClient. Ele dá suporte a implantações de nuvem híbridas e do Azure.

## <a name="before-you-begin"></a>Antes de começar 

Nos monitores de conexão criados no monitor de conexão (versão prévia), você pode adicionar máquinas locais e VMs do Azure como fontes. Esses monitores de conexão também podem monitorar a conectividade com pontos de extremidade. Os pontos de extremidade podem estar no Azure ou qualquer outra URL ou IP.

O monitor de conexão (versão prévia) inclui as seguintes entidades:

* **Recurso de monitor de conexão** – um recurso do Azure específico da região. Todas as entidades a seguir são propriedades de um recurso de monitor de conexão.
* **Ponto de extremidade** – uma origem ou um destino que participa das verificações de conectividade. Os exemplos de pontos de extremidade incluem VMs do Azure, agentes locais, URLs e IPs.
* **Configuração de teste** – uma configuração específica de protocolo para um teste. Com base no protocolo escolhido, você pode definir a porta, os limites, a frequência de teste e outros parâmetros.
* **Grupo de teste** – o grupo que contém pontos de extremidade de origem, pontos de extremidade de destino e configurações de teste. Um monitor de conexão pode conter mais de um grupo de teste.
* **Teste** – a combinação de um ponto de extremidade de origem, ponto de extremidade de destino e configuração de teste. Um teste é o nível mais granular no qual os dados de monitoramento estão disponíveis. Os dados de monitoramento incluem a porcentagem de verificações que falharam e o RTT (tempo de ida e volta).

    ![Diagrama mostrando um monitor de conexão, definindo a relação entre os grupos de teste e os testes](./media/connection-monitor-2-preview/cm-tg-2.png)

## <a name="steps-to-create-with-sample-arm-template"></a>Etapas para criar com modelo ARM de exemplo

Use o código a seguir para criar um monitor de conexão usando o ARMClient.

```armclient
$connectionMonitorName = "sampleConnectionMonitor"

$ARM = "https://management.azure.com"

$SUB = "subscriptions/<subscription id 1>;"

$NW = "resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher\_<region>"

$body =

"{

location: '<region>',

properties: {

endpoints: [{

name: 'workspace',

resourceId: '/subscriptions/<subscription id>/resourcegroups/<resource group>/providers/Microsoft.OperationalInsights/workspaces/sampleWorkspace',

filter: {

 items: [{

type: 'AgentAddress',

address: '<FQDN of your on-premises agent>'

}]

}

          },

 {

name: 'vm1',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

},

 {

name: 'vm2',

resourceId: '/subscriptions/<subscription id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<vm-name>'

   },

{

name: 'azure portal'

address: '<URL>'

   },

 {

    name: 'ip',

     address: '<IP>'

 }

  ],

  testGroups: [{

    name: 'Connectivity to Azure Portal and Public IP',

    testConfigurations: ['http', 'https', 'tcpEnabled', 'icmpEnabled'],

    sources: ['vm1', 'workspace'],

    destinations: ['azure portal', 'ip']

   },

{

    name: 'Connectivty from Azure VM 1 to Azure VM 2',

   // Choose your protocol
   
    testConfigurations: ['http', 'https', 'tcpDisabled', 'icmpDisabled'],

    sources: ['vm1'],

    destinations: ['vm2'],

    disable: true

   }

  ],

  testConfigurations: [{

    name: 'http',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    successThreshold: {

     checksFailedPercent: <threshold for checks failed %>,

     roundTripTimeMs: <threshold for RTT>

    }

   }, {

    name: 'https',

    testFrequencySec: <frequency>,

    protocol: 'HTTP',

    httpConfiguration: {

     preferHTTPS: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpEnabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'icmpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'ICMP',

    icmpConfiguration: {

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }, {

    name: 'tcpDisabled',

    testFrequencySec: <frequency>,

    protocol: 'TCP',

    tcpConfiguration: {

     port: 80,

     disableTraceRoute: true

    },

    successThreshold: {

     checksFailedPercent: <choose your checks failed threshold>,

     roundTripTimeMs: <choose your RTT threshold>

    }

   }

  ]

 }

} "
```

Este é o comando de implantação:
```
armclient PUT $ARM/$SUB/$NW/connectionMonitors/$connectionMonitorName/?api-version=2019-07-01 $body -verbose
```

## <a name="description-of-properties"></a>Descrição das propriedades

* connectionMonitorName-nome do recurso de monitor de conexão

* ID da subassinatura da assinatura na qual você deseja criar o monitor de conexão

* NW-ID do recurso do observador de rede no qual CM será criado 

* local-região na qual o monitor de conexão será criado

* Pontos de extremidade
    * nome – nome exclusivo para cada ponto de extremidade
    * ResourceId – para pontos de extremidade do Azure, a ID de recurso refere-se à ID de recurso de Azure Resource Manager (ARM) para máquinas virtuais. Para pontos de extremidade não Azure, a ID de recurso refere-se à ID de recurso do ARM para o espaço de trabalho Log Analytics vinculado a agentes não Azure.
    * Endereço – aplicável somente quando a ID do recurso não é especificada ou se a ID do recurso é Log Analytics espaço de trabalho. Se usado com Log Analytics ID de recurso, isso se refere ao FQDN do agente que pode ser usado para monitoramento. Se usado sem a ID do recurso, pode ser a URL ou o IP de qualquer ponto de extremidade público.
    * filtro – para pontos de extremidade não Azure, use filtrar para selecionar agentes de Log Analytics espaço de trabalho que será usado para monitoramento no recurso de monitor de conexão. Se os filtros não estiverem definidos, todos os agentes pertencentes ao espaço de trabalho Log Analytics poderão ser usados para monitoramento
        * tipo – defina o tipo como "endereço do agente"
        * Endereço – defina o endereço como o FQDN do agente local

* Grupos de teste
    * nome-nome do seu grupo de teste.
    * testConfigurations-testar configurações com base em quais pontos de extremidade de origem se conectam aos pontos de extremidade de destino
    * fontes – escolha um dos pontos de extremidade criados acima. Os pontos de extremidade de origem baseados no Azure precisam ter a extensão do observador de rede do Azure instalada e os pontos de extremidade de origem não baseados no Azure precisam haveAzure o agente Log Analytics instalado. Para instalar um agente para sua origem, consulte [instalar agentes de monitoramento](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#install-monitoring-agents).
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

## <a name="scale-limits"></a>Limites de escala

Os monitores de conexão têm os seguintes limites de escala:

* Máximo de monitores de conexão por assinatura por região: 100
* Máximo de grupos de teste por monitor de conexão: 20
* Máximo de origens e destinos por monitor de conexão: 100
* Configurações de teste máximas por monitor de conexão: 20 via ARMClient

## <a name="next-steps"></a>Próximas etapas

* Saiba [como analisar dados de monitoramento e definir alertas](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#analyze-monitoring-data-and-set-alerts)
* Saiba [como diagnosticar problemas em sua rede](https://docs.microsoft.com/azure/network-watcher/connection-monitor-preview#diagnose-issues-in-your-network)
