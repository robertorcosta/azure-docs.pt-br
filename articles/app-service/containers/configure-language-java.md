---
title: Configurar aplicativos Java do Linux
description: Saiba como configurar um contêiner Java predefinido para seu aplicativo. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativo do Azure, aplicativo Web, Linux, OSS, Java, Java EE, JEE, Java
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: ffc7c289fd675a68c8b02af1777fea3d4530e17a
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82889488"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Configurar um aplicativo Java do Linux para o Serviço de Aplicativo do Azure

O serviço de Azure App no Linux permite aos desenvolvedores de Java compilar, implantar e dimensionar rapidamente seus aplicativos Web empacotados Tomcat ou Java Standard Edition (SE) em um serviço totalmente gerenciado baseado em Linux. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece os principais conceitos e instruções para desenvolvedores de Java que usam um contêiner do Linux interno no serviço de aplicativo. Se você nunca usou Azure App serviço, siga o [início rápido do Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar o [plug-in do Maven para Azure app serviço](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar arquivos. jar e. War. A implantação com IDEs populares também tem suporte com [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Caso contrário, o método de implantação dependerá do tipo de arquivo morto:

- Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Para implantar arquivos .jar nas imagens do Java SE, use o ponto de extremidade `/api/zipdeploy/` do site do Kudu. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Não implante seu .war ou .jar usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de runtime. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, consulte [visão geral do diagnóstico de serviço Azure app](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso ao console SSH

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [Stream logs in Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Seus logs de aplicativo Java e Tomcat podem ser encontrados no diretório */Home/LogFiles/Application/*

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Ferramentas de solução de problemas

As imagens Java internas são baseadas no sistema operacional [alpineum Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) . Use o `apk` Gerenciador de pacotes para instalar qualquer ferramenta ou comando de solução de problemas.

### <a name="flight-recorder"></a>Flight Recorder

Todas as imagens Java do Linux no serviço de aplicativo têm o Zulu Flight Recorder instalado para que você possa se conectar facilmente à JVM e iniciar uma gravação do criador de perfil ou gerar um despejo de heap.

#### <a name="timed-recording"></a>Gravação cronometrada

Para começar, use o SSH em seu serviço de aplicativo e `jcmd` execute o comando para ver uma lista de todos os processos Java em execução. Além do próprio jcmd, você deve ver seu aplicativo Java em execução com um PID (número de ID de processo).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando a seguir para iniciar uma gravação de 30 segundos da JVM. Isso fará o perfil da JVM e criará um arquivo JFR chamado *jfr_example. JFR* no diretório base. (Substitua 116 pelo PID do seu aplicativo Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, você pode validar se a gravação está ocorrendo `jcmd 116 JFR.check`executando. Isso mostrará todas as gravações para o processo Java fornecido.

#### <a name="continuous-recording"></a>Gravação contínua

Você pode usar o gravador de vôo Zulu para criar o perfil de seu aplicativo Java com impacto mínimo sobre o desempenho do tempo de execução ([origem](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Para fazer isso, execute o seguinte comando CLI do Azure para criar uma configuração de aplicativo chamada JAVA_OPTS com a configuração necessária. O conteúdo da configuração do aplicativo JAVA_OPTS é passado para o `java` comando quando seu aplicativo é iniciado.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Depois que a gravação for iniciada, você poderá despejar os dados de gravação atuais a `JFR.dump` qualquer momento usando o comando.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Para obter mais informações, consulte a [referência do comando Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisando gravações

Use [FTPS](../deploy-ftp.md) para baixar o arquivo JFR para o computador local. Para analisar o arquivo JFR, baixe e instale o [controle de missão Zulu](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o controle de missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalização e ajuste

O serviço de Azure App para Linux dá suporte ao ajuste e à personalização prontos para uso por meio do portal do Azure e da CLI. Examine os seguintes artigos para configuração de aplicativo Web não específica para Java:

- [Definir configurações do aplicativo](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Configurar um domínio personalizado](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Configurar associações SSL](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Adicionar uma CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Configurar o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de runtime do Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM nos ambientes Tomcat e Java SE, crie `JAVA_OPTS` uma configuração de [aplicativo](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) chamada com as opções. O Serviço de Aplicativo no Linux passa essa configuração como uma variável de ambiente para o runtime do Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para definir a configuração do aplicativo do plug-in do Maven, adicione marcas de configuração/valor na seção plug-in do Azure. O exemplo a seguir define um tamanho de heap de Java mínimo e máximo específico:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação no Plano do Serviço de Aplicativo podem usar as seguintes opções:

- Instâncias B1 e S1:`-Xms1024m -Xmx1024m`
- Instâncias B2 e S2:`-Xms3072m -Xmx3072m`
- Instâncias B3 e S3:`-Xms6144m -Xmx6144m`

Ao ajustar as configurações de heap do aplicativo, examine os detalhes do Plano do Serviço de Aplicativo e considere os vários aplicativos e slots de implantação necessários para encontrar a alocação de memória ideal.

Se você estiver implantando um aplicativo JAR, ele deverá ser nomeado *app. jar* para que a imagem interna possa identificar corretamente seu aplicativo. (O plug-in do Maven faz isso renomeando automaticamente.) Se você não quiser renomear o JAR para *app. jar*, poderá carregar um script de shell com o comando para executar o Jar. Em seguida, cole o caminho completo desse script na caixa de texto [Arquivo de inicialização](app-service-linux-faq.md#built-in-images), na seção Configuração do portal. O script de inicialização não é executado no diretório no qual ele é colocado. Portanto, sempre use caminhos absolutos para fazer referência a arquivos em seu script de inicialização (por exemplo: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Ajustar tempo limite de inicialização

Se seu aplicativo Java for particularmente grande, você deverá aumentar o limite de tempo de inicialização. Para fazer isso, crie uma configuração de aplicativo `WEBSITES_CONTAINER_START_TIME_LIMIT` e defina-a como o número de segundos que o serviço de aplicativo deve aguardar antes de atingir o tempo limite. O valor máximo é `1800` segundos.

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho de aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no serviço de aplicativo. Você pode usar o [plug-in do Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache sling ou usando esse [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicativos seguros

Os aplicativos Java em execução no Serviço de Aplicativo para Linux têm o mesmo conjunto de [melhores práticas de segurança](/azure/security/security-paas-applications-using-app-services) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (fácil autenticação)

Configure a autenticação de aplicativo no portal do Azure com a opção **autenticação e autorização** . Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação. Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json).

#### <a name="tomcat"></a>Tomcat

Seu aplicativo Tomcat pode acessar as declarações do usuário diretamente do servlet, convertendo o objeto principal em um objeto Map. O objeto MAP mapeará cada tipo de declaração para uma coleção de declarações para esse tipo. No código a seguir, `request` é uma instância do `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora você pode inspecionar `Map` o objeto para qualquer declaração específica. Por exemplo, o trecho de código a seguir itera em todos os tipos de declaração e imprime o conteúdo de cada coleção.

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

Para desconectar os usuários, use `/.auth/ext/logout` o caminho. Para executar outras ações, consulte a documentação sobre o [uso de autenticação e autorização do serviço de aplicativo](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Também há documentação oficial na [interface HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) do Tomcat e seus métodos. Os seguintes métodos servlets também são alimentados com base na configuração do serviço de aplicativo:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desabilitar esse recurso, crie uma configuração de aplicativo `WEBSITE_AUTH_SKIP_PRINCIPAL` chamada com um valor `1`de. Para desabilitar todos os filtros de servlet adicionados pelo serviço de aplicativo, crie `WEBSITE_SKIP_FILTERS` uma configuração chamada com `1`um valor de.

#### <a name="spring-boot"></a>Spring Boot

Os desenvolvedores do Spring Boot podem usar o [inicializador do Spring Boot do Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicativos usando APIs e anotações conhecidas do Spring Security. Certifique-se de aumentar o tamanho máximo do cabeçalho em seu arquivo *Application. Properties* . Sugerimos um valor igual a `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [proteger um nome DNS personalizado com uma associação SSL no serviço Azure app](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) para carregar um certificado SSL existente e associá-lo ao nome de domínio do seu aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Usar referências do keyvault

O [Azure keyvault](../../key-vault/general/overview.md) fornece gerenciamento de segredos centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou cadeias de conexão) no keyvault e acessar esses segredos em seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [conceder acesso ao aplicativo para Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência de keyvault para seu segredo em uma configuração de aplicativo](../app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência seja resolvida para o segredo imprimindo a variável de ambiente ao acessar remotamente o terminal do serviço de aplicativo.

Para injetar esses segredos em seu arquivo de configuração Spring ou Tomcat, use a sintaxe de`${MY_ENV_VAR}`injeção de variável de ambiente (). Para arquivos de configuração do Spring, consulte esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Usando o repositório de chaves Java

Por padrão, todos os certificados públicos ou privados [carregados no serviço de aplicativo Linux](../configure-ssl-certificate.md) serão carregados nos respectivos repositórios de chaves Java à medida que o contêiner for iniciado. Depois de carregar seu certificado, você precisará reiniciar o serviço de aplicativo para que ele seja carregado no repositório de chaves Java. Os certificados públicos são carregados no repositório de chaves `$JAVA_HOME/jre/lib/security/cacerts`em, e os certificados privados são `$JAVA_HOME/lib/security/client.jks`armazenados no.

A configuração adicional pode ser necessária para criptografar sua conexão JDBC com certificados no repositório de chaves Java. Consulte a documentação para o driver JDBC escolhido.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicializando o repositório de chaves Java

Para inicializar o `import java.security.KeyStore` objeto, carregue o arquivo do repositório de chaves com a senha. A senha padrão para ambos os repositórios de chaves é "changeit".

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

#### <a name="manually-load-the-key-store"></a>Carregar manualmente o repositório de chaves

Você pode carregar certificados manualmente no repositório de chaves. Crie uma configuração de aplicativo `SKIP_JAVA_KEYSTORE_LOAD`,, com um valor `1` de para desabilitar o serviço de aplicativo para carregar os certificados no repositório de chaves automaticamente. Todos os certificados públicos carregados no serviço de aplicativo por meio do portal do Azure `/var/ssl/certs/`são armazenados em. Os certificados privados são armazenados `/var/ssl/private/`em.

Você pode interagir ou depurar a ferramenta de chave do Java [abrindo uma conexão SSH](app-service-linux-ssh-support.md) para o serviço de aplicativo e executando `keytool`o comando. Consulte a [documentação da ferramenta de chave](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) para obter uma lista de comandos. Para obter mais informações sobre a API do keystore, consulte [a documentação oficial](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no serviço de Azure App no Linux com as plataformas de monitoramento de desempenho de aplicativos NewRelic e AppDynamics (APM).

### <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente Java de NewRelic, ele terá um nome de arquivo semelhante a *newrelic-Java-x. x. x. zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. Use [SSH em sua instância do serviço de aplicativo](app-service-linux-ssh-support.md) e crie um novo diretório */Home/site/wwwroot/APM*.
5. Carregue os arquivos do agente Java NewRelic desempacotados em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/newrelic*.
6. Modifique o arquivo YAML em */Home/site/wwwroot/APM/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado por sua própria chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente Java do site AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x. x. x. xxxxx. zip*
3. Use [SSH em sua instância do serviço de aplicativo](app-service-linux-ssh-support.md) e crie um novo diretório */Home/site/wwwroot/APM*.
4. Carregue os arquivos do agente Java em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar no */Home/site/wwwroot/APM/AppDynamics*.
5. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **Java SE**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.

> [!NOTE]
> Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `-javaagent:/...` opção ao final do valor atual.

## <a name="configure-jar-applications"></a>Configurar aplicativos JAR

### <a name="starting-jar-apps"></a>Iniciando aplicativos JAR

Por padrão, o serviço de aplicativo espera que seu aplicativo JAR seja nomeado *app. jar*. Se ele tiver esse nome, ele será executado automaticamente. Para usuários do Maven, você pode definir o nome do JAR `<finalName>app</finalName>` incluindo na `<build>` seção de seu *pom. xml*. [Você pode fazer o mesmo no gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) definindo a `archiveFileName` propriedade.

Se você quiser usar um nome diferente para o JAR, também deverá fornecer o comando de [inicialização](app-service-linux-faq.md#built-in-images) que executa o arquivo jar. Por exemplo, `java -jar my-jar-app.jar`. Você pode definir o valor para o comando de inicialização no portal, em configuração > configurações gerais ou com uma configuração de aplicativo denominada `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta do Servidor

O serviço de aplicativo Linux roteia solicitações de entrada para a porta 80, de modo que seu aplicativo também deve escutar na porta 80. Você pode fazer isso na configuração do aplicativo (como o arquivo *Application. Properties* da Spring) ou no comando de inicialização (por exemplo, `java -jar spring-app.jar --server.port=80`). Consulte a seguinte documentação para estruturas Java comuns:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Play Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
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

Para configurar o Tomcat para usar Java Database Connectivity (JDBC) ou a API de persistência Java (JPA), primeiro `CATALINA_OPTS` Personalize a variável de ambiente que é lida pelo Tomcat na inicialização. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis de ambiente na página**configurações do aplicativo** de **configuração** > no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados em nível de aplicativo

1. Crie um arquivo *Context. xml* no diretório *meta-inf/* do seu projeto. Crie o diretório *meta-inf/* se ele não existir.

2. Em *Context. xml*, adicione um `Context` elemento para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

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

3. Atualize o *Web. xml* do seu aplicativo para usar a fonte de dados em seu aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos de nível de servidor compartilhados

A adição de uma fonte de dados compartilhada no nível do servidor exigirá que você edite o Server. XML do Tomcat. Primeiro, carregue um [script de inicialização](app-service-linux-faq.md#built-in-images) e defina o caminho para o script no**comando de inicialização**da **configuração** > . Você pode carregar o script de inicialização usando [FTP](../deploy-ftp.md).

O script de inicialização fará uma [transformação XSL](https://www.w3schools.com/xml/xsl_intro.asp) para o arquivo Server. xml e produzirá o arquivo XML resultante `/usr/local/tomcat/conf/server.xml`para. O script de inicialização deve instalar o libxslt via apk. O arquivo XSL e o script de inicialização podem ser carregados via FTP. Veja abaixo um exemplo de script de inicialização.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Um exemplo de arquivo XSL é fornecido abaixo. O arquivo XSL de exemplo adiciona um novo nó de conector ao servidor Tomcat. xml.

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

Por fim, coloque os JARs do driver no classpath do Tomcat e reinicie o serviço de aplicativo.

1. Certifique-se de que os arquivos do driver JDBC estejam disponíveis para o carregador de diretório do Tomcat colocando-os no */Home/Tomcat/lib* Directory. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos em sua instância do serviço de aplicativo, execute as seguintes etapas:

    1. No [Cloud Shell](https://shell.azure.com), instale a extensão webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Execute o seguinte comando da CLI para criar um túnel SSH do seu sistema local para o serviço de aplicativo:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Conecte-se à porta de túnel local com seu cliente SFTP e carregue os arquivos na pasta */Home/Tomcat/lib*

    Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Se você tiver uma fonte de dados no nível do servidor, reinicie o aplicativo Linux Serviço de Aplicativo. O Tomcat redefinirá `CATALINA_BASE` como `/home/tomcat` e usará a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para se conectar a fontes de dados em aplicativos Spring boot, sugerimos criar cadeias de conexão e injeta-las em seu arquivo *Application. Properties* .

1. Na seção "configuração" da página Serviço de aplicativo, defina um nome para a cadeia de caracteres, Cole sua cadeia de conexão JDBC no campo valor e defina o tipo como "personalizado". Opcionalmente, você pode definir essa cadeia de conexão como configuração de slot.

    Essa cadeia de conexão é acessível ao nosso aplicativo como uma variável de `CUSTOMCONNSTR_<your-string-name>`ambiente chamada. Por exemplo, a cadeia de conexão que criamos acima será `CUSTOMCONNSTR_exampledb`nomeada.

2. No arquivo *Application. Properties* , referencie essa cadeia de conexão com o nome da variável de ambiente. Para nosso exemplo, usaremos o seguinte.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Consulte a [documentação do Spring boot sobre acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tópico.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Usar Redis como um cache de sessão com Tomcat

Você pode configurar o Tomcat para usar um repositório de sessão externa, como o [cache do Azure para Redis](/azure/azure-cache-for-redis/). Isso permite preservar o estado da sessão do usuário (como dados do carrinho de compras) quando um usuário é transferido para outra instância do aplicativo, por exemplo, quando o dimensionamento automático, reinicialização ou failover ocorre.

Para usar o Tomcat com Redis, você deve configurar seu aplicativo para usar uma implementação [persistentemanager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) . As etapas a seguir explicam esse processo usando o [Gerenciador de sessão dinâmica: Redis-Store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) como um exemplo.

1. Abra um terminal Bash e use `<variable>=<value>` para definir cada uma das variáveis de ambiente a seguir.

    | Variável                 | Valor                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | O nome do grupo de recursos que contém a instância do serviço de aplicativo.       |
    | WEBAPP_NAME              | O nome da instância do serviço de aplicativo.                                     |
    | WEBAPP_PLAN_NAME         | O nome do plano do serviço de aplicativo.                                         |
    | REGIÃO                   | O nome da região em que seu aplicativo está hospedado.                           |
    | REDIS_CACHE_NAME         | O nome do seu cache do Azure para a instância Redis.                           |
    | REDIS_PORT               | A porta SSL que o cache Redis escuta.                             |
    | REDIS_PASSWORD           | A chave de acesso primária para sua instância.                                  |
    | REDIS_SESSION_KEY_PREFIX | Um valor que você especifica para identificar as chaves de sessão que vêm do seu aplicativo. |

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

    Você pode encontrar as informações de nome, porta e chave de acesso na portal do Azure examinando as seções **Propriedades** ou **chaves de acesso** da sua instância de serviço.

2. Crie ou atualize o arquivo *src/main/webapp/META-INF/context. xml* do seu aplicativo com o seguinte conteúdo:

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

    Esse arquivo especifica e configura a implementação do Gerenciador de sessão para seu aplicativo. Ele usa as variáveis de ambiente que você definiu na etapa anterior para manter suas informações de conta fora dos seus arquivos de origem.

3. Use o FTP para carregar o arquivo JAR do Gerenciador de sessão em sua instância do serviço de aplicativo, colocando-o no diretório */Home/Tomcat/lib* Para obter mais informações, consulte [implantar seu aplicativo no serviço de Azure App usando FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Desabilite o [cookie de afinidade de sessão](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para sua instância do serviço de aplicativo. Você pode fazer isso na portal do Azure navegando até seu aplicativo e, em seguida, definindo **configuração > configurações gerais > afinidade arr** como **desativado**. Como alternativa, você pode usar o seguinte comando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Por padrão, o serviço de aplicativo usará cookies de afinidade de sessão para garantir que as solicitações de cliente com sessões existentes sejam roteadas para a mesma instância do seu aplicativo. Esse comportamento padrão não requer nenhuma configuração, mas não pode preservar o estado da sessão do usuário quando a instância do aplicativo é reiniciada ou quando o tráfego é redirecionado para outra instância. Ao [desabilitar a configuração de afinidade da instância arr existente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para desativar o roteamento baseado em cookie de sessão, você permite que o repositório de sessão configurado opere sem interferência.

5. Navegue até a seção **Propriedades** da instância do serviço de aplicativo e localize **endereços IP de saída adicionais**. Elas representam todos os endereços IP de saída possíveis para seu aplicativo. Copie-os para uso na próxima etapa.

6. Para cada endereço IP, crie uma regra de firewall em seu cache do Azure para a instância Redis. Você pode fazer isso no portal do Azure da seção de **Firewall** da instância do Redis. Forneça um nome exclusivo para cada regra e defina os valores de endereço IP **inicial** e **endereço IP final** como o mesmo endereço IP.

7. Navegue até a seção **Configurações avançadas** da instância do Redis e defina **permitir acesso somente via SSL** para **não**. Isso permite que sua instância do serviço de aplicativo se comunique com o cache Redis por meio da infraestrutura do Azure.

8. Atualize a `azure-webapp-maven-plugin` configuração no arquivo *pom. xml* do seu aplicativo para se referir às informações da sua conta do Redis. Esse arquivo usa as variáveis de ambiente que você definiu anteriormente para manter suas informações de conta fora dos seus arquivos de origem.

    Se necessário, altere `1.9.1` para a versão atual do [Plug-in do Maven para o Serviço de Aplicativo do Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.1</version>
        <configuration>            
            <!-- Web App information -->
            <schemaVersion>v2</schemaVersion>
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>            
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>

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

9. Recompile e reimplante seu aplicativo.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Agora, seu aplicativo usará o cache Redis para o gerenciamento de sessão.

Para obter um exemplo que você pode usar para testar essas instruções, consulte o repositório [Dimensioning-stateful-Java-Web-App-on-Azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) no github.

## <a name="docker-containers"></a>Contêineres do Docker

Para usar o JDK do Zulu com suporte do Azure em seus contêineres, efetue pull e use as imagens predefinidas conforme documentado em [página de download do Azul Zulu Enterprise para Azure com suporte](https://www.azul.com/downloads/azure-only/zulu/) ou use os exemplos do `Dockerfile` dos [Repositório GitHub do Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Declaração de suporte

### <a name="runtime-availability"></a>Disponibilidade de tempo de execução

O Serviço de Aplicativo para Linux dá suporte a dois runtimes para hospedagem gerenciada de aplicativos Web do Java:

- O [contêiner do servlet do Tomcat](https://tomcat.apache.org/) para execução de aplicativos empacotados como arquivos Web (WAR). As versões com suporte são 8.5 e 9.0.
- O ambiente de runtime do Java SE para executar aplicativos empacotados como arquivos Java (JAR). As versões com suporte são Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

Builds do Azul Zulu Enterprise do OpenJDK são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Eles contêm todos os componentes para criar e executar aplicativos Java SE. Você pode instalar o JDK da [instalação do Java JDK](https://aka.ms/azure-jdks).

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um runtime do Java com suporte for desativado, os desenvolvedores do Azure que usam o runtime afetado receberão um aviso de reprovação pelo menos seis meses antes de o runtime ser desativado.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

Acesse a central para [Desenvolvedores do Azure para Java](/java/azure/) para conferir inícios rápidos, tutoriais e documentação de referência do Java.

Dúvidas gerais sobre como usar o Serviço de Aplicativo para Linux que não são específicas do desenvolvimento em Java são respondidas nas [Perguntas frequentes do Serviço de Aplicativo no Linux](app-service-linux-faq.md).
