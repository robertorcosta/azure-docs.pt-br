---
title: Monitore aplicativos Java em qualquer ambiente - Azure Monitor Application Insights
description: Monitoramento de desempenho de aplicativos para aplicativos Java em execução em qualquer ambiente sem instrumentar o aplicativo. Rastreamento distribuído e mapa de aplicação.
ms.topic: conceptual
ms.date: 03/29/2020
ms.openlocfilehash: b9c1a52051e63beee9a784714a7bb1a6a79e8759
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687728"
---
# <a name="java-codeless-application-monitoring-azure-monitor-application-insights---public-preview"></a>Java aplicativo sem código monitorando Azure Monitor Application Insights - visualização pública

O monitoramento de aplicativos sem código Java tem tudo a ver com simplicidade - não há alterações de código, o agente Java pode ser ativado através de apenas algumas alterações de configuração.

 O agente Java funciona em qualquer ambiente e permite monitorar todas as suas aplicações Java. Em outras palavras, se você estiver executando seus aplicativos Java em VMs, no local, no AKS, no Windows, Linux - você o nomedele, o agente Java 3.0 irá monitorar o seu aplicativo.

Adicionar o Application Insights Java SDK ao seu aplicativo não é mais necessário, pois o agente 3.0 coleta automaticamente solicitações, dependências e logs por conta própria.

Você ainda pode enviar telemetria personalizada a partir de sua aplicação. O agente 3.0 irá rastreá-lo e correlaciona-lo juntamente com toda a telemetria coletada automaticamente.

## <a name="quickstart"></a>Guia de Início Rápido

**1. Baixe o agente**

Baixe [applicationinsights-agent-3.0.0-PREVIEW.3.jar](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.0.0-PREVIEW.3/applicationinsights-agent-3.0.0-PREVIEW.3.jar)

**2. Aponte o JVM para o agente**

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.3.jar` aos args JVM do seu aplicativo

Os args típicos `-Xmx512m` `-XX:+UseG1GC`da JVM incluem e . Então, se você sabe onde adicioná-los, então você já sabe onde adicionar isso.

Para obter ajuda adicional na configuração dos args JVM do seu aplicativo, consulte [3.0 Preview: Dicas para atualizar seus args JVM](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-arguments).

**3. Aponte o agente para o recurso Application Insights**

Se você ainda não tiver um recurso application Insights, você pode criar um novo seguindo as etapas do [guia de criação de recursos](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

Aponte o agente para o recurso Application Insights, seja definindo uma variável de ambiente:

```
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=00000000-0000-0000-0000-000000000000
```

Ou criando um arquivo `ApplicationInsights.json`de configuração chamado e `applicationinsights-agent-3.0.0-PREVIEW.3.jar`colocando-o no mesmo diretório com o seguinte conteúdo:

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Você pode encontrar sua seqüência de conexões no recurso Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="String de conexão insights de aplicativos":::

**4. É isso!**

Agora inicie sua aplicação e vá para o recurso Application Insights no portal Dozure para ver seus dados de monitoramento.

> [!NOTE]
> Pode levar alguns minutos para que seus dados de monitoramento apareçam no portal.


## <a name="configuration-options"></a>Opções de configuração

No `ApplicationInsights.json` arquivo, você pode configurar adicionalmente:

* Nome da função na nuvem
* Instância de função de nuvem
* Captura de registro de aplicativos
* Métricas JMX
* Micrometer
* Pulsação
* amostragem
* HTTP Proxy
* Autodiagnóstico

Veja detalhes em [3.0 Visualização Pública: Opções de configuração](https://docs.microsoft.com/azure/azure-monitor/app/java-standalone-config).

## <a name="autocollected-requests-dependencies-logs-and-metrics"></a>Solicitações, dependências, logs e métricas coletadas automaticamente

### <a name="requests"></a>Requests

* JMS Consumers
* Consumidores Kafka
* Netty/WebFlux
* Servlets
* Programação da Primavera

### <a name="dependencies-with-distributed-trace-propagation"></a>Dependências com propagação de vestígios distribuídos

* Apache HttpClient e HttpAsyncClient
* gRPC
* java.net.HttpURLConexão
* JMS
* Kafka
* Cliente Netty
* Okhttp

### <a name="other-dependencies"></a>Outras dependências

* Cassandra
* JDBC
* MongoDB (assincronismo e sincronização)
* Redis (Alface e Jedis)

### <a name="logs"></a>Logs

* java.util.logging
* Log4j
* SLF4J/Logback

### <a name="metrics"></a>Métricas

* Micrometer
* Métricas JMX

## <a name="sending-custom-telemetry-from-your-application"></a>Enviando telemetria personalizada a partir de sua aplicação

Nosso objetivo no 3.0+ é permitir que você envie sua telemetria personalizada usando APIs padrão.

Apoiamos o Micrometro, a API opentelemetria e as estruturas de registro populares. O Application Insights Java 3.0 capturará automaticamente a telemetria e correlacionará-a juntamente com toda a telemetria coletada automaticamente.

Por essa razão, não estamos planejando lançar um SDK com o Application Insights 3.0 no momento.

O Application Insights Java 3.0 já está ouvindo a telemetria enviada para o Aplicativo Insights Java SDK 2.x. Essa funcionalidade é uma parte importante da história de atualização para usuários 2.x existentes, e preenche uma lacuna importante em nosso suporte personalizado de telemetria até que a API OpenTelemettry seja GA.

## <a name="sending-custom-telemetry-using-application-insights-java-sdk-2x"></a>Enviando telemetria personalizada usando o Application Insights Java SDK 2.x

Adicione `applicationinsights-core-2.6.0.jar` ao seu aplicativo (todas as versões 2.x são suportadas pelo Application Insights Java 3.0, mas vale a pena usar o mais recente se você tiver uma escolha):

```xml
  <dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>applicationinsights-core</artifactId>
    <version>2.6.0</version>
  </dependency>
