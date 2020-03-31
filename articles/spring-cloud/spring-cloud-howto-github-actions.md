---
title: Azure Spring Cloud CI/CD com ações do GitHub
description: Como construir fluxo de trabalho de CI/CD para a Nuvem de Primavera do Azure com as ações do GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/15/2019
ms.openlocfilehash: 559c894a2212466761de820de7486ae203337802
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538457"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud CI/CD com ações do GitHub

O GitHub Actions suporta um fluxo de trabalho automatizado do ciclo de vida do desenvolvimento de software. Com o GitHub Actions for Azure Spring Cloud, você pode criar fluxos de trabalho em seu repositório para construir, testar, empacotar, liberar e implantar no Azure. 

## <a name="prerequisites"></a>Pré-requisitos
Este exemplo requer [o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="set-up-github-repository-and-authenticate"></a>Configure o repositório e autenticação do GitHub
Você precisa de uma credencial de princípio de serviço do Azure para autorizar a ação de login do Azure. Para obter uma credencial do Azure, execute os seguintes comandos em sua máquina local:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Para acessar um grupo de recursos específico, você pode reduzir o escopo:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
O comando deve produzir um objeto JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Este exemplo usa a amostra [Piggy Metrics](https://github.com/Azure-Samples/piggymetrics) no GitHub.  Garfo a amostra, abra a página do repositório do GitHub e clique na guia **Configurações.** Menu **Segredos** abertos e clique **em Adicionar um novo segredo**:

 ![Adicionar novo segredo](./media/github-actions/actions1.png)

Defina o `AZURE_CREDENTIALS` nome secreto e seu valor na seqüência JSON que você encontrou o título *Configure seu repositório e autenticação gitHub*.

 ![Definir dados secretos](./media/github-actions/actions2.png)

Você também pode obter a credencial de login do Azure do Key Vault nas ações do GitHub, conforme explicado no [Authenticate Azure Spring com key vault no GitHub Actions](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Instância de serviço de provisão
Para provisionar sua instância de serviço Azure Spring Cloud, execute os seguintes comandos usando o Azure CLI.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Construa o fluxo de trabalho
O fluxo de trabalho é definido usando as seguintes opções.

### <a name="prepare-for-deployment-with-azure-cli"></a>Prepare-se para implantação com a Cli do Azure
No `az spring-cloud app create` momento, o comando não é idempotente.  Recomendamos esse fluxo de trabalho nos aplicativos e instâncias existentes do Azure Spring Cloud.

Use os seguintes comandos azure CLI para preparação:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Implantar diretamente com o Azure CLI
Criar `.github/workflow/main.yml` o arquivo no repositório:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Implantar com ação CLI do Azure
O comando `run` az usará a versão mais recente do Azure CLI. Se houver alterações de quebra, você também pode usar uma versão `action`específica do Azure CLI com azure/CLI . 

> [!Note] 
> Este comando será executado em um `env` novo contêiner, portanto não funcionará, e o acesso a arquivos de ação cruzada pode ter restrições extras.

Crie o arquivo .github/workflow/main.yml no repositório:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Implantar com plugin Maven
Outra opção é usar o [Plugin Maven](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-maven) para implantar o Jar e atualizar as configurações do Aplicativo. O `mvn azure-spring-cloud:deploy` comando é idempotente e criará automaticamente Aplicativos, se necessário. Você não precisa criar aplicativos correspondentes com antecedência.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@master
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -D skipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

## <a name="run-the-workflow"></a>Executar o fluxo de trabalho
As **ações do** GitHub devem ser `.github/workflow/main.yml` ativadas automaticamente após o push para o GitHub. A ação será desencadeada quando você empurrar um novo compromisso. Se você criar esse arquivo no navegador, sua ação já deveria ter sido executada.

Para verificar se a ação foi ativada, clique na guia **Ações** na página do repositório do GitHub:

 ![Verificar ação ativada](./media/github-actions/actions3.png)

Se a sua ação for executada por engano, por exemplo, se você não tiver definido a credencial do Azure, você poderá reexecutar as verificações após corrigir o erro. Na página do repositório do GitHub, clique em **Ações,** selecione a tarefa de fluxo de trabalho específica e clique no botão **Reexecutar verificações** para executar novamente:

 ![Verificações de reprise](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Próximas etapas
* [Cofre-chave para ações do Spring Cloud GitHub](./spring-cloud-github-actions-key-vault.md)
* [Diretores de serviços de diretório ativo do Azure](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)
* [Ações do GitHub para o Azure](https://github.com/Azure/actions/)
