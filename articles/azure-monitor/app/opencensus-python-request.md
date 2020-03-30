---
title: Rastreamento de solicitação de entrada no Azure Application Insights com OpenCensus Python | Microsoft Docs
description: Monitore chamadas de solicitação para seus aplicativos Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669940"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Acompanhe as solicitações recebidas com o OpenCensus Python

Os dados de solicitação de entrada são coletados usando o OpenCensus Python e suas várias integrações. Rastreie os dados de solicitação de entrada enviados para `django` `flask` seus `pyramid`aplicativos web construídos em cima dos quadros web populares e . Os dados são então enviados para o `requests` Application Insights no Azure Monitor como telemetria.

Primeiro, instrumente seu aplicativo Python com o Mais recente [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="tracking-django-applications"></a>Rastreamento de aplicações Django

1. Baixe e `opencensus-ext-django` instale a partir de `django` [PyPI](https://pypi.org/project/opencensus-ext-django/) e instrumente seu aplicativo com o middleware. As solicitações recebidas enviadas ao seu `django` aplicativo serão rastreadas.

2. Inclua `opencensus.ext.django.middleware.OpencensusMiddleware` em `settings.py` seu `MIDDLEWARE`arquivo em .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Certifique-se de que o AzureExporter está configurado corretamente em seu `settings.py` under `OPENCENSUS`.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
        }
    }
    ```

4. Você também pode adicionar `settings.py` `BLACKLIST_PATHS` urls a under para solicitações que você não deseja rastrear.

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=0.5)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent from it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Rastreamento de aplicativos de frascos

1. Baixe e `opencensus-ext-flask` instale a partir de `flask` [PyPI](https://pypi.org/project/opencensus-ext-flask/) e instrumente seu aplicativo com o middleware. As solicitações recebidas enviadas ao seu `flask` aplicativo serão rastreadas.

    ```python
    
    from flask import Flask
    from opencensus.ext.azure.trace_exporter import AzureExporter
    from opencensus.ext.flask.flask_middleware import FlaskMiddleware
    from opencensus.trace.samplers import ProbabilitySampler
    
    app = Flask(__name__)
    middleware = FlaskMiddleware(
        app,
        exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"),
        sampler=ProbabilitySampler(rate=1.0),
    )
    
    @app.route('/')
    def hello():
        return 'Hello World!'
    
    if __name__ == '__main__':
        app.run(host='localhost', port=8080, threaded=True)
    
    ```

2. Você pode configurar `flask` seu middleware diretamente no código. Para solicitações de urls que você não `BLACKLIST_PATHS`deseja rastrear, adicione-os a .

    ```python
    app.config['OPENCENSUS'] = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>",
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-pyramid-applications"></a>Rastreamento de aplicativos de pirâmide

1. Baixe e `opencensus-ext-django` instale a partir de `pyramid` [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e instrumente seu aplicativo com a interpolação. As solicitações recebidas enviadas ao seu `pyramid` aplicativo serão rastreadas.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Você pode configurar `pyramid` sua interpolação diretamente no código. Para solicitações de urls que você não `BLACKLIST_PATHS`deseja rastrear, adicione-os a .

    ```python
    settings = {
        'OPENCENSUS': {
            'TRACE': {
                'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1.0)',
                'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                    connection_string="InstrumentationKey=<your-ikey-here>",
                )''',
                'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
            }
        }
    }
    config = Configurator(settings=settings)
    ```

## <a name="next-steps"></a>Próximas etapas

* [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Pesquisar](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnóstico da transação](../../azure-monitor/app/transaction-diagnostics.md)
