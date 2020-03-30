---
title: Gráficos de Comando de Loja
description: Saiba como armazenar gráficos helm para seus aplicativos Kubernetes usando repositórios no Registro de Contêineres do Azure
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 6304486ac493e235ed74f26ab4be4f843ef52513
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131473"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Empurre e puxe os gráficos do Helm para um registro de contêineres do Azure

Para gerenciar e implanta rapidamente aplicativos para Kubernetes, é possível usar o [gerenciador de pacotes do Helm de software livre][helm]. Com helm, os pacotes de aplicativos são definidos como [gráficos,](https://helm.sh/docs/topics/charts/)que são coletados e armazenados em um [repositório de gráficos Helm](https://helm.sh/docs/topics/chart_repository/).

Este artigo mostra como hospedar repositórios de gráficos Helm em um registro de contêiner do Azure, usando uma instalação do Helm 3 ou do Helm 2. Em muitos cenários, você construiria e carregaria seus próprios gráficos para os aplicativos que você desenvolve. Para obter mais informações sobre como construir seus próprios gráficos Helm, consulte o [Guia do Desenvolvedor de Modelos de Gráficos][develop-helm-charts]. Você também pode armazenar um gráfico Helm existente de outro repo Helm.

> [!IMPORTANT]
> O suporte aos gráficos helm no Azure Container Registry está atualmente em pré-visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso][terms-of-use]suplementar . Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="helm-3-or-helm-2"></a>Leme 3 ou Helm 2?

