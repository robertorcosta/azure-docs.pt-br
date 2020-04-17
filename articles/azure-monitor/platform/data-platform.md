---
title: Plataforma de dados do Azure Monitor | Microsoft Docs
description: Os dados de monitoramento coletados pelo Azure Monitor são separados em métricas leves e que podem dar suporte a logs e cenários quase em tempo real que são usados para análise avançada.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 58f542238c952088777ed9809b57dae3cdb9cf12
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457256"
---
# <a name="azure-monitor-data-platform"></a>Plataforma de dados do Azure Monitor

Permitir a observância nos ambientes de computação complexos atuais executando aplicativos distribuídos que dependem tanto de serviços em nuvem quanto de instalações, requer a coleta de dados operacionais de todas as camadas e componentes do sistema distribuído. Você precisa ser capaz de realizar insights profundos sobre esses dados e consolidá-los em um único painel de vidro com diferentes perspectivas para apoiar a multidão de stakeholders em sua organização.

[O Azure Monitor](../overview.md) coleta e agrega dados de uma variedade de fontes em uma plataforma de dados comum, onde pode ser usado para análise, visualização e alerta. Ele fornece uma experiência consistente em cima de dados de várias fontes, o que lhe fornece insights profundos sobre todos os seus recursos monitorados e até mesmo com dados de outros serviços que armazenam seus dados no Azure Monitor.


![Visão geral do Azure Monitor](media/data-platform/overview.png)

## <a name="observability-data-in-azure-monitor"></a>Dados de observação no Monitor Do Azure
Métricas, registros e traços distribuídos são comumente referidos como os três pilares da observância. Estes são os diferentes tipos de dados que uma ferramenta de monitoramento deve coletar e analisar para fornecer observância suficiente de um sistema monitorado. A observabilidade pode ser alcançada correlacionando dados de múltiplos pilares e agregando dados em todo o conjunto de recursos que estão sendo monitorados. Como o Azure Monitor armazena dados de várias fontes em conjunto, os dados podem ser correlacionados e analisados usando um conjunto comum de ferramentas. Ele também correlaciona dados em várias assinaturas e inquilinos do Azure, além de hospedar dados para outros serviços.

Os recursos do Azure geram uma quantidade significativa de dados de monitoramento. O Azure Monitor consolida esses dados juntamente com os dados de monitoramento de outras fontes em uma plataforma Métricas ou Logs. Cada um é otimizado para cenários de monitoramento específicos, e cada um suporta recursos diferentes no Azure Monitor. Recursos como análise de dados, visualizações ou alerta exigem que você entenda as diferenças para que você possa implementar o seu cenário necessário da maneira mais eficiente e econômica. Insights no Azure Monitor, como [Application Insights](../app/app-insights-overview.md) ou [Azure Monitor for VMs,](../insights/vminsights-overview.md) possuem ferramentas de análise que permitem que você se concentre no cenário de monitoramento particular sem ter que entender as diferenças entre os dois tipos de dados. 


### <a name="metrics"></a>Métricas
[Métricas](data-platform-metrics.md) são valores numéricos que descrevem algum aspecto de um sistema em um determinado ponto do tempo. Eles são coletados em intervalos regulares e são identificados com um carimbo de data e hora, um nome, um valor e um ou mais rótulos definidores. As métricas podem ser agregadas usando uma variedade de algoritmos, em comparação com outras métricas, e analisadas para tendências ao longo do tempo. 

As métricas no Azure Monitor são armazenadas em um banco de dados de séries temporias que é otimizado para analisar dados carimbados por tempo. Isso torna as métricas particularmente adequadas para alertar e detectar rapidamente problemas. Eles podem dizer como seu sistema está funcionando, mas normalmente precisam ser combinados com logs para identificar a causa raiz dos problemas.

