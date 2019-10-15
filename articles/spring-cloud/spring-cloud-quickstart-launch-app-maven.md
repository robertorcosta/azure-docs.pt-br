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
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166508"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Início Rápido: Iniciar um aplicativo Azure Spring Cloud usando o plug-in do Maven

O plug-in do Maven do Azure Spring Cloud permite que você crie e atualize facilmente seus aplicativos de serviço do Azure Spring Cloud. Ao predefinir uma configuração, você pode implantar aplicativos em seu serviço do Azure Spring Cloud existente. Neste artigo, usamos um aplicativo de exemplo chamado PiggyMetrics para demonstrar esse recurso.

>[!Note]
> Antes de começar este início rápido, verifique se sua assinatura do Azure tem acesso ao Azure Spring Cloud.  Como um serviço de versão prévia, pedimos que os clientes entrem em contato conosco para podermos adicionar sua assinatura à nossa lista de permissões.  Se desejar explorar as funcionalidades do Azure Spring Cloud, [preencha este formulário](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
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

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Provisionar uma instância de serviço no portal do Azure

1. Em um navegador da Web, abra o [portal do Azure](https://portal.azure.com) e entre em sua conta.

1. Pesquise o **Azure Spring Cloud** e selecione-o para acessar a página de visão geral. Selecione o botão **Criar** para começar.

1. Preencha o formulário, considerando as seguintes diretrizes:
    - Nome do Serviço: especifique o nome da instância de serviço.  O nome deve ter entre 4 e 32 caracteres e pode conter apenas letras minúsculas, números e hifens.  O primeiro caractere do nome do serviço deve ser uma letra e o último caractere deve ser uma letra ou um número.
    - Assinatura: selecione a assinatura da qual você deseja que este recurso seja cobrado.  Verifique se essa assinatura foi adicionada à nossa lista de permissões do Azure Spring Cloud.
    - Grupo de recursos: a criação de grupos de recursos para novos recursos é uma melhor prática.
    - Localização: selecione a localização de sua instância de serviço. No momento, os locais com suporte incluem o Leste dos EUA, Oeste dos EUA 2, Oeste da Europa e Sudeste Asiático.
    
Leva cerca de 5 minutos para o serviço ser implantado.  Depois de implantada, a página **Visão Geral** da instância de serviço será exibida.

## <a name="set-up-your-configuration-server"></a>Definir seu servidor de configuração

1. Acesse a página **Visão Geral** do serviço e selecione o **Servidor de Configuração**.
1. Na seção **Repositório padrão**, defina **URI** como "https://github.com/Azure-Samples/piggymetrics", defina **RÓTULO** como "configuração" e selecione **Aplicar** para salvar suas alterações.

## <a name="clone-and-build-the-sample-application-repository"></a>Clonar e criar o repositório do aplicativo de exemplo

1. Clone o repositório git executando o seguinte comando.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Altere o diretório e crie o projeto executando o seguinte comando.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Gerar e implantar a configuração do Azure Spring Cloud

1. Adicione o seguinte ao `pom.xml` ou ao `settings.xml` para permitir que o Maven funcione com o Azure Spring Cloud.

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

1. Gere uma configuração executando o seguinte comando.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Selecione os módulos `gateway`, `auth-service` e `account-service`.

    1. Selecione sua assinatura e o cluster de serviço do Azure Spring Cloud.

    1. Na lista de projetos fornecida, insira o número correspondente ao `gateway` para conceder-lhe acesso público.
    
    1. Confirme a configuração.

1. Implante os aplicativos usando o seguinte comando:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. Você pode acessar o PiggyMetrics usando a URL fornecida na saída do comando anterior.

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou um aplicativo Spring Cloud em um repositório do Maven.  Para saber mais sobre o Azure Spring Cloud, passe para o tutorial sobre como preparar seu aplicativo para implantação.

> [!div class="nextstepaction"]
> [Prepare seu aplicativo Azure Spring Cloud para implantação](spring-cloud-tutorial-prepare-app-deployment.md)
