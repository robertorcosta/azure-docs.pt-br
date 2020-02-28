---
title: Acompanhamento de solicitação de entrada no Aplicativo Azure insights com Python OpenCensus | Microsoft Docs
description: Monitore chamadas de solicitação para seus aplicativos Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: 0396bd8d150c6145a39f36e7be9e6e2dcacef2c4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669940"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Acompanhar solicitações de entrada com Python OpenCensus

Os dados de solicitação de entrada são coletados usando Python OpenCensus e suas várias integrações. Acompanhe os dados de solicitação de entrada enviados aos seus aplicativos Web criados com base nas estruturas web populares `django`, `flask` e `pyramid`. Em seguida, os dados são enviados para Application Insights em Azure Monitor como telemetria `requests`.

Primeiro, instrumentar seu aplicativo Python com o [SDK do Python OpenCensus](../../azure-monitor/app/opencensus-python.md)mais recente.

## <a name="tracking-django-applications"></a>Acompanhamento de aplicativos Django

1. Baixe e instale o `opencensus-ext-django` do [PyPI](https://pypi.org/project/opencensus-ext-django/) e Instrumente seu aplicativo com o middleware de `django`. As solicitações de entrada enviadas ao seu aplicativo `django` serão rastreadas.

2. Inclua `opencensus.ext.django.middleware.OpencensusMiddleware` em seu arquivo de `settings.py` em `MIDDLEWARE`.

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Verifique se o AzureExporter está configurado corretamente em seu `settings.py` em `OPENCENSUS`.

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

4. Você também pode adicionar URLs a `settings.py` em `BLACKLIST_PATHS` para solicitações que você não deseja controlar.

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

## <a name="tracking-flask-applications"></a>Acompanhamento de aplicativos Flask

1. Baixe e instale o `opencensus-ext-flask` do [PyPI](https://pypi.org/project/opencensus-ext-flask/) e Instrumente seu aplicativo com o middleware de `flask`. As solicitações de entrada enviadas ao seu aplicativo `flask` serão rastreadas.

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

2. Você pode configurar seu middleware `flask` diretamente no código. Para solicitações de URLs que você não deseja controlar, adicione-as ao `BLACKLIST_PATHS`.

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

## <a name="tracking-pyramid-applications"></a>Rastrear aplicativos de pirâmide

1. Baixe e instale o `opencensus-ext-django` do [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e Instrumente seu aplicativo com a interpolação de `pyramid`. As solicitações de entrada enviadas ao seu aplicativo `pyramid` serão rastreadas.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Você pode configurar sua `pyramid` interpolação diretamente no código. Para solicitações de URLs que você não deseja controlar, adicione-as ao `BLACKLIST_PATHS`.

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

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Pesquisar](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnóstico de transação](../../azure-monitor/app/transaction-diagnostics.md)
