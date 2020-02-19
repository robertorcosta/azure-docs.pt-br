---
title: Permissões para repositórios no registro de contêiner do Azure
description: Crie um token com permissões com escopo para repositórios específicos em um registro para efetuar pull ou enviar imagens por Push ou executar outras ações
ms.topic: article
ms.date: 02/13/2020
ms.openlocfilehash: 7d390bf4d97561e374c70f184534ac4f98a40611
ms.sourcegitcommit: 6e87ddc3cc961945c2269b4c0c6edd39ea6a5414
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77444260"
---
# <a name="create-a-token-with-repository-scoped-permissions"></a>Criar um token com permissões no escopo do repositório

Este artigo descreve como criar tokens e mapas de escopo para gerenciar permissões no escopo do repositório no registro de contêiner. Com a criação de tokens, um proprietário do registro pode fornecer aos usuários ou serviços com acesso limitado por tempo e limite aos repositórios para efetuar pull ou enviar por push imagens ou executar outras ações. Um token fornece permissões mais refinadas do que outras [Opções de autenticação](container-registry-authentication.md)de registro, que são permissões de escopo para um registro inteiro. 

Os cenários para criar um token incluem:

* Permitir que dispositivos IoT com tokens individuais recebam uma imagem de um repositório
* Fornecer uma organização externa com permissões para um repositório específico 
* Limite o acesso do repositório a diferentes grupos de usuários em sua organização. Por exemplo, forneça acesso de gravação e leitura aos desenvolvedores que criam imagens direcionadas a repositórios específicos e acesso de leitura às equipes que implantam esses repositórios.

> [!IMPORTANT]
> Esse recurso está na versão prévia no momento; algumas [limitações se aplicam](#preview-limitations). As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="preview-limitations"></a>Limitações de visualização

* Esse recurso só está disponível em um registro de contêiner **Premium** . Para obter informações sobre limites e camadas de serviço do registro, consulte [SKUs do registro de contêiner do Azure](container-registry-skus.md).
* Atualmente, não é possível atribuir permissões no escopo do repositório a uma identidade Azure Active Directory, como uma entidade de serviço ou uma identidade gerenciada.

## <a name="concepts"></a>Conceitos

Para configurar permissões no escopo do repositório, você cria um *token* com um *mapa de escopo*associado. 

* Um **token** junto com uma senha gerada permite que o usuário autentique com o registro. Você pode definir uma data de validade para uma senha de token ou desabilitar um token a qualquer momento.  

  Depois de autenticar com um token, o usuário ou serviço pode executar uma ou mais *ações* no escopo de um ou mais repositórios.

  |Ação  |DESCRIÇÃO  | Exemplo |
  |---------|---------|--------|
  |`content/delete`    | Remover dados do repositório  | Excluir um repositório ou um manifesto |
  |`content/read`     |  Ler dados do repositório |  Efetuar pull de um artefato |
  |`content/write`     |  Gravar dados no repositório     | Usar com `content/read` para enviar um artefato por push |
  |`metadata/read`    | Ler metadados do repositório   | Listar marcas ou manifestos |
  |`metadata/write`     |  Gravar metadados no repositório  | Habilitar ou desabilitar operações de leitura, gravação ou exclusão |

* Um **mapa de escopo** agrupa as permissões de repositório que você aplica a um token e pode reaplicar a outros tokens. Cada token é associado a um único mapa de escopo. 

   Com um mapa de escopo:

    * Configurar vários tokens com permissões idênticas para um conjunto de repositórios
    * Atualizar permissões de token ao adicionar ou remover ações de repositório no mapa de escopo ou aplicar um mapa de escopo diferente 

  O registro de contêiner do Azure também fornece vários mapas de escopo definidos pelo sistema que você pode aplicar, com permissões fixas em todos os repositórios.

A imagem a seguir mostra a relação entre tokens e mapas de escopo. 

![Tokens de registro e mapas de escopo](media/container-registry-repository-scoped-permissions/token-scope-map-concepts.png)

## <a name="prerequisites"></a>Prerequisites

* Os comandos **CLI do Azure** CLI do Azure para criar e gerenciar tokens estão disponíveis no CLI do Azure versão 2.0.76 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli).
* **Docker** – para autenticar com o registro para efetuar pull ou enviar imagens por push, você precisa de uma instalação local do Docker. O Docker fornece instruções de instalação para sistemas [macOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) e [Linux](https://docs.docker.com/engine/installation/#supported-platforms).
* **Registro de contêiner** -se você não tiver um, crie um registro de contêiner Premium em sua assinatura do Azure ou atualize um registro existente. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md). 

