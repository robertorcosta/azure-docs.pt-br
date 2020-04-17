---
title: Configure aplicativos Linux Java
description: Saiba como configurar um contêiner Java pré-construído para o seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativo azure, web app, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: f4f6de807628704051cdddf74bcefbed678f8fcd
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457885"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurar um aplicativo Java do Linux para o Serviço de Aplicativo do Azure

O Azure App Service on Linux permite que os desenvolvedores Java construam, implantem e dimensionem rapidamente seus aplicativos web embalados tomcat ou Java Standard Edition (SE) em um serviço baseado em Linux totalmente gerenciado. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos e instruções importantes para desenvolvedores Java que usam um contêiner Linux integrado no App Service. Se você nunca usou o Azure App Service, siga o [Java quickstart](quickstart-java.md).

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar [o Plugin Maven para o Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar arquivos .jar e .war. A implantação com IDEs populares também é suportada com [o Azure Toolkit para IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit para Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, seu método de implantação dependerá do seu tipo de arquivamento:

- Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Para implantar arquivos .jar nas imagens do Java SE, use o ponto de extremidade `/api/zipdeploy/` do site do Kudu. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Não implante seu .war ou .jar usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de runtime. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, consulte [a visão geral dos diagnósticos do Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso ao console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [Stream logs in Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Os logs do aplicativo Java e Tomcat podem ser encontrados no diretório */home/LogFiles/Application/.*

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Ferramentas de solução de problemas

As imagens Java incorporadas são baseadas no sistema operacional [Alpine Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Use `apk` o gerenciador de pacotes para instalar quaisquer ferramentas ou comandos de solução de problemas.

### <a name="flight-recorder"></a>Flight Recorder

Todas as imagens do Linux Java no App Service têm o Zulu Flight Recorder instalado para que você possa facilmente se conectar ao JVM e iniciar uma gravação de profiler ou gerar um dump de pilha.

#### <a name="timed-recording"></a>Gravação cronometrada

Para começar, ssh em seu Serviço `jcmd` de aplicativo e executar o comando para ver uma lista de todos os processos Java em execução. Além do próprio JCMD, você deve ver seu aplicativo Java em execução com um número de ID de processo (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando abaixo para iniciar uma gravação de 30 segundos do JVM. Isso fará o perfil do JVM e criará um arquivo JFR chamado *jfr_example.jfr* no diretório inicial. (Substitua 116 pelo pid do seu aplicativo Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, você pode `jcmd 116 JFR.check`validar a gravação que está ocorrendo executando . Isso mostrará todas as gravações para o processo Java dado.

#### <a name="continuous-recording"></a>Gravação contínua

Você pode usar o Zulu Flight Recorder para fazer o perfil contínuo do seu aplicativo Java com impacto mínimo no desempenho de tempo de execução[(fonte).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) Para isso, execute o seguinte comando Azure CLI para criar uma configuração de aplicativo nomeada JAVA_OPTS com a configuração necessária. O conteúdo da configuração do `java` aplicativo JAVA_OPTS é passado para o comando quando seu aplicativo é iniciado.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Uma vez iniciada a gravação, você pode despejar os `JFR.dump` dados de gravação atuais a qualquer momento usando o comando.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Para obter mais informações, consulte o [Comando JCMD Reference](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisando gravações

Use [FTPS](../deploy-ftp.md) para baixar seu arquivo JFR para sua máquina local. Para analisar o arquivo JFR, baixe e instale [o Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Controle de Missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

## <a name="customization-and-tuning"></a>Personalização e ajuste

O Azure App Service for Linux suporta a sintonia e personalização fora da caixa através do portal Azure e da CLI. Revise os seguintes artigos para configuração de aplicativo web não específica de Java:

- [Definir configurações de aplicativo](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Configurar um domínio personalizado](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Configurar vinculações SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Adicionar uma CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Configure o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de runtime do Java

Para definir a memória alocada ou outras opções de tempo de execução JVM nos ambientes Tomcat e Java SE, crie uma [configuração](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) de aplicativo nomeada `JAVA_OPTS` com as opções. O Serviço de Aplicativo no Linux passa essa configuração como uma variável de ambiente para o runtime do Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para configurar a configuração do aplicativo do plugin Maven, adicione tags de configuração/valor na seção plugin do Azure. O exemplo a seguir define um tamanho de pilha java mínimo e máximo específico:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação no Plano do Serviço de Aplicativo podem usar as seguintes opções:

- As instâncias B1 e S1:`-Xms1024m -Xmx1024m`
- As instâncias B2 e S2:`-Xms3072m -Xmx3072m`
- As instâncias B3 e S3:`-Xms6144m -Xmx6144m`

Ao ajustar as configurações de heap do aplicativo, examine os detalhes do Plano do Serviço de Aplicativo e considere os vários aplicativos e slots de implantação necessários para encontrar a alocação de memória ideal.

Se você estiver implantando um aplicativo JAR, ele deve ser nomeado *app.jar* para que a imagem incorporada possa identificar corretamente o seu aplicativo. (O plugin Maven faz essa renomeação automaticamente.) Se você não quiser renomear seu JAR para *app.jar,* você pode carregar um script shell com o comando para executar o seu JAR. Em seguida, cole o caminho completo desse script na caixa de texto [Arquivo de inicialização](app-service-linux-faq.md#built-in-images), na seção Configuração do portal. O script de inicialização não é executado no diretório no qual ele é colocado. Portanto, sempre use caminhos absolutos para fazer referência a arquivos em seu script de inicialização (por exemplo: `java -jar /home/myapp/myapp.jar`).

### <a name="turn-on-web-sockets"></a>Ativar os soquetes da Web

Ative o suporte para soquetes da Web no portal do Azure, nas **Configurações de aplicativo**. Será necessário reiniciar o aplicativo para a configuração entrar em vigor.

Ative o suporte para soquete da Web usando a CLI do Azure com o seguinte comando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Depois, reinicie o aplicativo:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Definir a codificação de caractere padrão

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` com o valor `-Dfile.encoding=UTF-8`.

Como alternativa, é possível definir a configuração do aplicativo usando o plug-in do Maven do Serviço de Aplicativo. Adicione as marcas de nome e valor de configuração à configuração do plug-in:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Ajuste o tempo de tempo de inicialização

Se o aplicativo Java for particularmente grande, você deve aumentar o limite de tempo de inicialização. Para fazer isso, crie `WEBSITES_CONTAINER_START_TIME_LIMIT` uma configuração de aplicativo e defina-a para o número de segundos que o App Service deve esperar antes de cronometrar. O valor `1800` máximo é de segundos.

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho dos aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no App Service. Você pode usar o [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache Sling, ou usar este [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicativos seguros

Os aplicativos Java em execução no Serviço de Aplicativo para Linux têm o mesmo conjunto de [melhores práticas de segurança](/azure/security/security-paas-applications-using-app-services) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (Easy Auth)

Configure a autenticação do aplicativo no portal Azure com a opção **Autenticação e Autorização.** Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação. Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json).

#### <a name="tomcat"></a>Tomcat

Seu aplicativo Tomcat pode acessar as reivindicações do usuário diretamente do servlet lançando o objeto Principal para um objeto Mapa. O objeto Mapa mapeará cada tipo de reclamação para uma coleção de reivindicações para esse tipo. No código abaixo, `request` está `HttpServletRequest`uma instância de .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora você pode `Map` inspecionar o objeto para qualquer reclamação específica. Por exemplo, o seguinte código corta todos os tipos de solicitação e imprime o conteúdo de cada coleção.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Para sair, use `/.auth/ext/logout` o caminho. Para realizar outras ações, consulte a documentação sobre [o uso de Autenticação e Autorização de Serviço de Aplicativo.](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) Há também documentação oficial na interface Tomcat [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) e seus métodos. Os seguintes métodos de servlet também são hidratados com base na configuração do App Service:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desativar esse recurso, crie `WEBSITE_AUTH_SKIP_PRINCIPAL` uma configuração `1`de aplicativo nomeada com um valor de . Para desativar todos os filtros servlet adicionados `WEBSITE_SKIP_FILTERS` pelo App `1`Service, crie uma configuração nomeada com um valor de .

#### <a name="spring-boot"></a>Spring Boot

Os desenvolvedores do Spring Boot podem usar o [inicializador do Spring Boot do Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicativos usando APIs e anotações conhecidas do Spring Security. Certifique-se de aumentar o tamanho máximo do cabeçalho no arquivo *application.properties.* Sugerimos um valor igual a `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [Secure um nome DNS personalizado com uma vinculação SSL no Azure App Service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) para carregar um certificado SSL existente e vinculá-lo ao nome de domínio do aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Use referências keyvault

[O Azure KeyVault](../../key-vault/general/overview.md) fornece gerenciamento secreto centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou strings de conexão) no KeyVault e acessar esses segredos em seu aplicativo através de variáveis de ambiente.

Primeiro, siga as instruções para [conceder acesso ao seu aplicativo ao Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e fazer uma referência do [KeyVault ao seu segredo em uma configuração de aplicativo](../app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência resolve o segredo imprimindo a variável ambiente enquanto acessa remotamente o terminal do Serviço de Aplicativo.

Para injetar esses segredos no seu arquivo de configuração Spring ou`${MY_ENV_VAR}`Tomcat, use a sintaxe de injeção variável do ambiente (). Para arquivos de configuração de mola, consulte esta documentação em [configurações externalizadas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Usando a Loja de Chaves Java

Por padrão, quaisquer certificados públicos ou privados [carregados no App Service Linux](../configure-ssl-certificate.md) serão carregados nas respectivas Lojas De Chaves Java quando o contêiner for iniciado. Depois de carregar seu certificado, você precisará reiniciar seu Serviço de Aplicativo para que ele seja carregado na Java Key Store. Os certificados públicos são carregados `$JAVA_HOME/jre/lib/security/cacerts`na Key Store em `$JAVA_HOME/lib/security/client.jks`, e os certificados privados são armazenados em .

Uma configuração adicional pode ser necessária para criptografar sua conexão JDBC com certificados na Java Key Store. Por favor, consulte a documentação do driver JDBC escolhido.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicializando a Loja de Chaves Java

Para inicializar `import java.security.KeyStore` o objeto, carregue o arquivo keystore com a senha. A senha padrão para ambas as lojas-chave é "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Carregar manualmente a loja-chave

Você pode carregar certificados manualmente para a loja-chave. Crie uma configuração de aplicativo, `SKIP_JAVA_KEYSTORE_LOAD`com um valor de `1` desativar o App Service de carregar os certificados na loja-chave automaticamente. Todos os certificados públicos enviados ao App Service `/var/ssl/certs/`através do portal Azure são armazenados em . Os certificados privados são armazenados em `/var/ssl/private/`.

Você pode interagir ou depurar a Ferramenta de Chave Java [abrindo uma conexão SSH ao](app-service-linux-ssh-support.md) seu Serviço de Aplicativo e executando o comando `keytool`. Consulte a [documentação da Ferramenta Chave](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) para obter uma lista de comandos. Para obter mais informações sobre a API keystore, consulte [a documentação oficial](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configure plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no Azure App Service no Linux com as plataformas APM (Application Monitoring, monitoramento de desempenho de aplicativos NewRelic e AppDynamics).

### <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente Java do NewRelic, ele terá um nome de arquivo semelhante ao *newrelic-java-x.x.zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. [SSH em sua instância de serviço de aplicativo](app-service-linux-ssh-support.md) e criar um novo diretório */home/site/wwwroot/apm*.
5. Carregue os arquivos de agente NewRelic Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos para o seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o arquivo YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado por sua própria chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente Java do site da AppDynamics, o nome do arquivo será semelhante ao *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH em sua instância de serviço de aplicativo](app-service-linux-ssh-support.md) e criar um novo diretório */home/site/wwwroot/apm*.
4. Carregue os arquivos do agente Java em um diretório em */home/site/wwwroot/apm*. Os arquivos para o seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **Java SE**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.

> [!NOTE]
> Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `-javaagent:/...` opção ao final do valor atual.

## <a name="configure-jar-applications"></a>Configurar aplicativos JAR

### <a name="starting-jar-apps"></a>Iniciar aplicativos JAR

Por padrão, o App Service espera que seu aplicativo JAR seja chamado *app.jar*. Se tiver esse nome, será executado automaticamente. Para usuários do Maven, você pode `<finalName>app</finalName>` definir `<build>` o nome JAR incluindo na seção do seu *pom.xml*. [Você pode fazer o mesmo em Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) definindo a `archiveFileName` propriedade.

Se você quiser usar um nome diferente para o seu JAR, você também deve fornecer o [Comando de Inicialização](app-service-linux-faq.md#built-in-images) que executa seu arquivo JAR. Por exemplo, `java -jar my-jar-app.jar`. Você pode definir o valor do comando de inicialização no Portal, em `STARTUP_COMMAND`Configuração > Configurações Gerais ou com uma Configuração de aplicativo nomeada .

### <a name="server-port"></a>Porta do Servidor

O App Service Linux encaminha solicitações recebidas para a porta 80, de modo que seu aplicativo deve ouvir na porta 80 também. Você pode fazer isso na configuração do aplicativo (como o arquivo *Spring's application.properties)* ou no comando Startup (por exemplo, `java -jar spring-app.jar --server.port=80`). Consulte a documentação a seguir para estruturas Java comuns:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronauta](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Estrutura de jogo](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Fontes de dados

### <a name="tomcat"></a>Tomcat

Essas instruções se aplicam a todas as conexões de banco de dados. Você precisará preencher os espaços reservados com nome de classe do driver do banco de dados escolhido e o arquivo JAR. É fornecida uma tabela com nomes de classe e downloads de driver para bancos de dados comuns.

| Banco de dados   | Nome de Classe do Driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (Selecione "Independente de Plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Para configurar o Tomcat para usar o Java Database Connectivity (JDBC) ou `CATALINA_OPTS` a API de Persistência java (JPA), primeiro personalize a variável de ambiente que é lida pelo Tomcat na inicialização. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis **Configuration** > de ambiente na página**Configurações do aplicativo configuração** no portal Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados em nível de aplicativo

1. Crie um arquivo *context.xml* no *diretório META-INF/do* seu projeto. Crie o *diretório META-INF se* ele não existir.

2. Em *context.xml,* `Context` adicione um elemento para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. Atualize o *web.xml* do seu aplicativo para usar a fonte de dados em seu aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos compartilhados em nível de servidor

A adição de uma fonte de dados compartilhada no nível do servidor exigirá que você edite o servidor do Tomcat.xml. Primeiro, faça upload de um [script de inicialização](app-service-linux-faq.md#built-in-images) e defina o caminho para o script no **Comando de Inicialização de Configuração** > **Startup Command**. Você pode carregar o script de inicialização usando [FTP](../deploy-ftp.md).

Seu script de inicialização fará uma [transformação xsl](https://www.w3schools.com/xml/xsl_intro.asp) para o arquivo `/usr/local/tomcat/conf/server.xml`server.xml e a saída do arquivo xml resultante para . O script de inicialização deve instalar libxslt via apk. Seu arquivo xsl e script de inicialização podem ser carregados via FTP. Abaixo está um exemplo de script de inicialização.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Um exemplo de arquivo xsl é fornecido abaixo. O arquivo xsl de exemplo adiciona um novo nó conector ao servidor Tomcat.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Por fim, coloque os JARs do driver no classpath do Tomcat e reinicie o serviço de aplicativos.

1. Certifique-se de que os arquivos de driver JDBC estão disponíveis para o classe Loader do Tomcat, colocando-os no diretório */home/tomcat/lib.* (Crie este diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, execute as seguintes etapas:

    1. No [Cloud Shell,](https://shell.azure.com)instale a extensão do webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Execute o seguinte comando CLI para criar um túnel SSH do seu sistema local para o Serviço de Aplicativos:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Conecte-se à porta de tunelamento local com seu cliente SFTP e carregue os arquivos para a pasta */home/tomcat/lib.*

    Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Se você tiver uma fonte de dados no nível do servidor, reinicie o aplicativo Linux Serviço de Aplicativo. O Tomcat redefinirá `CATALINA_BASE` como `/home/tomcat` e usará a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para se conectar a fontes de dados em aplicativos spring boot, sugerimos criar strings de conexão e injetá-las no arquivo *application.properties.*

1. Na seção "Configuração" da página Serviço do aplicativo, defina um nome para a seqüência de string, cole sua seqüência de conexão JDBC no campo de valor e defina o tipo como "Personalizado". Opcionalmente, você pode definir essa seqüência de conexão como configuração de slot.

    Esta seqüência de conexões é `CUSTOMCONNSTR_<your-string-name>`acessível ao nosso aplicativo como uma variável de ambiente chamada . Por exemplo, a seqüência de `CUSTOMCONNSTR_exampledb`conexões que criamos acima será nomeada .

2. No arquivo *application.properties,* faça referência a essa seqüência de conexão com o nome da variável ambiente. Para nosso exemplo, usaríamos o seguinte.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a [documentação do Iniciale de Primavera sobre acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [configurações externalizadas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tópico.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Use Redis como cache de sessão com tomcat

Você pode configurar o Tomcat para usar uma loja de sessão externa, como [o Cache Azure for Redis](/azure/azure-cache-for-redis/). Isso permite preservar o estado da sessão do usuário (como dados do carrinho de compras) quando um usuário é transferido para outra instância do seu aplicativo, por exemplo, quando ocorre o autoscaling, reinicialização ou failover.

Para usar o Tomcat com redis, você deve configurar seu aplicativo para usar uma implementação [PersistentManager.](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) As etapas a seguir explicam esse processo usando [o Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) como exemplo.

1. Abra um terminal `<variable>=<value>` Bash e use para definir cada uma das seguintes variáveis de ambiente.

    | Variável                 | Valor                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | O nome do grupo de recursos que contém a instância do Serviço de Aplicativo.       |
    | WEBAPP_NAME              | O nome da instância do Serviço de Aplicativo.                                     |
    | WEBAPP_PLAN_NAME         | O nome do seu plano de Serviço de Aplicativo.                                         |
    | REGIÃO                   | O nome da região onde seu aplicativo está hospedado.                           |
    | REDIS_CACHE_NAME         | O nome do seu Cache Azure para instância Redis.                           |
    | REDIS_PORT               | A porta SSL que o cache Redis ouve.                             |
    | REDIS_PASSWORD           | A chave de acesso principal para sua instância.                                  |
    | REDIS_SESSION_KEY_PREFIX | Um valor que você especifica para identificar chaves de sessão que vêm do seu aplicativo. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Você pode encontrar o nome, a porta e acessar informações-chave no portal do Azure procurando nas seções **Propriedades** ou Chaves de **acesso** da sua instância de serviço.

2. Crie ou atualize o arquivo *src/principal/webapp/META-INF/context.xml* do seu aplicativo com o seguinte conteúdo:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Este arquivo especifica e configura a implementação do gerenciador de sessão para o seu aplicativo. Ele usa as variáveis de ambiente que você definiu na etapa anterior para manter as informações da sua conta fora dos arquivos de origem.

3. Use ftp para carregar o arquivo JAR do gerenciador de sessão para a instância do Serviço de Aplicativo, colocando-o no diretório */home/tomcat/lib.* Para obter mais informações, consulte [Implantar seu aplicativo no Azure App Service usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Desative o [cookie de afinidade de sessão](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para a instância do Serviço de Aplicativo. Você pode fazer isso a partir do portal Azure navegando para o seu aplicativo e, em seguida, definindo **Configuração > configurações gerais > afinidade ARR** com **Off**. Alternativamente, você pode usar o seguinte comando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Por padrão, o App Service usará cookies de afinidade de sessão para garantir que as solicitações de clientes com sessões existentes sejam roteadas para a mesma instância do seu aplicativo. Esse comportamento padrão não requer configuração, mas não pode preservar o estado da sessão do usuário quando a instância do aplicativo é reiniciada ou quando o tráfego é redirecionado para outra instância. Quando você [desativa a](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) configuração existente de Afinidade de Ocorrência de INSTÂNCIA ARR para desativar o roteamento baseado em cookies de sessão, você permite que o armazenamento de sessão configurado funcione sem interferência.

5. Navegue até a seção **Propriedades** da instância do Serviço de Aplicativo e encontre **endereços IP adicionais de saída**. Estes representam todos os possíveis endereços IP de saída para o seu aplicativo. Copie-os para uso na próxima etapa.

6. Para cada endereço IP, crie uma regra de firewall no cache do Azure para a instância Redis. Você pode fazer isso no portal Azure a partir da seção **Firewall** da sua instância Redis. Forneça um nome exclusivo para cada regra e defina os valores de **endereço IP iniciar** e final **para** o mesmo endereço IP.

7. Navegue até a seção **de configurações avançadas** da instância Redis e defina **permitir o acesso somente via SSL** para **Não**. Isso permite que a instância do Serviço de Aplicativos se comunique com o cache Redis através da infra-estrutura do Azure.

8. Atualize `azure-webapp-maven-plugin` a configuração no arquivo *pom.xml* do seu aplicativo para consultar as informações da sua conta Redis. Este arquivo usa as variáveis de ambiente definidas anteriormente para manter as informações da sua conta fora dos arquivos de origem.

    Se necessário, altere `1.7.0` para a versão atual do [Plug-in do Maven para o Serviço de Aplicativo do Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Reconstrua e reimplante seu aplicativo.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Seu aplicativo agora usará seu cache Redis para gerenciamento de sessões.

Para obter uma amostra que você pode usar para testar essas instruções, consulte o repo [de scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) no GitHub.

## <a name="docker-containers"></a>Contêineres do Docker

Para usar o JDK do Zulu com suporte do Azure em seus contêineres, efetue pull e use as imagens predefinidas conforme documentado em [página de download do Azul Zulu Enterprise para Azure com suporte](https://www.azul.com/downloads/azure-only/zulu/) ou use os exemplos do `Dockerfile` dos [Repositório GitHub do Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Declaração de apoio

### <a name="runtime-availability"></a>Disponibilidade de tempo de execução

O Serviço de Aplicativo para Linux dá suporte a dois runtimes para hospedagem gerenciada de aplicativos Web do Java:

- O [contêiner do servlet do Tomcat](https://tomcat.apache.org/) para execução de aplicativos empacotados como arquivos Web (WAR). As versões com suporte são 8.5 e 9.0.
- O ambiente de runtime do Java SE para executar aplicativos empacotados como arquivos Java (JAR). As versões suportadas são Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

Builds do Azul Zulu Enterprise do OpenJDK são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Eles contêm todos os componentes para criar e executar aplicativos Java SE. Você pode instalar o JDK a partir da [instalação Java JDK](https://aka.ms/azure-jdks).

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um runtime do Java com suporte for desativado, os desenvolvedores do Azure que usam o runtime afetado receberão um aviso de reprovação pelo menos seis meses antes de o runtime ser desativado.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

Acesse a central para [Desenvolvedores do Azure para Java](/java/azure/) para conferir inícios rápidos, tutoriais e documentação de referência do Java.

Dúvidas gerais sobre como usar o Serviço de Aplicativo para Linux que não são específicas do desenvolvimento em Java são respondidas nas [Perguntas frequentes do Serviço de Aplicativo no Linux](app-service-linux-faq.md).
