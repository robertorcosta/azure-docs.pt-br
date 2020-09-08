---
title: Guia de início rápido – Criar e implantar aplicativos no Azure Spring Cloud
description: Descreve a implantação de aplicativos no Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050821"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Início Rápido: Criar e implantar aplicativos no Azure Spring Cloud

Este documento explica como criar e implantar aplicativos de microsserviço no Azure Spring Cloud usando:
* CLI do Azure
* Plug-in Maven
* IntelliJ

Antes da implantação com a CLI do Azure ou o Maven, conclua os exemplos que [provisionam uma instância do Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md) e [configuram o Config Server](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a CLI do Azure versão 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e instale a extensão do Azure Spring Cloud com o comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [conecte-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Procedimentos de implantação

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Criar os aplicativos de microsserviços localmente

1. Clone o repositório de aplicativos de exemplo para a sua conta do Azure Cloud.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Altere o diretório e crie o projeto.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

A compilação do projeto leva cerca de 5 minutos. Depois de concluída, você deverá ter arquivos JAR individuais para cada serviço em suas respectivas pastas.

### <a name="create-and-deploy-the-apps"></a>Criar e implantar os aplicativos

1. Defina o nome do grupo de recursos padrão e o nome do cluster usando os seguintes comandos:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Crie os microsserviços do Azure Spring Cloud usando os arquivos JAR criados na etapa anterior. Você criará três aplicativos: **gateway**, **auth-service** e **account-service**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Precisamos implantar aplicativos criados na etapa anterior para o Azure. Use os seguintes comandos para implantar todos os três aplicativos:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Atribuir um ponto de extremidade público ao gateway

Precisamos de uma maneira de acessar o aplicativo por meio de um navegador da Web. Nosso aplicativo de gateway precisa de um ponto de extremidade voltado para o público.

1. Atribua o ponto de extremidade usando o seguinte comando:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Consulte o aplicativo de **gateway** quanto ao IP público dele para você poder verificar se ele está em execução:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Clonar e criar o repositório do aplicativo de exemplo

1. Clone o repositório Git executando o seguinte comando:

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Altere o diretório e crie o projeto executando o seguinte comando:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Gerar configurações e implantar no Azure Spring Cloud

1. Gere configurações executando o comando a seguir na pasta raiz de PiggyMetrics que contém o POM pai. Se você já tiver entrado com a CLI do Azure, o comando selecionará automaticamente as credenciais. Caso contrário, ele fará com que você entre com as instruções de prompt. Confira a nossa [página wiki](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) para obter mais detalhes.

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    Você precisará selecionar:
    * **Módulos:** Selecione `gateway`, `auth-service` e `account-service`.
    * **Assinatura:** Essa é a sua assinatura usada para criar uma instância do Azure Spring Cloud.
    * **Instância de Serviço:** Esse é o nome da sua instância do Azure Spring Cloud.
    * **Ponto de extremidade público:** Na lista de projetos fornecidos, insira o número que corresponde ao `gateway`.  Isso faz com que ela tenha acesso público.

1. Agora o POM contém as dependências e configurações do plug-in. Implante os aplicativos usando o comando a seguir. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Importar o projeto de exemplo no IntelliJ

1. Baixe e descompacte o repositório de origem deste tutorial ou clone-o usando Git: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Abra a caixa de diálogo **Bem-vindo(a)** do IntelliJ e selecione **Importar Projeto** para abrir o assistente de importação.

1. Selecione a pasta `piggymetric`.

    ![Importar projeto](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Implantar o aplicativo de gateway no Azure Spring Cloud
Para implantar no Azure, você precisa entrar com a sua conta do Azure com o Azure Toolkit for IntelliJ e escolher a sua assinatura. Para obter detalhes de entrada, confira [Instalação e entrada](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Clique com o botão direito do mouse no seu projeto no explorador de projeto do IntelliJ e selecione **Azure** -> **Implantar no Azure Spring Cloud**.

    ![Implantar no Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. No campo **Nome**, acrescentar *:gateway* ao **Nome** existente fará referência à configuração.
1. Na caixa de texto **Artefato**, selecione *com.piggymetrics:gateway:1.0-SNAPSHOT*.
1. Na caixa de texto **Assinatura**, verifique a sua assinatura.
1. Na caixa de texto **Spring Cloud**, selecione a instância do Azure Spring Cloud criada em [Provisionar a instância do Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Defina **Ponto de Extremidade Público** como *Habilitar*.
1. Na caixa de texto **Aplicativo:** , selecione **Criar aplicativo...** .
1. Insira *gateway* e clique em **OK**.

    ![Implantar no Azure OK](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. Na seção **Antes da inicialização** da caixa de diálogo, clique duas vezes em *Executar Meta do Maven*.
1. Na caixa de texto **Diretório de trabalho**, navegue até a pasta *piggymetrics/gateway*.
1. Na caixa de texto **Linha de comando**, insira *package -DskipTests*. Clique em **OK**.
1. Inicie a implantação clicando no botão **Executar** na parte inferior da caixa de diálogo **Implantar aplicativo do Azure Spring Cloud**. O plug-in executará o comando `mvn package` no aplicativo `gateway` e implantará o JAR gerado pelo comando `package`.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Implantar o auth-service e o account-service no Azure Spring Cloud
Você pode repetir as etapas acima para implantar `auth-service` e os aplicativos `account-service` no Azure Spring Cloud:

1. Modifique o **Nome** e o **Artefato** para identificar o aplicativo `auth-service`.
1. Na caixa de texto **Aplicativo:** , selecione **Criar aplicativo...** para criar aplicativos `auth-service`.
1. Verifique se a opção **Ponto de Extremidade Público** está definida como *Desabilitado*.
1. Na seção **Antes da inicialização** da caixa de diálogo, alterne o **Diretório de trabalho** para a pasta *piggymetrics/auth-service*.
1. Inicie a implantação clicando no botão **Executar** na parte inferior da caixa de diálogo **Implantar aplicativo do Azure Spring Cloud**. 
1. Repita esses procedimentos para configurar e implantar o `account-service`.
---

Navegue até a URL fornecida na saída das etapas anteriores para acessar o aplicativo PiggyMetrics. Por exemplo: `https://<service instance name>-gateway.azuremicroservices.io`

![Acessar PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Também é possível navegar pelo portal do Azure para localizar a URL. 
1. Navegue até o serviço
2. Selecione **Aplicativos**
3. Selecione **Gateway**

    ![Navegar no aplicativo](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Localize a URL na página **gateway | Visão geral**

    ![Navegar pelo aplicativo segundo](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Limpar os recursos
Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos por meio do portal ou executando o seguinte comando no Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
Nas etapas anteriores, você também define o nome do grupo de recursos padrão. Para limpar o padrão, execute o seguinte comando no Cloud Shell:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Logs, Métricas e Rastreamento](spring-cloud-quickstart-logs-metrics-tracing.md)
