---
title: Monitorar aplicativos Python com Azure Monitor (versão prévia) | Microsoft Docs
description: Fornece instruções para conectar o Python OpenCensus com o Azure Monitor
ms.topic: conceptual
author: reyang
ms.author: reyang
ms.date: 10/11/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 6b8343d08962d8ce749e1160b0226b68571571f8
ms.sourcegitcommit: fc0431755effdc4da9a716f908298e34530b1238
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/24/2020
ms.locfileid: "83815716"
---
# <a name="set-up-azure-monitor-for-your-python-application"></a>Configurar Azure Monitor para seu aplicativo Python

O Azure Monitor permite o rastreamento distribuído, a coleta de métricas e o log de aplicativos Python por meio da integração com o [OpenCensus](https://opencensus.io). Este artigo orientará você durante o processo de configuração do OpenCensus para Python e o envio de dados de monitoramento para o Azure Monitor.

## <a name="prerequisites"></a>Pré-requisitos

- Uma assinatura do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Instalação do Python. Este artigo usa o [Python 3.7.0](https://www.python.org/downloads/), embora versões anteriores provavelmente funcionem com pequenas alterações.

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [portal do Azure](https://portal.azure.com/).

## <a name="create-an-application-insights-resource-in-azure-monitor"></a>Criar um recurso do Application Insights no Azure Monitor

Inicialmente, crie um recurso do Application Insights no Azure Monitor que vai gerar uma chave de instrumentação (ikey). Em seguida, o iKey é usado para configurar o SDK do OpenCensus para enviar dados de telemetria para Azure Monitor.

1. Selecione **Criar um recurso** > **Ferramentas de desenvolvedor** > **Application Insights**.

   ![Adicione um recurso do Application Insights](./media/opencensus-python/0001-create-resource.png)

1. Será exibida uma caixa de configuração. Use a tabela a seguir para preencher os campos de entrada.

   | Configuração        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor globalmente exclusivo | Nome que identifica o aplicativo que você está monitorando |
   | **Grupo de recursos**     | myResourceGroup      | Nome do novo grupo de recursos no qual hospedar dados do Application Insights |
   | **Localidade** | Leste dos EUA | Uma localização perto de você ou de onde seu aplicativo está hospedado |

1. Selecione **Criar**.

## <a name="instrument-with-opencensus-python-sdk-for-azure-monitor"></a>Instrumento com SDK de Python OpenCensus para o Azure Monitor

Instale exportadores OpenCensus Azure Monitor:

```console
python -m pip install opencensus-ext-azure
```

Para obter uma lista completa de pacotes e integrações, consulte [Pacotes OpenCensus](https://docs.microsoft.com/azure/azure-monitor/app/nuget#common-packages-for-python-using-opencensus).

> [!NOTE]
> O comando `python -m pip install opencensus-ext-azure` pressupõe que você tenha uma variável de ambiente `PATH` definida para sua instalação do Python. Se você não tiver configurado essa variável, precisará fornecer o caminho completo do diretório para o local em que o executável do Python está localizado. Execute um comando como este: `C:\Users\Administrator\AppData\Local\Programs\Python\Python37-32\python.exe -m pip install opencensus-ext-azure`.

O SDK usa três exportadores Azure Monitor para enviar tipos diferentes de telemetria para o Azure Monitor: rastreamento, métricas e logs. Para obter mais informações sobre esses tipos de telemetria, consulte [Visão geral da plataforma de dados](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform). Use as instruções a seguir para enviar esses tipos de telemetria por meio dos três exportadores.

## <a name="telemetry-type-mappings"></a>Mapeamentos do tipo de telemetria

Aqui estão os exportadores que o OpenCensus fornece mapeados para os tipos de telemetria que você verá no Azure Monitor.

![Captura de tela do mapeamento de tipos de telemetria do OpenCensus para o Azure Monitor](./media/opencensus-python/0012-telemetry-types.png)

### <a name="trace"></a>Trace

> [!NOTE]
> `Trace` em OpenCensus refere-se a [rastreamento distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing). O `AzureExporter` envia a telemetria `requests` e `dependency` para o Azure Monitor.

1. Primeiro vamos gerar alguns dados de rastreamento localmente. No Python IDLE, ou seu editor preferencial, digite o seguinte código.

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

2. Executar o código solicitará repetidamente que você insira um valor. A cada entrada, o valor será impresso no shell, e o módulo OpenCensus Python vai gerar uma parte correspondente de `SpanData`. O projeto OpenCensus define um [rastreamento como uma árvore de spans](https://opencensus.io/core-concepts/tracing/).
    
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

3. Embora a inserção de valores seja útil para fins de demonstração, na realidade, desejamos emitir o `SpanData` para o Azure Monitor. Passe a cadeia de conexão diretamente no exportador ou você pode especificá-la em uma variável de ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modifique seu código da etapa anterior com base no seguinte exemplo de código:

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

4. Agora, quando você executa o script Python, ainda deve ser solicitado a inserir valores, mas apenas o valor que está sendo impresso no shell. O `SpanData` criado será enviado para o Azure Monitor. Você pode encontrar os dados de span emitidos em `dependencies`. Para obter mais detalhes sobre as solicitações de saída, consulte [as dependências](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-dependency) de OpenCensus Python.
Para obter mais detalhes sobre as solicitações de chegada, consulte as [solicitações](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python-request) de OpenCensus Python.

#### <a name="sampling"></a>amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada em [amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="trace-correlation"></a>Correlação de rastreamento

Para obter detalhes sobre a correlação de telemetria em seus dados de rastreamento, confira a [correlação de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/correlation#telemetry-correlation-in-opencensus-python) do OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificar telemetria

Para obter detalhes sobre como modificar a telemetria acompanhada antes de enviá-la para Azure Monitor, consulte os [processadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) do OpenCensus Python.

### <a name="metrics"></a>Métricas

1. Primeiro vamos gerar alguns dados métricos locais. Vamos criar uma métrica simples para acompanhar o número de vezes que o usuário pressiona ENTER.

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
2. Executar o código solicitará repetidamente que você insira Enter. Uma métrica é criada para rastrear o número de vezes que Enter é pressionado. Com cada entrada, o valor será incrementado e as informações de métrica serão exibidas no console. As informações incluem o valor atual e o carimbo de data/hora atual quando a métrica foi atualizada.

    ```
    Press enter.
    Point(value=ValueLong(5), timestamp=2019-10-09 20:58:04.930426)
    Press enter.
    Point(value=ValueLong(6), timestamp=2019-10-09 20:58:06.570167)
    Press enter.
    Point(value=ValueLong(7), timestamp=2019-10-09 20:58:07.138614)
    ```

3. Embora a inserção de valores seja útil para fins de demonstração, na realidade, desejamos emitir os dados de métrica para o Azure Monitor. Passe a cadeia de conexão diretamente no exportador ou você pode especificá-la em uma variável de ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modifique seu código da etapa anterior com base no seguinte exemplo de código:

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

4. O exportador enviará dados de métrica para o Azure Monitor em um intervalo fixo. O padrão é a cada 15 segundos. Estamos acompanhando uma única métrica, portanto, esses dados de métrica, com qualquer valor e carimbo de data/hora que ele contém, serão enviados a cada intervalo. Você pode encontrar os dados em `customMetrics`.

#### <a name="standard-metrics"></a>Métricas padrão

Por padrão, o exportador de métricas enviará um conjunto de métricas padrão para o Azure Monitor. Você pode desabilitar isso definindo o sinalizador `enable_standard_metrics` como `False` no construtor do exportador de métricas.

```python
...
exporter = metrics_exporter.new_metrics_exporter(
  enable_standard_metrics=False,
  connection_string='InstrumentationKey=<your-instrumentation-key-here>')
...
```
Abaixo está uma lista de métricas padrão que são enviadas atualmente:

- Memória disponível (bytes)
- Tempo do processador da CPU (percentual)
- Taxa de solicitação de entrada (por segundo)
- Tempo médio de execução da solicitação de entrada (milissegundos)
- Taxa de solicitação de saída (por segundo)
- Uso da CPU do processo (percentual)
- Bytes privados do processo (bytes)

Você deve ser capaz de ver essas métricas em `performanceCounters`. A taxa de solicitação de entrada estaria em `customMetrics`. Para obter mais informações, consulte [Contadores de desempenho](https://docs.microsoft.com/azure/azure-monitor/app/performance-counters).

#### <a name="modify-telemetry"></a>Modificar telemetria

Para obter detalhes sobre como modificar a telemetria acompanhada antes de enviá-la para Azure Monitor, consulte os [processadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) do OpenCensus Python.

### <a name="logs"></a>Logs

1. Primeiro vamos gerar alguns dados de log locais.

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

2.  O código solicitará continuamente um valor a ser inserido. Uma entrada de log é emitida para cada valor inserido.

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

3. Embora a inserção de valores seja útil para fins de demonstração, na realidade, desejamos emitir os dados de log para o Azure Monitor. Passe a cadeia de conexão diretamente no exportador ou você pode especificá-la em uma variável de ambiente `APPLICATIONINSIGHTS_CONNECTION_STRING`. Modifique seu código da etapa anterior com base no seguinte exemplo de código:

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

4. O exportador enviará dados de log para o Azure Monitor. Você pode encontrar os dados em `traces`. 

    > [!NOTE]
    > `traces` nesse contexto, não é o mesmo que `Tracing`. `traces` refere-se ao tipo de telemetria que você verá no Azure Monitor ao utilizar o `AzureLogHandler`. `Tracing` refere-se a um conceito em OpenCensus e está relacionado a [rastreamento distribuído](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing).

5. Para formatar suas mensagens de log, você pode usar `formatters` na [API de registro em log](https://docs.python.org/3/library/logging.html#formatter-objects) do Python.

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

6. Você também pode adicionar propriedades personalizadas às suas mensagens de log no argumento de palavra-chave *extra*, usando o campo custom_dimensions. Elas serão exibidas como pares de chave-valor em `customDimensions` no Azure Monitor.
    > [!NOTE]
    > Para que esse recurso funcione, você precisa passar um dicionário para o campo custom_dimensions. Se você passar argumentos de qualquer outro tipo, o agente vai ignorá-los.

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

#### <a name="sending-exceptions"></a>Exceções no envio

O OpenCensus Python não rastreia e envia automaticamente a telemetria de `exception`. Eles são enviados por `AzureLogHandler` usando exceções por meio da biblioteca de log do Python. Você pode adicionar propriedades personalizadas, assim como com o log normal.

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
Como você deve registrar exceções explicitamente, cabe ao usuário saber como deseja registrar exceções sem tratamento. O OpenCensus não coloca restrições na forma como um usuário deseja fazer isso, desde que registre explicitamente uma telemetria de exceção.

#### <a name="sampling"></a>amostragem

Para obter informações sobre amostragem no OpenCensus, dê uma olhada em [amostragem no OpenCensus](sampling.md#configuring-fixed-rate-sampling-for-opencensus-python-applications).

#### <a name="log-correlation"></a>Correlação de logs

Para obter detalhes sobre como enriquecer seus logs com dados de contexto de rastreamento, consulte a [integração de logs](https://docs.microsoft.com/azure/azure-monitor/app/correlation#log-correlation) do OpenCensus Python.

#### <a name="modify-telemetry"></a>Modificar telemetria

Para obter detalhes sobre como modificar a telemetria acompanhada antes de enviá-la para Azure Monitor, consulte os [processadores de telemetria](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#opencensus-python-telemetry-processors) do OpenCensus Python.

## <a name="view-your-data-with-queries"></a>Visualização dos seus dados com consultas

Você pode exibir os dados de telemetria que foram enviados do seu aplicativo por meio da guia **Logs (Análise)** .

![Captura de tela do painel Visão geral com "Logs (Análise)" selecionado em uma caixa vermelha](./media/opencensus-python/0010-logs-query.png)

Na lista em **Ativo**:

- Para a telemetria enviada com o exportador de rastreamento do Azure Monitor, as solicitações de entrada aparecem em `requests`. As solicitações de saída ou em andamento aparecem em `dependencies`.
- Para a telemetria enviada com o exportador de métricas do Azure Monitor, as métricas enviadas aparecem em `customMetrics`.
- Para a telemetria enviada com o exportador de logs do Azure Monitor, os logs aparecem em `traces`. As exceções aparecem em `exceptions`.

Para obter informações mais detalhadas sobre como usar consultas e logs, consulte [Logs no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs).

## <a name="learn-more-about-opencensus-for-python"></a>Saiba mais sobre o OpenCensus para Python

* [OpenCensus Python no GitHub](https://github.com/census-instrumentation/opencensus-python)
* [Personalização](https://github.com/census-instrumentation/opencensus-python/blob/master/README.rst#customization)
* [Exportadores Azure Monitor no GitHub](https://github.com/census-instrumentation/opencensus-python/tree/master/contrib/opencensus-ext-azure)
* [OpenCensus Integrations](https://github.com/census-instrumentation/opencensus-python#extensions)
* [Aplicativos de exemplo do Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opencensus-python)

## <a name="next-steps"></a>Próximas etapas

* [Acompanhar soluções de entrada](./../../azure-monitor/app/opencensus-python-dependency.md)
* [Acompanhar soluções de saída](./../../azure-monitor/app/opencensus-python-request.md)
* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)

### <a name="alerts"></a>Alertas

* [Testes de disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md): Crie testes para garantir que o site esteja visível na Web.
* [Diagnóstico inteligente](../../azure-monitor/app/proactive-diagnostics.md): Esses testes são executados automaticamente, portanto, nenhuma configuração adicional será necessária. Eles informam se o aplicativo tem uma taxa incomum de solicitações com falha.
* [Alertas de métrica](../../azure-monitor/platform/alerts-log.md): Definir alertas para avisar se uma métrica ultrapassar um limite. Você pode defini-los em métricas personalizadas que você codifica em seu aplicativo.