## <a name="create-token---cli"></a>Criar token-CLI

### <a name="create-token-and-specify-repositories"></a>Criar token e especificar repositórios

Crie um token usando o comando [AZ ACR token Create][az-acr-token-create] . Ao criar um token, você pode especificar um ou mais repositórios e ações associadas em cada repositório. Os repositórios não precisam estar no registro ainda. Para criar um token especificando um mapa de escopo existente, consulte a próxima seção.

O exemplo a seguir cria um token no registro *myregistry* com as seguintes permissões no repositório de `samples/hello-world`: `content/write` e `content/read`. Por padrão, o comando define o status do token padrão como `enabled`, mas você pode atualizar o status para `disabled` a qualquer momento.

```azurecli
az acr token create --name MyToken --registry myregistry \
  --repository samples/hello-world \
  content/write content/read
```

A saída mostra detalhes sobre o token, incluindo duas senhas geradas. É recomendável salvar as senhas em um local seguro para usar posteriormente para autenticação. As senhas não podem ser recuperadas novamente, mas novas podem ser geradas.

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

A saída inclui detalhes sobre o mapa de escopo que o comando criou. Você pode usar o mapa de escopo, aqui chamado `MyToken-scope-map`, para aplicar as mesmas ações de repositório a outros tokens. Ou atualize o mapa de escopo posteriormente para alterar as permissões dos tokens associados.

### <a name="create-token-and-specify-scope-map"></a>Criar token e especificar mapa de escopo

Uma maneira alternativa de criar um token é especificar um mapa de escopo existente. Se você ainda não tiver um mapa de escopo, primeiro crie um especificando repositórios e ações associadas. Em seguida, especifique o mapa de escopo ao criar um token. 

Para criar um mapa de escopo, use o comando [AZ ACR Scope-MAP Create][az-acr-scope-map-create] . O comando a seguir cria um mapa de escopo com as mesmas permissões no repositório de `samples/hello-world` usado anteriormente. 

```azurecli
az acr scope-map create --name MyScopeMap --registry myregistry \
  --repository samples/hello-world \
  content/write content/read \
  --description "Sample scope map"
```

Execute [AZ ACR token Create][az-acr-token-create] para criar um token, especificando o mapa de escopo *MyScopeMap* . Como no exemplo anterior, o comando define o status do token padrão como `enabled`.

```azurecli
az acr token create --name MyToken \
  --registry myregistry \
  --scope-map MyScopeMap
```

A saída mostra detalhes sobre o token, incluindo duas senhas geradas. É recomendável salvar as senhas em um local seguro para usar posteriormente para autenticação. As senhas não podem ser recuperadas novamente, mas novas podem ser geradas.

## <a name="create-token---portal"></a>Criar um token-Portal

Você pode usar o portal do Azure para criar tokens e mapas de escopo. Assim como ocorre com o comando `az acr token create` CLI, você pode aplicar um mapa de escopo existente ou criar um mapa de escopo ao criar um token especificando um ou mais repositórios e ações associadas. Os repositórios não precisam estar no registro ainda. 

O exemplo a seguir cria um token e cria um mapa de escopo com as seguintes permissões no repositório de `samples/hello-world`: `content/write` e `content/read`.

1. No portal, navegue até o registro de contêiner.
1. Em **Serviços**, selecione **tokens (versão prévia) > + Adicionar**.
  ![criar token no portal](media/container-registry-repository-scoped-permissions/portal-token-add.png)
1. Insira um nome de token.
1. Em **mapa de escopo**, selecione **criar novo**.
1. Configurar o mapa de escopo:
    1. Insira um nome e uma descrição para o mapa de escopo. 
    1. Em **repositórios**, insira `samples/hello-world`e, em **permissões**, selecione `content/read` e `content/write`. Em seguida, selecione **+ Adicionar**.  
    ![criar mapa de escopo no portal](media/container-registry-repository-scoped-permissions/portal-scope-map-add.png)

    1. Depois de adicionar repositórios e permissões, selecione **Adicionar** para adicionar o mapa de escopo.
