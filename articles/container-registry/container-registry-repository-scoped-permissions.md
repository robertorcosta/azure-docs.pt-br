---
title: Permissões para repositórios no Registro de Contêineres do Azure
description: Crie um token com permissões escopo para repositórios específicos em um registro para puxar ou empurrar imagens ou executar outras ações
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 9004c45401833d3070266055dd7eb99a2bb43bde
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618837"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Crie um token com permissões com escopo de repositório

Este artigo descreve como criar tokens e mapas de escopo para gerenciar permissões com escopo de repositório no registro de contêineres. Ao criar tokens, um proprietário de registro pode fornecer aos usuários ou serviços acesso limitado e escopo a repositórios para puxar ou empurrar imagens ou executar outras ações. Um token fornece mais permissões de grãos finos do que outras opções de [autenticação](container-registry-authentication.md)de registro, que escopo permissões para um registro inteiro. 

Os cenários para criar um token incluem:

* Permitir que dispositivos IoT com tokens individuais puxem uma imagem de um repositório
* Forneça a uma organização externa permissões para um repositório específico 
* Limitar o acesso ao repositório a diferentes grupos de usuários em sua organização. Por exemplo, forneça acesso à gravação e leitura a desenvolvedores que constroem imagens que visam repositórios específicos e leiam acesso a equipes que implantam a partir desses repositórios.

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="preview-limitations"></a>Limitações de visualização