Para armazenar, gerenciar e instalar gráficos Helm, você usa um cliente Helm e o Helm CLI. Os principais lançamentos do cliente Helm incluem Helm 3 e Helm 2. O Helm 3 suporta um novo formato de gráfico e não instala mais o componente do lado do servidor Tiller. Para obter detalhes sobre as diferenças de versão, consulte a [versão FAQ](https://helm.sh/docs/faq/). Se você já implantou anteriormente gráficos helm 2, consulte [Uifa migratória v2 para v3](https://helm.sh/docs/topics/v2_v3_migration/).

Você pode usar o Helm 3 ou o Helm 2 para hospedar gráficos Helm no Registro de Contêineres do Azure, com fluxos de trabalho específicos para cada versão:

* [Cliente Helm](#use-the-helm-3-client) 3 `helm chart` - use comandos no Helm CLI para gerenciar gráficos em seu registro como [artefatos OCI](container-registry-image-formats.md#oci-artifacts)
* [Cliente Helm 2](#use-the-helm-2-client) - use comandos [az acr helm][az-acr-helm] no Azure CLI para adicionar e gerenciar seu registro de contêineres como um repositório de gráficos Helm

### <a name="additional-information"></a>Informações adicionais

* Para a maioria dos cenários, recomendamos `helm chart` usar o fluxo de trabalho Helm 3 com comandos nativos para gerenciar gráficos como artefatos OCI.
* Você pode usar comandos e fluxo de trabalho [az acr leme az acr][az-acr-helm] legado com o cliente e gráficos helm 3. No entanto, certos `az acr helm list` comandos, como não são compatíveis com os gráficos do Helm 3.
* A partir do Helm 3, os comandos [az acr helm][az-acr-helm] são suportados principalmente para compatibilidade com o cliente Helm 2 e formato de gráfico. O desenvolvimento futuro desses comandos não está planejado no momento.

## <a name="use-the-helm-3-client"></a>Use o cliente Helm 3

### <a name="prerequisites"></a>Pré-requisitos

- **Um registro de contêiner do Azure** em sua assinatura do Azure. Se necessário, crie um registro usando o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
- **Versão do cliente Helm 3.1.0 ou posterior** - Corra `helm version` para encontrar sua versão atual. Para saber mais sobre como instalar e usar o Helm, consulte [Instalar o Helm][helm-install].
- **Um cluster Kubernetes** onde você instalará um gráfico Helm. Se necessário, crie [um cluster Azure Kubernetes Service][aks-quickstart]. 
- **Azure CLI versão 2.0.71** `az --version` ou posterior - Corra para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com **helm 3** você:

* Pode criar um ou mais repositórios Helm em um registro de contêiner do Azure
* Armazenar gráficos Helm 3 em um registro como [artefatos OCI](container-registry-image-formats.md#oci-artifacts). Atualmente, o suporte do Helm 3 para OCI é *experimental*.
* Autenticar com seu registro `helm registry login` usando o comando.
* Use `helm chart` comandos no Helm CLI para empurrar, puxar e gerenciar gráficos helm em um registro
* Use `helm install` para instalar gráficos em um cluster Kubernetes a partir de um cache de repositório local.

Veja as seções a seguir, por exemplo.

### <a name="enable-oci-support"></a>Habilite o suporte ao OCI

Defina a seguinte variável de ambiente para habilitar o suporte ao OCI no cliente Helm 3. Atualmente, esse suporte é experimental. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Criar um gráfico de amostras

Crie um gráfico de teste usando os seguintes comandos:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Como exemplo básico, altere `templates` o diretório para a pasta e primeiro exclua o conteúdo lá:

```console
rm -rf *
```

Na `templates` pasta, crie um `configmap.yaml` arquivo chamado com os seguintes conteúdos:

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
```

Para obter mais informações sobre como criar e executar este exemplo, consulte [Getting Started](https://helm.sh/docs/chart_template_guide/getting_started/) no Helm Docs.

### <a name="save-chart-to-local-registry-cache"></a>Salvar gráfico no cache de registro local

Mude o diretório `hello-world` para o subdiretório. Em seguida, execute `helm chart save` para salvar uma cópia do gráfico localmente e também crie um alias com o nome totalmente qualificado do registro (todas as minúsculas) e do repositório e tag de destino. 

No exemplo a seguir, o nome do registro de registro é *mycontainerregistry*, o repo de destino é *hello-world*, e a tag de gráfico de destino é *v1,* mas os valores substitutos para o seu ambiente:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Execute `helm chart list` para confirmar se você salvou os gráficos no cache do registro local. A saída é semelhante a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Autenticar com o registro

Execute `helm registry login` o comando no Helm 3 CLI para [autenticar com o registro](container-registry-authentication.md) usando credenciais apropriadas para o seu cenário.

Por exemplo, crie um principal de serviço de diretório ativo do Azure [com permissões de puxar e empurrar](container-registry-auth-service-principal.md#create-a-service-principal) (função AcrPush) para o registro. Em seguida, forneça `helm registry login`as credenciais principais do serviço para . O exemplo a seguir fornece a senha usando uma variável de ambiente:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Gráfico de impulso para registro de contêineres do Azure

Execute `helm chart push` o comando no Helm 3 CLI para empurrar o gráfico para o repositório de destino totalmente qualificado:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Depois de um empurrão bem sucedido, a saída é semelhante a:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Listar gráficos no repositório

Como acontece com imagens armazenadas em um registro de contêiner do Azure, você pode usar comandos [de repositório az acr][az-acr-repository] para mostrar os repositórios hospedando seus gráficos e marcar tags e manifestos. 

Por exemplo, execute [aacr repositório para][az-acr-repository-show] ver as propriedades do repo que você criou na etapa anterior:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

A saída é semelhante a:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Execute o comando [az acr repositório show-manifests][az-acr-repository-show-manifests] para ver detalhes do gráfico armazenado no repositório. Por exemplo: 

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

A saída, abreviada neste exemplo, `configMediaType` mostra `application/vnd.cncf.helm.config.v1+json`um de:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

### <a name="pull-chart-to-local-cache"></a>Puxar gráfico para cache local

Para instalar um gráfico Helm no Kubernetes, o gráfico deve estar no cache local. Neste exemplo, execute `helm chart remove` primeiro para remover o `mycontainerregistry.azurecr.io/helm/hello-world:v1`gráfico local existente chamado :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Execute `helm chart pull` para baixar o gráfico do registro de contêiner do Azure para o cache local:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Gráfico do Helm de Exportação

Para trabalhar ainda mais com o gráfico, `helm chart export`exporte-o para um diretório local usando . Por exemplo, exporte o `install` gráfico que você puxou para o diretório:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Para visualizar as informações do gráfico exportado `helm show chart` no repo, execute o comando no diretório onde você exportou o gráfico.

```console
cd install
helm show chart hello-world
```

Helm retorna informações detalhadas sobre a versão mais recente do seu gráfico, conforme mostrado na seguinte saída de amostra:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Instale o gráfico Helm

Execute `helm install` para instalar o gráfico Helm que você puxou para o cache local e exportou. Especifique um nome de lançamento `--generate-name` como *myhelmtest*ou passe o parâmetro. Por exemplo: 

```console
helm install myhelmtest ./hello-world
```

A saída após a instalação de gráfico bem-sucedida é semelhante a:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Para verificar a instalação, execute o `helm get manifest` comando. O comando retorna os dados `configmap.yaml` YAML em seu arquivo de modelo.

Execute `helm uninstall` para desinstalar a versão do gráfico em seu cluster:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Excluir um gráfico do Helm do repositório

Para excluir um gráfico do repositório, use o comando [az acr repositório delete.][az-acr-repository-delete] Execute o seguinte comando e confirme a operação quando solicitado:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Use o cliente Helm 2

### <a name="prerequisites"></a>Pré-requisitos

- **Um registro de contêiner do Azure** em sua assinatura do Azure. Se necessário, crie um registro usando o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
- **Versão de cliente do Helm 2.11.0 (não uma versão RC) ou posterior**: execute `helm version` para descobrir sua versão atual. Também é necessário um servidor do Helm (Tiller) inicializado dentro de um cluster Kubernetes. Se necessário, crie [um cluster Azure Kubernetes Service][aks-quickstart]. Para saber mais sobre como instalar e usar o Helm, consulte [Instalar o Helm][helm-install-v2].
- **CLI do Azure versão 2.0.46 ou posterior** – Execute `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com **helm 2** você:

* Configure seu registro de contêiner Azure como um *único* repositório de gráficos Helm. O Azure Container Registry gerencia a definição de índice à medida que você adiciona e remove gráficos no repositório.
* Autenticar com o registro de contêiner do Azure através da CLI do Azure, que atualiza automaticamente o cliente Helm com o URI de registro e credenciais. Você não precisa especificar manualmente essas informações de registro, para que as credenciais não sejam expostas no histórico de comandos.
* Use os comandos [de comando az acr helm][az-acr-helm] no Azure CLI para adicionar seu registro de contêiner Azure como um repositório de gráficos Helm e para empurrar e gerenciar gráficos. Estes comandos Azure CLI envolvem comandos de clientes Helm 2.
* Adicione o repositório de gráficos no registro de contêiner do Azure ao índice local de repo Helm, suportando pesquisa de gráficos.
* Use `helm install` para instalar gráficos em um cluster Kubernetes a partir de um cache de repositório local.

Veja as seções a seguir, por exemplo.

### <a name="add-repository-to-helm-client"></a>Adicionar repositório ao cliente Helm

Adicione o repositório do gráfico Helm do Azure Container Registry ao seu cliente Helm usando o [comando az acr helm repo add.][az-acr-helm-repo-add] Esse comando obtém um token de autenticação para seu Registro de Contêiner do Azure usado pelo cliente do Helm. O token de autenticação é válido por 3 horas. Semelhante ao `docker login`, é possível executar esse comando em futuras sessões da CLI para autenticar o cliente do Helm com o repositório de gráficos do Helm do Registro de Contêiner do Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Adicione um gráfico de exemplo ao repositório

Primeiro, crie um diretório local em *~/acr-helm,* em seguida, baixe o gráfico *estável/wordpress* existente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Digite `ls` para listar o gráfico baixado e observe a versão do Wordpress incluída no nome do arquivo. O comando `helm fetch stable/wordpress` não especificou uma versão específica, portanto, foi buscada a versão *mais recente*. Na saída de exemplo a seguir, o gráfico wordpress é a versão *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Empurre o gráfico para o repositório de gráficos Helm no Registro de Contêineres Do Azure usando o comando [az acr helm push][az-acr-helm-push] no Azure CLI. Especifique o nome do seu gráfico Helm baixado na etapa anterior, como *wordpress-8.1.0.tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Após alguns momentos, o Azure CLI informa que seu gráfico está salvo, como mostrado na saída de exemplo a seguir:

```output
{
  "saved": true
}
```

### <a name="list-charts-in-the-repository"></a>Listar gráficos no repositório

Para usar o gráfico carregado na etapa anterior, o índice local do repositório do Helm deve ser atualizado. É possível reindexar os repositórios no cliente do Helm ou usar a CLI do Azure para atualizar o índice do repositório. Cada vez que você adiciona um gráfico ao seu repositório, essa etapa deve ser concluída:

```azurecli
az acr helm repo add --name mycontainerregistry
```

Com um gráfico armazenado em seu repositório e o índice atualizado disponível localmente, é possível usar os comandos regulares do cliente do Helm para pesquisar ou instalar. Para ver todos os gráficos em seu `helm search` repositório, use o comando, fornecendo seu próprio nome do Registro de Contêiner do Azure:

```console
helm search mycontainerregistry
```

O gráfico do Wordpress enviado por push na etapa anterior é listado, conforme mostrado na saída de exemplo a seguir:

```output
NAME                  CHART VERSION    APP VERSION    DESCRIPTION
helmdocs/wordpress    8.1.0           5.3.2          Web publishing platform for building blogs and websites.
```

Também é possível listar os gráficos com a CLI do Azure usando [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list --name mycontainerregistry
```

### <a name="show-information-for-a-helm-chart"></a>Mostrar informações para um gráfico do Helm

Para exibir informações de um gráfico específico no `helm inspect` repo, você pode usar o comando.

```console
helm inspect mycontainerregistry/wordpress
```

Quando nenhum número de versão é fornecido, a versão *mais recente* é usada. O Helm retorna informações detalhadas sobre seu gráfico, conforme mostrado na saída de exemplo condensada a seguir:

```output
apiVersion: v1
appVersion: 5.3.2
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: http://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: Bitnami
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 8.1.0
[...]
```

Também é possível mostrar as informações de um gráfico com o comando [az acr helm show][az-acr-helm-show] da CLI do Azure. Novamente, a versão *mais recente* de um gráfico é retornada por padrão. Você pode `--version` anexar para listar uma versão específica de um gráfico, como *8.1.0*:

```azurecli
az acr helm show --name mycontainerregistry wordpress
```

### <a name="install-a-helm-chart-from-the-repository"></a>Instalar um gráfico do Helm do repositório

O gráfico Helm em seu repositório é instalado especificando o nome do repositório e o nome do gráfico. Use o cliente do Helm para instalar o gráfico do Wordpress:

```console
helm install mycontainerregistry/wordpress
```

> [!TIP]
> Se você efetuar push para seu repositório de gráficos do Helm do Registro de Contêiner do Azure e, posteriormente, retornar em uma nova sessão de CLI, seu cliente local do Helm precisará de um token de autenticação atualizado. Para obter um novo token de autenticação, use o comando [az acr helm repo add][az-acr-helm-repo-add].

As seguintes etapas são concluídas durante o processo de instalação:

- O cliente do Helm pesquisa o índice do repositório local.
- O gráfico correspondente é baixado do repositório do Registro de Contêiner do Azure.
- O gráfico é implantado usando o Tiller em seu cluster Kubernetes.

À medida que a instalação prossegue, siga as instruções na saída de comando para ver as URLs e credenciais worPress. Você também pode `kubectl get pods` executar o comando para ver os recursos do Kubernetes implantados através do gráfico Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Excluir um gráfico do Helm do repositório

Para excluir um gráfico do repositório, use o comando [az acr helm delete][az-acr-helm-delete]. Especifique o nome do gráfico, como *wordpress,* e a versão a excluir, como *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Se desejar excluir todas as versões do gráfico nomeado, deixe o parâmetro `--version` de fora.

O gráfico continua a ser devolvido `helm search`quando você executa . Novamente, o cliente do Helm não atualiza automaticamente a lista de gráficos disponíveis em um repositório. Para atualizar o índice do repositório do cliente do Helm, use o comando [az acr helm repo add][az-acr-helm-repo-add] novamente:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como criar e implantar gráficos do Helm, confira [Developing Helm charts][develop-helm-charts] (Desenvolvendo gráficos do Helm).
* Saiba mais sobre a instalação de aplicativos com o Helm no [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Os gráficos do Helm podem ser usados como parte do processo de build do contêiner. Para obter mais informações, consulte [Use Azure Container Registry Tasks][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-v2]: https://v2.helm.sh/docs/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
