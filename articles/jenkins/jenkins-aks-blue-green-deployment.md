---
title: Implantar no Serviço de Kubernetes do Azure usando o Jenkins e o padrão de implantação azul/verde
description: Saiba como implantar no Azure Kubernetes Service (AKS) usando o Jenkins e o padrão de implantação azul/verde.
ms.service: jenkins
keywords: jenkins, azure, devops, kubernetes, k8s, aks, implantação azul verde, entrega contínua, cd
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/09/2019
ms.openlocfilehash: de9088333f69a22246fe5873d6e09ddb7ba3044a
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249401"
---
# <a name="deploy-to-azure-kubernetes-service-aks-by-using-jenkins-and-the-bluegreen-deployment-pattern"></a>Implantar no Azure Kubernetes Service (AKS) usando o Jenkins e o padrão de implantação azul / verde

O Serviço de Kubernetes do Azure (AKS) gerencia seu ambiente hospedado do Kubernetes, tornando rápido e fácil implantar e gerenciar aplicativos em contêiner. Você não precisa de conhecimentos em orquestração de contêineres. A AKS também elimina a carga de operações e manutenção contínuas, provisionando, atualizando e dimensionando recursos sob demanda. Você não precisa deixar seus aplicativos offline. Para obter mais informações sobre o AKS, consulte o [documentação do AKS](/azure/aks/).

A implantação em azul / verde é um padrão de entrega contínua do DevOps do Azure que se baseia em manter uma versão existente (azul) ativa, enquanto uma nova (verde) é implantada. Normalmente, esse padrão emprega o balanceamento de carga para direcionar o crescente aumento de tráfego à implantação verde. Se o monitoramento descobrir um incidente, o tráfego poderá ser redirecionado para a implantação azul, que ainda está em execução. Para obter mais informações sobre a entrega contínua, consulte [o que é a entrega contínua](/azure/devops/what-is-continuous-delivery).

Neste tutorial, você aprenderá a executar as seguintes tarefas:

> [!div class="checklist"]
> * Saiba mais sobre o padrão de implantação azul/verde
> * Criar um cluster Kubernetes gerenciado
> * Executar um script de exemplo para configurar um cluster de Kubernetes
> * Configurar manualmente um cluster de Kubernetes
> * Criar e executar um trabalho do Jenkins

