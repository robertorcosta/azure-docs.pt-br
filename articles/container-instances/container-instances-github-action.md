---
title: Implantar instância de contêiner por ação do GitHub
description: Configure uma ação do GitHub que automatize etapas para construir, empurrar e implantar uma imagem de contêiner em Instâncias de Contêiner do Azure
ms.topic: article
ms.date: 03/18/2020
ms.custom: ''
ms.openlocfilehash: 13397cee8197afc65b93c587ae1505e59cfdebc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258032"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Configure uma ação do GitHub para criar uma instância de contêiner

[GitHub Actions](https://help.github.com/actions/getting-started-with-github-actions/about-github-actions) é um conjunto de recursos no GitHub para automatizar seus fluxos de trabalho de desenvolvimento de software no mesmo lugar em que você armazena código e colabora em solicitações e problemas de atração.

Use a ação [Deploy to Azure Container Instances](https://github.com/azure/aci-deploy) GitHub para automatizar a implantação de um contêiner em Instâncias de Contêiner do Azure. A ação permite definir propriedades para uma instância de contêiner semelhante àdo do comando [az container create.][az-container-create]

Este artigo mostra como configurar um fluxo de trabalho em um repo do GitHub que executa as seguintes ações:

* Criar uma imagem com base em um Dockerfile
* Empurre a imagem para um registro de contêiner Do Zure
* Implante a imagem do contêiner em uma instância de contêiner Azure

Este artigo mostra duas maneiras de configurar o fluxo de trabalho:

* Configure um fluxo de trabalho você mesmo em um repo do GitHub usando a ação Implantar para instâncias de contêiner do Azure e outras ações.  
* Use `az container app up` o comando na extensão [Deploy to Azure](https://github.com/Azure/deploy-to-azure-cli-extension) na CLI do Azure. Este comando simplifica a criação das etapas de fluxo de trabalho e implantação do GitHub.

> [!IMPORTANT]
> A ação do GitHub para instâncias de contêiner do Azure está atualmente em pré-visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="prerequisites"></a>Pré-requisitos

* **Conta do GitHub** - https://github.com Crie uma conta sobre se você ainda não tiver uma.
* **Azure CLI** - Você pode usar o Azure Cloud Shell ou uma instalação local da Cli Azure para completar as etapas do Azure CLI. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].
* **Registro de contêineres do Azure** - Se você não tiver um, crie um registro de contêiner Azure no nível Básico usando o [azure CLI](../container-registry/container-registry-get-started-azure-cli.md), [portal Azure](../container-registry/container-registry-get-started-portal.md)ou outros métodos. Tome nota do grupo de recursos usado para a implantação, que é usado para o fluxo de trabalho do GitHub.

## <a name="set-up-repo"></a>Configurar repo

* Para os exemplos deste artigo, use o GitHub para bifurcar o seguinte repositório:https://github.com/Azure-Samples/acr-build-helloworld-node

  Este repo contém um arquivo Docker e arquivos de origem para criar uma imagem de contêiner de um pequeno aplicativo web.

  ![Captura de tela do botão Fork (realçado) no GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Certifique-se de que as ações estão habilitadas para o seu repositório. Navegue até o repositório bifurcado e selecione **Ações de Configurações** > **Actions**. Em **Permissões de Ações,** **certifique-se de que as ações locais e de terceiros para este repositório** sejam selecionadas.

## <a name="configure-github-workflow"></a>Configurar o fluxo de trabalho do GitHub

### <a name="create-service-principal-for-azure-authentication"></a>Crie o principal de serviço para autenticação do Azure

No fluxo de trabalho do GitHub, você precisa fornecer credenciais do Azure para autenticar para o Azure CLI. O exemplo a seguir cria um principal de serviço com a função Contribuinte escopo para o grupo de recursos para o registro de contêineres.

Primeiro, obtenha o ID de recursos do seu grupo de recursos. Substitua o nome do seu grupo no seguinte comando [de show de grupo az:][az-acr-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Use [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar o principal de serviço:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

A saída é semelhante a:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Salve a saída JSON porque ela é usada em uma etapa posterior. Além disso, tome nota do `clientId`, que você precisa atualizar o principal de serviço na próxima seção.

### <a name="update-service-principal-for-registry-authentication"></a>Atualizar o principal do serviço para autenticação de registro

Atualize as principais credenciais do serviço Azure para permitir permissões de push e pull no registro do contêiner. Esta etapa permite que o fluxo de trabalho do GitHub use o principal do serviço para [autenticar com o registro do contêiner](../container-registry/container-registry-auth-service-principal.md). 

Obtenha o id de recurso do seu registro de contêineres. Substitua o nome do seu registro no seguinte comando [az acr show:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Use [a atribuição de função az para][az-role-assignment-create] atribuir a função AcrPush, que dá acesso push e pull ao registro. Substitua o ID do cliente do seu principal de serviço:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Salvar credenciais no repo do GitHub

1. Na UI do GitHub, navegue até o seu repositório bifurcado e selecione**Segredos** **de Configurações** > . 

1. Selecione **Adicionar um novo segredo** para adicionar os seguintes segredos:

|Segredo  |Valor  |
|---------|---------|
|`AZURE_CREDENTIALS`     | Toda a saída JSON da criação principal do serviço |
|`REGISTRY_LOGIN_SERVER`   | O nome do servidor de login do seu registro (todas as minúsculas). Exemplo: *myregistry.azure.cr.io*        |
|`REGISTRY_USERNAME`     |  A `clientId` saída do JSON da criação principal do serviço       |
|`REGISTRY_PASSWORD`     |  A `clientSecret` saída do JSON da criação principal do serviço |
| `RESOURCE_GROUP` | O nome do grupo de recursos que você usou para escopo do principal de serviço |

### <a name="create-workflow-file"></a>Criar arquivo de fluxo de trabalho

1. Na ui do GitHub, selecione **Ações** > **Novo fluxo de trabalho**.
1. Selecione **Configurar um fluxo de trabalho você mesmo**.
1. Em **Editar novo arquivo,** cole os seguintes conteúdos YAML para substituir o código de amostra. Aceite o nome `main.yml`de arquivo padrão ou forneça um nome de arquivo escolhido.
1. Selecione **Iniciar compromisso,** fornecer opcionalmente descrições curtas e estendidas do seu commit e selecionar **Comprometer novo arquivo**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@master
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Validar o fluxo de trabalho

Depois de comprometer o arquivo de fluxo de trabalho, o fluxo de trabalho é acionado. Para rever o progresso do fluxo de trabalho, navegue até **os fluxos** > **de trabalho de ações**. 

![Ver o progresso do fluxo de trabalho](./media/container-instances-github-action/github-action-progress.png)

Consulte [Gerenciar um fluxo de trabalho executado](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run) para obter informações sobre a visualização do status e dos resultados de cada etapa do seu fluxo de trabalho.

Quando o fluxo de trabalho for concluído, obtenha informações sobre a instância do contêiner chamada *aci-sampleapp* executando o comando [az container show.][az-container-show] Substitua o nome do seu grupo de recursos: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

A saída é semelhante a:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Depois que a instância for provisionada, navegue até o FQDN do contêiner no seu navegador para visualizar o aplicativo web em execução.

![Executando o aplicativo web no navegador](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Use o implante para extensão do Azure

Alternativamente, use a [extensão Implantar para o Azure](https://github.com/Azure/deploy-to-azure-cli-extension) na CLI do Azure para configurar o fluxo de trabalho. O `az container app up` comando na extensão leva parâmetros de entrada de você para configurar um fluxo de trabalho para implantar em Instâncias de Contêiner do Azure. 

O fluxo de trabalho criado pelo Azure CLI é semelhante ao fluxo de trabalho que você pode [criar manualmente usando o GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Pré-requisito adicional

Além dos [pré-requisitos](#prerequisites) e [configuração de repo](#set-up-repo) para este cenário, você precisa instalar a **extensão Deploy to Azure** para o Azure CLI.

Execute o comando [add de extensão az][az-extension-add] para instalar a extensão:

```azurecli
az extension add \
  --name deploy-to-azure
```

Para obter informações sobre como encontrar, instalar e gerenciar extensões, consulte [Usar extensões com o Azure CLI](/cli/azure/azure-cli-extensions-overview).

### <a name="run-az-container-app-up"></a>Execute `az container app up`

Para executar o comando [az container app up,][az-container-app-up] forneça no mínimo:

* O nome do seu registro de contêiner Azure, por exemplo, *myregistry*
* A URL para o seu repo GitHub, por exemplo,`https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Comando de exemplo:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Progresso do comando

* Quando solicitado, forneça suas credenciais do GitHub ou forneça um [Pat (Personal Access Token) (GitHub)](https://help.github.com/github/authenticating-to-github/creating-a-personal-access-token-for-the-command-line) que tenha *escopos de relo* e *usuário* para autenticar com seu registro. Se você fornecer credenciais do GitHub, o comando criará um PAT para você.

* O comando cria segredos de relo para o fluxo de trabalho:

  * Principais credenciais de serviço para o Azure CLI
  * Credenciais para acessar o registro de contêineres do Azure

* Depois que o comando compromete o arquivo de fluxo de trabalho no seu repo, o fluxo de trabalho é acionado. 

A saída é semelhante a:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

### <a name="validate-workflow"></a>Validar o fluxo de trabalho

O fluxo de trabalho implanta uma instância de contêiner Azure com o nome base do seu repo GitHub, neste caso, *acr-build-helloworld-node*. No seu navegador, você pode navegar até o link fornecido para visualizar o aplicativo web em execução. Se o aplicativo ouvir em uma porta diferente de 8080, especifique isso na URL.

Para visualizar o status do fluxo de trabalho e os resultados de cada etapa da ui do GitHub, consulte [Gerenciar uma execução de fluxo de trabalho](https://help.github.com/actions/configuring-and-managing-workflows/managing-a-workflow-run).

## <a name="clean-up-resources"></a>Limpar recursos

Pare a instância de contêiner com o comando [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Para excluir o grupo de recursos e todos os recursos nele, execute o comando [az group delete:][az-group-delete]

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Próximas etapas

Procure no [GitHub Marketplace](https://github.com/marketplace?type=actions) para obter mais ações para automatizar seu fluxo de trabalho de desenvolvimento


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
