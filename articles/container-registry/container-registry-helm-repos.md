---
title: Usar repositórios do Helm no Registro de Contêiner do Azure
description: Saiba como usar um repositório do Helm com o Registro de Contêiner do Azure para armazenar gráficos para seus aplicativos
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: 3f0aed1c97acc5dd5c9a9abe1f9171fd3886d83b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212562"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>Usar o Registro de Contêiner do Azure como um repositório do Helm para os gráficos de aplicativo

To quickly manage and deploy applications for Kubernetes, you can use the [open-source Helm package manager][helm]. Com o Helm, os aplicativos são definidos como *gráficos* armazenados em um repositório de gráficos do Helm. Esses gráficos definem configurações e dependências e podem ter controle de versão durante todo o ciclo de vida do aplicativo. O Registro de Contêiner do Azure pode ser usado no host para repositórios de gráficos do Helm.

Com o Registro de Contêiner do Azure, você tem um repositório de gráficos do Helm privado e seguro, que pode integrar-se aos pipelines de build ou a outros serviços do Azure. Os repositórios de gráficos do Helm no Registro de Contêiner do Azure incluem recursos de replicação geográfica para manter seus gráficos próximos a implantações e para redundância. Você só paga pelo armazenamento usado pelos gráficos e estão disponíveis em todas as faixas de preço do Registro de Contêiner do Azure.

Este artigo mostra como usar um repositório de gráficos do Helm armazenado no Registro de Contêiner do Azure.

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As versões prévias são disponibilizadas com a condição de que você concorde com os [termos de uso complementares][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="before-you-begin"></a>Antes de começar

Para concluir as etapas deste artigo, os pré-requisitos devem ser atendidos:

- **Registro de Contêiner do Azure** – Crie um registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md).
- **Versão de cliente do Helm 2.11.0 (não uma versão RC) ou posterior**: execute `helm version` para descobrir sua versão atual. Também é necessário um servidor do Helm (Tiller) inicializado dentro de um cluster Kubernetes. If needed, you can [create an Azure Kubernetes Service cluster][aks-quickstart]. For more information on how to install and upgrade Helm, see [Installing Helm][helm-install].
- **CLI do Azure versão 2.0.46 ou posterior** – Execute `az --version` para localizar a versão. Se você precisa instalar ou fazer upgrade, veja [Instalar a CLI do Azure][azure-cli-install].

## <a name="add-a-repository-to-helm-client"></a>Adicionar um repositório ao cliente do Helm

Um repositório do Helm é um servidor HTTP que pode armazenar gráficos do Helm. O Registro de Contêiner do Azure pode fornecer esse armazenamento para gráficos do Helm e gerenciar a definição do índice enquanto você adiciona e remove gráficos ao/do repositório.

Para adicionar seu Registro de Contêiner do Azure como um repositório de gráficos do Helm, use a CLI do Azure. Com essa abordagem, seu cliente do Helm é atualizado com o URI e as credenciais do repositório com respaldo do Registro de Contêiner do Azure. Não é necessário especificar manualmente essas informações do repositório, portanto, as credenciais não são expostas no histórico de comandos, por exemplo.

Se necessário, faça logon na CLI do Azure e siga os prompts:

```azurecli
az login
```

Configure the Azure CLI defaults with the name of your Azure Container Registry using the [az configure][az-configure] command. No exemplo a seguir, substitua `<acrName>` pelo nome do Registro:

```azurecli
az configure --defaults acr=<acrName>
```

Now add your Azure Container Registry Helm chart repository to your Helm client using the [az acr helm repo add][az-acr-helm-repo-add] command. Esse comando obtém um token de autenticação para seu Registro de Contêiner do Azure usado pelo cliente do Helm. O token de autenticação é válido por 1 hora. Semelhante ao `docker login`, é possível executar esse comando em futuras sessões da CLI para autenticar o cliente do Helm com o repositório de gráficos do Helm do Registro de Contêiner do Azure:

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>Adicionar um gráfico ao repositório

Neste artigo, vamos obter um gráfico do Helm existente do repositório *estável* do Helm. O repositório *estável* é um repositório público e curado que inclui gráficos comuns do aplicativo. Os mantenedores do pacote podem enviar seus gráficos ao repositório *estável*, da mesma maneira como o Hub do Docker oferece um Registro público para imagens de contêiner comuns. O gráfico baixado do repositório *estável* público pode ser enviado por push para seu repositório privado do Registro de Contêiner do Azure. Na maioria dos cenários, você criaria e faria upload dos seus próprios gráficos para os aplicativos desenvolvidos por você. For more information on how to build your own Helm charts, see [developing Helm charts][develop-helm-charts].

Primeiro, crie um diretório em *~/acr-helm*; em seguida, baixe o gráfico *estável/wordpress* existente:

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

