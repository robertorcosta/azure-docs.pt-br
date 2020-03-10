---
title: Implantar no Serviço de Aplicativo do Azure usando o plugin Jenkins
description: Saiba como usar o plugin Jenkins do Serviço de Aplicativo do Azure para implantar um aplicativo Web do Java no Azure no Jenkins
keywords: jenkins, azure, devops, serviço de aplicativo
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: fcaf45003e865cc5aac3f6bd4580479a27d38b50
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78251453"
---
# <a name="deploy-to-azure-app-service-by-using-the-jenkins-plugin"></a>Implantar no Serviço de Aplicativo do Azure usando o plugin Jenkins 

Para implantar um aplicativo Web do Java no Azure, você pode usar a CLI do Azure no [Pipeline do Jenkins](/azure/jenkins/execute-cli-jenkins-pipeline) ou pode usar o [Plugin Jenkins do Serviço de Aplicativo do Azure](https://plugins.jenkins.io/azure-app-service). A versão 1.0 do plugin Jenkins dá suporte à implantação contínua usando o recurso de aplicativos Web do Serviço de Aplicativo do Azure por meio de:
* Upload de arquivo.
* Docker para Aplicativos Web no Linux.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Configurar o Jenkins para implantar aplicativos Web por meio de upload de arquivo.
> * Configure o Jenkins para implantar Aplicativos Web para Contêineres.

## <a name="create-and-configure-a-jenkins-instance"></a>Criar e configurar uma instância do Jenkins

Se você ainda não tiver um mestre do Jenkins, comece com o [modelo de solução](install-jenkins-solution-template.md), que inclui a versão 8 do Kit de Desenvolvimento de Java (JDK) e os plugins Jenkins necessários a seguir:

* [Plugin de cliente Git do Jenkins](https://plugins.jenkins.io/git-client) versão 2.4.6 
* [Plugin Docker Commons](https://plugins.jenkins.io/docker-commons) versão 1.4.0
* [Credenciais do Azure](https://plugins.jenkins.io/azure-credentials) versão 1.2
* [Serviço de Aplicativo do Azure](https://plugins.jenkins.io/azure-app-service) versão 0.1

Você pode usar o plugin Jenkins para implantar um aplicativo Web em qualquer linguagem compatível com aplicativos Web, como C#, PHP, Python, Java e Node.js. Neste tutorial, usamos um [aplicativo Web de Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Para bifurcar o repositório para sua conta do GitHub, selecione o botão **Bifurcação** no canto superior direito da interface GitHub.  

> [!NOTE]
> O Java JDK e o Maven são necessários para compilar o projeto Java. Instale esses componentes no mestre do Jenkins ou no agente de VM, caso você use o agente para a integração contínua. Se você estiver implantando um aplicativo Java SE, ZIP também é necessário no servidor de build.
>

Para instalar o componente, entre na instância do Jenkins com SSH e execute os seguintes comandos:

```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

Para implantar Aplicativos Web para Contêineres, instale o Docker no Jenkins mestre ou no agente de VM que é usado para o build. Para obter instruções de instalação, consulte [Instalar Docker no Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntu/).

## <a name="service-principal"></a> Adicionar uma entidade de serviço do Azure nas credenciais do Jenkins

É necessária uma entidade de serviço do Azure para implantar no Azure. 


1. Para criar uma entidade de serviço do Azure, use a [CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) ou o [portal do Azure](/azure/azure-resource-manager/resource-group-create-service-principal-portal).
2. No painel do Jenkins, selecione **Credenciais** > **Sistema**. Em seguida, selecione **Credenciais globais (irrestrito)** .
3. Para adicionar uma entidade de serviço do Microsoft Azure, selecione **Adicionar credenciais**. Forneça valores para os campos **ID de assinatura**, **ID do cliente**, **Segredo do cliente** e **Ponto de extremidade de token do OAuth 2.0**. Defina o campo **ID** como **mySp**. Usaremos essa ID em etapas subsequentes nesse artigo.


## <a name="configure-jenkins-to-deploy-web-apps-by-uploading-files"></a>Configurar o Jenkins para implantar aplicativos Web ao carregar arquivos

Para implantar seu projeto em Aplicativos Web, você pode carregar seus artefatos de build pelo upload de arquivo. O Serviço de Aplicativo do Azure oferece suporte a várias opções de implantação. O plug-in Jenkins do Serviço de Aplicativo do Azure torna simples para você e deriva a opção de implantação com base no tipo de arquivo. 

* Para os aplicativos do Java EE, a [Implantação WAR](/azure/app-service/deploy-zip#deploy-war-file) é usada.
* Para os aplicativos do Java SE, a [Implantação ZIP](/azure/app-service/deploy-zip#deploy-zip-file) é usada.
* Para outros idiomas, [implantação do Git](/azure/app-service/deploy-local-git) é usado.

Antes de configurar o trabalho em Jenkins, você precisa de um plano do Serviço de Aplicativo do Azure e de um aplicativo Web para executar o aplicativo Java.


1. Crie um Plano do Serviço de Aplicativo do Azure com o tipo de preço **GRATUITO** usando o [comando da CLI do Azure](/cli/azure/appservice/plan#az-appservice-plan-create) `az appservice plan create`. O plano do Serviço de Aplicativo define os recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um Plano do Serviço de Aplicativo compartilham esses recursos. Recursos compartilhados o ajudam a economizar nos custos ao hospedar vários aplicativos.
2. Crie um aplicativo Web. Você pode usar o [portal do Azure](/azure/app-service/configure-common) ou o seguinte comando da CLI do Azure `az`:
    ```azurecli-interactive 
    az webapp create --name <myAppName> --resource-group <myResourceGroup> --plan <myAppServicePlan>
    ```
    
3. Defina a configuração de runtime do Java de que seu aplicativo precisa. O comando da CLI do Azure a seguir configura o aplicativo Web para ser executado em um JDK 8 recente e a versão 8.0 do [Apache Tomcat](https://tomcat.apache.org/):
    ```azurecli-interactive
    az webapp config set \
    --name <myAppName> \
    --resource-group <myResourceGroup> \
    --java-version 1.8 \
    --java-container Tomcat \
    --java-container-version 8.0
    ```

### <a name="set-up-the-jenkins-job"></a>Configurar o trabalho do Jenkins

1. Crie um novo projeto de **estilo livre** no painel do Jenkins.
2. Configure o campo **Gerenciamento de Código-fonte** para usar o seu fork local do [Aplicativo Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Forneça o valor de **URL do repositório**. Por exemplo: http:\//github.com/&lt;your_ID>/javawebappsample.
3. Adicione uma etapa para criar o projeto usando Maven adicionando o comando **Executar shell**. Neste exemplo, precisamos de um comando adicional para renomear o arquivo \*.war na pasta de destino para **ROOT.war**:   
    ```bash
    mvn clean package
    mv target/*.war target/ROOT.war
    ```

4. Adicione uma ação pós-build selecionando **Publicar um Aplicativo Web do Azure**.
5. Forneça **mySp** como a entidade de serviço do Azure. Essa entidade foi armazenada como as [Credenciais do Azure](#service-principal) em uma etapa anterior.
6. Na seção **Configuração do Aplicativo**, escolha o aplicativo Web e o grupo de recursos em sua assinatura. O plugin Jenkins detecta automaticamente se o aplicativo Web é do Windows ou do Linux. Para um aplicativo Web do Windows, a opção **Publicar arquivos** é apresentada.
7. Preencha os arquivos que deseja implantar. Por exemplo, especifique o pacote WAR se estiver usando Java. Use os parâmetros opcionais **Diretório de Origem** e **Diretório de Destino** para especificar as pastas de origem e de destino a serem usadas para o upload do arquivo. O aplicativo Web Java no Azure é executado em um servidor Tomcat. Portanto, para o Java, carregue o pacote WAR na pasta webapps. Neste exemplo, defina o valor de **Diretório de Origem** como **destino** e valor de **Diretório de Destino** como **webapps**.
8. Se desejar implantar em um slot que não seja de produção, você também poderá definir o nome do **Slot**.
9. Salve o projeto e compile-o. Seu aplicativo Web é implantado no Azure quando o build estiver concluído.

### <a name="deploy-web-apps-by-uploading-files-using-jenkins-pipeline"></a>Implantar aplicativos Web, carregando arquivos usando o Pipeline do Jenkins

O plugin do Jenkins do Serviço de Aplicativo do Azure vem pronto para pipeline. Você pode se referir à seguinte amostra no repositório GitHub.

1. Na interface do GitHub, abra o arquivo **Jenkinsfile_ftp_plugin**. Para editar o arquivo, selecione o ícone de lápis. Atualize as definições de **resourceGroup** e **webAppName** para o aplicativo Web nas linhas 11 e 12, respectivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Configure a definição de **withCredentials** na linha 14 como a ID da credencial na sua instância do Jenkins:
    ```java
    withCredentials([azureServicePrincipal('<mySp>')]) {
    ```

### <a name="create-a-jenkins-pipeline"></a>Crie um pipeline do Jenkins

1. Abra o Jenkins em um navegador da Web. Selecione **Novo Item**.
2. Dê um nome para o trabalho e selecione **Pipeline**. Selecione **OK**.
3. Selecione a guia **Pipeline**.
4. Para o valor de **Definição**, selecione **Script de pipeline do SCM**.
5. Para o valor de **SCM**, selecione **Git**. Insira a URL do GitHub para seu repositório bifurcado. Por exemplo: https://&lt;your_forked_repo>.git.
6. Atualize o valor de **Caminho de Script** para **Jenkinsfile_ftp_plugin**.
7. Selecione **Salvar** e execute o trabalho.

## <a name="configure-jenkins-to-deploy-web-app-for-containers"></a>Configurar o Jenkins para implantar Aplicativos Web para Contêineres

Aplicativos Web no Linux oferecem suporte à implantação usando o Docker. Para implantar seu aplicativo Web usando o Docker, você precisa fornecer um Dockerfile que empacote o aplicativo Web com um runtime de serviço em uma imagem do Docker. O plugin do Jenkins então compila a imagem, envia-a por push a um registro de Docker e a implanta em seu aplicativo Web.

Os aplicativos Web no Linux também dão suporte a métodos de implantação tradicionais, como Git e upload de arquivo, mas somente para linguagens internas (.NET Core, Node.js, PHP e Ruby). Para outras linguagens, você precisa empacotar o código do aplicativo e runtime do serviço juntos em uma imagem do Docker e usar o Docker para implantar.

Antes de configurar o trabalho no Jenkins, você precisa de um aplicativo Web no Linux. Você também precisará de um registro de contêiner para armazenar e gerenciar suas imagens privadas de contêiner Docker. Você pode usar o DockerHub para criar o registro de contêiner. Nesse exemplo, usamos o Registro de Contêiner do Azure.

* [Crie um aplicativo Web no Linux](../app-service/containers/quickstart-nodejs.md).
* O Registro de Contêiner do Azure é um serviço [Docker Registry](https://docs.docker.com/registry/) gerenciado com base no software de fonte aberta Docker Registry versão 2.0. [Crie um registro de contêiner do Azure](/azure/container-registry/container-registry-get-started-azure-cli). Você também pode usar o DockerHub.

### <a name="set-up-the-jenkins-job-for-docker"></a>Configurar o trabalho do Jenkins para o Docker

1. Crie um novo projeto de **estilo livre** no painel do Jenkins.
2. Configure o campo **Gerenciamento de Código-fonte** para usar o seu fork local do [Aplicativo Web Java simples para o Azure](https://github.com/azure-devops/javawebappsample). Forneça o valor de **URL do repositório**. Por exemplo: http:\//github.com/&lt;your_ID>/javawebappsample.
3. Adicione uma etapa para criar o projeto usando Maven adicionando um comando **Executar shell**. Inclua a seguinte linha no comando:
    ```bash
    mvn clean package
    ```

4. Adicione uma ação pós-build selecionando **Publicar um Aplicativo Web do Azure**.
5. Forneça **mySp** como a entidade de serviço do Azure. Essa entidade foi armazenada como as [Credenciais do Azure](#service-principal) em uma etapa anterior.
6. Na seção **Configuração do Aplicativo**, escolha o aplicativo Web do Linux e o grupo de recursos em sua assinatura.
7. Escolha **Publicar por meio do Docker**.
8. Preencha o valor do caminho **Dockerfile**. Você pode manter o /Dockerfile de valor padrão.
Para o valor de **URL de registro de Docker**, forneça a URL usando o formato https://&lt;yourRegistry>.azurecr.io se usar o Registro de Contêiner do Azure. Se você usar o DockerHub, deixe o valor em branco.
9. Para o valor de **Credenciais de registro**, adicione a credencial para o Registro de Contêiner. Você pode obter a ID de usuário e senha executando os comandos a seguir na CLI do Azure. O primeiro comando habilita a conta de administrador:
    ```azurecli-interactive
    az acr update -n <yourRegistry> --admin-enabled true
    az acr credential show -n <yourRegistry>
    ```

10. O nome da imagem do Docker e o valor da marca na guia **Avançado** são opcionais. Por padrão, o valor para o nome da imagem é obtido do nome da imagem que você configurou no Portal do Azure na configuração do **Contêiner do Docker**. A marca é gerada com base em $BUILD_NUMBER.
    > [!NOTE]
    > Certifique-se de especificar o nome da imagem no portal do Azure ou forneça um valor para **Imagem do Docker** na guia **Avançado**. Neste exemplo, defina o valor de **Imagem do Docker** como &lt;your_Registry>.azurecr.io/calculator e deixe o valor de **Marca de imagem do Docker** em branco.

11. A implantação falhará se você usar uma configuração de imagem do Docker integrada. Altere a configuração do Docker para usar uma imagem personalizada na configuração **Contêiner do Docker** no Portal do Azure. Para uma imagem interna, use a abordagem de upload de arquivo para implantar.
12. Semelhante à abordagem de upload de arquivo, você pode escolher um nome de **Slot** diferente que não seja de **produção**.
13. Salve e compile o projeto. A imagem de contêiner é enviada por push para o registro e o aplicativo Web é implantado.

### <a name="deploy-web-app-for-containers-by-using-jenkins-pipeline"></a>Implantar Aplicativos Web para Contêineres usando o pipeline do Jenkins

1. Na interface do GitHub, abra o arquivo **Jenkinsfile_container_plugin**. Para editar o arquivo, selecione o ícone de lápis. Atualize as definições de **resourceGroup** e **webAppName** para o aplicativo Web nas linhas 11 e 12, respectivamente:
    ```java
    def resourceGroup = '<myResourceGroup>'
    def webAppName = '<myAppName>'
    ```

2. Altere a linha 13 para o servidor de registro de contêiner:
    ```java
    def registryServer = '<registryURL>'
    ```

3. Altere a linha 16 para usar a ID de credencial na sua instância do Jenkins:
    ```java
    azureWebAppPublish azureCredentialsId: '<mySp>', publishType: 'docker', resourceGroup: resourceGroup, appName: webAppName, dockerImageName: imageName, dockerImageTag: imageTag, dockerRegistryEndpoint: [credentialsId: 'acr', url: "http://$registryServer"]
    ```

### <a name="create-a-jenkins-pipeline"></a>Crie um pipeline do Jenkins    

1. Abra o Jenkins em um navegador da Web. Selecione **Novo Item**.
2. Dê um nome para o trabalho e selecione **Pipeline**. Selecione **OK**.
3. Selecione a guia **Pipeline**.
4. Para o valor de **Definição**, selecione **Script de pipeline do SCM**.
5. Para o valor de **SCM**, selecione **Git**. Insira a URL do GitHub para seu repositório bifurcado. Por exemplo: https://&lt;your_forked_repo>.git.
7. Atualize o valor de **Caminho de Script** para **Jenkinsfile_container_plugin**.
8. Selecione **Salvar** e execute o trabalho.

## <a name="verify-your-web-app"></a>Verifique seu aplicativo Web

1. Para verificar se o arquivo WAR foi implantado com êxito em seu aplicativo Web, abra um navegador da Web.
2. Vá para http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Substitua &lt;your_app_name> pelo nome do seu aplicativo Web. Você verá a mensagem:
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jun 17 16:39:10 UTC 2017
    ```

3. Vá para http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Substitua &lt;x> e &lt;y> por qualquer número para obter a soma de x + y. A calculadora mostra a soma: ![Calculadora: somar](./media/execute-cli-jenkins-pipeline/calculator-add.png)

### <a name="for-azure-app-service-on-linux"></a>Para Serviço de Aplicativo do Azure no Linux

1. Para verificar seu aplicativo web, execute o seguinte comando na CLI do Azure:
    
    ```azurecli
    az acr repository list -n <myRegistry> -o json
    ```
    
    A seguinte mensagem é exibida:
    
    ```CLI
    ["calculator"]
    ```
    
2. Vá para http://&lt;your_app_name>.azurewebsites.net/api/calculator/ping. Substitua &lt;your_app_name> pelo nome do seu aplicativo Web. Você verá a mensagem: 
    ```
    Welcome to Java Web App!!! This is updated!
    Sun Jul 09 16:39:10 UTC 2017
    ```

3. Vá para http://&lt;your_app_name>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y>. Substitua &lt;x> e &lt;y> por qualquer número para obter a soma de x + y.
    
## <a name="troubleshooting-the-jenkins-plugin"></a>O plug-in do Jenkins de solução de problemas

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você usou o plugin do Jenkins do Serviço de Aplicativo do Azure para implantar no Azure.

Você aprendeu a:

> [!div class="checklist"]
> * Configurar Jenkins para implantar o Serviço de Aplicativo do Azure por meio de upload de arquivo 
> * Configurar o Jenkins para implantar Aplicativos Web para Contêineres 
