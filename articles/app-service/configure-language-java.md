---
title: Configurar aplicativos Java do Windows
description: Saiba como configurar aplicativos Java para serem executados em instâncias de VM do Windows no serviço Azure App. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativo do Azure, aplicativo Web, Windows, OSS, Java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 5d94da91428da2270e0f690df4dcd43ae43d8597
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961645"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Configurar um aplicativo Java para o serviço Azure App

::: zone pivot="platform-windows"  

Azure App serviço permite que os desenvolvedores de Java compilem, implantem e dimensionem rapidamente seus aplicativos Web Tomcat em um serviço totalmente gerenciado baseado no Windows. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece os principais conceitos e instruções para desenvolvedores Java usando o no serviço de aplicativo. Se você nunca usou Azure App serviço, leia primeiro o [início rápido do Java](quickstart-java.md) . As perguntas gerais sobre como usar o serviço de aplicativo que não são específicas do desenvolvimento do Java são respondidas nas [perguntas frequentes do Windows do serviço de aplicativo](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar o [plug-in do aplicativo Web do Azure para Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar seus arquivos. War. A implantação com IDEs conhecidos também tem suporte com o [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou o [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Caso contrário, o método de implantação dependerá de seu tipo de arquivo:

- Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](./deploy-zip.md#deploy-war-file).
- Para implantar arquivos. jar em Java SE, use o `/api/zipdeploy/` ponto de extremidade do site kudu. Para obter mais informações sobre essa API, confira [essa documentação](./deploy-zip.md#rest).

Não implante seu .war ou .jar usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de runtime. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, confira [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Usar o Flight Recorder

Todos os tempos de execução do Java no serviço de aplicativo usando o azul JVMs vêm com o gravador de vôo Zulu. Você pode usar isso para registrar eventos de nível JVM, System e Java para monitorar o comportamento e solucionar problemas em seus aplicativos Java.

Para fazer uma gravação cronometrada, você precisará do PID (ID do processo) do aplicativo Java. Para localizar o PID, abra um navegador para o site do SCM do seu aplicativo Web em https://<your-site-Name>. scm.azurewebsites.net/ProcessExplorer/. Esta página mostra os processos em execução em seu aplicativo Web. Localize o processo chamado "Java" na tabela e copie o PID correspondente (ID do processo).

Em seguida, abra o **console de depuração** na barra de ferramentas superior do site do SCM e execute o comando a seguir. Substitua `<pid>` pela ID do processo que você copiou anteriormente. Esse comando iniciará uma gravação do criador de perfil de 30 segundos de seu aplicativo Java e gerará um arquivo chamado `timed_recording_example.jfr` no `D:\home` diretório.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Para obter mais informações, confira a [Referência de Comando do Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analisar `.jfr` arquivos

Use o [FTPS](deploy-ftp.md) para baixar o arquivo JFR no seu computador local. Para analisar o arquivo JFR, baixe e instale o [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Zulu Mission Control, confira a [documentação do Azul](https://docs.azul.com/zmc/) e as [instruções de instalação](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, confira [Logs de fluxo no Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Seus logs de aplicativo Java e Tomcat podem ser encontrados no diretório */LogFiles/Application/*

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](../azure-monitor/app/java-trace-logs.md).


## <a name="customization-and-tuning"></a>Personalização e ajuste

O serviço de Azure App dá suporte ao ajuste e à personalização prontos para uso por meio do portal do Azure e da CLI. Examine os seguintes artigos para saber mais sobre a configuração de aplicativos Web específica para não Java:

- [Definir as configurações do aplicativo](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Configurar associações TLS](configure-ssl-bindings.md)
- [Adicionar uma CDN](../cdn/cdn-add-to-web-app.md)
- [Configurar o site do Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Definir opções de runtime do Java

Para definir a memória alocada ou outras opções de tempo de execução da JVM, crie uma [configuração de aplicativo](configure-common.md#configure-app-settings) chamada `JAVA_OPTS` com as opções. O serviço de aplicativo passa essa configuração como uma variável de ambiente para o tempo de execução Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para definir a configuração de aplicativo do plug-in do Maven, adicione marcas de configuração/valor à seção de plug-in do Azure. O seguinte exemplo define um tamanho de heap de Java específico mínimo e máximo:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação no Plano do Serviço de Aplicativo podem usar as seguintes opções:

- Instâncias B1 e S1: `-Xms1024m -Xmx1024m`
- Instâncias B2 e S2: `-Xms3072m -Xmx3072m`
- Instâncias B3 e S3: `-Xms6144m -Xmx6144m`

Ao ajustar as configurações de heap do aplicativo, examine os detalhes do Plano do Serviço de Aplicativo e considere os vários aplicativos e slots de implantação necessários para encontrar a alocação de memória ideal.

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

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho dos aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no Serviço de Aplicativo. Você pode usar o [plug-in do Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache Sling ou usando este [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicativos seguros

Aplicativos Java em execução no serviço de aplicativo têm o mesmo conjunto de [práticas recomendadas de segurança](../security/fundamentals/paas-applications-using-app-services.md) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (autenticação fácil)

Configure a autenticação de aplicativo no portal do Azure com a opção **Autenticação e Autorização**. Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação. Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](configure-authentication-provider-aad.md) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](app-service-authentication-how-to.md).

#### <a name="tomcat"></a>Tomcat

Seu aplicativo Tomcat pode acessar as declarações do usuário diretamente do servlet, convertendo o objeto Principal em um objeto Map. O objeto Map mapeia cada tipo de declaração para uma coleção de declarações para esse tipo. No código abaixo, `request` é uma instância de `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora você pode inspecionar o objeto `Map` para qualquer declaração específica. Por exemplo, o trecho de código a seguir itera todos os tipos de declaração e imprime o conteúdo de cada coleção.

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

Para desconectar os usuários, use o caminho `/.auth/ext/logout`. Para executar outras ações, confira a documentação em [Uso de autenticação e autorização do Serviço de Aplicativo](./app-service-authentication-how-to.md). Também há documentação oficial na interface [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) do Tomcat e seus métodos. Os seguintes métodos de servlet também são hidratados com base na sua configuração do Serviço de Aplicativo:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desabilitar esse recurso, crie uma Configuração de Aplicativo chamada `WEBSITE_AUTH_SKIP_PRINCIPAL` com um valor de `1`. Para desabilitar todos os filtros de servlet adicionados pelo Serviço de Aplicativo, crie uma configuração chamada `WEBSITE_SKIP_FILTERS` com um valor `1`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [proteger um nome DNS personalizado com uma associação TLS no serviço Azure app](configure-ssl-bindings.md) para carregar um certificado TLS/SSL existente e associá-lo ao nome de domínio do seu aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Usar referências de Key Vault

O [Azure KeyVault](../key-vault/general/overview.md) fornece gerenciamento secreto centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou cadeias de conexão) no KeyVault e acessar esses segredos no seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [conceder ao seu aplicativo acesso ao Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência do KeyVault ao seu segredo em uma Configuração de Aplicativo](app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência seja resolvida no segredo imprimindo a variável de ambiente enquanto acessa remotamente o terminal do Serviço de Aplicativo.

Para injetar esses segredos em seu arquivo de configuração Spring ou Tomcat, use a sintaxe de injeção de variável de ambiente (`${MY_ENV_VAR}`). Para arquivos de configuração do Spring, confira esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no Serviço de Aplicativo do Azure no Linux às APM (plataformas de monitoramento de desempenho) NewRelic e AppDynamics.

### <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma nova conta do Relic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente de Java do NewRelic; ele terá um nome de arquivo semelhante a *newrelic-java-x.x.x.zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. Use o [console do kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */Home/site/wwwroot/APM*.
5. Carregue os arquivos do novo agente Java Relic desempacotados em um diretório em */Home/site/wwwroot/APM*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o arquivo YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor de licença de espaço reservado pela sua chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente de Java do site do AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x.x.x.xxxxx.zip*
3. Use o [console do kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */Home/site/wwwroot/APM*.
4. Carregue os arquivos do agente de Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **Java SE**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.

>  Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `-javaagent:/...` opção ao final do valor atual.

## <a name="data-sources"></a>Fontes de dados

### <a name="tomcat"></a>Tomcat

Essas instruções se aplicam a todas as conexões de banco de dados. Você precisará preencher os espaços reservados com nome de classe do driver do banco de dados escolhido e o arquivo JAR. É fornecida uma tabela com nomes de classe e downloads de driver para bancos de dados comuns.

| Banco de dados   | Nome de Classe do Driver                             | Driver JDBC                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Download](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Baixar](https://dev.mysql.com/downloads/connector/j/) (Selecione "Independente de Plataforma") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Para configurar o Tomcat para usar a JDBC (Conectividade de Banco de Dados Java) ou a JPA (API de Persistência Java), primeiro personalize a variável de ambiente `CATALINA_OPTS` lida em Tomcat na inicialização do backup. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis de ambiente na página **Configuração** > **Configurações de Aplicativo** no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados no nível do aplicativo

1. Crie um arquivo *context.xml* no diretório *META-INF/* do seu projeto. Crie o diretório *META-INF/* se ele não existir.

2. Em *context.xml*, adicione um elemento `Context` para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

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

3. Atualize o *web.xml* do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Finalizar a configuração

Por fim, colocaremos os JARs do driver no classpath do Tomcat e reiniciaremos o serviço de aplicativo. Verifique se os arquivos do driver JDBC estão disponíveis para o carregador de classes do Tomcat colocando-os no diretório */home/tomcat/lib*. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, siga estas etapas:

1. No [Cloud Shell](https://shell.azure.com), instale a extensão do aplicativo da Web:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Execute o seguinte comando de CLI para criar um túnel SSH do sistema local para o Serviço de Aplicativo:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Faça a conexão da porta de túnel local com o cliente SFTP e carregue os arquivos na pasta */home/tomcat/lib*.

Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configurando o Tomcat

Para editar os `server.xml` arquivos de configuração ou do Tomcat, primeiro anote a sua versão principal do Tomcat no Portal.

1. Localize o diretório base do Tomcat para sua versão executando o `env` comando. Procure a variável de ambiente que começa com `AZURE_TOMCAT` e corresponde à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório Tomcat para tomcat 8,5.
1. Depois de identificar o diretório base do Tomcat para sua versão, copie o diretório de configuração para `D:\home` . Por exemplo, se `AZURE_TOMCAT85_HOME` tiver um valor de `D:\Program Files (x86)\apache-tomcat-8.5.37` , o novo caminho do diretório copiado seria `D:\home\apache-tomcat-8.5.37` .

Por fim, reinicie o Serviço de Aplicativo. Suas implantações devem ir para `D:\home\site\wwwroot\webapps` assim como antes.

## <a name="configure-java-se"></a>Configurar Java SE

Ao executar um. Aplicativo JAR em Java SE no Windows, `server.port` é passado como uma opção de linha de comando à medida que seu aplicativo é iniciado. Você pode resolver manualmente a porta HTTP da variável de ambiente, `HTTP_PLATFORM_PORT` . O valor dessa variável de ambiente será a porta HTTP em que seu aplicativo deve escutar. 

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do runtime do Java

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O JDK (Java Development Kit) com suporte do Azure é o [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido pela [Azul Systems](https://www.azul.com/).

As atualizações da versão principal serão fornecidas por meio de novas opções de tempo de execução no serviço Azure App para Windows. Os clientes podem atualizar para essas versões mais recentes do Java configurando a implantação do Serviço de Aplicativo. Além disso, são responsáveis por testar e garantir que a atualização da versão principal atenda às suas necessidades.

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano. Para saber mais sobre Java no Azure, confira [este documento de suporte](/azure/developer/java/fundamentals/java-jdk-long-term-support).

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/vuln-metrics/cvss).

O Tomcat 8,0 atingiu [o fim da vida útil (EOL) a partir de 30 de setembro de 2018](https://tomcat.apache.org/tomcat-80-eol.html). Embora o tempo de execução ainda esteja disponível no serviço Azure App, o Azure não aplicará atualizações de segurança ao Tomcat 8,0. Se possível, migre seus aplicativos para o Tomcat 8,5 ou 9,0. O Tomcat 8,5 e o 9,0 estão disponíveis no serviço Azure App. Consulte o [site oficial do Tomcat](https://tomcat.apache.org/whichversion.html) para obter mais informações. 

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um runtime do Java com suporte for desativado, os desenvolvedores do Azure que usam o runtime afetado receberão um aviso de reprovação pelo menos seis meses antes de o runtime ser desativado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a Production Edition do Azul Zulu Enterprise JDK para desenvolvimento local do [site de download da Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

O suporte ao produto para o [azul Zulu com suporte do Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível por meio da Microsoft ao desenvolver para o azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte qualificado do Azure](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Suporte de runtime

Os desenvolvedores podem [abrir um problema](../azure-portal/supportability/how-to-create-azure-support-request.md) com o Azul Zulu JDK por meio do Suporte do Azure se tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Este tópico fornece a declaração de suporte do Java em tempo de execução para Azure App serviço no Windows.

- Para saber mais sobre como hospedar aplicativos Web com o serviço Azure App, consulte [visão geral do serviço de aplicativo](overview.md).
- Para obter informações sobre o Java no desenvolvimento do Azure, consulte [Azure para Java dev Center](/java/azure/?view=azure-java-stable).

::: zone-end

::: zone pivot="platform-linux"

O Serviço de Aplicativo do Azure no Linux permite que desenvolvedores de Java criem, implantem e escalonem rapidamente os aplicativos Web empacotados do Tomcat ou do Java SE (Standard Edition) em um serviço baseado em Linux totalmente gerenciado. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos e instruções essenciais para desenvolvedores de Java que usam um contêiner interno do Linux no Serviço de Aplicativo. Se você nunca usou o Serviço de Aplicativo do Azure, siga o [Início rápido do Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar o [plug-in do Maven para o Serviço de Aplicativo do Azure](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar arquivos .jar e .war. A implantação com IDEs conhecidos também tem suporte com o [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) ou o [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse).

Caso contrário, o método de implantação dependerá de seu tipo de arquivo:

- Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](./deploy-zip.md#deploy-war-file).
- Para implantar arquivos .jar nas imagens do Java SE, use o ponto de extremidade `/api/zipdeploy/` do site do Kudu. Para obter mais informações sobre essa API, confira [essa documentação](./deploy-zip.md#rest).

Não implante seu .war ou .jar usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de runtime. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, confira [Visão geral de diagnóstico do Serviço de Aplicativo do Azure](overview-diagnostics.md).

### <a name="ssh-console-access"></a>Acesso ao console SSH

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Para obter mais informações, confira [Logs de fluxo no Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Seus logs de aplicativos Java e Tomcat podem ser encontrados no diretório */home/LogFiles/Application/* .

>[!NOTE]
>O registro em log no sistema de arquivos local do Serviço de Aplicativo que fica desativado após 12 horas se aplica apenas aos Serviços de Aplicativo baseados no Windows. O registro em log do Armazenamento de Blobs do Azure para Serviços de Aplicativo com base no Linux pode ser configurado apenas usando o [Azure Monitor (versão prévia)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview) 

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](../azure-monitor/app/java-trace-logs.md).

### <a name="troubleshooting-tools"></a>Ferramentas de solução de problemas

As imagens Java internas são baseadas no sistema operacional [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html). Use o gerenciador de pacotes `apk` para instalar quaisquer ferramentas ou comandos de solução de problemas.

### <a name="flight-recorder"></a>Flight Recorder

Todas as imagens Java do Linux no Serviço de Aplicativo têm o Zulu Flight Recorder instalado para que você possa se conectar facilmente à JVM e iniciar uma gravação do criador de perfil ou gerar um despejo de heap.

#### <a name="timed-recording"></a>Gravação programada

Para começar, use o SSH no seu Serviço de Aplicativo e execute o comando `jcmd` para ver uma lista de todos os processos Java em execução. Além do próprio jcmd, você deve ver seu aplicativo Java em execução com um número de pid (ID do processo).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Execute o comando abaixo para iniciar uma gravação de 30 segundos da JVM. Isso criará o perfil da JVM e um arquivo JFR chamado *jfr_example.jfr* no diretório inicial. (Substitua 116 pelo pid do seu aplicativo Java.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Durante o intervalo de 30 segundos, é possível validar a gravação executando `jcmd 116 JFR.check`. Isso mostrará todas as gravações para o processo Java fornecido.

#### <a name="continuous-recording"></a>Gravação contínua

Você pode usar o Zulu Flight Recorder para criar um perfil contínuo de seu aplicativo Java com impacto mínimo no desempenho do tempo de execução ([origem](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Para fazer isso, execute o comando da CLI do Azure a seguir para criar uma Configuração de Aplicativo chamada JAVA_OPTS com a configuração necessária. O conteúdo da Configuração de Aplicativo JAVA_OPTS é passado para o comando `java` quando o aplicativo é iniciado.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Após o início da gravação, você pode despejar os dados da gravação atual a qualquer momento usando o comando `JFR.dump`.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Para obter mais informações, confira a [Referência de Comando do Jcmd](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analisar gravações

Use o [FTPS](deploy-ftp.md) para baixar o arquivo JFR no seu computador local. Para analisar o arquivo JFR, baixe e instale o [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Zulu Mission Control, confira a [documentação do Azul](https://docs.azul.com/zmc/) e as [instruções de instalação](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Personalização e ajuste

O Serviço de Aplicativo do Azure para Linux é compatível com o ajuste e a personalização de caixas por meio do portal e da CLI do Azure. Examine os seguintes artigos para saber mais sobre a configuração de aplicativos Web específica para não Java:

- [Definir as configurações do aplicativo](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Configurar associações SSL](configure-ssl-bindings.md)
- [Adicionar uma CDN](../cdn/cdn-add-to-web-app.md)
- [Configurar o site do Kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Definir opções de runtime do Java

Para definir a memória alocada ou outras opções de runtime da JVM nos ambientes do Tomcat e do Java SE, crie uma [configuração de aplicativo](configure-common.md#configure-app-settings) chamada `JAVA_OPTS` com as opções. O Serviço de Aplicativo no Linux passa essa configuração como uma variável de ambiente para o runtime do Java quando ele é iniciado.

No portal do Azure, em **Configurações do aplicativo** para o aplicativo Web, crie uma configuração de aplicativo denominada `JAVA_OPTS` que inclui as configurações adicionais, como `-Xms512m -Xmx1204m`.

Para definir a configuração de aplicativo do plug-in do Maven, adicione marcas de configuração/valor à seção de plug-in do Azure. O seguinte exemplo define um tamanho de heap de Java específico mínimo e máximo:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Os desenvolvedores que executam um único aplicativo com um slot de implantação no Plano do Serviço de Aplicativo podem usar as seguintes opções:

- Instâncias B1 e S1: `-Xms1024m -Xmx1024m`
- Instâncias B2 e S2: `-Xms3072m -Xmx3072m`
- Instâncias B3 e S3: `-Xms6144m -Xmx6144m`

Ao ajustar as configurações de heap do aplicativo, examine os detalhes do Plano do Serviço de Aplicativo e considere os vários aplicativos e slots de implantação necessários para encontrar a alocação de memória ideal.

Se você estiver implementando um aplicativo JAR, ele deverá ser nomeado *app.jar* para que a imagem interna possa identificar corretamente seu aplicativo. (O plug-in do Maven faz isso renomeando automaticamente.) Caso não deseje renomear o JAR como *app.jar*, você pode carregar um script de shell com o comando para executar seu JAR. Em seguida, cole o caminho completo desse script na caixa de texto [Arquivo de inicialização](faq-app-service-linux.md#built-in-images), na seção Configuração do portal. O script de inicialização não é executado no diretório no qual ele é colocado. Portanto, sempre use caminhos absolutos para fazer referência a arquivos em seu script de inicialização (por exemplo: `java -jar /home/myapp/myapp.jar`).

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

Se o seu aplicativo Java for particularmente grande, você deverá aumentar o limite de tempo de inicialização. Para fazer isso, crie uma configuração de aplicativo `WEBSITES_CONTAINER_START_TIME_LIMIT` e defina-a pelo número de segundos que o Serviço de Aplicativo deve aguardar antes de atingir o tempo limite. O valor máximo é de `1800` segundos.

### <a name="pre-compile-jsp-files"></a>Pré-compilar arquivos JSP

Para melhorar o desempenho dos aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no Serviço de Aplicativo. Você pode usar o [plug-in do Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache Sling ou usando este [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicativos seguros

Os aplicativos Java em execução no Serviço de Aplicativo para Linux têm o mesmo conjunto de [melhores práticas de segurança](../security/fundamentals/paas-applications-using-app-services.md) que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (autenticação fácil)

Configure a autenticação de aplicativo no portal do Azure com a opção **Autenticação e Autorização**. Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação. Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](configure-authentication-provider-aad.md) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](app-service-authentication-how-to.md).

#### <a name="tomcat"></a>Tomcat

Seu aplicativo Tomcat pode acessar as declarações do usuário diretamente do servlet, convertendo o objeto Principal em um objeto Map. O objeto Map mapeia cada tipo de declaração para uma coleção de declarações para esse tipo. No código abaixo, `request` é uma instância de `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Agora você pode inspecionar o objeto `Map` para qualquer declaração específica. Por exemplo, o trecho de código a seguir itera todos os tipos de declaração e imprime o conteúdo de cada coleção.

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

Para desconectar os usuários, use o caminho `/.auth/ext/logout`. Para executar outras ações, confira a documentação em [Uso de autenticação e autorização do Serviço de Aplicativo](./app-service-authentication-how-to.md). Também há documentação oficial na interface [HttpServletRequest](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) do Tomcat e seus métodos. Os seguintes métodos de servlet também são hidratados com base na sua configuração do Serviço de Aplicativo:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Para desabilitar esse recurso, crie uma Configuração de Aplicativo chamada `WEBSITE_AUTH_SKIP_PRINCIPAL` com um valor de `1`. Para desabilitar todos os filtros de servlet adicionados pelo Serviço de Aplicativo, crie uma configuração chamada `WEBSITE_SKIP_FILTERS` com um valor `1`.

#### <a name="spring-boot"></a>Spring Boot

Os desenvolvedores do Spring Boot podem usar o [inicializador do Spring Boot do Azure Active Directory](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) para proteger aplicativos usando APIs e anotações conhecidas do Spring Security. Lembre-se de aumentar o tamanho máximo do cabeçalho no arquivo *application.properties*. Sugerimos um valor igual a `16384`.

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções em [Proteger um nome DNS personalizado com uma associação SSL no Serviço de Aplicativo do Azure](configure-ssl-bindings.md) para carregar um certificado SSL existente e associá-lo ao nome de domínio do aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Usar referências de Key Vault

O [Azure KeyVault](../key-vault/general/overview.md) fornece gerenciamento secreto centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou cadeias de conexão) no KeyVault e acessar esses segredos no seu aplicativo por meio de variáveis de ambiente.

Primeiro, siga as instruções para [conceder ao seu aplicativo acesso ao Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e [fazer uma referência do KeyVault ao seu segredo em uma Configuração de Aplicativo](app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência seja resolvida no segredo imprimindo a variável de ambiente enquanto acessa remotamente o terminal do Serviço de Aplicativo.

Para injetar esses segredos em seu arquivo de configuração Spring ou Tomcat, use a sintaxe de injeção de variável de ambiente (`${MY_ENV_VAR}`). Para arquivos de configuração do Spring, confira esta documentação sobre [configurações externas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Usar o Repositório de Chaves Java

Por padrão, todos os certificados públicos ou privados [carregados no Serviço de Aplicativo do Linux](configure-ssl-certificate.md) serão carregados nos respectivos Repositório de Chaves Java quando o contêiner for iniciado. Após carregar seu certificado, você precisará reiniciar o Serviço de Aplicativo para que ele seja carregado no Repositório de Chaves Java. Os certificados públicos são carregados no Repositório de Chaves em `$JAVA_HOME/jre/lib/security/cacerts` e os certificados privados são armazenados em `$JAVA_HOME/lib/security/client.jks`.

A configuração adicional pode ser necessária para criptografar sua conexão JDBC com certificados no Repositório de Chaves Java. Confira a documentação para o driver JDBC escolhido.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Inicializar o Repositório de Chaves Java

Para inicializar o objeto `import java.security.KeyStore`, carregue o arquivo do repositório de chaves com a senha. A senha padrão para ambos os repositórios de chaves é "changeit".

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

Você pode carregar certificados manualmente no repositório de chaves. Crie uma configuração de aplicativo, `SKIP_JAVA_KEYSTORE_LOAD`, com um valor de `1` para desabilitar o Serviço de Aplicativo do carregamento dos certificados no repositório de chaves automaticamente. Todos os certificados públicos carregados no Serviço de Aplicativo por meio do portal do Azure são armazenados em `/var/ssl/certs/`. Os certificados particulares são armazenados em `/var/ssl/private/`.

Você pode interagir ou depurar a Java Key Tool [abrindo uma conexão SSH](configure-linux-open-ssh-session.md) no seu Serviço de Aplicativo e executando o comando `keytool`. Confira a [documentação da Key Tool](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) para obter uma lista de comandos. Para obter mais informações sobre a API KeyStore, confira [a documentação oficial](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Configurar plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no Serviço de Aplicativo do Azure no Linux às APM (plataformas de monitoramento de desempenho) NewRelic e AppDynamics.

### <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma conta de NewRelic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente de Java do NewRelic; ele terá um nome de arquivo semelhante a *newrelic-java-x.x.x.zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. [SSH em sua instância do Serviço de Aplicativo](configure-linux-open-ssh-session.md) e crie um diretório */home/site/wwwroot/apm*.
5. Carregue os arquivos do agente NewRelic Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o arquivo YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor de licença de espaço reservado pela sua chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente de Java do site do AppDynamics, o nome do arquivo será semelhante a *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH em sua instância do Serviço de Aplicativo](configure-linux-open-ssh-session.md) e crie um diretório */home/site/wwwroot/apm*.
4. Carregue os arquivos do agente de Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos do seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
5. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.
    - Se você estiver usando **Java SE**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` quando `<app-name>` for o nome do seu Serviço de Aplicativo.

    > [!NOTE]
    > Se você já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescente o `-javaagent:/...` opção ao final do valor atual.
    
## <a name="configure-jar-applications"></a>Configurar aplicativos JAR

### <a name="starting-jar-apps"></a>Iniciar aplicativos JAR

Por padrão, o Serviço de Aplicativo espera que seu aplicativo JAR seja nomeado para *app.jar*. Se ele tiver esse nome, será executado automaticamente. Para usuários do Maven, você pode definir o nome do JAR incluindo `<finalName>app</finalName>` na seção `<build>` de seu *pom. xml*. [Você pode fazer o mesmo no Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) definindo a propriedade `archiveFileName`.

Se você quiser usar um nome diferente para seu JAR, também deverá fornecer o [Comando de Inicialização](faq-app-service-linux.md#built-in-images) que executa o arquivo JAR. Por exemplo, `java -jar my-jar-app.jar`. Você pode definir o valor do seu comando de inicialização no Portal, em Configuração > Configurações Gerais, ou com uma Configuração de Aplicativo chamada `STARTUP_COMMAND`.

### <a name="server-port"></a>Porta do servidor

O Serviço de Aplicativo do Linux roteia solicitações de entrada para a porta 80, de modo que seu aplicativo também atenda na porta 80. Você pode fazer isso na configuração do aplicativo (como o arquivo *application.properties* do Spring) ou no comando de inicialização (por exemplo, `java -jar spring-app.jar --server.port=80`). Confira a seguinte documentação para estruturas Java comuns:

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
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Download](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Para configurar o Tomcat para usar a JDBC (Conectividade de Banco de Dados Java) ou a JPA (API de Persistência Java), primeiro personalize a variável de ambiente `CATALINA_OPTS` lida em Tomcat na inicialização do backup. Defina esses valores por meio de uma configuração de aplicativo no [plug-in Maven do Serviço de Aplicativo](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Ou defina as variáveis de ambiente na página **Configuração** > **Configurações de Aplicativo** no portal do Azure.

Em seguida, determine se a fonte de dados deve estar disponível para um aplicativo ou para todos os aplicativos em execução no servlet do Tomcat.

#### <a name="application-level-data-sources"></a>Fontes de dados no nível do aplicativo

1. Crie um arquivo *context.xml* no diretório *META-INF/* do seu projeto. Crie o diretório *META-INF/* se ele não existir.

2. Em *context.xml*, adicione um elemento `Context` para vincular a fonte de dados a um endereço JNDI. Substitua o espaço reservado `driverClassName` pelo nome de classe do seu driver da tabela acima.

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

3. Atualize o *web.xml* do aplicativo para usar a fonte de dados no aplicativo.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Recursos compartilhados no nível do servidor

A adição de uma fonte de dados compartilhada no nível do servidor exigirá a edição do server.xml do Tomcat. Primeiro, carregue um [script de inicialização](faq-app-service-linux.md#built-in-images) e defina o caminho para o script em **Configuração** > **Comando de Inicialização**. Você pode carregar o script de inicialização usando o [FTP](deploy-ftp.md).

O script de inicialização fará uma [transformação de xsl](https://www.w3schools.com/xml/xsl_intro.asp) no arquivo server.xml e produzirá o arquivo xml resultante para `/usr/local/tomcat/conf/server.xml`. O script de inicialização deve instalar o libxslt via apk. O arquivo xsl e o script de inicialização podem ser carregados via FTP. Confira abaixo um exemplo de script de inicialização.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Um exemplo de arquivo xsl é fornecido abaixo. O arquivo xsl de exemplo adiciona um novo nó do conector ao server.xml do Tomcat.

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

Por fim, coloque os JARs do driver no classpath do Tomcat e reinicie o Serviço de Aplicativo.

1. Verifique se os arquivos do driver JDBC estão disponíveis para o carregador de classes do Tomcat colocando-os no diretório */home/tomcat/lib*. (Crie esse diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, siga estas etapas:

    1. No [Cloud Shell](https://shell.azure.com), instale a extensão do aplicativo da Web:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Execute o seguinte comando de CLI para criar um túnel SSH do sistema local para o Serviço de Aplicativo:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Faça a conexão da porta de túnel local com o cliente SFTP e carregue os arquivos na pasta */home/tomcat/lib*.

    Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](deploy-configure-credentials.md).

2. Se você tiver uma fonte de dados no nível do servidor, reinicie o aplicativo Linux Serviço de Aplicativo. O Tomcat redefinirá `CATALINA_BASE` como `/home/tomcat` e usará a configuração atualizada.

### <a name="spring-boot"></a>Spring Boot

Para conectar-se a fontes de dados nos aplicativos Spring Boot, sugerimos criar cadeias de conexão e injetá-las no arquivo *application.properties*.

1. Na seção "Configuração" da página Serviço de Aplicativo, defina um nome para a cadeia de caracteres, cole sua cadeia de conexão JDBC no campo de valor e defina o tipo como "Personalizado". Opcionalmente, defina essa cadeia de conexão como configuração de slot.

    Essa cadeia de conexão é acessível ao nosso aplicativo como uma variável de ambiente nomeada `CUSTOMCONNSTR_<your-string-name>`. Por exemplo, a cadeia de conexão que criamos acima será nomeada `CUSTOMCONNSTR_exampledb`.

2. No arquivo *application.properties*, faça referência a essa cadeia de conexão com o nome da variável de ambiente. Para o nosso exemplo, usaríamos o seguinte:

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Confira a [documentação do Spring Boot sobre acesso a dados](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) e [configurações externalizadas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) para obter mais informações sobre este tópico.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Usar o Redis como um cache de sessão com o Tomcat

É possível configurar o Tomcat para usar um repositório de sessão externa como o [Cache Redis do Azure](../azure-cache-for-redis/index.yml). Isso permite preservar o estado de sessão do usuário (como dados do carrinho de compras) quando um usuário é transferido para outra instância do seu aplicativo, por exemplo, quando ocorre o dimensionamento automático, a reinicialização ou o failover.

Para usar o Tomcat com Redis, você deve configurar seu aplicativo para usar uma implementação [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html). As etapas a seguir explicam esse processo usando [Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) como exemplo.

1. Abra um terminal Bash e use `<variable>=<value>` para definir cada uma das variáveis de ambiente a seguir.

    | Variável                 | Valor                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | O nome do grupo de recursos que contém sua instância do Serviço de Aplicativo.       |
    | WEBAPP_NAME              | O nome da sua instância do Serviço de Aplicativo.                                     |
    | WEBAPP_PLAN_NAME         | O nome do Plano do Serviço de Aplicativo.                                         |
    | REGION                   | O nome da região em que o aplicativo está hospedado.                           |
    | REDIS_CACHE_NAME         | O nome da instância do Cache do Azure para Redis.                           |
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

    Você pode encontrar as informações de nome, porta e chave de acesso na portal do Azure examinando as seções **Propriedades** ou **Chaves de Acesso** de sua instância de serviço.

2. Crie ou atualize o arquivo *src/main/webapp/META-INF/context.xml* do seu aplicativo com o seguinte conteúdo:

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

    Esse arquivo especifica e configura a implementação do gerenciador de sessões para seu aplicativo. Ele usa as variáveis de ambiente definidas na etapa anterior para manter as informações da sua conta fora dos arquivos de origem.

3. Use o FTP para carregar o arquivo JAR do gerenciador de sessões na sua instância do Serviço de Aplicativo, colocando-o no diretório */Home/Tomcat/lib*. Para obter mais informações, confira [Implantar seu aplicativo no Serviço de Aplicativo do Azure usando FTP/S](./deploy-ftp.md).

4. Desabilite o [cookie de afinidade de sessão](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para sua instância do Serviço de Aplicativo. Você pode fazer isso no portal do Azure, navegando até seu aplicativo e definindo **Configuração > Configurações Gerais > Afinidade de ARR** para **Desativado**. Como alternativa, você pode usar o seguinte comando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Por padrão, o Serviço de Aplicativo usará cookies de afinidade de sessão para garantir que as solicitações de cliente com sessões existentes sejam roteadas para a mesma instância do seu aplicativo. Esse comportamento padrão não requer configuração, mas não pode preservar o estado da sessão do usuário quando a instância do aplicativo é reiniciada ou quando o tráfego é redirecionado para outra instância. Quando você [desabilita a configuração de afinidade de instância do ARR existente](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) para desativar o roteamento baseado em cookie de sessão, você permite que o armazenamento de sessão configurado opere sem interferência.

5. Navegue até a seção **Propriedades** da sua instância do Serviço de Aplicativo e encontre **Endereços IP Adicionais de Saída**. Eles representam todos os endereços IP de saída possíveis para o seu aplicativo. Copie-os para uso na próxima etapa.

6. Para cada endereço IP, crie uma regra de firewall na sua instância do Cache do Azure para Redis. Você pode fazer isso no portal do Azure na seção **Firewall** da sua instância do Redis. Forneça um nome exclusivo para cada regra e defina os valores de **Endereço IP inicial** e **Endereço IP final** para o mesmo endereço IP.

7. Navegue até a seção **Configurações Avançadas** da sua instância do Redis e defina **Permitir acesso apenas via SSL** como **Não**. Isso permite que sua instância do Serviço de Aplicativo se comunique com o cache do Redis por meio da infraestrutura do Azure.

8. Atualize a configuração `azure-webapp-maven-plugin` no arquivo *pom.xml* do seu aplicativo para consultar as informações da sua conta Redis. Esse arquivo usa as variáveis de ambiente definidas anteriormente para manter as informações da sua conta fora dos arquivos de origem.

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

9. Recompile e reimplemente seu aplicativo.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Seu aplicativo agora usará o cache do Redis para gerenciamento de sessões.

Para obter uma amostra que você pode usar para testar essas instruções, confira o repositório [scaling-stateful-java-web-app-on-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) no GitHub.

## <a name="docker-containers"></a>Contêineres do Docker

Para usar o JDK do Zulu com suporte do Azure em seus contêineres, efetue pull e use as imagens predefinidas conforme documentado em [página de download do Azul Zulu Enterprise para Azure com suporte](https://www.azul.com/downloads/azure-only/zulu/) ou use os exemplos do `Dockerfile` dos [Repositório GitHub do Microsoft Java](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Declaração de suporte

### <a name="runtime-availability"></a>Disponibilidade de runtime

O Serviço de Aplicativo para Linux dá suporte a dois runtimes para hospedagem gerenciada de aplicativos Web do Java:

- O [contêiner do servlet do Tomcat](https://tomcat.apache.org/) para execução de aplicativos empacotados como arquivos Web (WAR). As versões com suporte são 8.5 e 9.0.
- O ambiente de runtime do Java SE para executar aplicativos empacotados como arquivos Java (JAR). As versões com suporte são Java 8 e 11.

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

Builds do Azul Zulu Enterprise do OpenJDK são uma distribuição sem custo, multiplataforma e pronta para produção do OpenJDK para Azure e Azure Stack da Microsoft e da Azul Systems. Eles contêm todos os componentes para criar e executar aplicativos Java SE. Você pode instalar o JDK a partir da [Instalação do Java JDK](https://aka.ms/azure-jdks).

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano.

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um runtime do Java com suporte for desativado, os desenvolvedores do Azure que usam o runtime afetado receberão um aviso de reprovação pelo menos seis meses antes de o runtime ser desativado.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Próximas etapas

Acesse a central para [Desenvolvedores do Azure para Java](/java/azure/) para conferir inícios rápidos, tutoriais e documentação de referência do Java.

Dúvidas gerais sobre como usar o Serviço de Aplicativo para Linux que não são específicas do desenvolvimento em Java são respondidas nas [Perguntas frequentes do Serviço de Aplicativo no Linux](faq-app-service-linux.md).

::: zone-end