Liste o gráfico baixado e anote a versão do Wordpress incluída no nome do arquivo. O comando `helm fetch stable/wordpress` não especificou uma versão específica, portanto, foi buscada a versão *mais recente*. All Helm charts include a version number in the filename that follows the [SemVer 2][semver2] standard. No exemplo de saída a seguir, o gráfico do Wordpress é a versão *2.1.10*:

```
$ ls

wordpress-2.1.10.tgz
```

Now push the chart to your Helm chart repository in Azure Container Registry using the Azure CLI [az acr helm push][az-acr-helm-push] command. Especifique o nome do gráfico do Helm baixado na etapa anterior, como *wordpress-2.1.10.tgz*:

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

Após alguns instantes, a CLI do Azure informa que o seu gráfico foi salvo, conforme mostrado na saída de exemplo a seguir:

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>Listar gráficos no repositório

O cliente do Helm mantém uma cópia armazenada em cache local do conteúdo dos repositórios remotos. As alterações em um repositório remoto não atualizam automaticamente a lista de gráficos disponíveis conhecidos localmente pelo cliente do Helm. Quando você pesquisa gráficos em repositórios, o Helm usa seu índice local armazenado em cache. Para usar o gráfico carregado na etapa anterior, o índice local do repositório do Helm deve ser atualizado. É possível reindexar os repositórios no cliente do Helm ou usar a CLI do Azure para atualizar o índice do repositório. Cada vez que você adiciona um gráfico ao seu repositório, essa etapa deve ser concluída:

```azurecli
az acr helm repo add
```

Com um gráfico armazenado em seu repositório e o índice atualizado disponível localmente, é possível usar os comandos regulares do cliente do Helm para pesquisar ou instalar. Para ver todos os gráficos em seu repositório, use `helm search <acrName>`. Forneça seu próprio nome de Registro de Contêiner do Azure:

```console
helm search <acrName>
```

O gráfico do Wordpress enviado por push na etapa anterior é listado, conforme mostrado na saída de exemplo a seguir:

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

You can also list the charts with the Azure CLI, using [az acr helm list][az-acr-helm-list]:

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Mostrar informações para um gráfico do Helm

Para exibir informações para um gráfico específico no repositório, é possível usar novamente o cliente regular do Helm. Para ver informações do gráfico denominado *wordpress*, use `helm inspect`.

```console
helm inspect <acrName>/wordpress
```

Quando nenhum número de versão é fornecido, a versão *mais recente* é usada. O Helm retorna informações detalhadas sobre seu gráfico, conforme mostrado na saída de exemplo condensada a seguir:

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
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
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

You can also show the information for a chart with the Azure CLI [az acr helm show][az-acr-helm-show] command. Novamente, a versão *mais recente* de um gráfico é retornada por padrão. É possível acrescentar `--version` para listar uma versão específica de um gráfico, como *2.1.10*:

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>Instalar um gráfico do Helm do repositório

O gráfico do Helm em seu repositório é instalado especificando o nome do repositório e, em seguida, o nome de gráfico. Use o cliente do Helm para instalar o gráfico do Wordpress:

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Se você efetuar push para seu repositório de gráficos do Helm do Registro de Contêiner do Azure e, posteriormente, retornar em uma nova sessão de CLI, seu cliente local do Helm precisará de um token de autenticação atualizado. To obtain a new authentication token, use the [az acr helm repo add][az-acr-helm-repo-add] command.

As seguintes etapas são concluídas durante o processo de instalação:

- O cliente do Helm pesquisa o índice do repositório local.
- O gráfico correspondente é baixado do repositório do Registro de Contêiner do Azure.
- O gráfico é implantado usando o Tiller em seu cluster Kubernetes.

A saída do exemplo condensado a seguir mostra os recursos do Kubernetes implantados por meio do gráfico do Helm:

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>Excluir um gráfico do Helm do repositório

To delete a chart from the repository, use the [az acr helm delete][az-acr-helm-delete] command. Especifique o nome do gráfico, como *wordpress*, e a versão a ser excluída, como *2.1.10*.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

Se desejar excluir todas as versões do gráfico nomeado, deixe o parâmetro `--version` de fora.

O gráfico continua sendo retornado em `helm search <acrName>`. Novamente, o cliente do Helm não atualiza automaticamente a lista de gráficos disponíveis em um repositório. To update the Helm client repo index, use the [az acr helm repo add][az-acr-helm-repo-add] command again:

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>Próximos passos

Este artigo usou um gráfico existente do Helm do repositório *estável* público. For more information on how to create and deploy Helm charts, see [Developing Helm charts][develop-helm-charts].

Os gráficos do Helm podem ser usados como parte do processo de build do contêiner. For more information, see [use Azure Container Registry Tasks][acr-tasks].

For more information on how to use and manage Azure Container Registry, see the [best practices][acr-bestpractices].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://helm.sh/docs/topics/charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
