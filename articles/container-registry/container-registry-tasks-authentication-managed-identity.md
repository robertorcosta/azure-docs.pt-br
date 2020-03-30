---
title: Identidade gerenciada na tarefa ACR
description: Habilite uma identidade gerenciada para recursos do Azure em uma tarefa de Registro de Contêineres do Azure para permitir que a tarefa acesse outros recursos do Azure, incluindo outros registros privados de contêineres.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111773"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Use uma identidade gerenciada pelo Azure em tarefas ACR 

Habilite uma [identidade gerenciada para recursos do Azure](../active-directory/managed-identities-azure-resources/overview.md) em uma [tarefa ACR,](container-registry-tasks-overview.md)para que a tarefa possa acessar outros recursos do Azure, sem a necessidade de fornecer ou gerenciar credenciais. Por exemplo, use uma identidade gerenciada para permitir que uma etapa de tarefa puxe ou empurre imagens de contêiner para outro registro.

Neste artigo, você aprende a usar o Azure CLI para habilitar uma identidade gerenciada atribuída pelo usuário ou atribuída ao sistema em uma tarefa ACR. Você pode usar o Azure Cloud Shell ou uma instalação local do Azure CLI. Se você quiser usá-lo localmente, a versão 2.0.68 ou posterior é necessária. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

Para fins de ilustração, os comandos de exemplo neste artigo usam a [tarefa az acr criar][az-acr-task-create] para criar uma tarefa básica de compilação de imagem que permite uma identidade gerenciada. Para que os cenários de exemplo acessem recursos protegidos de uma tarefa ACR usando uma identidade gerenciada, consulte:

* [Autenticação entre registros](container-registry-tasks-cross-registry-authentication.md)
* [Acesse recursos externos com segredos armazenados no Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Por que usar uma identidade gerenciada?

Uma identidade gerenciada para recursos do Azure fornece serviços Selecionados do Azure com uma identidade gerenciada automaticamente no Azure Active Directory. Você pode configurar uma tarefa ACR com uma identidade gerenciada para que a tarefa possa acessar outros recursos protegidos do Azure, sem passar credenciais nas etapas da tarefa.

Identidades gerenciadas são de dois tipos:

* *Identidades atribuídas pelo usuário,* que você pode atribuir a vários recursos e persistir pelo tempo que quiser. As identidades atribuídas pelo usuário estão atualmente em pré-visualização.

* Uma *identidade atribuída ao sistema,* que é exclusiva de um recurso específico, como uma tarefa de ACR, e dura a vida útil desse recurso.

Você pode habilitar ambos os tipos de identidade em uma tarefa ACR. Conceda o acesso à identidade a outro recurso, como qualquer diretor de segurança. Quando a tarefa é executada, ela usa a identidade para acessar o recurso em quaisquer etapas de tarefa que exijam acesso.

## <a name="steps-to-use-a-managed-identity"></a>Etapas para usar uma identidade gerenciada

Siga essas etapas de alto nível para usar uma identidade gerenciada com uma tarefa ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Opcional) Criar uma identidade atribuída pelo usuário

Se você planeja usar uma identidade atribuída pelo usuário, use uma identidade existente ou crie a identidade usando o Azure CLI ou outras ferramentas do Azure. Por exemplo, use o comando [az identity create.][az-identity-create] 

Se você planeja usar apenas uma identidade atribuída ao sistema, pule esta etapa. Você cria uma identidade atribuída ao sistema quando cria a tarefa ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Habilite a identidade em uma tarefa ACR

Quando você cria uma tarefa ACR, habilite opcionalmente uma identidade atribuída pelo usuário, uma identidade atribuída ao sistema ou ambos. Por exemplo, `--assign-identity` passe o parâmetro quando executar o comando [az acr task create][az-acr-task-create] no Azure CLI.

Para habilitar uma identidade atribuída `--assign-identity` ao sistema, passe sem valor ou `assign-identity [system]`. O comando de exemplo a seguir cria uma tarefa Linux a `hello-world` partir de um repositório público do GitHub que constrói a imagem e permite uma identidade gerenciada atribuída pelo sistema:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Para habilitar uma identidade atribuída `--assign-identity` pelo usuário, passe com um valor do *ID* de recurso da identidade. O comando de exemplo a seguir cria uma tarefa Linux a `hello-world` partir de um repositório público do GitHub que constrói a imagem e permite uma identidade gerenciada atribuída pelo usuário:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Você pode obter o ID de recurso da identidade executando o comando [az identity show.][az-identity-show] O ID de recurso do ID *myUserAssignedIdentity* no grupo de recursos *myResourceGroup* é do formulário: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Conceda as permissões de identidade para acessar outros recursos do Azure

Dependendo dos requisitos de sua tarefa, conceda as permissões de identidade para acessar outros recursos do Azure. Os exemplos incluem:

* Atribuir à identidade gerenciada uma função com puxar, empurrar e puxar, ou outras permissões para um registro de contêiner de destino no Azure. Para obter uma lista completa de funções de registro, consulte [as funções e permissões do Registro de Contêineres do Azure](container-registry-roles.md). 
* Atribuir à identidade gerenciada um papel para ler segredos em um cofre de chaves do Azure.

Use a CLI do [Azure](../role-based-access-control/role-assignments-cli.md) ou outras ferramentas do Azure para gerenciar o acesso baseado em função aos recursos. Por exemplo, execute o [comando a az role assignment create][az-role-assignment-create] para atribuir à identidade uma função ao recurso. 

O exemplo a seguir atribui a uma identidade gerenciada as permissões para retirar de um registro de contêiner. O comando especifica o *ID principal* da identidade da tarefa e o *ID* de recurso do registro de destino.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Opcional) Adicionar credenciais à tarefa

Se sua tarefa precisar de credenciais para puxar ou empurrar imagens para outro registro personalizado ou para acessar outros recursos, adicione credenciais à tarefa. Execute o comando [az acr task task add][az-acr-task-credential-add] command `--use-identity` para adicionar credenciais e passe o parâmetro para indicar que a identidade pode acessar as credenciais. 

Por exemplo, para adicionar credenciais para uma identidade atribuída ao sistema para autenticar `use-identity [system]`com o registro de *destino*do contêiner Azure, passe:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Para adicionar credenciais para uma identidade atribuída pelo usuário para `use-identity` autenticar com o registro de *destino*do registro, passe com um valor do *ID* do cliente da identidade. Por exemplo: 

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Você pode obter a identificação do cliente da identidade executando o comando [az identity show.][az-identity-show] O ID do cliente é `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`um GUID do formulário .

### <a name="5-run-the-task"></a>5. Execute a tarefa

Depois de configurar uma tarefa com uma identidade gerenciada, execute a tarefa. Por exemplo, para testar uma das tarefas criadas neste artigo, acione-a manualmente usando o comando [az acr task run.][az-acr-task-run] Se você configurou gatilhos de tarefas adicionais e automatizados, a tarefa será executada quando acionada automaticamente.

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você aprendeu como habilitar e usar uma identidade gerenciada atribuída pelo usuário ou atribuída ao sistema em uma tarefa ACR. Para que os cenários acessem recursos protegidos de uma tarefa ACR usando uma identidade gerenciada, consulte:

* [Autenticação entre registros](container-registry-tasks-cross-registry-authentication.md)
* [Acesse recursos externos com segredos armazenados no Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
