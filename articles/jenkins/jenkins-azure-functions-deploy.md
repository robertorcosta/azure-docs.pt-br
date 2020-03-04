---
title: Implantar no Azure Functions usando o plug-in Jenkins do Azure Functions
description: Aprenda a implantar no Azure Functions usando o plug-in Jenkins do Azure Functions
keywords: jenkins, azure, devops, java, azure functions
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: edf8c763a46a6f519175842a6525b2c1b314fb66
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/19/2020
ms.locfileid: "77470659"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Implantar no Azure Functions usando o plug-in Jenkins do Azure Functions

O [Azure Functions](/azure/azure-functions/) é um serviço de computação sem servidor. Usando o Azure Functions, você pode executar código sob demanda sem provisionamento nem gerenciamento de infraestrutura. Este tutorial mostra como implantar uma função Java no Azure Functions usando o plug-in do Azure Functions.

## <a name="prerequisites"></a>Prerequisites

- **Assinatura do Azure**: Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- **Servidor Jenkins**: se você não tiver um servidor Jenkins instalado, leia o artigo [Criar um servidor Jenkins no Azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > O código-fonte usado para este tutorial está localizado no [Repositório GitHub do Visual Studio China](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Criar uma função Java

Para criar uma função Java com a pilha de runtime do Java, use o [portal do Azure](https://portal.azure.com) ou a [CLI do Azure](/cli/azure/?view=azure-cli-latest).

As etapas a seguir mostram como criar uma função Java usando a CLI do Azure:

1. Crie um grupo de recursos substituindo o espaço reservado **&lt;resource_group>** pelo nome do seu grupo de recursos.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Crie uma conta de armazenamento do Azure substituindo os espaços reservados pelos valores apropriados.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Crie o aplicativo de funções de teste substituindo os espaços reservados pelos valores apropriados.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Preparar o servidor Jenkins

As etapas a seguir explicam como preparar o servidor Jenkins:

1. Implante um [servidor Jenkins](https://aka.ms/jenkins-on-azure) no Azure. Se você ainda não tiver uma instância do servidor Jenkins instalado, o artigo [Criar um servidor Jenkins no Azure](./install-jenkins-solution-template.md) o orientará pelo processo.

1. Entre na instância do Jenkins com SSH.

1. Na instância do Jenkins, instale o maven usando o seguinte comando:

    ```terminal
    sudo apt install -y maven
    ```

1. Na instância do Jenkins, instale [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) emitindo os seguintes comandos no prompt do terminal:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. No painel do Jenkins, instale os seguintes plug-ins:

    - Plug-in do Azure Functions
    - Plug-in do EnvInject

1. O Jenkins precisa de uma entidade de serviço do Azure para autenticar e acessar recursos do Azure. Veja [Implantar no Serviço de Aplicativo do Azure](./tutorial-jenkins-deploy-web-app-azure-app-service.md) para obter instruções passo a passo.

1. Usando a entidade de serviço do Azure, adicione um tipo de credencial "Entidade de Serviço do Microsoft Azure" no Jenkins. Veja o tutorial [Implantar no Serviço de Aplicativo do Azure](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins).

## <a name="fork-the-sample-github-repo"></a>Criar fork do repositório GitHub de exemplo

1. [Entre no repositório GitHub do aplicativo de exemplo par ou ímpar](https://github.com/VSChina/odd-or-even-function.git).

1. No canto superior direito do GitHub, escolha **Fork**.

1. Siga os prompts para escolher sua conta do GitHub e concluir o fork.

## <a name="create-a-jenkins-pipeline"></a>Criar um pipeline do Jenkins

Nesta seção, você criará o [Pipeline do Jenkins](https://jenkins.io/doc/book/pipeline/).

1. No painel do Jenkins, crie um Pipeline.

1. Habilite **Preparar um ambiente para a execução**.

1. Adicione as seguintes variáveis de ambiente ao **Conteúdo de Propriedades** substituindo os espaços reservados pelos valores apropriados para seu ambiente:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Na seção **Pipeline->definição**, selecione **Script do pipeline do SCM**.

1. Insira a URL e o caminho do script do fork do GitHub ("doc/resources/jenkins/JenkinsFile") para usar no [exemplo JenkinsFile](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Criar e implantar

Agora é hora de executar o trabalho do Jenkins.

1. Primeiro, obtenha a chave de autorização por meio das instruções do artigo [Gatilhos e associações HTTP do Azure Functions](/azure/azure-functions/functions-bindings-http-webhook-trigger#authorization-keys).

1. No navegador, digite a URL do aplicativo. Substitua os espaços reservados pelos valores apropriados e especifique um valor numérico para **&lt;input_number>** como entrada para a função de Java.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. Você verá resultados semelhantes à seguinte saída de exemplo (em que um número ímpar – 365 – foi usado como teste):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Limpar os recursos

Se você não quiser continuar usando este aplicativo, exclua os recursos associados que você criou com a seguinte etapa:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Azure Functions, veja o seguinte recurso:
> [!div class="nextstepaction"]
> [Documentação do Azure Functions](/azure/azure-functions/)
