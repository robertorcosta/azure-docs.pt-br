---
title: Monitoramento de desempenho de aplicativos web Java - Azure Application Insights
description: Desempenho e monitoramento de uso estendidos do seu site Java com o Application Insights.
ms.topic: conceptual
ms.date: 01/10/2019
ms.openlocfilehash: b29618179d22eac97a07bf41906465aba1fd7929
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657020"
---
# <a name="monitor-dependencies-caught-exceptions-and-method-execution-times-in-java-web-apps"></a>Monitorar dependências, exceções capturadas e tempos de execução de métodos em aplicativos web Java


Se você [instrumentou seu aplicativo Web em Java com o Application Insights][java], será possível usar o Agente Java para obter uma visão mais aprofundada, sem nenhuma alteração de código:

* **Dependências:** dados sobre chamadas de seu aplicativo a outros componentes, incluindo:
  * **As chamadas HTTP de saída** feitas via `java.net.HttpURLConnection` Apache HttpClient, OkHttp e são capturadas.
  * **As chamadas de Redis** feitas através do cliente Jedi são capturadas.
  * **Consultas JDBC** - Para MySQL e PostgreSQL, se a chamada demorar mais de 10 segundos, o agente informa o plano de consulta.

* **Registro de aplicativos:** Capture e correlaciona seus registros de aplicativos com solicitações HTTP e outras telemetrias
  * **Log4j 1.2**
  * **Log4j2**
  * **Logback**

* **Melhor nomeação de operação:** (usado para agregação de solicitações no portal)
  * **Primavera** - `@RequestMapping`baseada em .
  * **JAX-RS** - `@Path`baseado em . 

Para usar o agente Java, instale-o no servidor. Seus aplicativos Web devem ser instrumentados com o [SDK do Java do Application Insights][java]. 

## <a name="install-the-application-insights-agent-for-java"></a>Instalar o agente do Application Insights para Java
1. No computador que está executando o servidor Java, [baixe o agente](https://github.com/Microsoft/ApplicationInsights-Java/releases/latest). Certifique-se de baixar a mesma versão do Agente Java que o núcleo e os pacotes da Web do SDK de Java do Application Insights.
2. Edite o script de inicialização do servidor de aplicativos e adicione o seguinte argumento JVM:
   
    `-javaagent:<full path to the agent JAR file>`
   
    Por exemplo, no Tomcat em um computador Linux:
   
    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`
3. Reinicie o servidor de aplicativos.

## <a name="configure-the-agent"></a>Configurar o agente
Crie um arquivo chamado `AI-Agent.xml` e coloque-o na mesma pasta que o arquivo JAR do agente.

Defina o conteúdo do arquivo xml. Edite o exemplo a seguir para incluir ou omitir os recursos desejados.

```XML
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">

         <!-- capture logging via Log4j 1.2, Log4j2, and Logback, default is true -->
         <Logging enabled="true" />

         <!-- capture outgoing HTTP calls performed through Apache HttpClient, OkHttp,
              and java.net.HttpURLConnection, default is true -->
         <HTTP enabled="true" />

         <!-- capture JDBC queries, default is true -->
         <JDBC enabled="true" />

         <!-- capture Redis calls, default is true -->
         <Jedis enabled="true" />

         <!-- capture query plans for JDBC queries that exceed this value (MySQL, PostgreSQL),
              default is 10000 milliseconds -->
         <MaxStatementQueryLimitInMS>1000</MaxStatementQueryLimitInMS>

      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="additional-config-spring-boot"></a>Config adicional (Bota de Mola)

`java -javaagent:/path/to/agent.jar -jar path/to/TestApp.jar`

Para os serviços de aplicativos do Azure, faça o seguinte:

* Selecione Configurações > Configurações do Aplicativo
* Em configurações do aplicativo, adicione um novo par de chave/valor:

Chave: `JAVA_OPTS` Valor:`-javaagent:D:/home/site/wwwroot/applicationinsights-agent-2.5.0.jar`

Para a versão mais recente do agente Java, verifique as versões [aqui](https://github.com/Microsoft/ApplicationInsights-Java/releases
). 

O agente deve ser embalado como um recurso em seu projeto de tal forma que ele acabe no diretório D:/home/site/wwwroot/. Você pode confirmar que seu agente está no diretório correto do App Service indo para **o Development Tools** > **Advanced Tools** > **Debug Console** e examinando o conteúdo do diretório do site.    

* Salve as configurações e Reinicie seu aplicativo. (Essas etapas só se aplicam aos Serviços de Aplicativos em execução no Windows.)

> [!NOTE]
> IA-Agent.xml e o arquivo jar do agente devem estar na mesma pasta. Frequentemente, eles são colocados juntos na pasta `/resources` do projeto.  

#### <a name="enable-w3c-distributed-tracing"></a>Habilite o rastreamento distribuído do W3C

Adicione o seguinte ao AI-Agent.xml:

```xml
<Instrumentation>
   <BuiltIn enabled="true">
      <HTTP enabled="true" W3C="true" enableW3CBackCompat="true"/>
   </BuiltIn>
</Instrumentation>
```

> [!NOTE]
> O modo de compatibilidade com versões anteriores está habilitado por padrão e o parâmetro enableW3CBackCompat é opcional e deve ser usado apenas quando você quiser desativá-lo. 

Idealmente, esse seria o caso quando todos os seus serviços fossem atualizados para a versão mais recente dos SDKs com suporte para o protocolo W3C. É altamente recomendável migrar para a versão mais recente dos SDKs do W3C com suporte assim que possível.

Verifique se **ambas as configurações de [entrada](correlation.md#enable-w3c-distributed-tracing-support-for-java-apps) e saídas (agente)** são exatamente as mesmas.

## <a name="view-the-data"></a>Exibir os dados
No recurso Application Insights, os tempos agregados de dependência remota e execução do método aparecem [sob o bloco performance][metrics].

Para procurar instâncias individuais de dependência, exceções e relatórios de método, abra [Pesquisar][diagnostic].

[Diagnosticando problemas de dependência – Saiba mais](../../azure-monitor/app/asp-net-dependencies.md#diagnosis).

## <a name="questions-problems"></a>Perguntas? Problemas?
* Não há dados? [Definir exceções de firewall](../../azure-monitor/app/ip-addresses.md)
* [Solucionar problemas de Java](java-troubleshoot.md)

<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiexceptions]: ../../azure-monitor/app/api-custom-events-metrics.md#track-exception
[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: java-get-started.md
[javalogs]: java-trace-logs.md
[metrics]: ../../azure-monitor/app/metrics-explorer.md
