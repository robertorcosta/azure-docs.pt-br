---
title: Configurar aplicativos Windows Java
description: Saiba como configurar aplicativos Java para serem executados nas instâncias do Windows VM no Azure App Service. Este artigo mostra as tarefas de configuração mais comuns.
keywords: serviço de aplicativo azure, web app, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 179a2120714460c955d1fd3e345ebcd963ae564d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453550"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Configure um aplicativo Windows Java para o Azure App Service

O Azure App Service permite que os desenvolvedores Java construam, implantem e dimensionem rapidamente seus aplicativos web Tomcat em um serviço totalmente gerenciado baseado no Windows. Implante aplicativos com o plug-ins do Maven na linha de comando ou em editores, como IntelliJ, Eclipse ou Visual Studio Code.

Este guia fornece conceitos-chave e instruções para desenvolvedores Java que usam no App Service. Se você nunca usou o Azure App Service, você deve ler o [Quickstart Java](app-service-web-get-started-java.md) primeiro. Perguntas gerais sobre o uso do App Service que não são específicas para o desenvolvimento java são respondidas no [App Service Windows FAQ](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Implantação do aplicativo

Você pode usar [o Azure Web App Plugin para Maven](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) para implantar seus arquivos .war. A implantação com IDEs populares também é suportada com [o Azure Toolkit para IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) ou [Azure Toolkit para Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Caso contrário, seu método de implantação dependerá do seu tipo de arquivamento:

- Para implantar arquivos .war para Tomcat, use o ponto de extremidade `/api/wardeploy/` para executar POST de seu arquivo morto. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Para implantar arquivos .jar em `/api/zipdeploy/` Java SE, use o ponto final do site kudu. Para obter mais informações sobre essa API, confira [essa documentação](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Não implante seu .war usando FTP. A ferramenta FTP foi projetada para carregar os scripts de inicialização, dependências ou outros arquivos de runtime. Não é a opção ideal para a implantação de aplicativos Web.

## <a name="logging-and-debugging-apps"></a>Aplicativos de registro em log e depuração

Relatórios de desempenho, visualizações de tráfego e verificações de integridade estão disponíveis para cada aplicativo por meio do portal do Azure. Para obter mais informações, consulte [a visão geral dos diagnósticos do Azure App Service](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Use gravador de voo

Todos os tempos de execução Java no App Service usando os JVMs Azul vêm com o Zulu Flight Recorder. Você pode usar isso para gravar eventos de nível JVM, sistema e Java para monitorar o comportamento e solucionar problemas em suas aplicações Java.

Para fazer uma gravação cronometrada, você precisará do PID (Process ID) do aplicativo Java. Para encontrar o PID, abra um navegador no site do SCM do seu aplicativo web em https://<seu nome no site>.scm.azurewebsites.net/ProcessExplorer/. Esta página mostra os processos em execução em seu aplicativo web. Encontre o processo chamado "java" na tabela e copie o PID (Process ID) correspondente.

Em seguida, abra o **Console de depuração** na barra de ferramentas superior do site do SCM e execute o seguinte comando. Substitua pelo `<pid>` ID de processo copiado anteriormente. Este comando iniciará uma gravação de perfil de 30 `timed_recording_example.jfr` segundos `D:\home` do seu aplicativo Java e gerará um arquivo nomeado no diretório.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Para obter mais informações, consulte o [Comando JCMD Reference](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analisar `.jfr` arquivos

Use [FTPS](deploy-ftp.md) para baixar seu arquivo JFR para sua máquina local. Para analisar o arquivo JFR, baixe e instale [o Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Para obter instruções sobre o Controle de Missão Zulu, consulte a [documentação azul](https://docs.azul.com/zmc/) e as [instruções de instalação.](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)

### <a name="stream-diagnostic-logs"></a>Logs de diagnóstico de fluxo

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Para obter mais informações, consulte [Stream logs in Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Registro em log do aplicativo

Habilite o [registro em log de aplicativos](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) por meio do portal do Azure ou da [CLI do Azure](/cli/azure/webapp/log#az-webapp-log-config) para configurar o Serviço de Aplicativo do Azure para gravar a saída do console padrão do aplicativo e os fluxos de erro do console padrão no sistema de arquivos local ou no Armazenamento de Blobs do Azure. O registro em log na instância do sistema de arquivos do Serviço de Aplicativo local será desabilitado 12 horas após ser configurado. Se você precisar de uma retenção mais longa, configure o aplicativo para gravar a saída em um contêiner do armazenamento de blobs. Os logs do aplicativo Java e Tomcat podem ser encontrados no diretório */LogFiles/Application/.*

Se o aplicativo usar [Logback](https://logback.qos.ch/) ou [Log4j](https://logging.apache.org/log4j) para rastreamento, será possível encaminhá-los para revisão no Azure Application Insights usando as instruções de configuração de estrutura de registros em [Explorar os logs de rastreamento de Java no Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Personalização e ajuste

O Azure App Service suporta a sintonia e a personalização da caixa através do portal Azure e da CLI. Revise os seguintes artigos para configuração de aplicativo web não específica de Java:

- [Definir configurações de aplicativo](configure-common.md#configure-app-settings)
- [Configurar um domínio personalizado](app-service-web-tutorial-custom-domain.md)
- [Configurar ligações TLS](configure-ssl-bindings.md)
- [Adicionar uma CDN](../cdn/cdn-add-to-web-app.md)
- [Configure o site kudu](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Definir opções de runtime do Java

Para definir a memória alocada ou outras opções `JAVA_OPTS` de tempo de execução jvm, crie uma [configuração](configure-common.md#configure-app-settings) de aplicativo nomeada com as opções. O App Service passa essa configuração como uma variável de ambiente para o tempo de execução Java quando ele é iniciado.

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

Para melhorar o desempenho dos aplicativos Tomcat, você pode compilar seus arquivos JSP antes de implantar no App Service. Você pode usar o [plugin Maven](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) fornecido pelo Apache Sling, ou usar este [arquivo de compilação Ant](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Aplicativos seguros

Os aplicativos Java em execução no App Service têm o mesmo conjunto de [práticas recomendadas](/azure/security/security-paas-applications-using-app-services) de segurança que outros aplicativos.

### <a name="authenticate-users-easy-auth"></a>Autenticar usuários (Easy Auth)

Configure a autenticação do aplicativo no portal Azure com a opção **Autenticação e Autorização.** Em seguida, será possível habilitar a autenticação usando o Azure Active Directory ou os logons de redes sociais, como Facebook, Google ou GitHub. A configuração do portal do Azure só funciona ao configurar um único provedor de autenticação. Para saber mais, confira [Configurar o aplicativo do Serviço de Aplicativo para usar o logon do Azure Active Directory](configure-authentication-provider-aad.md) e os artigos relacionados para outros provedores de identidade. Se você precisar habilitar vários provedores de entrada, siga as instruções no artigo [Personalizar a autenticação do Serviço de Aplicativo](app-service-authentication-how-to.md).

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

### <a name="configure-tlsssl"></a>Configurar TLS/SSL

Siga as instruções no [Secure um nome DNS personalizado com uma vinculação TLS no Azure App Service](configure-ssl-bindings.md) para carregar um certificado TLS/SSL existente e vinculá-lo ao nome de domínio do aplicativo. Por padrão, o aplicativo ainda permitirá conexões HTTP – siga as etapas específicas no tutorial para impor o SSL e o TLS.

### <a name="use-keyvault-references"></a>Use referências keyvault

[O Azure KeyVault](../key-vault/general/overview.md) fornece gerenciamento secreto centralizado com políticas de acesso e histórico de auditoria. Você pode armazenar segredos (como senhas ou strings de conexão) no KeyVault e acessar esses segredos em seu aplicativo através de variáveis de ambiente.

Primeiro, siga as instruções para [conceder acesso ao seu aplicativo ao Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) e fazer uma referência do [KeyVault ao seu segredo em uma configuração de aplicativo](app-service-key-vault-references.md#reference-syntax). Você pode validar que a referência resolve o segredo imprimindo a variável ambiente enquanto acessa remotamente o terminal do Serviço de Aplicativo.

Para injetar esses segredos no seu arquivo de configuração Spring ou`${MY_ENV_VAR}`Tomcat, use a sintaxe de injeção variável do ambiente (). Para arquivos de configuração de mola, consulte esta documentação em [configurações externalizadas](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Configure plataformas APM

Esta seção mostra como conectar aplicativos Java implantados no Azure App Service no Linux com as plataformas APM (Application Monitoring, monitoramento de desempenho de aplicativos NewRelic e AppDynamics).

### <a name="configure-new-relic"></a>Configurar o New Relic

1. Crie uma conta do New Relic em [NewRelic.com](https://newrelic.com/signup)
2. Baixe o agente Java do NewRelic, ele terá um nome de arquivo semelhante ao *newrelic-java-x.x.zip*.
3. Copie sua chave de licença, você precisará dela para configurar o agente mais tarde.
4. Use o [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */home/site/wwwroot/apm*.
5. Carregue os arquivos de agente New Relic Java descompactados em um diretório em */home/site/wwwroot/apm*. Os arquivos para o seu agente devem estar em */home/site/wwwroot/apm/newrelic*.
6. Modifique o arquivo YAML em */home/site/wwwroot/apm/newrelic/newrelic.yml* e substitua o valor da licença de espaço reservado por sua própria chave de licença.
7. No portal do Azure, navegue até seu aplicativo no serviço de aplicativo e criar uma nova configuração de aplicativo.
    - Se seu aplicativo estiver usando **Java SE**, crie uma variável de ambiente denominada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se você estiver usando **Tomcat**, crie uma variável de ambiente denominada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics em [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Baixe o agente Java do site da AppDynamics, o nome do arquivo será semelhante ao *AppServerAgent-x.x.x.xxxxx.zip*
3. Use o [console Kudu](https://github.com/projectkudu/kudu/wiki/Kudu-console) para criar um novo diretório */home/site/wwwroot/apm*.
4. Carregue os arquivos do agente Java em um diretório em */home/site/wwwroot/apm*. Os arquivos para o seu agente devem estar em */home/site/wwwroot/apm/appdynamics*.
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

#### <a name="finalize-configuration"></a>Finalizar a configuração

Finalmente, colocaremos os JARs do driver no classpath do Tomcat e reiniciaremos seu Serviço de Aplicativo. Certifique-se de que os arquivos de driver JDBC estão disponíveis para o classe Loader do Tomcat, colocando-os no diretório */home/tomcat/lib.* (Crie este diretório se ele ainda não existir.) Para carregar esses arquivos na instância do Serviço de Aplicativo, execute as seguintes etapas:

1. No [Cloud Shell,](https://shell.azure.com)instale a extensão do webapp:

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Execute o seguinte comando CLI para criar um túnel SSH do seu sistema local para o Serviço de Aplicativos:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Conecte-se à porta de tunelamento local com seu cliente SFTP e carregue os arquivos para a pasta */home/tomcat/lib.*

Como alternativa, você pode usar um cliente de FTP para carregar o driver JDBC. Siga estas [instruções para obter suas credenciais FTP](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Configuração do Tomcat

Para editar os arquivos `server.xml` de configuração do Tomcat ou outros, primeiro tome uma nota da versão principal do Tomcat no portal.

1. Encontre o diretório inicial do Tomcat `env` para sua versão executando o comando. Pesquise a variável de `AZURE_TOMCAT`ambiente que começa e corresponda à sua versão principal. Por exemplo, `AZURE_TOMCAT85_HOME` aponta para o diretório Tomcat para Tomcat 8.5.
1. Depois de identificar o diretório inicial do Tomcat para sua `D:\home`versão, copie o diretório de configuração para . Por exemplo, `AZURE_TOMCAT85_HOME` se tivesse `D:\Program Files (x86)\apache-tomcat-8.5.37`um valor de , o novo `D:\home\apache-tomcat-8.5.37`caminho do diretório copiado seria .

Por fim, reinicie o Serviço de Aplicativo. Suas implantações devem `D:\home\site\wwwroot\webapps` ir como antes.

## <a name="configure-java-se"></a>Configurar Java SE

Ao executar um . O aplicativo JAR no `server.port` Java SE no Windows, é passado como uma opção de linha de comando à medida que seu aplicativo é iniciado. Você pode resolver manualmente a porta `HTTP_PLATFORM_PORT`HTTP a partir da variável ambiente, . O valor desta variável de ambiente será a porta HTTP que seu aplicativo deve ouvir. 

## <a name="java-runtime-statement-of-support"></a>Declaração de suporte do runtime do Java

### <a name="jdk-versions-and-maintenance"></a>Versões e manutenção do JDK

O JDK (Java Development Kit) com suporte do Azure é o [Zulu](https://www.azul.com/downloads/azure-only/zulu/) fornecido pela [Azul Systems](https://www.azul.com/).

As principais atualizações de versão serão fornecidas através de novas opções de tempo de execução no Azure App Service para Windows. Os clientes podem atualizar para essas versões mais recentes do Java configurando a implantação do Serviço de Aplicativo. Além disso, são responsáveis por testar e garantir que a atualização da versão principal atenda às suas necessidades.

Os JDKs com suporte são corrigidos automaticamente a cada trimestre em janeiro, abril, julho e outubro de cada ano. Para obter mais informações sobre java no Azure, consulte [este documento de suporte](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Atualizações de segurança

Os patches e as correções para grandes vulnerabilidades de segurança serão liberados assim que forem disponibilizados pela Azul Systems. Uma "grande" vulnerabilidade é definida por uma pontuação básica de 9,0 ou mais na [versão 2 do NIST Common Vulnerability Scoring System](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 chegou ao [Fim da Vida (EOL) em 30 de setembro de 2018](https://tomcat.apache.org/tomcat-80-eol.html). Embora o tempo de execução ainda seja aviário no Azure App Service, o Azure não aplicará atualizações de segurança ao Tomcat 8.0. Se possível, migre seus aplicativos para o Tomcat 8.5 ou 9.0. Tanto o Tomcat 8.5 quanto o 9.0 estão disponíveis no Azure App Service. Consulte o site oficial da [Tomcat](https://tomcat.apache.org/whichversion.html) para obter mais informações. 

### <a name="deprecation-and-retirement"></a>Reprovação e desativação

Se um runtime do Java com suporte for desativado, os desenvolvedores do Azure que usam o runtime afetado receberão um aviso de reprovação pelo menos seis meses antes de o runtime ser desativado.

### <a name="local-development"></a>Desenvolvimento local

Os desenvolvedores podem baixar a Production Edition do Azul Zulu Enterprise JDK para desenvolvimento local do [site de download da Azul](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Suporte ao desenvolvimento

O suporte ao produto para o [Azul Zulu JDK suportado pelo Azure](https://www.azul.com/downloads/azure-only/zulu/) está disponível através da Microsoft ao desenvolver para o Azure ou [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) com um [plano de suporte qualificado do Azure.](https://azure.microsoft.com/support/plans/)

### <a name="runtime-support"></a>Suporte de runtime

Os desenvolvedores podem [abrir um problema](/azure/azure-portal/supportability/how-to-create-azure-support-request) com o Azul Zulu JDK por meio do Suporte do Azure se tiverem um [plano de suporte qualificado](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Próximas etapas

Este tópico fornece a declaração java runtime de suporte para o Azure App Service no Windows.

- Para saber mais sobre como hospedar aplicativos web com o Azure App Service, consulte [a visão geral do Serviço de Aplicativos](overview.md).
- Para obter informações sobre o desenvolvimento do Java on Azure, consulte [Azure for Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