* Esse recurso só está disponível em um registro de contêiner **Premium.** Para obter informações sobre os níveis e limites do serviço de registro, consulte [As SKUs de registro de contêineres do Azure](container-registry-skus.md).
* No momento, não é possível atribuir permissões com escopo de repositório a uma identidade do Azure Active Directory, como um principal de serviço ou uma identidade gerenciada.
* Você não pode criar um mapa de escopo em um registro habilitado para [acesso de tração anônimo](container-registry-faq.md#how-do-i-enable-anonymous-pull-access).

## <a name="concepts"></a>Conceitos

Para configurar permissões com escopo de repositório, crie um *token* com um *mapa de escopo*associado . 

* Um **token** juntamente com uma senha gerada permite que o usuário se autentique com o registro. Você pode definir uma data de validade para uma senha de token ou desativar um token a qualquer momento.  

  Depois de autenticar com um token, o usuário ou serviço pode executar uma ou mais *ações* escopo para um ou mais repositórios.

  |Ação  |Descrição  | Exemplo |
  |---------|---------|--------|
  |`content/delete`    | Remover dados do repositório  | Exclua um repositório ou um manifesto |
  |`content/read`     |  Leia dados do repositório |  Puxe um artefato |
  |`content/write`     |  Escreva dados para o repositório     | Use `content/read` com para empurrar um artefato |
  |`metadata/read`    | Leia metadados do repositório   | Liste tags ou manifestos |
  |`metadata/write`     |  Escreva metadados no repositório  | Habilite ou desative operações de leitura, gravação ou exclusão |

* Um **mapa de escopo** agrupa as permissões de repositório que você aplica a um token e pode se reaplicar a outros tokens. Cada token está associado a um único mapa de escopo. 

   Com um mapa de escopo:

    * Configure vários tokens com permissões idênticas a um conjunto de repositórios
    * Atualize as permissões de token quando você adicionar ou remover ações de repositório no mapa de escopo ou aplicar um mapa de escopo diferente 

  O Azure Container Registry também fornece vários mapas de escopo definidos pelo sistema que você pode aplicar, com permissões fixas em todos os repositórios.

A imagem a seguir mostra a relação entre tokens e mapas de escopo. 

![Tokens de registro e mapas de escopo](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Pré-requisitos

* Os comandos **Azure CLI** - Azure CLI para criar e gerenciar tokens estão disponíveis na versão 2.0.76 ou posterior do Azure CLI. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI](/cli/azure/install-azure-cli).
* **Docker** - Para autenticar com o registro para puxar ou empurrar imagens, você precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro de contêineres** - Se você não tiver um, crie um registro de contêiner Premium em sua assinatura do Azure ou atualize um registro existente. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Criar token - CLI

### <a name="create-token-and-specify-repositories"></a>Criar token e especificar repositórios

Crie um token usando o comando [az acr token create.][az-acr-token-create] Ao criar um token, você pode especificar um ou mais repositórios e ações associadas em cada repositório. Os repositórios ainda não precisam estar no registro. Para criar um token especificando um mapa de escopo existente, consulte a próxima seção.

O exemplo a seguir cria um token no *registro myregistry* `content/write` com `content/read`as seguintes permissões no `samples/hello-world` repo: e . Por padrão, o comando define o `enabled`status de token `disabled` padrão para , mas você pode atualizar o status a qualquer momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A saída mostra detalhes sobre o token, incluindo duas senhas geradas. Recomenda-se salvar as senhas em um lugar seguro para usar mais tarde para autenticação. As senhas não podem ser recuperadas novamente, mas novas podem ser geradas.

```console
{
  "creationDate": "2020-01-18T00:15:34.066221+00:00",
  "credentials": {
    "certificates": [],
    "passwords": [
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password1",
        "value": "uH54BxxxxK7KOxxxxRbr26dAs8JXxxxx"
      },
      {
        "creationTime": "2020-01-18T00:15:52.837651+00:00",
        "expiry": null,
        "name": "password2",
        "value": "kPX6Or/xxxxLXpqowxxxxkA0idwLtmxxxx"
      }
    ],
    "username": "MyToken"
  },
  "id": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/tokens/MyToken",
  "name": "MyToken",
  "objectId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myresourcegroup",
  "scopeMapId": "/subscriptions/xxxxxxxx-adbd-4cb4-c864-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.ContainerRegistry/registries/myregistry/scopeMaps/MyToken-scope-map",
  "status": "enabled",
  "type": "Microsoft.ContainerRegistry/registries/tokens"
```

A saída inclui detalhes sobre o mapa de escopo que o comando criou. Você pode usar o mapa `MyToken-scope-map`de escopo, aqui chamado, para aplicar as mesmas ações de repositório a outros tokens. Ou atualize o mapa de escopo mais tarde para alterar as permissões dos tokens associados.

### <a name="create-token-and-specify-scope-map"></a>Criar token e especificar mapa de escopo

Uma maneira alternativa de criar um token é especificar um mapa de escopo existente. Se você ainda não tiver um mapa de escopo, primeiro crie um especificando repositórios e ações associadas. Em seguida, especifique o mapa de escopo ao criar um token. 

Para criar um mapa de escopo, use o comando [az acr scope-map criar.][az-acr-scope-map-create] O comando a seguir cria um mapa `samples/hello-world` de escopo com as mesmas permissões no repositório usado anteriormente. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Executar [az acr token criar][az-acr-token-create] para criar um token, especificando o mapa do escopo *do MyScopeMap.* Como no exemplo anterior, o comando define `enabled`o status de token padrão para .

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A saída mostra detalhes sobre o token, incluindo duas senhas geradas. Recomenda-se salvar as senhas em um lugar seguro para usar mais tarde para autenticação. As senhas não podem ser recuperadas novamente, mas novas podem ser geradas.

## <a name="create-token---portal"></a>Criar token - portal

Você pode usar o portal Azure para criar tokens e mapas de escopo. Assim como `az acr token create` no comando CLI, você pode aplicar um mapa de escopo existente ou criar um mapa de escopo ao criar um token especificando um ou mais repositórios e ações associadas. Os repositórios ainda não precisam estar no registro. 

O exemplo a seguir cria um token e cria um `samples/hello-world` mapa de `content/write` escopo `content/read`com as seguintes permissões no repositório: e .

1. No portal, navegue até o seu registro de contêineres.
1. Em **Serviços,** selecione **Tokens (Preview) > +Add**.
  ![Criar token no portal](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Digite um nome de token.
1. Em **Escopo,** selecione **Criar novo**.
1. Configure o mapa de escopo:
    1. Digite um nome e descrição para o mapa de escopo. 
    1. Em **Repositórios,** `samples/hello-world`digite e em `content/write` **Permissões,** selecione `content/read` e . Em seguida, selecione **+Adicionar**.  
    ![Criar mapa de escopo no portal](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Depois de adicionar repositórios e permissões, **selecione Adicionar** para adicionar o mapa de escopo.
1. Aceite o **status** de token padrão de **Ativado** e, em seguida, selecione **Criar**.

Depois que o token é validado e criado, os detalhes do token aparecem na tela **Tokens.**

### <a name="add-token-password"></a>Adicionar senha de token

Gere uma senha depois de criar um token. Para autenticar com o registro, o token deve ser habilitado e ter uma senha válida.

Você pode gerar uma ou duas senhas e definir uma data de validade para cada uma. 

1. No portal, navegue até o seu registro de contêineres.
1. Em **Serviços,** selecione **Tokens (Preview)** e selecione um token.
1. Nos detalhes do token, selecione **senha1** ou **senha2**e selecione o ícone Gerar.
1. Na tela de senha, defina opcionalmente uma data de validade para a senha e selecione **Gerar**.
1. Depois de gerar uma senha, copie e salve-a em um local seguro. Você não pode recuperar uma senha gerada depois de fechar a tela, mas você pode gerar uma nova.

    ![Criar senha de token no portal](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Autenticar com token

Quando um usuário ou serviço usa um token para autenticar com o registro de destino, ele fornece o nome do token como um nome de usuário e uma de suas senhas geradas. O método de autenticação depende da ação configurada ou das ações associadas ao token.

|Ação  |Como autenticar  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete`no Azure CLI |
  |`content/read`     |  `docker login`<br/><br/>`az acr login`no Azure CLI  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login`no Azure CLI     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests`no Azure CLI   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update`no Azure CLI |

## <a name="examples-use-token"></a>Exemplos: Use token

Os exemplos a seguir usam o token criado anteriormente neste artigo para executar operações comuns em um repositório: empurrar e puxar imagens, excluir imagens e listar tags de repositório. O token foi configurado inicialmente`content/write` `content/read` com permissões `samples/hello-world` de push (e ações) no repositório.

### <a name="pull-and-tag-test-images"></a>Puxar e marcar imagens de teste

Para os exemplos a `hello-world` seguir, puxe as imagens do `alpine` Docker Hub e marque-as para o seu registro e repositório.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Autenticar usando token

Execute `docker login` para autenticar com o registro, forneça o nome do token como nome de usuário e forneça uma de suas senhas. O token deve `Enabled` ter o status.

O exemplo a seguir é formatado para o shell bash e fornece os valores usando variáveis de ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A saída deve mostrar autenticação bem-sucedida:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Após o login bem-sucedido, tente empurrar as imagens marcadas para o registro. Como o token tem permissões `samples/hello-world` para empurrar imagens para o repositório, o seguinte empurrão é bem sucedido:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

O token não tem permissões `samples/alpine` para o repo, então a seguinte `requested access to the resource is denied`tentativa de push falha com um erro semelhante a :

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Alterar permissões de pressão/puxar

Para atualizar as permissões de um token, atualize as permissões no mapa de escopo associado. O mapa de escopo atualizado é aplicado imediatamente a todos os tokens associados. 

Por exemplo, `MyToken-scope-map` `content/write` atualize `content/read` com `samples/alpine` e ações no repositório e remova a `content/write` ação no `samples/hello-world` repositório.  

Para usar o Azure CLI, execute [a atualização do mapa de escopo az acr][az-acr-scope-map-update] para atualizar o mapa do escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

No Portal do Azure:

1. Navegue até o registro do contêiner.
1. Em **Serviços,** selecione **Mapas de escopo (Visualização)** e selecione o mapa de escopo para atualizar.
1. Em **Repositórios,** `samples/alpine`digite e em `content/write` **Permissões,** selecione `content/read` e . Em seguida, selecione **+Adicionar**.
1. Em **Repositórios,** `samples/hello-world` selecione e em **Permissões, desmarque** `content/write`. Em seguida, **selecione Salvar**.

Depois de atualizar o mapa de escopo, o seguinte impulso é bem sucedido:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Como o mapa de `content/read` escopo `samples/hello-world` só tem a permissão no `samples/hello-world` repositório, uma tentativa de push para o repo agora falha:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

Puxar imagens de ambos os repositórios é bem sucedido, porque o mapa de escopo fornece `content/read` permissões em ambos os repositórios:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Excluir imagens

Atualize o mapa `content/delete` de escopo `alpine` adicionando a ação ao repositório. Esta ação permite a exclusão de imagens no repositório ou exclusão de todo o repositório.

Para a brevidade, mostramos apenas o comando [az acr scope-map update][az-acr-scope-map-update] para atualizar o mapa de escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Para atualizar o mapa de escopo usando o portal, consulte a seção anterior.

Use o seguinte comando [az acr repositório delete][az-acr-repository-delete] para excluir o `samples/alpine` repositório. Para excluir imagens ou repositórios, o token não autentica . `docker login` Em vez disso, passe o nome e a senha do token para o comando. O exemplo a seguir usa as variáveis de ambiente criadas anteriormente no artigo:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Mostrar tags de repo 

Atualize o mapa `metadata/read` de escopo `hello-world` adicionando a ação ao repositório. Esta ação permite a leitura de dados de manifesto e tag no repositório.

Para a brevidade, mostramos apenas o comando [az acr scope-map update][az-acr-scope-map-update] para atualizar o mapa de escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Para atualizar o mapa de escopo usando o portal, consulte a seção anterior.

Para ler metadados `samples/hello-world` no repositório, execute o comando [az acr repository show-manifests][az-acr-repository-show-manifests] ou [az acr repositório show-tags.][az-acr-repository-show-tags] 

Para ler metadados, o token não `docker login`autentica . Em vez disso, passe o nome e a senha do token para qualquer comando. O exemplo a seguir usa as variáveis de ambiente criadas anteriormente no artigo:

```azurecli
az acr repository show-tags \
  --name myregistry --repository samples/hello-world \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

Saída de exemplo:

```console
[
  "v1"
]
```
## <a name="manage-tokens-and-scope-maps"></a>Gerenciar tokens e mapas de escopo

### <a name="list-scope-maps"></a>Mapas de escopo de lista

Use o comando [az acr scope-map list,][az-acr-scope-map-list] ou a tela **'Visualização'** de mapas de escopo (Preview) no portal, para listar todos os mapas de escopo configurados em um registro. Por exemplo:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A saída mostra os mapas de escopo definidos e vários mapas de escopo definidos pelo sistema que você pode usar para configurar tokens:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Mostrar detalhes do token

Para visualizar os detalhes de um token, como suas datas de validade de status e senha, execute o comando [az acr token show][az-acr-token-show] ou selecione o token na tela **Tokens (Preview)** no portal. Por exemplo:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Use o comando [az acr token list][az-acr-token-list] ou a tela **Tokens (Preview)** no portal para listar todos os tokens configurados em um registro. Por exemplo:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Gerar senhas para token

Se você não tiver uma senha de token ou quiser gerar novas senhas, execute o comando [az acr token de gerar][az-acr-token-credential-generate] comando. 

O exemplo a seguir gera um novo valor para senha1 para o token *MyToken,* com um prazo de expiração de 30 dias. Ele armazena a senha `TOKEN_PWD`na variável ambiente . Este exemplo é formatado para a concha bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Para usar o portal Azure para gerar uma senha de token, consulte os passos em [Criar token - portal](#create-token---portal) anterior neste artigo.

### <a name="update-token-with-new-scope-map"></a>Atualizar token com novo mapa de escopo

Se você quiser atualizar um token com um mapa de escopo diferente, execute [a az acr token update][az-acr-token-update] e especifique o novo mapa de escopo. Por exemplo:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

No portal, na tela **Tokens (preview),** selecione o token e, em **escopo,** selecione um mapa de escopo diferente.

> [!TIP]
> Depois de atualizar um token com um novo mapa de escopo, você pode querer gerar novas senhas de token. Use o comando [az acr token gerar][az-acr-token-credential-generate] comando ou regenerar uma senha de token no portal Azure.

## <a name="disable-or-delete-token"></a>Desativar ou excluir token

Você pode precisar desativar temporariamente o uso das credenciais de token para um usuário ou serviço. 

Usando o Azure CLI, execute o comando [az acr token update][az-acr-token-update] para definir: `status` `disabled`

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

No portal, selecione o token na tela **Tokens (Preview)** e selecione **Disabled** em **Status**.

Para excluir um token para invalidar permanentemente o acesso por qualquer pessoa usando suas credenciais, execute o comando [az acr token delete.][az-acr-token-delete] 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

No portal, selecione o token na tela **Tokens (Preview)** e selecione **Descartar**.

## <a name="next-steps"></a>Próximas etapas

* Para gerenciar mapas de escopo e tokens, use comandos adicionais nos grupos [de comandos az acr scope-map][az-acr-scope-map] e [az acr token.][az-acr-token]
* Consulte a visão geral de [autenticação](container-registry-authentication.md) para outras opções para autenticar com um registro de contêiner do Azure, incluindo o uso de uma identidade do Azure Active Directory, um diretor de serviço ou uma conta de administrador.


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-repository]: /cli/azure/acr/repository/
[az-acr-repository-show-tags]: /cli/azure/acr/repository/#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository/#az-acr-repository-show-manifests
[az-acr-repository-delete]: /cli/azure/acr/repository/#az-acr-repository-delete
[az-acr-scope-map]: /cli/azure/acr/scope-map/
[az-acr-scope-map-create]: /cli/azure/acr/scope-map/#az-acr-scope-map-create
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-show
[az-acr-scope-map-show]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-scope-map-update]: /cli/azure/acr/scope-map/#az-acr-scope-map-update
[az-acr-scope-map-list]: /cli/azure/acr/scope-map/#az-acr-scope-map-list
[az-acr-token]: /cli/azure/acr/token/
[az-acr-token-show]: /cli/azure/acr/token/#az-acr-token-show
[az-acr-token-list]: /cli/azure/acr/token/#az-acr-token-list
[az-acr-token-delete]: /cli/azure/acr/token/#az-acr-token-delete
[az-acr-token-create]: /cli/azure/acr/token/#az-acr-token-create
[az-acr-token-update]: /cli/azure/acr/token/#az-acr-token-update
[az-acr-token-credential-generate]: /cli/azure/acr/token/credential/#az-acr-token-credential-generate
