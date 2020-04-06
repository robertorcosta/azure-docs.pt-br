---
title: Implantar do GitHub para o AKS com jenkins
titleSuffix: Azure Kubernetes Service
description: Configurar o Jenkins para integração contínua (CI) do GitHub e da implantação contínua (CD) para o Serviço de Kubernetes do Azure (AKS)
services: container-service
author: zr-msft
ms.author: zarhoads
ms.topic: article
ms.date: 01/09/2019
ms.openlocfilehash: a93cfc77178ff7638217663c2ceda500aae4cfd7
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668630"
---
# <a name="tutorial-deploy-from-github-to-azure-kubernetes-service-aks-with-jenkins-continuous-integration-and-deployment"></a>Tutorial: Implante do GitHub para o Azure Kubernetes Service (AKS) com a integração e implantação contínuas do Jenkins

Este tutorial implanta um aplicativo de amostra do GitHub em um [cluster do Serviço de Kubernetes do Azure (AKS)](/azure/aks/intro-kubernetes) configurando a integração contínua (IC) e a implantação contínua (CD) no Jenkins. Dessa forma, quando você atualiza seu aplicativo enviando confirmações para o GitHub, o Jenkins executa automaticamente uma nova compilação de contêiner, envia imagens do Registro de Contêiner do Azure (ACR) e executa seu aplicativo no AKS. 

Neste tutorial, você concluirá estas tarefas:

> [!div class="checklist"]
> * Implante um aplicativo de voto do Azure de exemplo para um cluster do AKS.
> * Crie um projeto básico do Jenkins.
> * Configure as credenciais para o Jenkins interagir com o ACR.
> * Crie um trabalho de build do Jenkins e o webhook do GitHub para compilações automatizadas.
> * Teste o pipeline de CI/CD para atualizar um aplicativo no AKS com base nas confirmações de código do GitHub.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará destes itens:

- Reconhecimento básico de Kubernetes, Git, CI/CD e imagens de contêiner

