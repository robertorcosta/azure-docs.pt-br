---
title: Monitore aplicativos Python com monitor azure (visualização) | Microsoft Docs
description: Fornece instruções para conectar o OpenCensus Python com o Monitor Azure
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6ef0675e3ae3f7a5da38138177f3033051723411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537101"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configure o Monitor Azure para o seu aplicativo Python

O Azure Monitor suporta rastreamento distribuído, coleta métrica e registro de aplicativos Python por meio da integração com [o OpenCensus](https://opencensus.io). Este artigo irá levá-lo através do processo de configurar OpenCensus para Python e enviar seus dados de monitoramento para o Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalação python. Este artigo usa [python 3.7.0](https://www.python.org/downloads/), embora versões anteriores provavelmente funcionarão com pequenas alterações.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Faça login no [portal Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Crie um recurso de insights de aplicativos no Azure Monitor

Primeiro você tem que criar um recurso Application Insights no Azure Monitor, que irá gerar uma chave de instrumentação (ikey). O ikey é então usado para configurar o OpenCensus SDK para enviar dados de telemetria ao Azure Monitor.

1. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor Deaplicativos** > **Insights**.

   ![Adicionando um recurso de insights de aplicativos](./media/opencensus-python/0001-create-resource.png)

1. Uma caixa de configuração é exibida. Use a tabela a seguir para preencher os campos de entrada.

   | Configuração        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor globalmente único | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome do novo grupo de recursos para hospedar dados do Application Insights |
   | **Local** | Leste dos EUA | Um local perto de você, ou perto de onde seu aplicativo está hospedado |

1. Selecione **Criar**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumento com OpenCensus Python SDK para Monitor Azure

Instale os exportadores do OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

Para obter uma lista completa de pacotes e integrações, consulte [pacotes OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> O `python -m pip install opencensus-ext-azure` comando pressupõe `PATH` que você tenha uma variável de ambiente definida para sua instalação python. Se você não configurou essa variável, você precisa dar o caminho completo do diretório para onde o executável Python está localizado. O resultado é um `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`comando como este: .

O SDK usa três exportadores do Azure Monitor para enviar diferentes tipos de telemetria ao Azure Monitor: trace, métricas e logs. Para obter mais informações sobre esses tipos de telemetria, consulte [a visão geral da plataforma de dados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Use as seguintes instruções para enviar esses tipos de telemetria através dos três exportadores.

## <a name="telemetry-type-mappings"></a>Mapeamentos do tipo telemetria

Aqui estão os exportadores que o OpenCensus fornece mapeados para os tipos de telemetria que você verá no Azure Monitor.

![Captura de tela de mapeamento de tipos de telemetria do OpenCensus para o Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trace

> [!NOTE]
> `Trace`no OpenCensus refere-se ao [rastreamento distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). Os `AzureExporter` `requests` envios e `dependency` telemetria para o Monitor Azure.

1. Primeiro, vamos gerar alguns dados de rastreamento localmente. Em Python IDLE, ou seu editor de escolha, digite o seguinte código.

    ```python
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer

    tracer = Tracer(sampler=ProbabilitySampler(1.0))

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2. Executar o código solicitará repetidamente que você insira um valor. A cada entrada, o valor será impresso na concha, e o Módulo `SpanData`Python OpenCensus gerará uma peça correspondente de . O projeto OpenCensus define um [rastreamento como uma árvore de spans](https://opencensus.io/core-concepts/tracing/).
    
    ```
    Enter a value: 4
    4
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='15ac5123ac1f6847', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:22.805429Z', end_time='2019-06-27T18:21:44.933405Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 25
    25
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='2e512f846ba342de', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:44.933405Z', end_time='2019-06-27T18:21:46.156787Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    Enter a value: 100
    100
    [SpanData(name='test', context=SpanContext(trace_id=8aa41bc469f1a705aed1bdb20c342603, span_id=None, trace_options=TraceOptions(enabled=True), tracestate=None), span_id='f3f9f9ee6db4740a', parent_span_id=None, attributes=BoundedDict({}, maxlen=32), start_time='2019-06-27T18:21:46.157732Z', end_time='2019-06-27T18:21:47.269583Z', child_span_count=0, stack_trace=None, annotations=BoundedList([], maxlen=32), message_events=BoundedList([], maxlen=128), links=BoundedList([], maxlen=32), status=None, same_process_as_parent_span=None, span_kind=0)]
    ```

3. Embora inserir valores seja útil para fins de `SpanData` demonstração, em última análise queremos emitir o Monitor do Azure. Passe sua seqüência de conexão diretamente para o `APPLICATIONINSIGHTS_CONNECTION_STRING`exportador, ou você pode especificá-la em uma variável de ambiente . Modifique seu código da etapa anterior com base na seguinte amostra de código:

    ```python
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.trace.samplers import ProbabilitySampler
    from opencensus.trace.tracer import Tracer
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    tracer = Tracer(
        exporter=AzureExporter(
            connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'),
        sampler=ProbabilitySampler(1.0),
    )

    def valuePrompt():
        with tracer.span(name="test") as span:
            line = input("Enter a value: ")
            print(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

4. Agora, quando você executa o script Python, você ainda deve ser solicitado a inserir valores, mas apenas o valor está sendo impresso na shell. Os criados serão enviados `SpanData` ao Azure Monitor. Você pode encontrar os dados `dependencies`de extensão emitidos em . Para obter mais detalhes sobre solicitações de saída, consulte [as dependências](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency)do OpenCensus Python .
Para obter mais detalhes sobre as solicitações recebidas, consulte [solicitações](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request)openCensus Python .

#### <a name="sampling"></a>amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada [na amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlação de traços

Para obter detalhes sobre a correlação de telemetria em seus dados de rastreamento, dê uma olhada na [correlação de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python)OpenCensus Python .

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter detalhes sobre como modificar a telemetria rastreada antes de ser enviada ao Azure Monitor, consulte [processadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python .

### <a name="metrics"></a>Métricas

1. Primeiro, vamos gerar alguns dados métricos locais. Criaremos uma métrica simples para rastrear o número de vezes que o usuário pressiona Enter.

    ```python
    from datetime import datetime
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```
2. A execução do código solicitará repetidamente que você pressione Enter. Uma métrica é criada para rastrear o número de vezes que o Enter é pressionado. A cada entrada, o valor será incrementado e as informações métricas serão exibidas no console. As informações incluem o valor atual e o carimbo de hora atual quando a métrica foi atualizada.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Embora inserir valores seja útil para fins de demonstração, em última análise, queremos emitir os dados métricos para o Azure Monitor. Passe sua seqüência de conexão diretamente para o `APPLICATIONINSIGHTS_CONNECTION_STRING`exportador, ou você pode especificá-la em uma variável de ambiente . Modifique seu código da etapa anterior com base na seguinte amostra de código:

    ```python
    from datetime import datetime
    from opencensus.ext.azure import metrics_exporter
    from opencensus.stats import aggregation as aggregation_module
    from opencensus.stats import measure as measure_module
    from opencensus.stats import stats as stats_module
    from opencensus.stats import view as view_module
    from opencensus.tags import tag_map as tag_map_module

    stats = stats_module.stats
    view_manager = stats.view_manager
    stats_recorder = stats.stats_recorder
    
    prompt_measure = measure_module.MeasureInt("prompts",
                                               "number of prompts",
                                               "prompts")
    prompt_view = view_module.View("prompt view",
                                   "number of prompts",
                                   [],
                                   prompt_measure,
                                   aggregation_module.CountAggregation())
    view_manager.register_view(prompt_view)
    mmap = stats_recorder.new_measurement_map()
    tmap = tag_map_module.TagMap()

    # TODO: replace the all-zero GUID with your instrumentation key.
    exporter = metrics_exporter.new_metrics_exporter(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')

    view_manager.register_exporter(exporter)

    def prompt():
        input("Press enter.")
        mmap.measure_int_put(prompt_measure, 1)
        mmap.record(tmap)
        metrics = list(mmap.measure_to_view_map.get_metrics(datetime.utcnow()))
        print(metrics[0].time_series[0].points[0])

    def main():
        while True:
            prompt()

    if __name__ == "__main__":
        main()
    ```

4. O exportador enviará dados métricos para o Azure Monitor em um intervalo fixo. O padrão é a cada 15 segundos. Estamos rastreando uma única métrica, então esses dados métricos, com qualquer valor e carimbo de hora que contenham, serão enviados a cada intervalo. Você pode encontrar `customMetrics`os dados em .

#### <a name="standard-metrics"></a>Métricas padrão

Por padrão, o exportador de métricas enviará um conjunto de métricas padrão para o Azure Monitor. Você pode desabilitá-lo `enable_standard_metrics` definindo a bandeira para `False` o construtor do exportador de métricas.

    ```python
    ...
    exporter = metrics_exporter.new_metrics_exporter(
      enable_standard_metrics=False,
      connection_string='InstrumentationKey=<your-instrumentation-key-here>')
    ...
    ```
Abaixo está uma lista de métricas padrão que são enviadas atualmente:

- Memória Disponível (bytes)
- Tempo do processador da CPU (porcentagem)
- Taxa de solicitação recebida (por segundo)
- Tempo médio de execução da solicitação recebida (milissegundos)
- Taxa de solicitação de saída (por segundo)
- Uso da CPU do processo (porcentagem)
- Process private bytes (bytes)

Você deve ser capaz de `performanceCounters`ver essas métricas em . A taxa de solicitação `customMetrics`recebida seria inferior a . Para obter mais informações, consulte [contadores de desempenho](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter detalhes sobre como modificar a telemetria rastreada antes de ser enviada ao Azure Monitor, consulte [processadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python .

### <a name="logs"></a>Logs

1. Primeiro, vamos gerar alguns dados de log local.

    ```python
    import logging

    logger = logging.getLogger(__name__)

    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)

    def main():
        while True:
            valuePrompt()

    if __name__ == "__main__":
        main()
    ```

2.  O código pedirá continuamente que um valor seja inserido. Uma entrada de log é emitida para cada valor inserido.

    ```
    Enter a value: 24
    24
    Enter a value: 55
    55
    Enter a value: 123
    123
    Enter a value: 90
    90
    ```

3. Embora inserir valores seja útil para fins de demonstração, em última análise, queremos emitir os dados de log para o Azure Monitor. Passe sua seqüência de conexão diretamente para o `APPLICATIONINSIGHTS_CONNECTION_STRING`exportador, ou você pode especificá-la em uma variável de ambiente . Modifique seu código da etapa anterior com base na seguinte amostra de código:

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

4. O exportador enviará dados de registro para o Azure Monitor. Você pode encontrar `traces`os dados em . 

> [!NOTE]
> `traces`neste contexto não é `Tracing`o mesmo que . `traces`refere-se ao tipo de telemetria que você verá no `AzureLogHandler`Azure Monitor ao utilizar o . `Tracing`refere-se a um conceito no OpenCensus e diz respeito ao [rastreamento distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Para formatar suas mensagens `formatters` de log, você pode usar na [API](https://docs.python.org/3/library/logging.html#formatter-objects)de registro de python incorporada .

    ```python
    import logging
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    
    format_str = '%(asctime)s - %(levelname)-8s - %(message)s'
    date_format = '%Y-%m-%d %H:%M:%S'
    formatter = logging.Formatter(format_str, date_format)
    # TODO: replace the all-zero GUID with your instrumentation key.
    handler = AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    handler.setFormatter(formatter)
    logger.addHandler(handler)
    
    def valuePrompt():
        line = input("Enter a value: ")
        logger.warning(line)
    
    def main():
        while True:
            valuePrompt()
    
    if __name__ == "__main__":
        main()
    ```

6. Você também pode adicionar propriedades personalizadas às suas mensagens de registro no argumento de palavras-chave *extras* usando o campo custom_dimensions. Estes aparecerão como pares `customDimensions` de valor-chave no Monitor Azure.
> [!NOTE]
> Para que esse recurso funcione, você precisa passar um dicionário para o campo custom_dimensions. Se você passar argumentos de qualquer outro tipo, o madeireiro irá ignorá-los.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in logging statements
    logger.warning('action', extra=properties)
    ```

#### <a name="sending-exceptions"></a>Envio de exceções

OpenCensus Python não rastreia e `exception` envia automaticamente telemetria. Eles são enviados através do uso de `AzureLogHandler` exceções através da biblioteca de registro Python. Você pode adicionar propriedades personalizadas, assim como com o registro normal.

    ```python
    import logging
    
    from opencensus.ext.azure.log_exporter import AzureLogHandler
    
    logger = logging.getLogger(__name__)
    # TODO: replace the all-zero GUID with your instrumentation key.
    logger.addHandler(AzureLogHandler(
        connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000')
    )

    properties = {'custom_dimensions': {'key_1': 'value_1', 'key_2': 'value_2'}}

    # Use properties in exception logs
    try:
        result = 1 / 0  # generate a ZeroDivisionError
    except Exception:
        logger.exception('Captured an exception.', extra=properties)
    ```
Uma vez que você deve registrar exceções explicitamente, cabe ao usuário como ele deseja registrar exceções não tratadas. O OpenCensus não coloca restrições na forma como um usuário quer fazer isso, desde que registre explicitamente uma telemetria de exceção.

#### <a name="sampling"></a>amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada [na amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlação de log

Para obter detalhes sobre como enriquecer seus logs com dados de contexto de rastreamento, consulte [a integração de logs do](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation)OpenCensus Python .

#### <a name="modify-telemetry"></a>Modificar a telemetria

Para obter detalhes sobre como modificar a telemetria rastreada antes de ser enviada ao Azure Monitor, consulte [processadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors)OpenCensus Python .

## <a name="view-your-data-with-queries"></a>Veja seus dados com consultas

Você pode visualizar os dados de telemetria enviados do seu aplicativo através da guia **Logs (Analytics).**

![Captura de tela do painel de visão geral com "Logs (Analytics)" selecionado em uma caixa vermelha](./media/opencensus-python/0010-logs-query.png)

Na lista em **Ativo**:

- Para a telemetria enviada com o exportador de rastreamento `requests`azure Monitor, as solicitações recebidas aparecem. Solicitações de saída ou `dependencies`em processo aparecem em .
- Para a telemetria enviada com o exportador de métricas `customMetrics`do Azure Monitor, as métricas enviadas aparecem em .
- Para a telemetria enviada com o exportador de logs do Monitor Azure, os logs aparecem em `traces`. Exceções aparecem `exceptions`em .

Para obter informações mais detalhadas sobre como usar consultas e logs, consulte [Logs no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Saiba mais sobre opencensus para Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Exportadores do Monitor Azure no GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [Integrações OpenCensus](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Aplicativos de amostra do Monitor do Azure](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Próximas etapas

* [Rastreamento de solicitações recebidas](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Rastreamento de solicitações de saída](./../../azure-monitor/app/opencensus-python-request.md)
* [Mapa de aplicativos](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): criar testes para verificar se seu site está visível na web.
* [Inteligente diagnóstico](../../azure-monitor/app/proactive-diagnostics.md): esses testes são executados automaticamente, portanto você não precisa fazer nada para configurá-los. Eles informam se o aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas métricos](../../azure-monitor/app/alerts.md): Defina alertas para avisá-lo se uma métrica cruzar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.