## <a name="prerequisites"></a>Pré-requisitos
- [Conta do GitHub](https://github.com): é necessário ter uma conta do GitHub para clonar o repositório de exemplo.
- [CLI do Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest): a CLI do Azure 2.0 é usada para criar o cluster do Kubernetes.
- [Chocolatey](https://chocolatey.org): um gerenciador de pacotes que você usa para instalar o kubectl.
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/): uma interface de linha de comando que você usa para executar comandos em clusters do Kubernetes.
- [jq](https://stedolan.github.io/jq/download/): um processador JSON leve, de linha de comando.

## <a name="clone-the-sample-app-from-github"></a>Clonar o aplicativo de exemplo do GitHub

No repositório da Microsoft no GitHub, você pode encontrar um aplicativo de exemplo que ilustra como implantar no AKS usando Jenkins e o padrão azul / verde. Nesta seção, você cria um fork desse repositório no GitHub e clona o aplicativo no sistema local.

1. Navegue até o repositório GitHub para o aplicativo de exemplo [todo-app-java-on-azure](https://github.com/microsoft/todo-app-java-on-azure.git).

    ![Captura de tela do aplicativo de exemplo no repositório GitHub da Microsoft](./media/jenkins-aks-blue-green-deployment/github-sample-msft.png)

1. Crie o fork do repositório, selecionando **Fork** no canto superior direito da página e siga as instruções para criar fork do repositório na conta do GitHub.

    ![Opção de captura de tela do GitHub para bifurcação](./media/jenkins-aks-blue-green-deployment/github-sample-msft-fork.png)

1. Depois que você bifurca o repo, você vê que o nome da conta muda para o nome da sua conta, e uma nota indica de onde o repo foi bifurcado (Microsoft).

    ![Captura de tela do nome e da anotação da conta do GitHub](./media/jenkins-aks-blue-green-deployment/github-sample-msft-forked.png)

1. Selecione **Clonar ou baixar**.

    ![Captura de tela da opção GitHub para clonar ou baixar um repositório](./media/jenkins-aks-blue-green-deployment/github-sample-clone.png)

1. Na janela **Clonar com HTTPS**, selecione o ícone **cópia**.

    ![Opção de captura de tela do GitHub para copiar a URL de clone para a área de transferência](./media/jenkins-aks-blue-green-deployment/github-sample-copy.png)

1. Abra uma janela de Git Bash ou terminal.

1. Altere os diretórios para o local desejado onde você quer armazenar a cópia local (clone) do repositório.

1. Usando o comando `git clone`, clone a URL que você copiou anteriormente.

    ![Captura de tela do Git Bash comando de clone de git](./media/jenkins-aks-blue-green-deployment/git-clone-command.png)

1. Pressione a tecla Enter para iniciar o processo de clonagem.

    ![Captura de tela do Git Bash git clone os resultados do comando](./media/jenkins-aks-blue-green-deployment/git-clone-results.png)

1. Altere os diretórios para o diretório criado recentemente que contém o clone da origem do aplicativo.

## <a name="create-and-configure-a-managed-kubernetes-cluster"></a>Crie e configure um cluster de Kubernetes gerenciado

Nesta seção, você executa as seguintes etapas:

- Use a CLI 2.0 do Azure para criar um cluster de Kubernetes gerenciado.
- Aprenda como configurar um cluster usando o script de configuração ou manualmente.
- Crie uma instância do serviço Azure Container Registry.

### <a name="use-the-azure-cli-20-to-create-a-managed-kubernetes-cluster"></a>Usar a CLI 2.0 do Azure para criar um cluster de Kubernetes gerenciado
Para criar um cluster do Kubernetes gerenciado com o [CLI 2.0 do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest), verifique se você está usando a CLI do Azure versão 2.0.25 ou posterior.

1. Entre na sua conta do Azure. Depois de digitar o seguinte comando, você receberá instruções que explicam como concluir a entrada. 
    
    ```bash
    az login
    ```

1. Quando você executa o `az login` de comando na etapa anterior, será exibida uma lista de todas as suas assinaturas do Azure (juntamente com suas IDs de assinatura). Nesta etapa, você deve definir a assinatura do Azure padrão. Substitua o espaço reservado &lt;your-subscription-id > pela ID de assinatura do Azure desejada. 

    ```bash
    az account set -s <your-subscription-id>
    ```

1. Crie um grupos de recursos. Substitua o espaço reservado &lt;your-resource-group-name > pelo nome do novo grupo de recursos e substitua o espaço reservado &lt;your-location> pelo local. O comando `az account list-locations` exibe todos os locais do Azure. Durante a visualização do AKS, nem todos os locais estão disponíveis. Se você inserir um local que não é válido no momento, a mensagem de erro listará os locais disponíveis.

    ```bash
    az group create -n <your-resource-group-name> -l <your-location>
    ```

1. Crie o cluster de Kubernetes. Substitua &lt;seu-resource-group-name> pelo nome do grupo de recursos criado na etapa anterior e substitua o &lt;seu-kubernetes-cluster-name> pelo nome do novo cluster. (A conclusão desse processo pode demorar vários minutos.)

    ```bash
    az aks create -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --generate-ssh-keys --node-count 2
    ```

### <a name="set-up-the-kubernetes-cluster"></a>Configurar o cluster de Kubernetes

Você pode configurar uma implantação azul / verde no AKS manualmente ou com um script de configuração fornecido na amostra clonada anteriormente. Nesta seção, você verá como fazer ambos.

#### <a name="set-up-the-kubernetes-cluster-via-the-sample-setup-script"></a>Configurar o cluster de Kubernetes por meio do exemplo de script de instalação
1. Edite o arquivo **deploy/aks/setup/setup.sh**, substituindo os seguintes espaços reservados pelos valores apropriados para o ambiente: 

   - **&lt;your-resource-group-name>**
   - **&lt;your-kubernetes-cluster-name>**
   - **&lt;your-location>**
   - **&lt;your-dns-name-suffix>**

     ![Captura de tela setup.sh script no bash, com vários espaços reservados realçado](./media/jenkins-aks-blue-green-deployment/edit-setup-script.png)

1. Execute o script de instalação.

    ```bash
    sh setup.sh
    ```

#### <a name="set-up-a-kubernetes-cluster-manually"></a>Configurar um cluster de Kubernetes manualmente 
1. Baixe a configuração do Kubernetes na sua pasta de perfil.

    ```bash
    az aks get-credentials -g <your-resource-group-name> -n <your-kubernetes-cluster-name> --admin
    ```

1. Altere o diretório para o **implantar/aks/instalação** directory. 

1. Execute o seguinte **kubectl** comandos para configurar os serviços para o ponto de extremidade público e dois pontos de extremidade do teste.

    ```bash
    kubectl apply -f  service-green.yml
    kubectl apply -f  test-endpoint-blue.yml
    kubectl apply -f  test-endpoint-green.yml
    ```

1. Atualize o nome DNS para o público e os pontos de extremidade do teste. Ao criar um cluster do Kubernetes, você também cria um [grupo de recursos adicionais](https://github.com/Azure/AKS/issues/3), com o padrão de nomeação de **MC_&lt; nome do seu grupo de recursos> _&lt;your-kubernetes -cluster-name>_ &lt;sua localização>** .

    Localize os IPs públicos no grupo de recursos.

    ![Captura de tela de IPs públicos no grupo de recursos](./media/jenkins-aks-blue-green-deployment/publicip.png)

    Para cada um dos serviços, localize o endereço IP externo, executando o seguinte comando:
    
    ```bash
    kubectl get service todoapp-service
    ```
    
    Atualize o nome DNS do endereço IP correspondente com o seguinte comando:

    ```bash
    az network public-ip update --dns-name aks-todoapp --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    Repita a chamada para `todoapp-test-blue` e `todoapp-test-green`:

    ```bash
    az network public-ip update --dns-name todoapp-blue --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>

    az network public-ip update --dns-name todoapp-green --ids /subscriptions/<your-subscription-id>/resourceGroups/MC_<resourcegroup>_<aks>_<location>/providers/Microsoft.Network/publicIPAddresses/kubernetes-<ip-address>
    ```

    O nome DNS deve ser exclusivo na assinatura. Para garantir a exclusividade, você pode usar `<your-dns-name-suffix>`.

### <a name="create-an-instance-of-container-registry"></a>Criar uma instância do registro de contêiner

1. Execute o comando `az acr create` para criar uma instância do Container Registry. Na próxima seção, você pode usar `login server` como a URL de registro do Docker.

    ```bash
    az acr create -n <your-registry-name> -g <your-resource-group-name>
    ```

1. Execute o `az acr credential` comando para mostrar suas credenciais de registro de contêiner. Observe o nome de usuário de registro de Docker e a senha, conforme necessário na próxima seção.

    ```bash
    az acr credential show -n <your-registry-name>
    ```

## <a name="prepare-the-jenkins-server"></a>Preparar o servidor Jenkins

Nesta seção, você verá como preparar o servidor Jenkins para executar uma compilação que é boa para teste. No entanto, você deve usar um [agente de VM do Azure](https://plugins.jenkins.io/azure-vm-agents) ou um [agente do Azure Container](https://plugins.jenkins.io/azure-container-agents) para criar um agente no Azure para executar suas compilações. Para obter mais informações, consulte o artigo de Jenkins sobre o [implicações de segurança de compilação no mestre](https://wiki.jenkins.io/display/JENKINS/Security+implication+of+building+on+master).

1. Implantar um [Mestre do Jenkins no Azure](https://aka.ms/jenkins-on-azure).

1. Conecte ao servidor via SSH e instale as ferramentas de compilação no servidor onde você executa a compilação.
   
   ```bash
   sudo apt-get install git maven 
   ```
   
1. [Instale o Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-ce). Certifique-se de que o usuário `jenkins` tem permissão para executar o `docker` comandos.

1. [Instale o kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/).

1. [Baixe o jq](https://stedolan.github.io/jq/download/).

1. Instale o jq usando o comando a seguir:

   ```bash
   sudo apt-get install jq
   ```
   
1. Instale os plug-ins no Jenkins executando as etapas a seguir no painel do Jenkins:

    1. Selecione **Gerenciar o Jenkins > Gerenciar Plug-ins > Disponíveis**.
    1. Procure e instale o plug-in do Serviço de Contêiner do Azure.

1. Adicione as credenciais para gerenciar recursos no Azure. Se você ainda não tiver o plug-in, instale o plug-in **Azure Credential**.

1. Adicione sua credencial do Azure Service Principal como o tipo do **Microsoft Azure Service Principal**.

1. Adicionar seu nome de usuário de registro de Docker do Azure e a senha (conforme obtido na seção, "Criar uma instância do registro de contêiner") como o tipo **nome de usuário com senha**.

## <a name="edit-the-jenkinsfile"></a>Editar o Jenkinsfile

1. Em seu próprio repo, vá para `/deploy/aks/` e abra `Jenkinsfile`.

2. Atualize o arquivo conforme descrito a seguir:

    ```groovy
    def servicePrincipalId = '<your-service-principal>'
    def resourceGroup = '<your-resource-group-name>'
    def aks = '<your-kubernetes-cluster-name>'

    def cosmosResourceGroup = '<your-cosmodb-resource-group>'
    def cosmosDbName = '<your-cosmodb-name>'
    def dbName = '<your-dbname>'

    def dockerRegistry = '<your-acr-name>.azurecr.io'
    ```
    
    Atualize a ID de credencial de registro de contêiner:
    
    ```groovy
    def dockerCredentialId = '<your-acr-credential-id>'
    ```

## <a name="create-the-job"></a>Criar o trabalho
1. Adicione um novo trabalho no tipo **Pipeline**.

1. Selecione **Pipeline** > **definição** > **script de Pipeline do SCM**.

1. Insira a URL do repositório SCM com seu &lt;repositório your bifurcado >.

1. Insira o caminho do script como `deploy/aks/Jenkinsfile`.

## <a name="run-the-job"></a>Executar o trabalho

1. Verifique se é possível executar o projeto com êxito no ambiente local. Faça assim: [Executar o projeto no computador local](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/README.md#run-it).

1. Execute o trabalho do Jenkins. Na primeira vez que você executar o trabalho, o Jenkins implanta o aplicativo todo no ambiente azul, que é o ambiente inativos do padrão. 

1. Para verificar se o trabalho foi executado, vá para essas URLs:
    - Ponto de extremidade público: `http://aks-todoapp<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Ponto de extremidade azul - `http://aks-todoapp-blue<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`
    - Ponto de extremidade verde - `http://aks-todoapp-green<your-dns-name-suffix>.<your-location>.cloudapp.azure.com`

O público e os pontos de extremidade do teste azul têm a mesma atualização, enquanto o ponto de extremidade verde mostra a imagem padrão do tomcat. 

Se você executar a compilação mais de uma vez, ele percorre as implantações de verdes e azuis. Em outras palavras, se o ambiente atual é azul, o trabalho implanta e testa o ambiente verde. Em seguida, se o testes são boas, o trabalho atualiza o ponto de extremidade público do aplicativo para rotear o tráfego para o ambiente verde.

## <a name="additional-information"></a>Informações adicionais

Para obter mais informações sobre a implantação de tempo de inatividade zero, consulte este [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/301-jenkins-aks-zero-downtime-deployment). 

## <a name="clean-up-resources"></a>Limpar recursos

Quando você não precisa mais os recursos criados neste tutorial, você pode excluí-los.

```bash
az group delete -y --no-wait -n <your-resource-group-name>
```

## <a name="troubleshooting"></a>solução de problemas

Se você encontrar bugs com os plug-ins do Jenkins, registre um problema no [JIRA do Jenkins](https://issues.jenkins-ci.org/) para o componente específico.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como implantar no AKS usando Jenkins e o padrão de implantação azul/verde. Para saber mais sobre o provedor do Jenkins do Azure, consulte o Jenkins no site do Azure.

> [!div class="nextstepaction"]
> [Jenkins no Azure](/azure/jenkins/)