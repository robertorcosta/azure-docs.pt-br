---
title: Construir imagem com buildpack nativo da nuvem
description: Use o comando az acr pack build para construir uma imagem de contêiner a partir de um aplicativo e empurrar para o Registro de Contêineres do Azure, sem usar um arquivo Docker.
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: c42bde6bbab5973094302a2d41f004d7600bdf9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087075"
---
# <a name="build-and-push-an-image-from-an-app-using-a-cloud-native-buildpack"></a>Construa e empurre uma imagem de um aplicativo usando um Buildpack nativo da nuvem

O comando `az acr pack build` Azure [`pack`](https://github.com/buildpack/pack) CLI usa a ferramenta CLI, da [Buildpacks,](https://buildpacks.io/)para construir um aplicativo e empurrar sua imagem para um registro de contêiner do Azure. Esse recurso oferece uma opção para construir rapidamente uma imagem de contêiner a partir do código-fonte do aplicativo em Node.js, Java e outros idiomas sem ter que definir um Arquivo Docker.

Você pode usar o Azure Cloud Shell ou uma instalação local do Azure CLI para executar os exemplos neste artigo. Se você quiser usá-lo localmente, a versão 2.0.70 ou posterior é necessária. Execute `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [Install Azure CLI][azure-cli-install].

> [!IMPORTANT]
> Esse recurso está atualmente na visualização. As visualizações são disponibilizadas para você com a condição de que você concorde com os [termos de uso suplementar][terms-of-use]. Alguns aspectos desse recurso podem alterar antes da GA (disponibilidade geral).

## <a name="use-the-build-command"></a>Use o comando build

Para construir e empurrar uma imagem de contêiner usando cloud native Buildpacks, execute o comando [az acr pack build.][az-acr-pack-build] Considerando que o comando [az acr build][az-acr-build] constrói e empurra uma imagem `az acr pack build` de uma fonte de arquivo Docker e código relacionado, com você especificar uma árvore de origem do aplicativo diretamente.

No mínimo, especifique `az acr pack build`o seguinte ao executar:

* Um registro de contêiner Azure onde você executa o comando
* Um nome de imagem e uma etiqueta para a imagem resultante
* Um dos locais de [contexto suportados](container-registry-tasks-overview.md#context-locations) para tarefas ACR, como um diretório local, um repo GitHub ou um tarball remoto
* O nome de uma imagem de construtor buildpack adequada para sua aplicação. O Azure Container Registry armazena `cloudfoundry/cnb:0.0.34-cflinuxfs3` imagens de construtores, como para compilações mais rápidas.  

`az acr pack build`suporta outros recursos de comandos ACR Tasks, incluindo [variáveis de execução](container-registry-tasks-reference-yaml.md#run-variables) e [logs de execução de tarefas](container-registry-tasks-logs.md) que são transmitidos e também salvos para recuperação posterior.

## <a name="example-build-nodejs-image-with-cloud-foundry-builder"></a>Exemplo: Construir imagem Node.js com o construtor de Fundição em Nuvem

O exemplo a seguir constrói uma imagem de contêiner a partir de um aplicativo Node.js no [azure-Samples/nodejs-docs-hello-world](https://github.com/Azure-Samples/nodejs-docs-hello-world) repo, usando o `cloudfoundry/cnb:0.0.34-cflinuxfs3` construtor. Este construtor é armazenado em cache pelo Registro `--pull` de Contêineres do Azure, portanto, um parâmetro não é necessário:

```azurecli
az acr pack build \
    --registry myregistry \
    --image {{.Run.Registry}}/node-app:1.0 \
    --builder cloudfoundry/cnb:0.0.34-cflinuxfs3 \
    https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

Este exemplo constrói `node-app` a `1.0` imagem com a tag e a empurra para o registro do contêiner *myregistry.* Neste exemplo, o nome do registro de destino é explicitamente preparado para o nome da imagem. Se não for especificado, o nome do servidor de login do registro é automaticamente preparado para o nome da imagem.

A saída de comando mostra o progresso da construção e empurrando a imagem. 

Depois que a imagem for construída com sucesso, você pode executá-la com o Docker, se você a tiver instalada. Primeiro sinal em seu registro:

```azurecli
az acr login --name myregistry
```

Execute a imagem:

```console
docker run --rm -p 1337:1337 myregistry.azurecr.io/node-app:1.0
```

Navegue `localhost:1337` até o seu navegador favorito para ver o aplicativo da web de exemplo. Pressione `[Ctrl]+[C]` para parar o recipiente.

## <a name="example-build-java-image-with-heroku-builder"></a>Exemplo: Construa imagem Java com construtor Heroku

O exemplo a seguir cria uma imagem de contêiner do aplicativo Java no rejogo [buildpack/sample-java-app,](https://github.com/buildpack/sample-java-app) usando o `heroku/buildpacks:18` construtor. O `--pull` parâmetro especifica que o comando deve puxar a imagem mais recente do construtor. 

```azurecli
az acr pack build \
    --registry myregistry \
    --image java-app:{{.Run.ID}} \
    --pull --builder heroku/buildpacks:18 \
    https://github.com/buildpack/sample-java-app.git
```

Este exemplo constrói `java-app` a imagem marcada com o ID de execução do comando e a empurra para o registro do contêiner *myregistry.*

A saída de comando mostra o progresso da construção e empurrando a imagem. 

Depois que a imagem for construída com sucesso, você pode executá-la com o Docker, se você a tiver instalada. Primeiro sinal em seu registro:

```azurecli
az acr login --name myregistry
```

Execute a imagem, substituindo sua tag de imagem por *runid:*

```console
docker run --rm -p 8080:8080 myregistry.azurecr.io/java-app:runid
```

Navegue `localhost:8080` até o seu navegador favorito para ver o aplicativo da web de exemplo. Pressione `[Ctrl]+[C]` para parar o recipiente.


## <a name="next-steps"></a>Próximas etapas

Depois de construir e empurrar `az acr pack build`uma imagem de contêiner com , você pode implantá-la como qualquer imagem para um alvo de sua escolha. As opções de implantação do Azure incluem executá-lo no [App Service](../app-service/containers/tutorial-custom-docker-image.md) ou [no Azure Kubernetes Service,](../aks/tutorial-kubernetes-deploy-cluster.md)entre outros.

Para obter mais informações sobre os recursos do ACR Tasks, consulte [Automatautomatizar compilações e manutenções](container-registry-tasks-overview.md)de imagens de contêiner com tarefas ACR .


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr/task
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