- Um [cluster do AKS](../aks/kubernetes-walkthrough.md) e `kubectl` configurado com as [credenciais do cluster do AKS](/cli/azure/aks#az-aks-get-credentials).

- Um [registro do ACR (Registro de Contêiner do Azure)](../container-registry/container-registry-get-started-azure-cli.md), o nome do servidor de logon do ACR e um cluster do AKS configurado para [autenticação com o Registro do ACR](../aks/cluster-container-registry-integration.md).

- A CLI do Azure versão 2.0.46 ou posterior, instalada e configurada. Execute  `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, confira  [Instalar a CLI do Azure](/cli/azure/install-azure-cli).

- [Docker instalado](https://docs.docker.com/install/) em seu sistema de desenvolvimento

- Uma conta GitHub, [token de acesso pessoal do GitHub](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)e cliente Git instalado em seu sistema de desenvolvimento

- Se você fornecer sua própria instância do Jenkins, em vez de usar esse método para implementar o Jenkins, sua instância do Jenkins precisará do [Docker instalado e configurado](https://docs.docker.com/install/) e [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

## <a name="prepare-your-app"></a>Preparação do aplicativo

Neste artigo, você usa um aplicativo de voto do Azure de exemplo que contém uma interface da Web hospedada em um ou mais pods, além de um segundo pod que hospeda o Redis para armazenamento de dados temporário. Antes de integrar o Jenkins e o AKS para implantações automatizadas, primeiro prepare e implante manualmente o aplicativo de voto do Azure no seu cluster do AKS. Essa implantação manual é a versão um do aplicativo e permite que você veja o aplicativo em ação.

> [!NOTE]
> O aplicativo de votação Sample Azure usa um pod Linux programado para ser executado em um nó Linux. O fluxo descrito neste artigo também funciona para um pod do Windows Server programado em um nó do Servidor Windows.

Bifurque o seguinte repositório GitHub [https://github.com/Azure-Samples/azure-voting-app-redis](https://github.com/Azure-Samples/azure-voting-app-redis)para a aplicação da amostra - . Para bifurcar o repositório para sua conta do GitHub, selecione o botão **Bifurcação** no canto superior direito.

Clone a bifurcação no sistema de desenvolvimento. Não se esqueça de usar a URL da sua bifurcação ao clonar este repositório:

```console
git clone https://github.com/<your-github-account>/azure-voting-app-redis.git
```

Altere para o diretório da sua bifurcação clonada:

```console
cd azure-voting-app-redis
```

Para criar as imagens de contêiner necessárias para o aplicativo de exemplo, use o arquivo *docker-compose.yaml* com `docker-compose`:

```console
docker-compose up -d
```

É efetuado o pull das imagens base necessárias e os contêineres do aplicativo são compilados. Depois, é possível usar o comando [docker images](https://docs.docker.com/engine/reference/commandline/images/) para ver a imagem criada. Três imagens foram baixadas ou criadas. A imagem `azure-vote-front` contém o aplicativo e usa a imagem `nginx-flask` como base. A imagem `redis` é usada para iniciar uma instância do Redis:

```
$ docker images

REPOSITORY                   TAG        IMAGE ID            CREATED             SIZE
azure-vote-front             latest     9cc914e25834        40 seconds ago      694MB
redis                        latest     a1b99da73d05        7 days ago          106MB
tiangolo/uwsgi-nginx-flask   flask      788ca94b2313        9 months ago        694MB
```

Antes de efetuar push da imagem de contêiner *azure-vote-front* para o ACR, obtenha seu servidor de logon do ACR com o comando [az acr list](/cli/azure/acr#az-acr-list). O exemplo a seguir obtém o endereço do servidor de logon do ACR para um registro no grupo de recursos denominado *myResourceGroup*:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Use o comando [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) para marcar a imagem com o nome do servidor de logon do ACR e um número de versão `v1`. Forneça seu próprio nome `<acrLoginServer>` obtido na etapa anterior:

```console
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:v1
```

Por fim, efetue push da imagem *azure-vote-front* para seu Registro do ACR. Novamente, substitua `<acrLoginServer>` pelo nome do servidor de logon do seu próprio Registro do ACR, como `myacrregistry.azurecr.io`:

```console
docker push <acrLoginServer>/azure-vote-front:v1
```

## <a name="deploy-the-sample-application-to-aks"></a>Implantar o aplicativo de exemplo no AKS

Para implantar o aplicativo de exemplo no cluster do AKS, você pode usar o arquivo de manifesto do Kubernetes na raiz do repositório de voto do Azure. Abra o arquivo de manifesto *azure-vote-all-in-one-redis.yaml* com um editor como `vi`. Substitua `microsoft` pelo nome do servidor de logon do ACR. Esse valor pode ser encontrado na linha **47** do arquivo de manifesto:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Em seguida, use o comando [kubectl apply](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply) para implantar o aplicativo no seu cluster do AKS:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Um serviço de balanceador de carga do Kubernetes é criado para expor o aplicativo à Internet. Esse processo pode levar alguns minutos. Para monitorar o progresso da implantação do balanceador de carga, use o comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) com o argumento `--watch`. Depois que o endereço *EXTERNAL-IP* for alterado de *pendente* para um *endereço IP*, use `Control + C` para interromper o processo kubectl watch.

```console
$ kubectl get service azure-vote-front --watch

NAME               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.215.27   <pending>     80:30747/TCP   22s
azure-vote-front   LoadBalancer   10.0.215.27   40.117.57.239   80:30747/TCP   2m
```

Para ver o aplicativo em ação, abra um navegador da Web no endereço IP externo do sue serviço. O aplicativo de voto do Azure é exibido, como mostrado no exemplo a seguir:

![Aplicativo de voto de exemplo do Azure em execução no AKS](media/jenkins-continuous-deployment/azure-vote.png)

## <a name="deploy-jenkins-to-an-azure-vm"></a>Implantar o Jenkins em uma VM do Azure

A fim de implantar rapidamente o Jenkins para uso neste artigo, você pode usar o script a seguir para implantar uma máquina virtual do Azure, configurar o acesso à rede e concluir uma instalação básica do Jenkins. Para autenticação entre o Jenkins e o cluster do AKS, o script copia o arquivo de configuração do Kubernetes do seu sistema de desenvolvimento para o sistema do Jenkins.

> [!WARNING]
> Esse script de amostra é para fins de demonstração para provisionar rapidamente um ambiente Jenkins executado em uma VM do Azure. Ele usa a extensão de script personalizado do Azure para configurar uma VM e, em seguida, exibir as credenciais necessárias. Sua *~/.kube/config* é copiada para a VM Jenkins.

Execute os comandos a seguir para baixar e executar o script. Você deve rever o conteúdo de qualquer [https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh](https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh)script antes de executá-lo - .

```console
curl https://raw.githubusercontent.com/Azure-Samples/azure-voting-app-redis/master/jenkins-tutorial/deploy-jenkins-vm.sh > azure-jenkins.sh
sh azure-jenkins.sh
```

Demora alguns minutos para criar a VM e implantar os componentes necessários para o Docker e o Jenkins. Quando o script for concluído, produzirá um endereço para o servidor do Jenkins e uma chave para desbloquear o painel, conforme mostrado na saída de exemplo a seguir:

```
Open a browser to http://40.115.43.83:8080
Enter the following to Unlock Jenkins:
667e24bba78f4de6b51d330ad89ec6c6
```

Abra um navegador da Web com a URL exibida e insira a chave de desbloqueio. Siga as instruções na tela para concluir a configuração do Jenkins:

- Escolha **Instalar os plug-ins sugeridos**
- Crie o primeiro usuário administrador. Insira um nome de usuário, por exemplo, *azureuser*; depois forneça sua própria senha de segurança. Por fim, digite um nome completo e o endereço de email.
- Selecione **Salvar e Concluir**
- Assim que o Jenkins estiver pronto, selecione **Começar a usar o Jenkins**
    - Se seu navegador da Web exibir uma página em branco quando você começar a usar o Jenkins, reinicie o serviço do Jenkins. Para reiniciar o serviço, faça SSH no endereço IP público de sua instância do Jenkins e digite `sudo service jenkins restart`. Depois que o serviço for reiniciado, atualize o navegador da Web.
- Entre no Jenkins com o nome de usuário e a senha que criou no processo de instalação.

## <a name="create-a-jenkins-environment-variable"></a>Criar uma variável de ambiente do Jenkins

Uma variável de ambiente do Jenkins é usada para armazenar o nome do servidor de logon do ACR. Essa variável é referenciada durante o trabalho de build do Jenkins. Para criar essa variável de ambiente, conclua as etapas a seguir:

- No lado esquerdo do portal Jenkins, selecione Gerenciar o**sistema de configuração de** **Jenkins** > 
- Em **Propriedades globais**, selecione **Variáveis de ambiente**. Adicione uma variável com o nome `ACR_LOGINSERVER` e o valor do seu servidor de logon do ACR.

    ![Variáveis de ambiente Jenkins](media/jenkins-continuous-deployment/env-variables.png)

- Ao concluir, clique em **Salvar** na parte inferior da página de configuração do Jenkins.

## <a name="create-a-jenkins-credential-for-acr"></a>Criar uma credencial do Jenkins para ACR

Para permitir que o Jenkins compile e, depois, efetue push das imagens de contêiner atualizadas para o ACR, é necessário especificar credenciais para o ACR. Essa autenticação pode usar as entidades de serviço do Azure Active Directory. Nos pré-requisitos, você configurou a entidade de serviço para o cluster do AKS com permissões de *Leitor* para o Registro do ACR. Essas permissões permitem que o cluster do AKS *efetue pull* das imagens do Registro do ACR. Durante o processo de CI/CD, o Jenkins compila novas imagens de contêiner com base nas atualizações do aplicativo e, em seguida, precisa *efetuar push* dessas imagens para o Registro do ACR. Para a separação de funções e permissões, configure uma entidade de serviço para o Jenkins com permissões de *Colaborador* para o Registro do ACR.

### <a name="create-a-service-principal-for-jenkins-to-use-acr"></a>Criar uma entidade de serviço para o Jenkins usar o ACR

Em primeiro lugar, use o comando [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) para criar uma entidade de serviço:

```azurecli
$ az ad sp create-for-rbac --skip-assignment

{
  "appId": "626dd8ea-042d-4043-a8df-4ef56273670f",
  "displayName": "azure-cli-2018-09-28-22-19-34",
  "name": "http://azure-cli-2018-09-28-22-19-34",
  "password": "1ceb4df3-c567-4fb6-955e-f95ac9460297",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

Anote a *appId* e a *senha* mostradas na sua saída. Esses valores são usados nas etapas a seguir para configurar o recurso de credencial no Jenkins.

Obtenha a ID do recurso do Registro do ACR usando o comando [az acr show](/cli/azure/acr#az-acr-show) e armazene-a como variável. Forneça o nome de grupo de recursos e o nome do ACR:

```azurecli
ACR_ID=$(az acr show --resource-group myResourceGroup --name <acrLoginServer> --query "id" --output tsv)
```

Agora, crie uma atribuição de função para atribuir os direitos de *Colaborador* da entidade de serviço ao Registro do ACR. No exemplo a seguir, forneça ao seu próprio *appId* mostrado na saída um comando anterior para criar a entidade de serviço:

```azurecli
az role assignment create --assignee 626dd8ea-042d-4043-a8df-4ef56273670f --role Contributor --scope $ACR_ID
```

### <a name="create-a-credential-resource-in-jenkins-for-the-acr-service-principal"></a>Criar um recurso de credencial no Jenkins para a entidade de serviço do ACR

Com a atribuição de função criada no Azure, armazene suas credenciais do ACR em um objeto de credencial do Jenkins. Essas credenciais são referenciadas durante o trabalho de build do Jenkins.

De volta ao lado esquerdo do portal Jenkins, clique em **Credenciais** > **Jenkins** > **Global credenciais (irrestritas)** > **Adicionar credenciais**

Verifique se o tipo de credencial é **Nome de usuário com senha** e insira os seguintes itens:

- **Nome de usuário** – o *appId* da entidade de serviço criada para autenticação com o Registro do ACR.
- **Senha** – a *senha* da entidade de serviço criada para autenticação com o Registro do ACR.
- **ID** – identificador de credenciais, como *acr-credentials*

Ao concluir, o formulário de credenciais será semelhante ao exemplo a seguir:

![Criar um objeto de credencial do Jenkins com as informações da entidade de serviço](media/jenkins-continuous-deployment/acr-credentials.png)

Clique em **OK** e retorne ao portal do Jenkins.

## <a name="create-a-jenkins-project"></a>Criar um projeto do Jenkins

Na página inicial do portal do Jenkins, selecione **Novo item** no lado esquerdo:

1. Insira *azure-vote* como nome do trabalho. Escolha **Projeto Freestyle** e selecione **OK**
1. Na seção **Geral,** selecione o **projeto GitHub** e insira sua URL de repo bifurcada, como *\/https: /github.com/\<sua conta-github\>/azure-voting-app-redis*
1. Na seção **De gerenciamento de código-fonte,** selecione **Git**, digite seu URL de repo *.git* bifurcado, como *https:\//github.com/\<sua conta-github/azure-voting-app-redis.git\>*

1. Na seção **Gatilhos de Build**, selecione **Gatilho de gancho do GitHub para sondagem de GITscm**
1. Em **Ambiente de Build**, selecione **Usar arquivos ou textos secretos**
1. Em **Associações**, selecione **Adicionar** > **Nome de usuário e senha (separados)**
   - Digite `ACR_ID` para a **Variável de Nome de Usuário** e `ACR_PASSWORD` para a **Variável de Senha**

     ![Associações do Jenkins](media/jenkins-continuous-deployment/bindings.png)

1. Opte por adicionar uma **Etapa de Build** do tipo **Executar shell** e use o texto a seguir. Esse script compila uma nova imagem de contêiner e envia-a por push para o registro de ACR.

    ```bash
    # Build new image and push to ACR.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    docker build -t $WEB_IMAGE_NAME ./azure-vote
    docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
    docker push $WEB_IMAGE_NAME
    ```

1. Adicione outra **Etapa de Build** do tipo **Executar shell** e use o texto a seguir. Esse script atualiza a implantação do aplicativo no AKS com a nova imagem de contêiner do ACR.

    ```bash
    # Update kubernetes deployment with new image.
    WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
    kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config
    ```

1. Depois de concluído, clique em **Salvar**.

## <a name="test-the-jenkins-build"></a>Testar o build do Jenkins

Antes de automatizar o trabalho com base nas confirmações do GitHub, primeiro teste manualmente o build do Jenkins. Esse build manual valida que o trabalho foi configurado corretamente, o arquivo de autenticação do Kubernetes adequado está em vigor e que a autenticação com o ACR funciona.

No menu esquerdo do projeto, selecione **Compilar agora**.

![Build de teste do Jenkins](media/jenkins-continuous-deployment/test-build.png)

O primeiro build demora um ou dois minutos enquanto é efetuado pull das camadas de imagem do Docker no servidor do Jenkins. Os builds subsequentes podem usar as camadas de imagem em cache para melhorar os tempos de build.

Durante o processo de build, o repositório do GitHub é clonado para o servidor de build do Jenkins. Uma nova imagem de contêiner é compilada e enviada por push para o registro do ACR. Por fim, o aplicativo de voto do Azure em execução no cluster do AKS é atualizado para usar a nova imagem. Já que nenhuma alteração foi feita ao código do aplicativo, o aplicativo não será alterado se você exibir o aplicativo de exemplo em um navegador da Web.

Quando o trabalho de build for concluído, clique em **build nº 1** em histórico de build. Selecione **Saída do console** e exiba a saída do processo de build. A linha final deve indicar um build bem-sucedido.

## <a name="create-a-github-webhook"></a>Criar um webhook do GitHub

Com um build manual de sucesso concluído, agora integre o GitHub no build do Jenkins. Um webhook pode ser usado para executar o trabalho de build do Jenkins sempre que uma confirmação de código for feita no GitHub. Para criar o webhook do GitHub, conclua as etapas a seguir:

1. Navegue até seu repositório bifurcado do GitHub em um navegador da Web.
1. Selecione **Configurações** e, em seguida, **Webhooks** no lado esquerdo.
1. Escolha **Adicionar webhook**. Para a *URL de payload*, digite `http://<publicIp:8080>/github-webhook/`, em que `<publicIp>` é o endereço IP do servidor do Jenkins. Não deixe de incluir a barra (/) à direita. Deixe os outros padrões para o tipo de conteúdo e para gatilho em eventos de *push*.
1. Selecione **Adicionar webhook**.

    ![Criar um webhook do GitHub para o Jenkins](media/jenkins-continuous-deployment/webhook.png)

## <a name="test-the-complete-cicd-pipeline"></a>Testar o pipeline de CI/CD completo

Agora, é possível testar o pipeline de CI/CD inteiro. Quando você efetua push de uma confirmação de código para o GitHub, acontecem as etapas a seguir:

1. O webhook do GitHub entra em contato com o Jenkins.
1. O Jenkins inicia o trabalho de build e efetua pull da confirmação de código mais recente do GitHub.
1. Um build do Docker é iniciado usando o código atualizado; a nova imagem de contêiner é marcada com o número de build mais recente.
1. É efetuado push da nova imagem de contêiner para o Registro de Contêiner do Azure.
1. O aplicativo implantado no Serviço de Kubernetes do Azure é atualizado com a imagem de contêiner mais recente do registro do Registro de Contêiner do Azure.

No computador de desenvolvimento, abra o aplicativo clonado com um editor de código. No diretório */azure-vote/azure-vote*, abra o arquivo denominado **config_file.cfg**. Atualize os valores de voto neste arquivo para algo diferente de gatos e cachorros, como mostrado no exemplo a seguir:

```
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Ao atualizar, salve o arquivo, confirme as alterações e efetue push para a bifurcação do repositório do GitHub. O webhook do GitHub dispara um novo trabalho de build no Jenkins. No painel da Web do Jenkins, monitore o processo de build. Demora alguns segundos para efetuar pull do código mais recente, criar e efetuar push da imagem atualizada e implantar o aplicativo atualizado no AKS.

Depois que o build estiver concluído, atualize seu navegador da Web do aplicativo de voto do Azure de exemplo. As alterações serão exibidas, como mostra o exemplo a seguir:

![Voto do Azure de exemplo no AKS atualizado pelo trabalho de build do Jenkins](media/jenkins-continuous-deployment/azure-vote-updated.png)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/jenkins)