---
title: Autenticação externa da tarefa ACR
description: Configure uma tarefa de registro de contêiner do Azure (ACR Task) para ler as credenciais do Docker Hub armazenadas em um cofre de chaves do Azure, usando uma identidade gerenciada para recursos do Azure.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842513"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Autenticação externa em uma tarefa ACR usando uma identidade gerenciada pelo Azure 

Em uma [tarefa ACR,](container-registry-tasks-overview.md)você pode [habilitar uma identidade gerenciada para os recursos do Azure](container-registry-tasks-authentication-managed-identity.md). A tarefa pode usar a identidade para acessar outros recursos do Azure, sem a necessidade de fornecer ou gerenciar credenciais. 

Neste artigo, você aprende como habilitar uma identidade gerenciada em uma tarefa que acessa segredos armazenados em um cofre de chaves do Azure. 

Para criar os recursos do Azure, este artigo exige que você execute a versão 2.0.68 do Azure CLI ou posterior. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli].

## <a name="scenario-overview"></a>Visão geral do cenário

A tarefa de exemplo lê as credenciais do Docker Hub armazenadas em um cofre de chaves do Azure. As credenciais são para uma conta do Docker Hub com permissões de gravação (push) para um repositório privado do Docker Hub. Para ler as credenciais, você configura a tarefa com uma identidade gerenciada e atribui permissões apropriadas a ela. A tarefa associada à identidade constrói uma imagem e entra no Docker Hub para empurrar a imagem para o repo privado. 

Este exemplo mostra etapas usando uma identidade gerenciada atribuída pelo usuário ou atribuída ao sistema. Sua escolha de identidade depende das necessidades da sua organização.

Em um cenário real, uma empresa pode publicar imagens para um repo privado no Docker Hub como parte de um processo de construção. 

## <a name="prerequisites"></a>Pré-requisitos

Você precisa de um registro de contêiner Do Zure no qual você executa a tarefa. Neste artigo, este registro é chamado *myregistry*. Substitua com seu próprio nome de registro em etapas posteriores.

Se você ainda não tiver um registro de contêiner Azure, consulte [Quickstart: Crie um registro de contêiner privado usando o Azure CLI](container-registry-get-started-azure-cli.md). Você não precisa empurrar imagens para o registro ainda.

Você também precisa de um repositório privado no Docker Hub e uma conta do Docker Hub com permissões para escrever no repo. Neste exemplo, este repo é chamado *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Crie um cofre de chaves e armazene segredos

Primeiro, se você precisar, crie um grupo de recursos chamado *myResourceGroup* no local *eastus* com o seguinte comando [de criação de grupo az:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Use o comando [az keyvault create][az-keyvault-create] para criar um cofre de chaves. Certifique-se de especificar um nome de cofre de chave exclusivo. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Armazene as credenciais necessárias do Docker Hub no cofre de chaves usando o comando [az keyvault secret set.][az-keyvault-secret-set] Nesses comandos, os valores são passados nas variáveis do ambiente:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

Em um cenário real, segredos provavelmente seriam definidos e mantidos em um processo separado.

## <a name="define-task-steps-in-yaml-file"></a>Definir etapas de tarefa no arquivo YAML

As etapas para esta tarefa de exemplo são definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md). Crie um `dockerhubtask.yaml` arquivo nomeado em um diretório de trabalho local e cole o seguinte conteúdo. Certifique-se de substituir o nome do cofre de chaves no arquivo pelo nome do seu cofre de chaves.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

As etapas da tarefa fazem o seguinte:

* Gerencie credenciais secretas para autenticar com o Docker Hub.
* Auteire com o Docker Hub `docker login` passando os segredos para o comando.
* Construa uma imagem usando um arquivo Docker de exemplo no repo [Azure-Samples/acr-tasks.](https://github.com/Azure-Samples/acr-tasks.git)
* Empurre a imagem para o repositório privado do Docker Hub.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Opção 1: Criar tarefa com identidade atribuída pelo usuário

As etapas desta seção criam uma tarefa e permitem uma identidade atribuída pelo usuário. Se você quiser habilitar uma identidade atribuída ao sistema, consulte [Opção 2: Criar tarefa com identidade atribuída ao sistema](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *dockerhubtask* executando o seguinte comando [de criação de tarefa az acr.][az-acr-task-create] A tarefa é executada sem um contexto de `dockerhubtask.yaml` código-fonte, e o comando faz referência ao arquivo no diretório de trabalho. O `--assign-identity` parâmetro passa o ID de recurso da identidade atribuída pelo usuário. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opção 2: Criar tarefa com identidade atribuída ao sistema

As etapas desta seção criam uma tarefa e permitem uma identidade atribuída ao sistema. Se você quiser habilitar uma identidade atribuída pelo usuário, consulte [Opção 1: Criar tarefa com identidade atribuída pelo usuário](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Criar tarefa

Crie a tarefa *dockerhubtask* executando o seguinte comando [de criação de tarefa az acr.][az-acr-task-create] A tarefa é executada sem um contexto de `dockerhubtask.yaml` código-fonte, e o comando faz referência ao arquivo no diretório de trabalho. O `--assign-identity` parâmetro sem valor permite a identidade atribuída ao sistema na tarefa.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Conceder acesso à identidade ao cofre-chave

Execute o seguinte comando [az keyvault set-policy][az-keyvault-set-policy] para definir uma política de acesso no cofre de chaves. O exemplo a seguir permite que a identidade leia segredos do cofre de chaves. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Execute manualmente a tarefa

Para verificar se a tarefa na qual você habilitou uma identidade gerenciada é executada com sucesso, acione manualmente a tarefa com o comando [az acr task run.][az-acr-task-run] O `--set` parâmetro é usado para passar o nome de repo privado para a tarefa. Neste exemplo, o nome de relo do espaço reservado é *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Quando a tarefa é executada com sucesso, a saída mostra autenticação bem-sucedida no Docker Hub e a imagem é construída e empurrada com sucesso para o repo privado:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Para confirmar se a imagem é empurrada, verifique a tag (neste`cf24` exemplo) no repo privado do Docker Hub.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [como ativar uma identidade gerenciada em uma tarefa ACR](container-registry-tasks-authentication-managed-identity.md).
* Consulte a [referência YAML de tarefas ACR](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
