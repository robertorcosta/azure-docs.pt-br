---
title: Guia de início rápido – Implantar o seu primeiro aplicativo Azure Spring Cloud
description: Neste guia de início rápido, implantaremos um aplicativo do Spring Cloud no Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 245516e0a54865d3a6097c4bb566b850cb738ad6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260537"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Início Rápido: Implantar seu primeiro aplicativo do Azure Spring Cloud

Este guia de início rápido explica como implantar aplicativos de microsserviço simples do Azure Spring Cloud para execução no Azure. 

O código do aplicativo usado neste tutorial é um aplicativo simples criado com o Spring Initializr. Quando você concluir este exemplo, o aplicativo estará acessível online e poderá ser gerenciado por meio do portal do Azure.

Este guia de início rápido explica como:

> [!div class="checklist"]
> * Gerar um projeto básico do Spring Cloud
> * Provisionar uma instância de serviço
> * Criar e implantar o aplicativo com um ponto de extremidade público
> * Transmitir logs em tempo real

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este guia de início rápido:

* [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)
* (Opcional) [Instale a CLI do Azure versão 2.0.67 ou superior](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) e a extensão do Azure Spring Cloud com o comando: `az extension add --name spring-cloud`
* (Opcional) [Instale o Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) e [conecte-se](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Gerar um projeto do Spring Cloud

Comece com o [Spring Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) para gerar um projeto de exemplo com as dependências recomendadas para o Azure Spring Cloud. A imagem a seguir mostra o Initializr configurado para este projeto de exemplo.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Página do Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Clique em **Gerar** quando todas as dependências forem definidas. Baixe e descompacte o pacote e crie um controlador da Web para um aplicativo Web simples adicionando `src/main/java/com/example/hellospring/HelloController.java` da seguinte maneira:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Provisionar uma instância do Azure Spring Cloud

O procedimento a seguir cria uma instância do Azure Spring Cloud usando o portal do Azure.

1. Em uma nova guia, abra o [portal do Azure](https://ms.portal.azure.com/). 

2. Na caixa de pesquisa superior, pesquise *Azure Spring Cloud*.

3. Selecione *Azure Spring Cloud* nos resultados.

    ![Ícone de início do ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na página do Azure Spring Cloud, clique em **+ Adicionar**.

    ![Ícone de adição do ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Preencha o formulário na página **Criar** do Azure Spring Cloud.  Considere as seguintes diretrizes:
    - **Assinatura**: selecione a assinatura da qual você deseja que este recurso seja cobrado.
    - **Grupo de recursos**: a criação de grupos de recursos para novos recursos é uma melhor prática. Isso será usado em uma etapa posterior como **\<resource group name\>** .
    - **Nome/Detalhes do Serviço**: Especifique o **\<service instance name\>** .  O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - **Localização**: selecione a região da instância de serviço.

    ![Início do portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Clique em **Examinar e criar**.

## <a name="build-and-deploy-the-app"></a>Compilar e implantar o aplicativo
    
#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
O procedimento a seguir cria e implanta o aplicativo usando a CLI do Azure. Execute o comando a seguir na raiz do projeto.

1. Compile o projeto usando o Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. Caso ainda não tenha feito isso, instale a extensão do Azure Spring Cloud para a CLI do Azure:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Crie o aplicativo com o ponto de extremidade público atribuído:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Implante o arquivo JAR para o aplicativo:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. A implantação do aplicativo leva alguns minutos para ser concluída. Para confirmar se ele foi implantado, acesse a folha **Aplicativos** no portal do Azure. Você verá o status do aplicativo.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

O procedimento a seguir usa o plug-in do IntelliJ para Azure Spring Cloud a fim de implantar o aplicativo de exemplo no IntelliJ IDEA.  

### <a name="import-project"></a>Projeto de importação

1. Abra a caixa de diálogo **Bem-vindo(a)** do IntelliJ e selecione **Importar Projeto** para abrir o assistente de importação.
1. Selecione a pasta `hellospring`.

    ![Importar projeto](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Implantar o aplicativo
Para a implantação no Azure, você precisará entrar com a sua conta do Azure e escolher sua assinatura.  Para obter detalhes de entrada, confira [Instalação e entrada](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Clique com o botão direito do mouse no seu projeto no explorador de projeto do IntelliJ e selecione **Azure** -> **Implantar no Azure Spring Cloud**.

    [ ![Implantação no Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png) ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Aceite o nome do aplicativo no campo **Nome**. **Nome** refere-se à configuração, não ao nome do aplicativo. Normalmente, os usuários não precisam alterá-lo.
1. Na caixa de texto **Artefato**, selecione *hellospring-0.0.1-SNAPSHOT.jar*.
1. Na caixa de texto **Assinatura**, verifique a sua assinatura.
1. Na caixa de texto **Spring Cloud**, selecione a instância do Azure Spring Cloud criada em [Provisionar a instância do Azure Spring Cloud](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Defina **Ponto de Extremidade Público** como *Habilitar*.
1. Na caixa de texto **Aplicativo:** , selecione **Criar aplicativo...** .
1. Insira *hellospring* e clique em **OK**.

    [ ![Implantação com êxito no Azure](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png) ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Inicie a implantação clicando no botão **Executar** na parte inferior da caixa de diálogo **Implantar aplicativo do Azure Spring Cloud**. O plug-in executará o comando `mvn package` no aplicativo `hellospring` e implantará o JAR gerado pelo comando `package`.
---

Depois que a implantação for concluída, acesse o aplicativo em `https://<service instance name>-hellospring.azuremicroservices.io/`.

  [ ![Acessar o aplicativo no navegador](media/spring-cloud-quickstart-java/access-app-browser.png) ](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Como transmitir logs em tempo real

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

Use o comando a seguir para obter os logs em tempo real do aplicativo.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Os logs serão exibidos nos resultados:

[ ![Logs de streaming](media/spring-cloud-quickstart-java/streaming-logs.png) ](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Use `az spring-cloud app logs -h` para explorar mais parâmetros e funcionalidades de fluxo de log.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Selecione **Azure Explorer** e, em seguida, **Spring Cloud**.
1. Clique com o botão direito do mouse no aplicativo em execução.
1. Selecione **Logs de Streaming** na lista suspensa.
1. Selecione a instância.

    [ ![Selecionar logs de streaming](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. O log de streaming ficará visível na janela de saída.

    [ ![Saída do log de streaming](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Para obter recursos avançados de análise de logs, acesse a guia **Logs** do menu no [portal do Azure](https://portal.azure.com/). Os logs aqui têm uma latência de alguns minutos.

[ ![Análise de logs](media/spring-cloud-quickstart-java/logs-analytics.png) ](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Limpar os recursos
Nas etapas anteriores, você criou os recursos do Azure em um grupo de recursos. Se você acha que não precisará desses recursos no futuro, exclua o grupo de recursos por meio do portal ou executando o seguinte comando no Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

Neste guia de início rápido, você aprendeu a:

> [!div class="checklist"]
> * Gerar um projeto básico do Azure Spring Cloud
> * Provisionar uma instância de serviço
> * Criar e implantar o aplicativo com um ponto de extremidade público
> * Como transmitir logs em tempo real
## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Criar e executar microsserviços](spring-cloud-quickstart-sample-app-introduction.md)

Mais exemplos estão disponíveis no GitHub: [Exemplos do Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