As métricas estão disponíveis para análise interativa no portal Azure com [o Azure Metrics Explorer](../platform/metrics-getting-started.md). Eles podem ser adicionados a um [painel do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados e usados para [alertas](alerts-metric.md)quase em tempo real .

Leia mais sobre o Azure Monitor Metrics, incluindo suas fontes de dados em [Métricas no Azure Monitor](data-platform-metrics.md).

### <a name="logs"></a>Logs
[Logs](data-platform-logs.md) são eventos que ocorreram dentro do sistema. Eles podem conter diferentes tipos de dados e podem ser estruturados ou texto de formulário livre com um carimbo de data e hora. Eles podem ser criados esporadicamente à medida que eventos no ambiente geram entradas de log, e um sistema sob carga pesada normalmente gerará mais volume de log.

Os logs no Azure Monitor são armazenados em um espaço de trabalho do Log Analytics baseado no [Azure Data Explorer,](/azure/data-explorer/) que fornece um poderoso mecanismo de análise e [uma linguagem de consulta rica.](/azure/kusto/query/) Os registros normalmente fornecem informações suficientes para fornecer o contexto completo do problema que está sendo identificado e são valiosos para identificar casos raiz de problemas.

> [!NOTE]
> É importante distinguir entre registros do Monitor Do Azure e fontes de dados de log no Azure. Por exemplo, eventos de nível de assinatura no Azure são gravados em um registro de [atividades](platform-logs-overview.md) que você pode exibir no menu do Monitor do Azure. A maioria dos recursos gravará informações operacionais em um [registro de recursos](platform-logs-overview.md) que você pode encaminhar para diferentes locais. O Azure Monitor Logs é uma plataforma de dados de log que coleta registros de atividades e registros de recursos, juntamente com outros dados de monitoramento para fornecer análises profundas em todo o seu conjunto de recursos.


 Você pode trabalhar com [consultas de log](../log-query/log-query-overview.md) interativamente com o Log [Analytics](../log-query/portals.md) no portal Azure ou adicionar os resultados a um [painel do Azure](../learn/tutorial-app-dashboards.md) para visualização em combinação com outros dados. Você também pode criar [alertas de log](alerts-log.md) que desencadearão um alerta com base nos resultados de uma consulta de agendamento.

Leia mais sobre o Azure Monitor Logs, incluindo suas fontes de dados no [Logs in Azure Monitor](data-platform-logs.md).

### <a name="distributed-traces"></a>Traços distribuídos
Os traces são uma série de eventos relacionados que seguem uma solicitação do usuário através de um sistema distribuído. Eles podem ser usados para determinar o comportamento do código do aplicativo e o desempenho de diferentes transações. Embora os logs sejam frequentemente criados por componentes individuais de um sistema distribuído, um rastreamento mede a operação e o desempenho de sua aplicação em todo o conjunto de componentes.

O rastreamento distribuído no Azure Monitor é habilitado com o [Application Insights SDK](../app/distributed-tracing.md), e os dados de rastreamento são armazenados com outros dados de registro de aplicativos coletados pelo Application Insights. Isso o disponibiliza para as mesmas ferramentas de análise que outros dados de log, incluindo consultas de log, painéis e alertas.

Leia mais sobre rastreamento distribuído no [Que é Traçado Distribuído?](../app/distributed-tracing.md).


## <a name="compare-azure-monitor-metrics-and-logs"></a>Compare métricas e logs do Monitor Do Azure

A tabela a seguir compara Métricas e Logs no Monitor Do Azure.

| Atributo  | Métricas | Logs |
|:---|:---|:---|
| Benefícios | Leve e capaz de cenários quase reais, como alerta. Ideal para detecção rápida de problemas. | Analisada com linguagem de consulta rica. Ideal para análise profunda e identificação da causa raiz. |
| Dados | Valores numéricos apenas | Texto ou dados numéricos |
| Estrutura | Conjunto padrão de propriedades, incluindo tempo de amostra, recurso sendo monitorado, um valor numérico. Algumas métricas incluem múltiplas dimensões para uma definição posterior. | Conjunto único de propriedades, dependendo do tipo de registro. |
| Coleção | Coletados em intervalos regulares. | Pode ser coletado esporadicamente à medida que os eventos desencadeiam um registro a ser criado. |
| Exibir no portal do Azure | Metrics Explorer | Log Analytics |
| As fontes de dados incluem | Métricas da plataforma coletadas dos recursos do Azure.<br>Aplicativos monitorados pelo Application Insights.<br>Personalizado definido por aplicativo ou API. | Registros de aplicativos e recursos.<br>Soluções de monitoramento.<br>Agentes e extensões vm.<br>Solicitações de inscrição e exceções.<br>Centro de Segurança Azure.<br>API de coletor de dados. |

## <a name="collect-monitoring-data"></a>Coletar dados de monitoramento
Diferentes [fontes de dados do Azure Monitor](data-sources.md) serão escritas em log analytics (Logs) ou no banco de dados de métricas do Azure Monitor (Metrics) ou ambos. Algumas fontes gravarão diretamente nesses armazenamentos de dados, enquanto outras podem escrever para outro local, como o armazenamento Azure e exigir alguma configuração para preencher logs ou métricas. 

Consulte [Métricas no Monitor do Azure](data-platform-metrics.md) e [Logs no Monitor Do Azure](data-platform-logs.md) para obter uma lista de diferentes fontes de dados que preencham cada tipo.


## <a name="stream-data-to-external-systems"></a>Transmitir dados para sistemas externos
Além de usar as ferramentas no Azure para analisar os dados de monitoramento, pode haver a exigência de encaminhá-los para uma ferramenta externa, como um produto SIEM (gerenciamento de eventos e informações de segurança). Esse encaminhamento normalmente é feito diretamente de recursos monitorados por meio dos [Hubs de Eventos](/azure/event-hubs/). Algumas fontes podem ser configuradas para enviar dados diretamente para um hub de eventos, enquanto você pode usar outro processo, como um App Lógico para recuperar os dados necessários. Consulte [os dados de monitoramento do Stream Azure em um centro de eventos para consumo por uma ferramenta externa](stream-monitoring-data-event-hubs.md) para obter detalhes.



## <a name="next-steps"></a>Próximas etapas

- Leia mais sobre [métricas no Monitor Azure](data-platform-metrics.md).
- Leia mais sobre [logs no Azure Monitor](data-platform-logs.md).
- Saiba mais sobre os [dados de monitoramento disponíveis](data-sources.md) para diferentes recursos no Azure.