1. Aceite o **status** de token padrão de **habilitado** e, em seguida, selecione **criar**.

Depois que o token for validado e criado, os detalhes do token serão exibidos na tela **tokens** .

### <a name="add-token-password"></a>Adicionar senha do token

Gere uma senha depois de criar um token. Para autenticar com o registro, o token deve ser habilitado e ter uma senha válida.

Você pode gerar uma ou duas senhas e definir uma data de expiração para cada uma. 

1. No portal, navegue até o registro de contêiner.
1. Em **Serviços**, selecione **tokens (versão prévia)** e selecione um token.
1. Nos detalhes do token, selecione **password1** ou **password2**e selecione o ícone gerar.
1. Na tela senha, defina opcionalmente uma data de validade para a senha e selecione **gerar**.
1. Depois de gerar uma senha, copie-a e salve-a em um local seguro. Não é possível recuperar uma senha gerada após fechar a tela, mas você pode gerar uma nova.

    ![Criar senha de token no portal](media/container-registry-repository-scoped-permissions/portal-token-password.png)

## <a name="authenticate-with-token"></a>Autenticar com token

Quando um usuário ou serviço usa um token para autenticar com o registro de destino, ele fornece o nome do token como um nome de usuário e uma de suas senhas geradas. O método de autenticação depende da ação configurada ou das ações associadas ao token.

|Ação  |Como autenticar  |
  |---------|---------|
  |`content/delete`    | `az acr repository delete` em CLI do Azure |
  |`content/read`     |  `docker login`<br/><br/>`az acr login` em CLI do Azure  |
  |`content/write`     |  `docker login`<br/><br/>`az acr login` em CLI do Azure     |
  |`metadata/read`    | `az acr repository show`<br/><br/>`az acr repository show-tags`<br/><br/>`az acr repository show-manifests` em CLI do Azure   |
  |`metadata/write`     |  `az acr repository untag`<br/><br/>`az acr repository update` em CLI do Azure |

## <a name="examples-use-token"></a>Exemplos: usar token

Os exemplos a seguir usam o token criado anteriormente neste artigo para executar operações comuns em um repositório: enviar por push e efetuar pull de imagens, excluir imagens e listar marcas de repositório. O token foi configurado inicialmente com permissões push (`content/write` e ações de `content/read`) no repositório `samples/hello-world`.

### <a name="pull-and-tag-test-images"></a>Imagens de teste de pull e marca

Para os exemplos a seguir, extraia as imagens `hello-world` e `alpine` do Hub do Docker e marque-as para o registro e o repositório.

```bash
docker pull hello-world
docker pull alpine
docker tag hello-world myregistry.azurecr.io/samples/hello-world:v1
docker tag hello-world myregistry.azurecr.io/samples/alpine:v1
```

### <a name="authenticate-using-token"></a>Autenticar usando token

Execute `docker login` para autenticar com o registro, forneça o nome do token como o nome de usuário e forneça uma de suas senhas. O token deve ter o status de `Enabled`.

O exemplo a seguir é formatado para o shell bash e fornece os valores usando variáveis de ambiente.

```bash
TOKEN_NAME=MyToken
TOKEN_PWD=<token password>

echo $TOKEN_PWD | docker login --username $TOKEN_NAME --password-stdin myregistry.azurecr.io
```

A saída deve mostrar a autenticação bem-sucedida:

```console
Login Succeeded
```

### <a name="push-images-to-registry"></a>Efetuar push de imagens para registro

Após o logon bem-sucedido, tente enviar por push as imagens marcadas para o registro. Como o token tem permissões para enviar imagens por push para o repositório de `samples/hello-world`, o push a seguir é executado com sucesso:

```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

O token não tem permissões para o repositório de `samples/alpine`, portanto, a seguinte tentativa de push falha com um erro semelhante a `requested access to the resource is denied`:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

### <a name="change-pushpull-permissions"></a>Alterar permissões de push/pull

Para atualizar as permissões de um token, atualize as permissões no mapa de escopo associado. O mapa de escopo atualizado é aplicado imediatamente a todos os tokens associados. 

Por exemplo, atualize `MyToken-scope-map` com `content/write` e `content/read` ações no repositório de `samples/alpine` e remova a ação `content/write` no repositório `samples/hello-world`.  

Para usar o CLI do Azure, execute [AZ ACR Scope-MAP Update][az-acr-scope-map-update] para atualizar o mapa de escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/write content/read \
  --remove samples/hello-world content/write 
```

No Portal do Azure:

1. Navegue até o registro de contêiner.
1. Em **Serviços**, selecione **mapas de escopo (versão prévia)** e selecione o mapa de escopo a ser atualizado.
1. Em **repositórios**, insira `samples/alpine`e, em **permissões**, selecione `content/read` e `content/write`. Em seguida, selecione **+ Adicionar**.
1. Em **repositórios**, selecione `samples/hello-world` e, em **permissões**, desmarque `content/write`. Em seguida, selecione **Salvar**.

Depois de atualizar o mapa de escopo, o push a seguir terá sucesso:

```bash
docker push myregistry.azurecr.io/samples/alpine:v1
```

Como o mapa de escopo tem apenas a permissão `content/read` no repositório `samples/hello-world`, uma tentativa de envio por push para o `samples/hello-world` repositório agora falhará:
 
```bash
docker push myregistry.azurecr.io/samples/hello-world:v1
```

A extração de imagens de ambos os repositórios é realizada com sucesso, porque o mapa de escopo fornece permissões de `content/read` em ambos os repositórios:

```bash
docker pull myregistry.azurecr.io/samples/alpine:v1
docker pull myregistry.azurecr.io/samples/hello-world:v1
```
### <a name="delete-images"></a>Excluir imagens

Atualize o mapa de escopo adicionando a ação de `content/delete` ao repositório de `alpine`. Essa ação permite a exclusão de imagens no repositório ou a exclusão de todo o repositório.

Para resumir, mostramos apenas o comando [AZ ACR Scope-MAP Update][az-acr-scope-map-update] para atualizar o mapa de escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/alpine content/delete
``` 

Para atualizar o mapa de escopo usando o portal, consulte a seção anterior.

Use o comando [AZ ACR Repository Delete][az-acr-repository-delete] a seguir para excluir o repositório `samples/alpine`. Para excluir imagens ou repositórios, o token não é autenticado por meio de `docker login`. Em vez disso, passe o nome e a senha do token para o comando. O exemplo a seguir usa as variáveis de ambiente criadas anteriormente neste artigo:

```azurecli
az acr repository delete \
  --name myregistry --repository samples/alpine \
  --username $TOKEN_NAME --password $TOKEN_PWD
```

### <a name="show-repo-tags"></a>Mostrar marcas do repositório 

Atualize o mapa de escopo adicionando a ação de `metadata/read` ao repositório de `hello-world`. Essa ação permite a leitura de dados de manifesto e marca no repositório.

Para resumir, mostramos apenas o comando [AZ ACR Scope-MAP Update][az-acr-scope-map-update] para atualizar o mapa de escopo:

```azurecli
az acr scope-map update \
  --name MyScopeMap \
  --registry myregistry \
  --add samples/hello-world metadata/read 
```  

Para atualizar o mapa de escopo usando o portal, consulte a seção anterior.

Para ler metadados no repositório de `samples/hello-world`, execute o comando [AZ ACR Repository show-manifestas][az-acr-repository-show-manifests] ou [AZ ACR Repository show-Tags][az-acr-repository-show-tags] . 

Para ler metadados, o token não é autenticado por meio de `docker login`. Em vez disso, passe o nome e a senha do token para o comando. O exemplo a seguir usa as variáveis de ambiente criadas anteriormente neste artigo:

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

### <a name="list-scope-maps"></a>Listar mapas de escopo

Use o comando [AZ ACR Scope-MAP List][az-acr-scope-map-list] ou a tela **mapas de escopo (versão prévia)** no portal para listar todos os mapas de escopo configurados em um registro. Por exemplo:

```azurecli
az acr scope-map list \
  --registry myregistry --output table
