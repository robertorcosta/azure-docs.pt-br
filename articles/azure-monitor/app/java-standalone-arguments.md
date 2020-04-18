---
title: Monitore aplicativos Java em execução em qualquer ambiente - Azure Monitor Application Insights
description: Monitoramento de desempenho de aplicativos para aplicativos Java em execução em qualquer ambiente com agente autônomo Java sem instrumentar o aplicativo. Rastreamento distribuído e mapa de aplicação.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641869"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Configuração jvm args java agente autônomo para azure Monitor Application Insights



## <a name="azure-environments"></a>Ambientes do Azure

Configurar [serviços de aplicativos](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Adicione o JVM `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `-jar <myapp.jar>`arg em algum lugar antes, por exemplo:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args colocados depois `-jar <myapp.jar>` são passados para o aplicativo como args programa.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat instalado `apt-get` via ou`yum`

Se você instalou o `apt-get` `yum`Tomcat via ou `/etc/tomcat8/tomcat8.conf`, então você deve ter um arquivo .  Adicione esta linha ao final desse arquivo:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat instalado via download e descompacta

Se você instalou o Tomcat [https://tomcat.apache.org](https://tomcat.apache.org)via download e descompactar de , então você deve ter um arquivo `<tomcat>/bin/catalina.sh`.  Crie um novo arquivo no `<tomcat>/bin/setenv.sh` mesmo diretório nomeado com o seguinte conteúdo:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se o `<tomcat>/bin/setenv.sh` arquivo já existir, então `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`modifique esse arquivo e adicione a .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Executando Tomcat a partir da linha de comando

Localizar o `<tomcat>/bin/catalina.bat`arquivo .  Crie um novo arquivo no `<tomcat>/bin/setenv.bat` mesmo diretório nomeado com o seguinte conteúdo:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Aspas não são necessárias, mas se você quiser incluí-las, a colocação adequada é:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Se o `<tomcat>/bin/setenv.bat` arquivo já existir, basta `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`modificar esse arquivo e adicionar a .

### <a name="running-tomcat-as-a-windows-service"></a>Executando o Tomcat como um serviço do Windows

Localizar o `<tomcat>/bin/tomcat8w.exe`arquivo .  Execute esse executável `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` e `Java Options` adicione `Java` ao abaixo da guia.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Servidor autônomo

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` à variável `JAVA_OPTS` de ambiente `JBOSS_HOME/bin/standalone.conf` existente no `JBOSS_HOME/bin/standalone.conf.bat` arquivo (Linux) ou (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Servidor de domínio

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ao existente `jvm-options` `JBOSS_HOME/domain/configuration/host.xml`em:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Se você estiver executando vários servidores gerenciados em um `applicationinsights.agent.id` único `system-properties` host, `server`você precisará adicionar ao para cada um:

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

O valor `applicationinsights.agent.id` especificado deve ser único. Ele é usado para criar um subdiretório sob o diretório applicationinsights, já que cada processo JVM precisa de sua própria configuração de aplicativos locais e arquivo de registro de applicationinsights local. Além disso, se reportar ao `applicationinsights.properties` coletor central, o arquivo é compartilhado pelos vários `applicationinsights.agent.id` servidores gerenciados e, portanto, o especificado é necessário para substituir a `agent.id` configuração nesse arquivo compartilhado. `applicationinsights.agent.rollup.id`pode ser especificado da mesma `system-properties` forma no servidor se `agent.rollup.id` você precisar substituir a configuração por servidor gerenciado.


## <a name="jetty-9"></a>Cais 9

Adicione essas linhas para`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Adicione `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` ao existente `jvm-options` `glassfish/domains/domain1/config/domain.xml`em:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Open Management Console vá para **servidores > servidores de aplicativos do WebSphere > servidores de aplicativos,** escolha os servidores de aplicativos apropriados e clique em: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
Em "Argumentos Genéricos JVM" adicione o seguinte:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Depois disso, salve e reinicie o servidor de aplicativos.


## <a name="openliberty-18"></a>OpenLiberty 18

Crie um `jvm.options` novo arquivo no diretório `<openliberty>/usr/servers/defaultServer`do servidor (por exemplo), e adicione esta linha:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
