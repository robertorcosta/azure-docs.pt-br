---
title: Iniciar um aplicativo Spring Java usando a CLI do Azure
description: Neste início rápido, você implanta um aplicativo de exemplo no Azure Spring Cloud na CLI do Azure.
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 6d399f04015140477af17f718c3e2205b8c3855f
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72170548"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Início Rápido: Iniciar um aplicativo Spring Java usando a CLI do Azure

O Azure Spring Cloud permite que você execute facilmente um aplicativos de microsserviços baseado no Spring Boot no Azure.

Este início rápido mostra como implantar um aplicativo Spring Cloud Java existente no Azure. Quando você terminar, poderá continuar gerenciando o aplicativo por meio da CLI do Azure ou alterar para usar o portal do Azure.

Seguindo este início rápido, você aprenderá a:

> [!div class="checklist"]
> * Provisionar uma instância de serviço
> * Definir um servidor de configuração para uma instância
> * Criar um aplicativo de microsserviço localmente
> * Implantar cada microsserviço
> * Atribuir um ponto de extremidade público para seu aplicativo

## <a name="prerequisites"></a>Pré-requisitos

>[!Note]
> Antes de começar este início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud.  Tratando-se de um serviço em versão prévia, pedimos que você entre em contato conosco para que possamos adicionar sua assinatura à nossa lista de permissões.  Se desejar explorar as funcionalidades do Azure Spring Cloud, [preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

>[!TIP]
> O Azure Cloud Shell é um shell interativo grátis que pode ser usado para executar as etapas neste artigo.  Ele tem ferramentas comuns do Azure pré-instaladas, incluindo as versões mais recentes do Git, do JDK, do Maven e da CLI do Azure. Se você fez logon na assinatura do Azure, inicie o [Azure Cloud Shell](https://shell.azure.com) em shell.azure.com.  Você pode saber mais sobre o Azure Cloud Shell [lendo nossa documentação](../cloud-shell/overview.md)

Para concluir este guia de início rápido:

1. [Instalar o Git](https://git-scm.com/)
2. [Instalar o JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Instalar o Maven 3.0 ou posterior](https://maven.apache.org/download.cgi)
4. [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Inscrever-se em uma assinatura do Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalar a extensão da CLI do Azure

Instalar a extensão do Azure Spring Cloud para a CLI do Azure usando o seguinte comando

```azurecli
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Provisionar uma instância de serviço na CLI do Azure

1. Faça logon na CLI do Azure e escolha sua assinatura ativa. Escolha a assinatura ativa que está na lista de permissões para o Azure Spring Cloud

    ```azurecli
        az login
        az account list -o table
        az account set --subscription
    ```

2. Prepare um nome para seu serviço do Azure Spring Cloud.  O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.

3. Crie um grupo de recursos para conter seu serviço do Azure Spring Cloud.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```
    Saiba mais sobre [grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

4. Abra uma janela da CLI do Azure e execute os seguintes comandos para provisionar uma instância do Azure Spring Cloud.

    ```azurecli
        az spring-cloud create -n <service name> -g <resource group name>
    ```

    A instância de serviço levará cerca de cinco minutos para ser implantada.

5. Defina o nome do grupo de recursos padrão e o nome do cluster usando os seguintes comandos:

    ```azurecli
        az configure --defaults group=<service group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

## <a name="setup-your-configuration-server"></a>Configure seu servidor de configuração

Atualize o config-server com a localização do repositório git do nosso projeto:

```git
az spring-cloud config-server git set -n <your-service-name> --uri https://github.com/Azure-Samples/piggymetrics --label config
```

## <a name="build-the-microservices-applications-locally"></a>Criar os aplicativos de microsserviços localmente

1. Crie uma pasta e clone o repositório de aplicativos de exemplo para sua conta de nuvem do Azure.  

    ```azurecli
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Altere o diretório e crie o projeto.

    ```azurecli
        cd PiggyMetrics
        mvn clean package -D skipTests
    ```

A compilação do projeto leva cerca de 5 minutos.  Depois de concluída, você deverá ter arquivos JAR individuais para cada serviço em suas respectivas pastas.

## <a name="create-the-microservices"></a>Criar os microsserviços

Crie os microsserviços do Spring Cloud usando os arquivos JAR criados na etapa anterior. Você criará três microsserviços: **gateway**, **auth-service** e **account-service**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

>[!NOTE]
> Os nomes de aplicativo devem corresponder aos nomes dos JARS exatamente para o servidor de configuração fornecido para funcionar corretamente.

## <a name="deploy-applications-and-set-environment-variables"></a>Implantar aplicativos e definir variáveis de ambiente

Precisamos implantar realmente nossos aplicativos no Azure. Use os seguintes comandos para implantar todos os três aplicativos:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

## <a name="assign-public-endpoint-to-gateway"></a>Atribuir um ponto de extremidade público ao gateway

Precisamos de uma maneira de acessar o aplicativo por meio de um navegador da Web. Nosso aplicativo de gateway precisa de um ponto de extremidade voltado para o público, que pode ser atribuído usando o seguinte comando:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

Por fim, consulte o aplicativo de **gateway** quanto ao seu IP público para você poder verificar se o ele está em execução:

```azurecli
az spring-cloud app show --name gateway | grep url
```

Navegue até a URL fornecida pelo comando anterior para ver o aplicativo PiggyMetrics em execução.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um aplicativo Spring Cloud na CLI do Azure.  Para saber mais sobre o Azure Spring Cloud, passe para o tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Prepare seu aplicativo Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
