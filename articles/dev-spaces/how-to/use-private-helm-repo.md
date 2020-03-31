---
title: Como usar um repositório particular helm em Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/22/2019
ms.topic: conceptual
description: Use um repositório de Helm privado em um Espaço Azure Dev.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, contêineres, Helm
manager: gwallace
ms.openlocfilehash: c8f0e463bc78d278d8162f8389664dbb46a83301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240462"
---
# <a name="use-a-private-helm-repository-in-azure-dev-spaces"></a>Use um repositório privado helm em Azure Dev Spaces

[Helm][helm] é um gerente de pacotes da Kubernetes. Helm usa um formato [de gráfico][helm-chart] para empacotar dependências. Os gráficos de leme são armazenados em um repositório, que pode ser público ou privado. O Azure Dev Spaces só recupera gráficos helm de repositórios públicos ao executar seu aplicativo. Nos casos em que o repositório Helm é privado ou o Azure Dev Spaces não pode acessá-lo, você pode adicionar um gráfico desse repositório diretamente ao seu aplicativo. A adição do gráfico permite diretamente que o Azure Dev Spaces execute seu aplicativo sem ter que acessar o repositório privado helm.

## <a name="add-the-private-helm-repository-to-your-local-machine"></a>Adicione o repositório privado Helm à sua máquina local

Use [o repo helm add][helm-repo-add] e a atualização do [repo helm][helm-repo-update] para acessar o repositório privado Helm da sua máquina local.

```cmd
helm repo add privateRepoName http://example.com/helm/v1/repo --username user --password 5tr0ng_P@ssw0rd!
helm repo update
```

## <a name="add-the-chart-to-your-application"></a>Adicione o gráfico ao seu aplicativo

Navegue até o diretório do `azds prep`seu projeto e execute.

```cmd
azds prep --enable-ingress
```

> [!TIP]
> O comando `prep` tenta gerar [um gráfico do Dockerfile e do Helm](../how-dev-spaces-works-prep.md#prepare-your-code) para o seu projeto. O Azure Dev Spaces usa esses arquivos para compilar e executar seu código, mas você poderá modificar esses arquivos se quiser, a fim de alterar a maneira como o projeto é compilado e executado.

Crie um arquivo [requirements.yaml][helm-requirements] com seu gráfico no diretório de gráficos do aplicativo. Por exemplo, se seu aplicativo for chamado *app1,* você criará *gráficos/app1/requirements.yaml*.

```yaml
dependencies:
    - name: mychart
      version: 0.1.0
      repository:  http://example.com/helm/v1/repo
```

Navegue até o diretório de gráficos do seu aplicativo e use a [atualização da dependência do leme][helm-dependency-update] para atualizar as dependências do Helm para o seu aplicativo e baixe o gráfico do repositório privado.

```cmd
helm dependency update
```

Verifique se um subdiretório *de gráficos* com um arquivo *tgz* foi adicionado ao diretório de gráficos do aplicativo. Por exemplo, *gráficos/app1/gráficos/mychart-0.1.0.tgz*.

O gráfico do seu repositório particular Helm foi baixado e adicionado ao seu projeto. Remova os *requisitos.yaml* file para que o Dev Spaces não tente atualizar essa dependência.

## <a name="run-your-application"></a>Execute seu aplicativo.

Navegue até o diretório raiz `azds up` do seu projeto e execute para verificar se seu aplicativo é executado com sucesso no seu espaço de desenvolvimento.

```cmd
$ azds up
Using dev space 'default' with target 'MyAKS'
Synchronizing files...2s
Installing Helm chart...2s
Waiting for container image build...2m 25s
Building container image...
...
Service 'app1' port 'http' is available at http://app1.1234567890abcdef1234.eus.azds.io/
Service 'app1' port 80 (http) is available at http://localhost:54256
...
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre [Helm e como ele funciona.][helm]

[helm]: https://docs.helm.sh
[helm-chart]: https://helm.sh/docs/topics/charts/
[helm-dependency-update]: https://helm.sh/docs/topics/charts/#managing-dependencies-with-the-dependencies-field
[helm-repo-add]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-repo-update]: https://helm.sh/docs/intro/using_helm/#helm-repo-working-with-repositories
[helm-requirements]: https://helm.sh/docs/topics/charts/#chart-dependencies
