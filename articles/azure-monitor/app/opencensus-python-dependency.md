---
title: Rastreamento de dependência no Azure Application Insights com OpenCensus Python | Microsoft Docs
description: Monitore as chamadas de dependência para seus aplicativos Python via OpenCensus Python.
ms.topic: conceptual
author: lzchen
ms.author: lechen
ms.date: 10/15/2019
ms.openlocfilehash: e400669fd96518adead74a81fc332767c5f9b23b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669923"
---
# <a name="track-dependencies-with-opencensus-python"></a>Rastrear dependências com OpenCensus Python

Uma dependência é um componente externo que é chamado pelo seu aplicativo. Os dados de dependência são coletados usando o OpenCensus Python e suas várias integrações. Os dados são então enviados para o `dependencies` Application Insights no Azure Monitor como telemetria.

Primeiro, instrumente seu aplicativo Python com o Mais recente [OpenCensus Python SDK](../../azure-monitor/app/opencensus-python.md).

## <a name="in-process-dependencies"></a>Dependências em processo

OpenCensus Python SDK for Azure Monitor permite que você envie telemetria de dependência "em processo" (informações e lógica que ocorrem dentro do seu aplicativo). As dependências em processo `type` terão o campo como `INPROC` em análises.

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='foo'): # <-- A dependency telemetry item will be sent for this span "foo"
    print('Hello, World!')
```

## <a name="dependencies-with-requests-integration"></a>Dependências com integração de "pedidos"

Acompanhe suas solicitações de `requests` saída com a integração OpenCensus.

Baixe e `opencensus-ext-requests` instale do [PyPI](https://pypi.org/project/opencensus-ext-requests/) e adicione-o às integrações de rastreamento. As solicitações enviadas usando a biblioteca [de solicitações](https://pypi.org/project/requests/) Python serão rastreadas.

```python
import requests
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['requests'])  # <-- this line enables the requests integration

tracer = Tracer(exporter=AzureExporter(connection_string="InstrumentationKey=<your-ikey-here>"), sampler=ProbabilitySampler(1.0))

with tracer.span(name='parent'):
    response = requests.get(url='https://www.wikipedia.org/wiki/Rabbit') # <-- this request will be tracked
```

## <a name="dependencies-with-httplib-integration"></a>Dependências com integração "httplib"

Acompanhe suas solicitações de `httplib` saída com a integração OpenCensus.

Baixe e `opencensus-ext-httplib` instale do [PyPI](https://pypi.org/project/opencensus-ext-httplib/) e adicione-o às integrações de rastreamento. As solicitações enviadas usando [http.client](https://docs.python.org/3.7/library/http.client.html) para Python3 ou [httplib](https://docs.python.org/2/library/httplib.html) for Python2 serão rastreadas.

```python
import http.client as httplib
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace import config_integration
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

config_integration.trace_integrations(['httplib'])
conn = httplib.HTTPConnection("www.python.org")

tracer = Tracer(
    exporter=AzureExporter(),
    sampler=ProbabilitySampler(1.0)
)

conn.request("GET", "http://www.python.org", "", {})
response = conn.getresponse()
conn.close()
```

## <a name="dependencies-with-django-integration"></a>Dependências com integração "django"

Acompanhe suas solicitações de Django `django` de saída com a integração OpenCensus.

Baixe e `opencensus-ext-django` instale a partir de `MIDDLEWARE` [PyPI](https://pypi.org/project/opencensus-ext-django/) e adicione a seguinte linha à seção no arquivo Django. `settings.py`

```python
MIDDLEWARE = [
    ...
    'opencensus.ext.django.middleware.OpencensusMiddleware',
]
```

Configuração adicional pode ser fornecida, ler [personalizações](https://github.com/census-instrumentation/opencensus-python#customization) para uma referência completa.

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

## <a name="dependencies-with-mysql-integration"></a>Dependências com integração "mysql"

Acompanhe suas dependências MYSQL `mysql` com a integração OpenCensus. Essa integração suporta a biblioteca [mysql-connector.](https://pypi.org/project/mysql-connector-python/)

Baixe e `opencensus-ext-mysql` instale [do PyPI](https://pypi.org/project/opencensus-ext-mysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['mysql'])
```

## <a name="dependencies-with-pymysql-integration"></a>Dependências com integração "pymysql"

Acompanhe suas dependências PyMySQL `pymysql` com a integração OpenCensus.

Baixe e `opencensus-ext-pymysql` instale [do PyPI](https://pypi.org/project/opencensus-ext-pymysql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymysql'])
```

## <a name="dependencies-with-postgresql-integration"></a>Dependências com integração "postgresql"

Acompanhe suas dependências do PostgreSQL com a integração OpenCensus. `postgresql` Essa integração suporta a biblioteca [psycopg2.](https://pypi.org/project/psycopg2/)

Baixe e `opencensus-ext-postgresql` instale [do PyPI](https://pypi.org/project/opencensus-ext-postgresql/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['postgresql'])
```

## <a name="dependencies-with-pymongo-integration"></a>Dependências com integração "pymongo"

Acompanhe suas dependências do MongoDB com a integração OpenCensus. `pymongo` Essa integração suporta a biblioteca [pymongo.](https://pypi.org/project/pymongo/)

Baixe e `opencensus-ext-pymongo` instale [do PyPI](https://pypi.org/project/opencensus-ext-pymongo/) e adicione as seguintes linhas ao seu código.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['pymongo'])
```

### <a name="dependencies-with-sqlalchemy-integration"></a>Dependências com integração "sqlalchemy"

Acompanhe suas dependências usando SQLAlchemy usando a integração OpenCensus. `sqlalchemy` Essa integração acompanha o uso do pacote [de sqlalchemy,](https://pypi.org/project/SQLAlchemy/) independentemente do banco de dados subjacente.

```python
from opencensus.trace import config_integration

config_integration.trace_integrations(['sqlalchemy'])
```

## <a name="next-steps"></a>Próximas etapas

* [Mapa do aplicativo](../../azure-monitor/app/app-map.md)
* [Disponibilidade](../../azure-monitor/app/monitor-web-app-availability.md)
* [Pesquisar](../../azure-monitor/app/diagnostic-search.md)
* [Consulta de log (Analytics)](../../azure-monitor/log-query/log-query-overview.md)
* [Diagnóstico da transação](../../azure-monitor/app/transaction-diagnostics.md)
