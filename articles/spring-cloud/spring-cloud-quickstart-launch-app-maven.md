---
title: 'Início Rápido: Iniciar um aplicativo usando o Maven – Azure Spring Cloud'
description: Iniciar um aplicativo de exemplo usando o Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554564"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Início Rápido: Iniciar um aplicativo do Azure Spring Cloud usando o plug-in do Maven

Com o plug-in do Maven para Azure Spring Cloud, crie e atualize com facilidade seus aplicativos do serviço Azure Spring Cloud. Ao predefinir uma configuração, você pode implantar aplicativos em seu serviço existente do Azure Spring Cloud. Neste artigo, você usará um aplicativo de exemplo chamado PiggyMetrics para demonstrar esse recurso.

>[!Note]
> Antes de começar este início rápido, verifique se a sua assinatura do Azure tem acesso ao Azure Spring Cloud. Como um serviço de versão prévia, convidamos você a entrar em contato conosco para podermos adicionar sua assinatura à nossa lista de permissões. Caso deseje explorar as funcionalidades do Azure Spring Cloud, preencha e envie o [Formulário de Interesse – Azure Spring Cloud (versão prévia privada)](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).

>[!TIP]
> O Azure Cloud Shell é um shell interativo gratuito que pode ser usado para executar os comandos deste artigo. Ele tem ferramentas comuns do Azure pré-instaladas, incluindo as últimas versões do Git, do JDK (Java Development Kit), do Maven e da CLI do Azure. Se estiver conectado à sua assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com). Para obter mais informações, consulte [Visão geral do Azure Cloud Shell](../cloud-shell/overview.md).

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/).
2. [Instale o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Instale o Maven 3.0 ou posterior](https://maven.apache.org/download.cgi).
4. [Instale a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Inscreva-se em uma assinatura gratuita do Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instale a extensão Azure Spring Cloud para a CLI do Azure usando o seguinte comando:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionar uma instância de serviço no portal do Azure

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com) e entre em sua conta.

1. Pesquise e, em seguida, selecione **Azure Spring Cloud**. 
1. Na página de visão geral, selecione **Criar** e, em seguida, faça o seguinte:  

    a. Na caixa **Nome do Serviço**, especifique o nome da instância de serviço. O nome precisa ter entre 4 e 32 caracteres e só pode conter letras minúsculas, números e hifens. O primeiro caractere do nome do serviço precisa ser uma letra e o último caractere precisa ser uma letra ou um número.  

    b. Na lista suspensa **Assinatura**, selecione a assinatura de cobrança para esse recurso. Verifique se essa assinatura foi adicionada à nossa lista de permissões do Azure Spring Cloud.  

    c. Na caixa **Grupo de recursos**, crie um grupo de recursos. A criação de grupos de recursos para novos recursos é uma melhor prática.  

    d. Na lista suspensa **Localização**, selecione a localização da instância de serviço. No momento, os locais com suporte incluem o Leste dos EUA, Oeste dos EUA 2, Oeste da Europa e Sudeste Asiático.
    
São necessários cerca de 5 minutos para o serviço ser implantado. Depois que o serviço for implantado, a página **Visão Geral** da instância de serviço será exibida.

## <a name="set-up-your-configuration-server"></a>Definir seu servidor de configuração

1. Na página **Visão Geral** do serviço, selecione **Servidor de Configuração**.
1. Na seção **Repositório padrão**, defina **URI** como **https://github.com/Azure-Samples/piggymetrics** , defina **Rótulo** como **config** e, em seguida, selecione **Aplicar** para salvar as alterações.

## <a name="clone-and-build-the-sample-application-repository"></a>Clonar e criar o repositório do aplicativo de exemplo

1. Clone o repositório Git executando o seguinte comando:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Altere o diretório e crie o projeto executando o seguinte comando:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Gerar e implantar a configuração do Azure Spring Cloud

1. Para permitir que o Maven funcione com o Azure Spring Cloud, adicione o código a seguir ao arquivo *pom.xml* ou *settings.xml*.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Gere uma configuração executando o seguinte comando:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Selecione os módulos `gateway`, `auth-service` e `account-service`.

    b. Selecione sua assinatura e o cluster de serviço do Azure Spring Cloud.

    c. Na lista de projetos fornecidos, insira o número que corresponde ao `gateway` para fornecer acesso público a ele.
    
    d. Confirme a configuração.

1. Implante os aplicativos usando o seguinte comando:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Acesse o PiggyMetrics usando a URL fornecida na saída do comando anterior.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um aplicativo Spring Cloud em um repositório do Maven. Para saber mais sobre o Azure Spring Cloud, prossiga para o tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Prepare seu aplicativo Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
