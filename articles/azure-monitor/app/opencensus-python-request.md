---
title: Acompanhamento de solicitação de entrada no Aplicativo Azure insights com Python OpenCensus | Microsoft Docs
description: Monitore chamadas de solicitação para seus aplicativos Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.custom: devx-track-python
ms.openlocfilehash: c94bc949f13ee19a9d2150c9d3c1b6a2bdb959b2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87850059"
---
# <a name="track-incoming-requests-with-opencensus-python"></a>Acompanhar solicitações de entrada com Python OpenCensus

Os dados de solicitação de entrada são coletados usando Python OpenCensus e suas várias integrações. Acompanhe os dados de solicitação de entrada enviados aos seus aplicativos Web criados com base nas estruturas populares da Web `django` `flask` e no `pyramid` . Os dados são enviados para Application Insights em Azure Monitor como `requests` telemetria.

Primeiro, instrumentar seu aplicativo Python com o [SDK do Python OpenCensus](./opencensus-python.md)mais recente.

## <a name="tracking-django-applications"></a>Acompanhamento de aplicativos Django

1. Baixe e instale `opencensus-ext-django` do [PyPI](https://pypi.org/project/opencensus-ext-django/) e Instrumente seu aplicativo com o `django` middleware. As solicitações de entrada enviadas ao seu `django` aplicativo serão rastreadas.

2. Inclua `opencensus.ext.django.middleware.OpencensusMiddleware` em seu `settings.py` arquivo em `MIDDLEWARE` .

    ```python
    MIDDLEWARE = (
        ...
        'opencensus.ext.django.middleware.OpencensusMiddleware',
        ...
    )
    ```

3. Verifique se o AzureExporter está configurado corretamente no seu `settings.py` em `OPENCENSUS` . Para solicitações de URLs que você não deseja controlar, adicione-as ao `BLACKLIST_PATHS` .

    ```python
    OPENCENSUS = {
        'TRACE': {
            'SAMPLER': 'opencensus.trace.samplers.ProbabilitySampler(rate=1)',
            'EXPORTER': '''opencensus.ext.azure.trace_exporter.AzureExporter(
                connection_string="InstrumentationKey=<your-ikey-here>"
            )''',
            'BLACKLIST_PATHS': ['https://example.com'],  <--- These sites will not be traced if a request is sent to it.
        }
    }
    ```

## <a name="tracking-flask-applications"></a>Acompanhamento de aplicativos Flask

1. Baixe e instale `opencensus-ext-flask` do [PyPI](https://pypi.org/project/opencensus-ext-flask/) e Instrumente seu aplicativo com o `flask` middleware. As solicitações de entrada enviadas ao seu `flask` aplicativo serão rastreadas.

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

2. Você também pode configurar seu `flask` aplicativo por meio do `app.config` . Para solicitações de URLs que você não deseja controlar, adicione-as ao `BLACKLIST_PATHS` .

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

1. Baixe e instale `opencensus-ext-django` do [PyPI](https://pypi.org/project/opencensus-ext-pyramid/) e Instrumente seu aplicativo com a `pyramid` interpolação. As solicitações de entrada enviadas ao seu `pyramid` aplicativo serão rastreadas.

    ```python
    def main(global_config, **settings):
        config = Configurator(settings=settings)
    
        config.add_tween('opencensus.ext.pyramid'
                         '.pyramid_middleware.OpenCensusTweenFactory')
    ```

2. Você pode configurar sua `pyramid` interpolação diretamente no código. Para solicitações de URLs que você não deseja controlar, adicione-as ao `BLACKLIST_PATHS` .

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

* [Mapa do aplicativo](./app-map.md)
* [Disponibilidade](./monitor-web-app-availability.md)
* [Pesquisa](./diagnostic-search.md)
* [Consulta de log (Analytics)](../log-query/log-query-overview.md)
* [Diagnóstico da transação](./transaction-diagnostics.md)

