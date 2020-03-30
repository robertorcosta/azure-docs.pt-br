---
title: Escolha uma solução de processamento de fluxo e tempo real no Azure
description: Saiba como escolher as análises e tecnologias de processamento de streaming em tempo real para construir seu aplicativo no Azure.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 2146b1bd782aba5d98729a2d37d956744e469ba1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75860241"
---
# <a name="choose-a-real-time-analytics-and-streaming-processing-technology-on-azure"></a>Escolha uma tecnologia de análise e processamento de streaming em tempo real no Azure

Existem vários serviços disponíveis para análise em tempo real e processamento de streaming no Azure. Este artigo fornece as informações que você precisa para decidir qual tecnologia é a mais adequada para sua aplicação.

## <a name="when-to-use-azure-stream-analytics"></a>Quando usar o Azure Stream Analytics

O Azure Stream Analytics é o serviço recomendado para análise de fluxo no Azure. Destina-se a uma ampla gama de cenários que incluem, mas não se limitam a:

* Dashboards para visualização de dados
* [Alertas](stream-analytics-set-up-alerts.md) em tempo real de padrões ou anomalias temporais e espaciais
* ETL (extrair, transformar e carregar)
* [Padrão de Sourcing de Eventos](/azure/architecture/patterns/event-sourcing)
* [Borda IoT](stream-analytics-edge.md)

Adicionar um trabalho do Azure Stream Analytics ao seu aplicativo é a maneira mais rápida de colocar as análises de streaming em funcionamento no Azure, usando a linguagem SQL que você já conhece. O Azure Stream Analytics é um serviço de trabalho, então você não precisa gastar tempo gerenciando clusters, e não precisa se preocupar com o tempo de inatividade com um SLA de 99,9% no nível de trabalho. O faturamento também é feito no nível de trabalho, tornando os custos de inicialização baixos (uma Unidade de Streaming), mas escaláveis (até 192 Unidades de Streaming). É muito mais econômico executar alguns trabalhos do Stream Analytics do que executar e manter um cluster.

O Azure Stream Analytics tem uma rica experiência fora da caixa. Você pode aproveitar imediatamente os seguintes recursos sem qualquer configuração adicional:

* Operadores temporais incorporados, como [agregados janelas,](stream-analytics-window-functions.md)junções temporais e funções analíticas temporais.
* Adaptadores nativos de [entrada](stream-analytics-add-inputs.md) e [saída](stream-analytics-define-outputs.md) do Azure
* Suporte para [dados de referência](stream-analytics-use-reference-data.md) de mudança lenta (também conhecidos como tabelas de pesquisa), incluindo a junção com dados de referência geoespacial para geofencing.
* Soluções integradas, como [detecção de anomalias](stream-analytics-machine-learning-anomaly-detection.md)
* Várias janelas de tempo na mesma consulta
* Capacidade de compor múltiplos operadores temporais em seqüências arbitrárias.
* latência de ponta a ponta de 100 ms desde a entrada que chega aos Event Hubs, até o desembarque de saída em Event Hubs, incluindo o atraso de rede de e para hubs de eventos, com alta alta produção sustentada

## <a name="when-to-use-other-technologies"></a>Quando usar outras tecnologias

### <a name="you-want-to-write-udfs-udas-and-custom-deserializers-in-a-language-other-than-javascript-or-c"></a>Você deseja escrever UDFs, UDAs e desserializadores personalizados em uma linguagem diferente de JavaScript ou C #

O Azure Stream Analytics suporta funções definidas pelo usuário (UDF) ou agregados definidos pelo usuário (UDA) em JavaScript para trabalhos na nuvem e C# para trabalhos de IoT Edge. Desserializadores c# definidos pelo usuário também são suportados. Se você quiser implementar um desserializador, um UDF ou um UDA em outras línguas, como Java ou Python, você pode usar o Streaming Estruturado spark. Você também pode executar o Event Hubs **EventProcessorHost** em suas próprias máquinas virtuais para fazer processamento de streaming arbitrário.

### <a name="your-solution-is-in-a-multi-cloud-or-on-premises-environment"></a>Sua solução está em um ambiente multi-nuvem ou no local

O Azure Stream Analytics é a tecnologia proprietária da Microsoft e só está disponível no Azure. Se você precisar que sua solução seja portátil entre nuvens ou no local, considere tecnologias de código aberto, como o Spark Structured Streaming ou o Storm.

## <a name="next-steps"></a>Próximas etapas

* [Crie um trabalho de Stream Analytics usando o portal Azure](stream-analytics-quick-create-portal.md)
* [Criar um trabalho do Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Crie um trabalho de Stream Analytics usando o Visual Studio](stream-analytics-quick-create-vs.md)
* [Crie um trabalho de Stream Analytics usando o Visual Studio Code](quick-create-vs-code.md)
