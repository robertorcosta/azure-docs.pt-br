---
title: Monitore eventos de cluster Linux no Azure Service Fabric
description: Aprenda a monitorar eventos de cluster Do Service Fabric Linux escrevendo eventos da plataforma Service Fabric no Syslog.
author: srrengar
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: srrengar
ms.openlocfilehash: 5bd3bda71943b2ba8a34cd4fbd0b20917b875670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645745"
---
# <a name="service-fabric-linux-cluster-events-in-syslog"></a>Eventos de cluster no Service Fabric do Linux no Syslog

O Service Fabric expõe um conjunto de eventos de plataforma para informá-lo de atividade importante no seu cluster. A lista completa de eventos que são expostos está disponível [aqui](service-fabric-diagnostics-event-generation-operational.md). Há várias maneiras por meio dos quais esses eventos podem ser consumidos. Neste artigo, vamos discutir como configurar o Service Fabric para gravar esses eventos de Syslog.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="introduction"></a>Introdução

Na versão 6.4, foi introduzido o SyslogConsumer para enviar os eventos de plataforma do Service Fabric para Syslog para clusters do Linux. Assim que ativado, os eventos irão fluir automaticamente ao Syslog que pode ser coletado e enviado pelo Agente do Log Analytics.

Cada evento de Syslog tem 4 componentes
* Recurso
* Identidade
* Mensagem
* Severity

O SyslogConsumer grava todos os eventos de plataforma usando o Recurso `Local0`. Você pode atualizar para qualquer instalação válida alterando a configuração de configuração. A identidade `ServiceFabric`usada é . O campo de Mensagem contém o evento inteiro serializado em JSON para que possa ser consultado e consumido por uma variedade de ferramentas. 

## <a name="enable-syslogconsumer"></a>Habilitar SyslogConsumer

Para habilitar o SyslogConsumer, você precisa executar uma atualização do cluster. A `fabricSettings` seção precisa ser atualizada com o código a seguir. Observe que esse código inclui apenas as seções relacionadas ao SyslogConsumer

```json
    "fabricSettings": [
        {
            "name": "Diagnostics",
            "parameters": [
            {
                "name": "ConsumerInstances",
                "value": "AzureWinFabCsv, AzureWinFabCrashDump, AzureTableWinFabEtwQueryable, SyslogConsumer"
            }
            ]
        },
        {
            "name": "SyslogConsumer",
            "parameters": [
            {
                "name": "ProducerInstance",
                "value": "WinFabLttProducer"
            },
            {
            "name": "ConsumerType",
            "value": "SyslogConsumer"
            },
            {
                "name": "IsEnabled",
                "value": "true"
            }
            ]
        },
        {
            "name": "Common",
            "parameters": [
            {
                "name": "LinuxStructuredTracesEnabled",
                "value": "true"
            }
            ]
        }
    ],
```

Aqui estão as alterações para ressaltar
1. Na seção Comum, há um novo parâmetro chamado `LinuxStructuredTracesEnabled`. **Isso é necessário ter eventos de Linus estruturados e serializado quando enviado para Syslog.**
2. Na seção Diagnósticos, um novo ConsumerInstance: SyslogConsumer foi adicionado. Isso informa à plataforma que há outro consumidor de eventos. 
3. A nova seção SyslogConsumer precisa ter `IsEnabled` como `true`. É configurado para usar o recurso de Local0 automaticamente. Você pode substituir isso, adicionando outro parâmetro.

```json
    {
        "name": "New LogFacility",
        "value": "<Valid Syslog Facility>"
    }
```

## <a name="azure-monitor-logs-integration"></a>Integração de logs do Azure Monitor
Você pode ler esses eventos do Syslog em uma ferramenta de monitoramento, como logs do Monitor Do Azure. Você pode criar um espaço de trabalho do Log Analytics usando o Azure Marketplace usando essas [instruções].(.. / azure-monitor/learn/quick-create-workspace.md) você também precisará adicionar o agente do Log Analytics ao seu cluster para coletar e enviar esses dados para o espaço de trabalho. Isso é o mesmo agente usado para coletar contadores de desempenho. 

1. Navegue até a `Advanced Settings` folha

    ![Configurações do Workspace](media/service-fabric-diagnostics-oms-syslog/workspace-settings.png)

2. Clique em `Data`
3. Clique em `Syslog`
4. Configure local0 como a instalação a ser rastreada. Você pode adicionar outra instalação se você o alterou em configurações de malha

    ![Configurar Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-configure.png)
5. Vá para o Gerenciador de consultas clicando `Logs` no menu do recurso do workspace para começar a consultar

    ![Logs do workspace](media/service-fabric-diagnostics-oms-syslog/workspace-logs.png)
6. Você pode consultar a `Syslog` tabela procurando `ServiceFabric` como ProcessName. A consulta a seguir é um exemplo de como analisar o JSON no evento e exibir seu conteúdo

```kusto
    Syslog | where ProcessName == "ServiceFabric" | extend $payload = parse_json(SyslogMessage) | project $payload
```

![Consulta Syslog](media/service-fabric-diagnostics-oms-syslog/syslog-query.png)

O exemplo acima é de um evento NodeDown. Você pode exibir a lista completa de eventos [aqui](service-fabric-diagnostics-event-generation-operational.md).

## <a name="next-steps"></a>Próximas etapas
* [Implantar o Agente do Log Analytics](service-fabric-diagnostics-oms-agent.md) para os nós para coletar os contadores de desempenho e coletar logs e estatísticas do docker para seus contêineres
* Familiarize-se com os recursos de [pesquisa e consulta de log](../log-analytics/log-analytics-log-searches.md) oferecidos como parte dos logs do Monitor do Azure
* [Use o View Designer para criar visualizações personalizadas nos registros do Monitor do Azure](../log-analytics/log-analytics-view-designer.md)
* Referência de como o [Azure Monitor registra a integração com o Syslog](../log-analytics/log-analytics-data-sources-syslog.md).
