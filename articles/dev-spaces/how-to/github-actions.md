---
title: GitHub Ações & Serviço Azure Kubernetes (visualização)
services: azure-dev-spaces
ms.date: 04/03/2020
ms.topic: conceptual
description: Revisar e testar alterações a partir de uma solicitação de tração diretamente no Azure Kubernetes Service usando gitHub actions e Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contêineres, GitHub Actions, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
manager: gwallace
ms.openlocfilehash: a83da0ef3958748831eb0eeda1aa5e91efa7ef2e
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637942"
---
# <a name="github-actions--azure-kubernetes-service-preview"></a>GitHub Ações & Serviço Azure Kubernetes (visualização)

O Azure Dev Spaces fornece um fluxo de trabalho usando o GitHub Actions que permite testar alterações de uma solicitação de tração diretamente no AKS antes que a solicitação de tração seja mesclada no ramo principal do seu repositório. Ter um aplicativo em execução para revisar as alterações de uma solicitação de puxar pode aumentar a confiança tanto do desenvolvedor quanto dos membros da equipe. Este aplicativo em execução também pode ajudar os membros da equipe, como, gerentes de produto e designers, a se tornarem parte do processo de revisão durante os estágios iniciais de desenvolvimento.

Neste guia, você aprenderá a:

* Configurar o Azure Dev Spaces em um cluster Kubernetes gerenciado no Azure.
* Implantar um aplicativo grande com vários microsserviços em um espaço de desenvolvimento.
* Configure ci/cd com ações do GitHub.
* Testar um único microsserviço em um espaço de desenvolvimento isolado dentro do contexto do aplicativo completo.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Caso não tenha uma assinatura do Azure, é possível criar uma [conta gratuita](https://azure.microsoft.com/free).
* A [CLI do Azure][azure-cli-installed] instalada.
* [Helm 3 instalado][helm-installed].
* Uma conta do GitHub com [o GitHub Actions ativado][github-actions-beta-signup].
* O [aplicativo de exemplo Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp/README.md) em execução em um cluster AKS.

## <a name="create-an-azure-container-registry"></a>Criar um Registro de Contêiner do Azure

Criar um ACR (Registro de Contêiner do Azure):

```azurecli
az acr create --resource-group MyResourceGroup --name <acrName> --sku Basic
```

> [!IMPORTANT]
> O nome do seu ACR deve ser único dentro do Azure e conter 5-50 caracteres alfanuméricos. Todas as letras que você usa devem ser minúsculas.

Salve o valor do servidor de *login* da saída porque ele é usado em uma etapa posterior.

## <a name="create-a-service-principal-for-authentication"></a>Crie um princípio de serviço para autenticação

Use [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] para criar um diretor de serviço. Por exemplo:

```azurecli
az ad sp create-for-rbac --sdk-auth --skip-assignment
```

Salve a saída JSON porque ela é usada em uma etapa posterior.

Use [az aks show][az-aks-show] para exibir o *ID* do seu cluster AKS:

```azurecli
az aks show -g MyResourceGroup -n MyAKS  --query id
```

Use [az acr show][az-acr-show] para exibir o *ID* do ACR:

```azurecli
az acr show --name <acrName> --query id
```

Use [a atribuição de função az para][az-role-assignment-create] dar ao *Contribuinte* acesso ao seu cluster AKS e *ao acrPush* acesso ao seu ACR.

```azurecli
az role assignment create --assignee <ClientId> --scope <AKSId> --role Contributor
az role assignment create --assignee <ClientId>  --scope <ACRId> --role AcrPush
```

> [!IMPORTANT]
> Você deve ser o proprietário do seu cluster AKS e ACR para dar ao seu serviço acesso principal a esses recursos.

## <a name="configure-your-github-action"></a>Configure sua ação do GitHub

> [!IMPORTANT]
> Você deve ter as Ações do GitHub habilitadas para o seu repositório. Para habilitar as ações do GitHub para o seu repositório, navegue até o repositório no GitHub, clique na guia Ações e opte por habilitar ações para este repositório.

Navegue até o repositório bifurcado e clique *em Configurações*. Clique em *Segredos* na barra lateral esquerda. Clique *em Adicionar um novo segredo* para adicionar cada novo segredo abaixo:

1. *AZURE_CREDENTIALS:* toda a saída da criação principal do serviço.
1. *RESOURCE_GROUP*: o grupo de recursos para o seu cluster AKS, que neste exemplo é *MyResourceGroup*.
1. *CLUSTER_NAME*: o nome do seu cluster AKS, que neste exemplo é *MyAKS*.
1. *CONTAINER_REGISTRY:* o *servidor de login* do ACR.
1. *HOST*: o hospedeiro do seu Espaço Dev, que toma a forma *<MASTER_SPACE>.<APP_NAME>.<>HOST_SUFFIX *, que neste exemplo é *dev.bikesharingweb.fedcab0987.eus.azds.io*.
1. *IMAGE_PULL_SECRET:* o nome do segredo que você deseja usar, por exemplo, *demo-secret*.
1. *MASTER_SPACE:* o nome do seu pai Dev Space, que neste exemplo é *dev*.
1. *REGISTRY_USERNAME*: o *clientId* da saída JSON da criação principal do serviço.
1. *REGISTRY_PASSWORD*: o *clientSecret* da saída JSON da criação principal do serviço.

> [!NOTE]
> Todos esses segredos são usados pela ação GitHub e são configurados em [.github/workflows/bikes.yml][github-action-yaml].

Opcionalmente, se você quiser atualizar o espaço mestre após a fusão do seu RP, adicione o segredo *GATEWAY_HOST,* que toma o formulário *<MASTER_SPACE>.gateway.<HOST_SUFFIX>*, que neste exemplo é *dev.gateway.fedcab0987.eus.azds.io*. Uma vez que você mescla suas alterações no ramo mestre em seu garfo, outra ação será executada para reconstruir e executar toda a sua aplicação no espaço de desenvolvimento mestre. Neste exemplo, o espaço mestre é *dev*. Esta ação está configurada em [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

Além disso, se você quiser que as mudanças em seu RP para executar em um espaço de neto, atualize os segredos *MASTER_SPACE* e *HOST.* Por exemplo, se a sua aplicação estiver sendo executada em *desenvolvimento* com um *dev/azureuser 1*do espaço infantil, para que o RP seja executado em um espaço infantil *de dev/azureuser1*:

* Atualize *MASTER_SPACE* ao espaço infantil que você deseja como espaço pai, neste exemplo *azureuser1*.
* Atualize *o HOST* para GRANDPARENT_SPACE<> APP_NAME APP_NAME *> <APP_NAME.<HOST_SUFFIX><, *neste *exemplo, dev.bikesharingweb.fedcab0987.eus.azds.io*.

## <a name="create-a-new-branch-for-code-changes"></a>Crie um novo ramo para alterações de código

Navegue `BikeSharingApp/` e crie um novo ramo chamado *bike-images*.

```cmd
cd dev-spaces/samples/BikeSharingApp/
git checkout -b bike-images
```

Editar [bikes/server.js][bikes-server-js] para remover as linhas 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

A seção agora deve parecer:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salve o arquivo `git add` `git commit` e use e para encenar suas alterações.

```cmd
git add Bikes/server.js 
git commit -m "Removing hard coded imageUrl from /bikes/:id route"
```

## <a name="push-your-changes"></a>Empurre suas mudanças

Use `git push` para empurrar seu novo ramo para o seu repositório bifurcado:

```cmd
git push origin bike-images
```

Depois que o push estiver concluído, navegue até o seu repositório bifurcado no GitHub para criar uma solicitação de tração com o ramo *principal* em seu repositório bifurcado como o ramo base em comparação com o ramo *de imagens de bicicleta.*

Depois que sua solicitação de pull for aberta, navegue até a guia *Ações.* Verifique se uma nova ação foi iniciada e está construindo o serviço *Bikes.*

## <a name="view-the-child-space-with-your-changes"></a>Veja o espaço infantil com suas alterações

Depois que a ação for concluída, você verá um comentário com uma URL no seu novo espaço infantil com base nas alterações na solicitação de puxar.

> [!div class="mx-imgBorder"]
> ![Url de ação do GitHub](../media/github-actions/github-action-url.png)

Navegue até o serviço *de compartilhamento de bicicletas,* abrindo a URL a partir do comentário. Selecione *Aurelia Briggs (cliente)* como usuário e selecione uma bicicleta para alugar. Verifique se você não vê mais a imagem de espaço reservado para a moto.

Se você mesclar suas alterações no ramo *mestre* em seu garfo, outra ação será executada para reconstruir e executar toda a sua aplicação no espaço de desenvolvimento pai. Neste exemplo, o espaço dos pais é *dev*. Esta ação está configurada em [.github/workflows/bikesharing.yml][github-action-bikesharing-yaml].

## <a name="clean-up-your-azure-resources"></a>Limpar os recursos do Azure

```azurecli
az group delete --name MyResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Próximas etapas

Saiba como o Azure Dev Spaces ajuda você a desenvolver aplicativos mais complexos em vários contêineres e como você pode simplificar o desenvolvimento colaborativo trabalhando com versões diferentes ou branches do seu código em diferentes espaços.

> [!div class="nextstepaction"]
> [Desenvolvimento em equipe com o Azure Dev Spaces][team-quickstart]

[azure-cli-installed]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[bikes-server-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L232-L233
[bike-sharing-gh]: https://github.com/Azure/dev-spaces/
[bike-sharing-values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
[github-actions-beta-signup]: https://github.com/features/actions
[github-action-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikes.yml
[github-action-bikesharing-yaml]: https://github.com/Azure/dev-spaces/blob/master/.github/workflows/bikesharing.yml
[helm-installed]: https://helm.sh/docs/intro/install/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[sp-acr]: ../../container-registry/container-registry-auth-service-principal.md
[sp-aks]: ../../aks/kubernetes-service-principal.md
[team-quickstart]: ../quickstart-team-development.md