```

A saída mostra os mapas de escopo que você definiu e vários mapas de escopo definidos pelo sistema que você pode usar para configurar tokens:

```
NAME                 TYPE           CREATION DATE         DESCRIPTION
-------------------  -------------  --------------------  ------------------------------------------------------------
_repositories_admin  SystemDefined  2020-01-20T09:44:24Z  Can perform all read, write and delete operations on the ...
_repositories_pull   SystemDefined  2020-01-20T09:44:24Z  Can pull any repository of the registry
_repositories_push   SystemDefined  2020-01-20T09:44:24Z  Can push to any repository of the registry
MyScopeMap           UserDefined    2019-11-15T21:17:34Z  Sample scope map
```

### <a name="show-token-details"></a>Mostrar detalhes do token

Para exibir os detalhes de um token, como suas datas de status e de expiração de senha, execute o comando [AZ ACR token show][az-acr-token-show] ou selecione o token na tela **tokens (versão prévia)** no Portal. Por exemplo:

```azurecli
az acr scope-map show \
  --name MyScopeMap --registry myregistry
```

Use o comando [AZ ACR token List][az-acr-token-list] ou a tela **tokens (versão prévia)** no portal para listar todos os tokens configurados em um registro. Por exemplo:

```azurecli
az acr token list --registry myregistry --output table
```

### <a name="generate-passwords-for-token"></a>Gerar senhas para token

Se você não tiver uma senha de token ou se quiser gerar novas senhas, execute o comando [AZ ACR token Credential Generate][az-acr-token-credential-generate] . 

O exemplo a seguir gera um novo valor para password1 para o token *MyToken* , com um período de expiração de 30 dias. Ele armazena a senha na variável de ambiente `TOKEN_PWD`. Este exemplo é formatado para o shell bash.

```azurecli
TOKEN_PWD=$(az acr token credential generate \
  --name MyToken --registry myregistry --days 30 \
  --password1 --query 'passwords[0].value' --output tsv)
```

Para usar o portal do Azure para gerar uma senha de token, consulte as etapas em [criar token-portal](#create-token---portal) anteriormente neste artigo.

### <a name="update-token-with-new-scope-map"></a>Atualizar o token com o novo mapa de escopo

Se você quiser atualizar um token com um mapa de escopo diferente, execute [AZ ACR token Update][az-acr-token-update] e especifique o novo mapa de escopo. Por exemplo:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --scope-map MyNewScopeMap
```

No portal, na tela **tokens (versão prévia)** , selecione o token e, em **mapa de escopo**, selecione um mapa de escopo diferente.

> [!TIP]
> Depois de atualizar um token com um novo mapa de escopo, talvez você queira gerar novas senhas de token. Use o comando [AZ ACR token Credential Generate][az-acr-token-credential-generate] ou gere novamente uma senha de token no portal do Azure.

## <a name="disable-or-delete-token"></a>Desabilitar ou excluir token

Talvez seja necessário desabilitar temporariamente o uso das credenciais de token para um usuário ou serviço. 

Usando o CLI do Azure, execute o comando [AZ ACR token Update][az-acr-token-update] para definir o `status` como `disabled`:

```azurecli
az acr token update --name MyToken --registry myregistry \
  --status disabled
```

No portal, selecione o token na tela **tokens (versão prévia)** e selecione **desabilitado** em **status**.

Para excluir um token para invalidar permanentemente o acesso por qualquer pessoa usando suas credenciais, execute o comando [AZ ACR token Delete][az-acr-token-delete] . 

```azurecli
az acr token delete --name MyToken --registry myregistry
```

No portal, selecione o token na tela **tokens (versão prévia)** e selecione **descartar**.

## <a name="next-steps"></a>Próximas etapas

* Para gerenciar mapas e tokens de escopo, use comandos adicionais nos grupos de comando [AZ ACR Scope-MAP][az-acr-scope-map] e [AZ ACR token][az-acr-token] .
* Consulte a [visão geral de autenticação](container-registry-authentication.md) para outras opções para autenticar com um registro de contêiner do Azure, incluindo o uso de uma identidade de Azure Active Directory, uma entidade de serviço ou uma conta de administrador.


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