```

Criar um Cliente de Telemetria:

  ```java
private static final TelemetryClient telemetryClient = new TelemetryClient();
```

e usá-lo para enviar telemetria personalizada.

### <a name="events"></a>Eventos

  ```java
telemetryClient.trackEvent("WinGame");
```
### <a name="metrics"></a>Métricas

Você pode enviar telemetria métrica via [Micrômetro:](https://micrometer.io)

```java
  Counter counter = Metrics.counter("test_counter");
  counter.increment();
```

Ou você também pode usar o Application Insights Java SDK 2.x:

```java
  telemetryClient.trackMetric("queueLength", 42.0);
```

### <a name="dependencies"></a>Dependências

```java
  boolean success = false;
  long startTime = System.currentTimeMillis();
  try {
      success = dependency.call();
  } finally {
      long endTime = System.currentTimeMillis();
      RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
      telemetry.setTimestamp(new Date(startTime));
      telemetry.setDuration(new Duration(endTime - startTime));
      telemetryClient.trackDependency(telemetry);
  }
```

### <a name="logs"></a>Logs
Você pode enviar telemetria de log personalizada através de sua estrutura de registro favorita.

Ou você também pode usar o Application Insights Java SDK 2.x:

```java
  telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

### <a name="exceptions"></a>Exceções
Você pode enviar telemetria de exceção personalizada através de sua estrutura de registro favorita.

Ou você também pode usar o Application Insights Java SDK 2.x:

```java
  try {
      ...
  } catch (Exception e) {
      telemetryClient.trackException(e);
  }
```

## <a name="upgrading-from-application-insights-java-sdk-2x"></a>Atualizando do Application Insights Java SDK 2.x

Se você já estiver usando o Application Insights Java SDK 2.x em seu aplicativo, não há necessidade de removê-lo. O agente Java 3.0 irá detectá-lo e capturar e correlacionar qualquer telemetria personalizada que você está enviando através do Java SDK 2.x, enquanto suprimia qualquer coleção automática realizada pelo Java SDK 2.x para evitar a captura duplicada.

> [!NOTE]
> Nota: Java SDK 2.x TelemetriaOs iniciais e telemetrianão não serão executados ao usar o agente 3.0.
