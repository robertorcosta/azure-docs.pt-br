---
title: Azure Application Insights – coleta automática de dependência | Microsoft Docs
description: O Application Insights coleta e visualiza dependências automaticamente
ms.topic: reference
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/06/2020
ms.openlocfilehash: 21e98ee9dc59e7520fb715f1146e492b9198f883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82891604"
---
# <a name="dependency-auto-collection"></a>Coleta automática de dependência

Veja abaixo a lista atualmente com suporte de chamadas de dependência que são detectadas automaticamente como dependências sem a necessidade de qualquer modificação adicional no código do aplicativo. Essas dependências são visualizadas nas exibições [Mapa do aplicativo](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) e [Diagnóstico de transação](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) do Application Insights. Se a dependência não estiver na lista abaixo, você ainda poderá acompanhá-la manualmente com um comando [acompanhar chamada de dependência](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Estruturas do aplicativo| Versões |
| ------------------------|----------|
| Web Forms do ASP.NET | 4.5+ |
| ASP.NET MVC | 4+ |
| ASP.NET Web API | 4.5+ |
| ASP.NET Core | 1.1+ |
| <b> Bibliotecas de comunicação</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0+, NuGet 4.3.0 |
| [Microsoft.Data.SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 – versão estável mais recente. (Veja a observação abaixo.)
| [SDK do Cliente dos Hubs de Eventos](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [SDK do Cliente do Barramento de Serviço](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Clientes de armazenamento</b>|  |
| ADO.NET | 4.5+ |

> [!NOTE]
> Há um [problema conhecido](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) com versões mais antigas do Microsoft. Data. SqlClient. É recomendável usar o 1.1.0 ou posterior para atenuar esse problema. O Entity Framework Core não é fornecido necessariamente com a versão estável mais recente do Microsoft. Data. SqlClient, então aconselhamos a confirmação de que você está em pelo menos 1.1.0 para evitar esse problema.   


## <a name="java"></a>Java
| Servidores de aplicativo | Versões |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Estruturas do aplicativo </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Servlet Java | 3.1+ |
| <b>Bibliotecas de comunicação</b> |  |
| [Cliente Apache HTTP](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Clientes de armazenamento</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (suporte beta)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Bibliotecas de log</b> | |
| [Logback](https://logback.qos.ch/) | 1+ |
| [Log4J](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Bibliotecas de métricas</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *Exceto o suporte reativo de programação.
> <br>†Exige a instalação do [Agente JVM](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Clientes de armazenamento</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2. x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2,16. x |
| [PostgreSQL](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [pg-pool](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Bibliotecas de log</b> | |
| [MMC](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Bibliotecas de comunicação | Versões |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Tudo |

## <a name="next-steps"></a>Próximas etapas

- Configurar o acompanhamento de dependência personalizado para [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Configurar o acompanhamento de dependência personalizado para [Java](../../azure-monitor/app/java-agent.md).
- Configure o acompanhamento de dependência personalizado para [Python OpenCensus](../../azure-monitor/app/opencensus-python-dependency.md).
- [Escrever telemetria de dependência personalizada](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Consulte [modelo de dados](../../azure-monitor/app/data-model.md) para modelo de dados e tipos do Application Insights.
- Confira as [plataformas](../../azure-monitor/app/platforms.md) com suporte do Application Insights.
