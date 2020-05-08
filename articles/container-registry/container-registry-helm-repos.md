---
title: Armazenar gráficos do Helm
description: Saiba como armazenar gráficos Helm para seus aplicativos kubernetes usando repositórios no registro de contêiner do Azure
ms.topic: article
ms.date: 03/20/2020
ms.openlocfilehash: 04ba3aaf312188ab77c04a97ab960cf9b9af078f
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857618"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Enviar por push e efetuar pull de gráficos do Helm para um registro de contêiner do Azure

Para gerenciar e implanta rapidamente aplicativos para Kubernetes, é possível usar o [gerenciador de pacotes do Helm de software livre][helm]. Com o Helm, os pacotes de aplicativos são definidos como [gráficos](https://helm.sh/docs/topics/charts/), que são coletados e armazenados em um [repositório de gráfico do Helm](https://helm.sh/docs/topics/chart_repository/).

Este artigo mostra como hospedar repositórios de gráficos Helm em um registro de contêiner do Azure, usando uma instalação Helm 3 ou Helm 2. Em muitos cenários, você criaria e carregaria seus próprios gráficos para os aplicativos que você desenvolve. Para obter mais informações sobre como criar seus próprios gráficos do Helm, consulte o [Guia do desenvolvedor do modelo de gráfico][develop-helm-charts]. Você também pode armazenar um gráfico Helm existente de outro repositório do Helm.

> [!IMPORTANT]
> O suporte para gráficos Helm no registro de contêiner do Azure está atualmente em visualização. As visualizações são disponibilizadas para você sobre a condição de que você concorda com os [termos de uso][terms-of-use]complementares. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="helm-3-or-helm-2"></a>Helm 3 ou Helm 2?

Para armazenar, gerenciar e instalar gráficos do Helm, você usa um cliente do Helm e a CLI do Helm. As versões principais do cliente Helm incluem Helm 3 e Helm 2. O Helm 3 dá suporte a um novo formato de gráfico e não instala mais o componente do lado do servidor. Para obter detalhes sobre as diferenças de versão, consulte as [perguntas frequentes sobre a versão](https://helm.sh/docs/faq/). Se você tiver implantado anteriormente Helm 2 gráficos, consulte [migrando Helm v2 para v3](https://helm.sh/docs/topics/v2_v3_migration/).

Você pode usar o Helm 3 ou o Helm 2 para hospedar gráficos Helm no registro de contêiner do Azure, com fluxos de trabalho específicos de cada versão:

* [Helm 3](#use-the-helm-3-client) comandos de uso `helm chart` do cliente na CLI do Helm para gerenciar gráficos em seu registro como [artefatos de OCI](container-registry-image-formats.md#oci-artifacts)
* [Cliente Helm 2](#use-the-helm-2-client) -use os comandos [AZ acr Helm][az-acr-helm] no CLI do Azure para adicionar e gerenciar seu registro de contêiner como um repositório de gráfico de Helm

### <a name="additional-information"></a>Informações adicionais

* Para a maioria dos cenários, é recomendável usar o fluxo de `helm chart` trabalho Helm 3 com comandos nativos para gerenciar gráficos como artefatos de OCI.
* A partir de Helm 3, os comandos [AZ ACR Helm][az-acr-helm] têm suporte para compatibilidade com o cliente Helm 2 e o formato de gráfico. O desenvolvimento futuro desses comandos não está planejado no momento. Consulte o [roteiro do produto](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga).
* Gráficos Helm 2 não podem ser exibidos ou gerenciados usando o portal do Azure.

## <a name="use-the-helm-3-client"></a>Usar o cliente Helm 3

### <a name="prerequisites"></a>Pré-requisitos

- **Um registro de contêiner do Azure** em sua assinatura do Azure. Se necessário, crie um registro usando o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
- **Helm Client versão 3.1.0 ou posterior** -execute `helm version` para localizar sua versão atual. Para saber mais sobre como instalar e usar o Helm, consulte [Instalar o Helm][helm-install].
- **Um cluster kubernetes** em que você instalará um gráfico do Helm. Se necessário, crie um [cluster do serviço kubernetes do Azure][aks-quickstart]. 
- **CLI do Azure versão 2.0.71 ou posterior** -execute `az --version` para localizar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com o **Helm 3** , você:

* Pode criar um ou mais repositórios do Helm em um registro de contêiner do Azure
* Armazene gráficos Helm 3 em um registro como [artefatos de OCI](container-registry-image-formats.md#oci-artifacts). Atualmente, o suporte do Helm 3 para o OCI é *experimental*.
* Autentique com o registro usando o `helm registry login` comando.
* Usar `helm chart` comandos na CLI do Helm para enviar, extrair e gerenciar gráficos do Helm em um registro
* Use `helm install` para instalar gráficos em um cluster kubernetes de um cache de repositório local.

Consulte as seções a seguir para obter exemplos.

### <a name="enable-oci-support"></a>Habilitar o suporte a OCI

Defina a seguinte variável de ambiente para habilitar o suporte a OCI no cliente Helm 3. Atualmente, esse suporte é experimental. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

### <a name="create-a-sample-chart"></a>Criar um gráfico de exemplo

Crie um gráfico de teste usando os seguintes comandos:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Como um exemplo básico, altere o diretório para `templates` a pasta e primeiro exclua o conteúdo ali:

```console
cd hello-world/templates
rm -rf *
```

Na `templates` pasta, crie um arquivo chamado `configmap.yaml` com o seguinte conteúdo:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Para obter mais informações sobre como criar e executar este exemplo, consulte [introdução](https://helm.sh/docs/chart_template_guide/getting_started/) no docs Helm.

### <a name="save-chart-to-local-registry-cache"></a>Salvar gráfico no cache do Registro local

Altere o diretório para `hello-world` o subdiretório. Em seguida, `helm chart save` execute para salvar uma cópia do gráfico localmente e também criar um alias com o nome totalmente qualificado do registro (todas as letras minúsculas) e o repositório e a marca de destino. 

No exemplo a seguir, o nome do registro é *mycontainerregistry*, o repositório de destino é *Hello-World*e a marca do gráfico de destino é *v1*, mas valores substitutos para o seu ambiente:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Execute `helm chart list` para confirmar que você salvou os gráficos no cache do Registro local. A saída é semelhante a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

### <a name="authenticate-with-the-registry"></a>Autenticar com o registro

Execute o `helm registry login` comando na CLI do Helm 3 para [autenticar com o registro](container-registry-authentication.md) usando as credenciais apropriadas para seu cenário.

Por exemplo, crie uma [entidade de serviço Azure Active Directory com as permissões pull e Push](container-registry-auth-service-principal.md#create-a-service-principal) (função AcrPush) para o registro. Em seguida, forneça as credenciais da `helm registry login`entidade de serviço para. O exemplo a seguir fornece a senha usando uma variável de ambiente:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

### <a name="push-chart-to-azure-container-registry"></a>Enviar gráfico por push ao registro de contêiner do Azure

Execute o `helm chart push` comando na CLI do Helm 3 para enviar o gráfico por push para o repositório de destino totalmente qualificado:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Após um push bem-sucedido, a saída será semelhante a:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

### <a name="list-charts-in-the-repository"></a>Listar gráficos no repositório

Assim como acontece com as imagens armazenadas em um registro de contêiner do Azure, você pode usar os comandos [AZ ACR Repository][az-acr-repository] para mostrar os repositórios que hospedam seus gráficos e as marcas e os manifestos do gráfico. 

Por exemplo, execute [AZ ACR Repository show][az-acr-repository-show] para ver as propriedades do repositório que você criou na etapa anterior:

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

Execute o comando [AZ ACR Repository show-manifestas][az-acr-repository-show-manifests] para ver detalhes do gráfico armazenado no repositório. Por exemplo: 

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

A saída, abreviada neste exemplo, mostra um `configMediaType` de `application/vnd.cncf.helm.config.v1+json`:

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

### <a name="pull-chart-to-local-cache"></a>Gráfico de pull para cache local

Para instalar um gráfico do Helm no kubernetes, o gráfico deve estar no cache local. Neste exemplo, primeiro execute `helm chart remove` para remover o gráfico local existente chamado: `mycontainerregistry.azurecr.io/helm/hello-world:v1`

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Execute `helm chart pull` para baixar o gráfico do registro de contêiner do Azure para o cache local:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

### <a name="export-helm-chart"></a>Exportar gráfico Helm

Para trabalhar ainda mais com o gráfico, exporte-o para um `helm chart export`diretório local usando. Por exemplo, exporte o gráfico que você recebeu `install` para o diretório:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Para exibir informações do gráfico exportado no repositório, execute o `helm show chart` comando no diretório em que você exportou o gráfico.

```console
cd install
helm show chart hello-world
```

Helm retorna informações detalhadas sobre a versão mais recente do gráfico, conforme mostrado na seguinte saída de exemplo:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

### <a name="install-helm-chart"></a>Instalar o gráfico do Helm

Execute `helm install` para instalar o gráfico Helm que você recebeu no cache local e exportado. Especifique um nome de versão, como *myhelmtest*, ou passe `--generate-name` o parâmetro. Por exemplo: 

```console
helm install myhelmtest ./hello-world
```

A saída após a instalação bem-sucedida do gráfico é semelhante a:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Para verificar a instalação, execute o `helm get manifest` comando. O comando retorna os dados de YAML em `configmap.yaml` seu arquivo de modelo.

Execute `helm uninstall` para desinstalar a versão do gráfico em seu cluster:

```console
helm uninstall myhelmtest
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Excluir um gráfico do Helm do repositório

Para excluir um gráfico do repositório, use o comando [AZ ACR Repository Delete][az-acr-repository-delete] . Execute o comando a seguir e confirme a operação quando solicitado:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="use-the-helm-2-client"></a>Usar o cliente Helm 2

### <a name="prerequisites"></a>Pré-requisitos

- **Um registro de contêiner do Azure** em sua assinatura do Azure. Se necessário, crie um registro usando o [portal do Azure](container-registry-get-started-portal.md) ou o [CLI do Azure](container-registry-get-started-azure-cli.md).
- **Versão de cliente do Helm 2.11.0 (não uma versão RC) ou posterior**: execute `helm version` para descobrir sua versão atual. Também é necessário um servidor do Helm (Tiller) inicializado dentro de um cluster Kubernetes. Se necessário, crie um [cluster do serviço kubernetes do Azure][aks-quickstart]. Para saber mais sobre como instalar e usar o Helm, consulte [Instalar o Helm][helm-install-v2].
- **CLI do Azure versão 2.0.46 ou posterior** – Execute `az --version` para localizar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

### <a name="high-level-workflow"></a>Fluxo de trabalho de alto nível

Com o **Helm 2** , você:

* Configure o registro de contêiner do Azure como um *único* repositório de gráfico Helm. O registro de contêiner do Azure gerencia a definição de índice à medida que você adiciona e remove gráficos no repositório.
* Autentique com o registro de contêiner do Azure por meio do CLI do Azure, que então atualiza o cliente Helm automaticamente com o URI do registro e as credenciais. Você não precisa especificar manualmente essas informações de registro, portanto as credenciais não são expostas no histórico de comandos.
* Use os comandos [AZ ACR Helm][az-acr-helm] no CLI do Azure para adicionar o registro de contêiner do Azure como um repositório de gráfico do Helm e para enviar e gerenciar gráficos. Esses comandos CLI do Azure encapsulam comandos de cliente do Helm 2.
* Adicione o repositório de gráficos ao seu registro de contêiner do Azure ao seu índice de repositório Helm local, suporte à pesquisa de gráfico.
* Use `helm install` para instalar gráficos em um cluster kubernetes de um cache de repositório local.

Consulte as seções a seguir para obter exemplos.

### <a name="add-repository-to-helm-client"></a>Adicionar repositório ao cliente do Helm

Adicione o repositório de gráfico Helm do registro de contêiner do Azure ao cliente Helm usando o comando [AZ ACR Helm Repository Add][az-acr-helm-repo-add] . Esse comando obtém um token de autenticação para seu Registro de Contêiner do Azure usado pelo cliente do Helm. O token de autenticação é válido por 3 horas. Semelhante ao `docker login`, é possível executar esse comando em futuras sessões da CLI para autenticar o cliente do Helm com o repositório de gráficos do Helm do Registro de Contêiner do Azure:

```azurecli
az acr helm repo add --name mycontainerregistry
```

### <a name="add-a-sample-chart-to-the-repository"></a>Adicionar um gráfico de exemplo ao repositório

Primeiro, crie um diretório local em *~/ACR-Helm*e baixe o gráfico *estável/WordPress* existente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm repo update
helm fetch stable/wordpress
```

Digite `ls` para listar o gráfico baixado e anote a versão do WordPress incluída no nome do arquivo. O comando `helm fetch stable/wordpress` não especificou uma versão específica, portanto, foi buscada a versão *mais recente*. Na saída de exemplo a seguir, o gráfico do WordPress é a versão *8.1.0*:

```output
wordpress-8.1.0.tgz
```

Envie o gráfico por push ao seu repositório de gráficos do Helm no registro de contêiner do Azure usando o comando [AZ ACR Helm Push][az-acr-helm-push] no CLI do Azure. Especifique o nome do seu gráfico de Helm baixado na etapa anterior, como *WordPress-8.1.0. tgz*:

```azurecli
az acr helm push --name mycontainerregistry wordpress-8.1.0.tgz
```

Após alguns instantes, a CLI do Azure relata que o gráfico foi salvo, conforme mostrado na seguinte saída de exemplo:

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

Com um gráfico armazenado em seu repositório e o índice atualizado disponível localmente, é possível usar os comandos regulares do cliente do Helm para pesquisar ou instalar. Para ver todos os gráficos em seu repositório, use o `helm search` comando, fornecendo seu próprio nome de registro de contêiner do Azure:

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

Para exibir informações de um gráfico específico no repositório, você pode usar o `helm inspect` comando.

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

Também é possível mostrar as informações de um gráfico com o comando [az acr helm show][az-acr-helm-show] da CLI do Azure. Novamente, a versão *mais recente* de um gráfico é retornada por padrão. Você pode acrescentar `--version` para listar uma versão específica de um gráfico, como *8.1.0*:

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

Conforme a instalação continuar, siga as instruções na saída do comando para ver as URLs e as credenciais do WorPress. Você também pode executar o `kubectl get pods` comando para ver os recursos de kubernetes implantados por meio do gráfico Helm:

```output
NAME                                    READY   STATUS    RESTARTS   AGE
wordpress-1598530621-67c77b6d86-7ldv4   1/1     Running   0          2m48s
wordpress-1598530621-mariadb-0          1/1     Running   0          2m48s
[...]
```

### <a name="delete-a-helm-chart-from-the-repository"></a>Excluir um gráfico do Helm do repositório

Para excluir um gráfico do repositório, use o comando [az acr helm delete][az-acr-helm-delete]. Especifique o nome do gráfico, como *WordPress*, e a versão a ser excluída, como *8.1.0*.

```azurecli
az acr helm delete --name mycontainerregistry wordpress --version 8.1.0
```

Se desejar excluir todas as versões do gráfico nomeado, deixe o parâmetro `--version` de fora.

O gráfico continua a ser retornado quando você executa `helm search`. Novamente, o cliente do Helm não atualiza automaticamente a lista de gráficos disponíveis em um repositório. Para atualizar o índice do repositório do cliente do Helm, use o comando [az acr helm repo add][az-acr-helm-repo-add] novamente:

```azurecli
az acr helm repo add --name mycontainerregistry
```

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como criar e implantar gráficos do Helm, confira [Developing Helm charts][develop-helm-charts] (Desenvolvendo gráficos do Helm).
* Saiba mais sobre como instalar aplicativos com o Helm no [AKs (serviço kubernetes do Azure)](../aks/kubernetes-helm.md).
* Os gráficos do Helm podem ser usados como parte do processo de build do contêiner. Para obter mais informações, consulte [usar tarefas do registro de contêiner do Azure][acr-tasks].

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
