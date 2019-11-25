---
title: Visão geral das Tarefas do ACR
description: An introduction to ACR Tasks, a suite of features in Azure Container Registry that provides secure, automated container image build, management, and patching in the cloud.
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: b4710591dfd78f0633d5071c78d80e300349f498
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456148"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automate container image builds and maintenance with ACR Tasks

Os contêineres fornecem novos níveis de virtualização, isolando as dependências de aplicativo e desenvolvedor dos requisitos de infraestrutura e operacionais. What remains, however, is the need to address how this application virtualization is managed and patched over the container lifecycle.

## <a name="what-is-acr-tasks"></a>O que são as Tarefas do ACR?

As **Tarefas do ACR** são um pacote de recursos do Registro de Contêiner do Azure. It provides cloud-based container image building for [platforms](#image-platforms) including Linux, Windows, and ARM, and can automate [OS and framework patching](#automate-os-and-framework-patching) for your Docker containers. ACR Tasks not only extends your "inner-loop" development cycle to the cloud with on-demand container image builds, but also enables automated builds triggered by source code updates, updates to a container's base image, or timers. For example, with base image update triggers, you can automate your OS and application framework patching workflow, maintaining secure environments while adhering to the principles of immutable containers.

## <a name="task-scenarios"></a>Task scenarios

ACR Tasks supports several scenarios to build and maintain container images and other artifacts. See the following sections in this article for details.

* **[Quick task](#quick-task)** - Build and push a single container image to a container registry on-demand, in Azure, without needing a local Docker Engine installation. Pense em `docker build`, `docker push` na nuvem.
* **Automatically triggered tasks** - Enable one or more *triggers* to build an image:
  * **[Trigger on source code update](#trigger-task-on-source-code-update)** 
  * **[Trigger on base image update](#automate-os-and-framework-patching)** 
  * **[Trigger on a schedule](#schedule-a-task)** 
* **[Multi-step task](#multi-step-tasks)** - Extend the single image build-and-push capability of ACR Tasks with multi-step, multi-container-based workflows. 

Each ACR Task has an associated [source code context](#context-locations) - the location of a set of source files used to build a container image or other artifact. Example contexts include a Git repository or a local filesystem.

Tasks can also take advantage of [run variables](container-registry-tasks-reference-yaml.md#run-variables), so you can reuse task definitions and standardize tags for images and artifacts.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento de loop interno, o processo iterativo de escrever código, compilar e testar o aplicativo antes de confirmar no controle do código-fonte, é realmente o início do gerenciamento de ciclo de vida do contêiner.

Antes que você confirme sua primeira linha de código, o recurso [tarefas rápidas](container-registry-tutorial-quick-task.md) das Tarefas do ACR pode fornecer uma experiência de desenvolvimento integrada ao descarregar os builds de imagem de contêiner no Azure. Com as tarefas rápidas, você pode verificar suas definições de build automatizadas e detectar possíveis problemas antes de confirmar o código.

Using the familiar `docker build` format, the [az acr build][az-acr-build] command in the Azure CLI takes a [context](#context-locations) (the set of files to build), sends it ACR Tasks and, by default, pushes the built image to its registry upon completion.

For an introduction, see the quickstart to [build and run a container image](container-registry-quickstart-task-cli.md) in Azure Container Registry.  

O recurso Tarefas de ACR foi projetado como uma primitiva do ciclo de vida do contêiner. Por exemplo, é possível integrar as Tarefas do ACR à sua solução de CI/CD. By executing [az login][az-login] with a [service principal][az-login-service-principal], your CI/CD solution could then issue [az acr build][az-acr-build] commands to kick off image builds.

Saiba como usar as tarefas rápidas no primeiro tutorial das Tarefas do ACR, [Compilar imagens de contêiner na nuvem com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-quick-task.md).

> [!TIP]
> If you want to build and push an image directly from source code, without a Dockerfile, Azure Container Registry provides the [az acr pack build][az-acr-pack-build] command (preview). This tool builds and pushes an image from application source code using [Cloud Native Buildpacks](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Trigger task on source code update

Trigger a container image build or multi-step task when code is committed, or a pull request is made or updated, to a Git repository in GitHub or Azure DevOps. For example, configure a build task with the Azure CLI command [az acr task create][az-acr-task-create] by specifying a Git repository and optionally a branch and Dockerfile. When your team updates code in the repository, an ACR Tasks-created webhook triggers a build of the container image defined in the repo. 

ACR Tasks supports the following triggers when you set a Git repo as the task's context:

| Gatilho | Enabled by default |
| ------- | ------------------ |
| Confirmar | SIM |
| Pull request | Não |

To configure the trigger, you provide the task a personal access token (PAT) to set the webhook in the GitHub or Azure DevOps repo.

Saiba como disparar builds na confirmação do código-fonte no segundo tutorial das Tarefas do ACR, [Automatizar builds de imagem de contêiner com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizar sistema operacional e aplicação de patch de estrutura

O poder das Tarefas do ACR de realmente aprimorar seu fluxo de trabalho de build de contêiner vem da capacidade de detectar uma atualização para uma imagem de base. When the updated base image is pushed to your registry, or a base image is updated in a public repo such as in Docker Hub, ACR Tasks can automatically build any application images based on it.

As imagens de contêiner podem ser amplamente categorizadas em imagens de *base* e imagens de *aplicativo*. Suas imagens de base normalmente incluem o sistema operacional e estruturas de aplicativo com base nos quais seu aplicativo é criado, em conjunto com outras personalizações. These base images are themselves typically based on public upstream images, for example: [Alpine Linux][base-alpine], [Windows][base-windows], [.NET][base-dotnet], or [Node.js][base-node]. Várias das suas imagens de aplicativo podem compartilhar uma imagem de base comum.

Quando um sistema operacional ou uma imagem de estrutura de aplicativo é atualizado pelo mantenedor upstream, por exemplo, com um patch de segurança crítico de sistema operacional, você deverá também atualizar suas imagens de base para incluir a correção crítica. Cada imagem de aplicativo deve também ser reconstruída para incluir essas correções upstream agora incluídas em sua imagem de base.

Como as Tarefas do ACR descobrem dinamicamente as dependências da imagem base quando criam uma imagem de contêiner, elas podem detectar quando a imagem base de uma imagem de aplicativo é atualizada. Com uma [tarefa de build](container-registry-tutorial-base-image-update.md#create-a-task) pré-configurada, as Tarefas do ACR **recriam automaticamente cada imagem do aplicativo** para você. Com essa detecção e recriação automáticas, as Tarefas do ACR poupam o tempo e o esforço normalmente necessários para acompanhar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

For image builds from a Dockerfile, an ACR task tracks a base image update when the base image is in one of the following locations:

* O mesmo Registro de Contêiner do Azure em que a tarefa é executada
* Outro Registro de Contêiner do Azure na mesma região 
* Um repositório público no Docker Hub
* Um repositório público no Registro de Contêiner da Microsoft

> [!NOTE]
> * The base image update trigger is enabled by default in an ACR task. 
> * Currently, ACR Tasks only tracks base image updates for application (*runtime*) images. ACR Tasks doesn't track base image updates for intermediate (*buildtime*) images used in multi-stage Dockerfiles. 

Learn more about OS and framework patching in the third ACR Tasks tutorial, [Automate image builds on base image update with Azure Container Registry Tasks](container-registry-tutorial-base-image-update.md).

## <a name="schedule-a-task"></a>Agendar uma tarefa

Optionally schedule a task by setting up one or more *timer triggers* when you create or update the task. Scheduling a task is useful for running container workloads on a defined schedule, or running maintenance operations or tests on images pushed regularly to your registry. For details, see [Run an ACR task on a defined schedule](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Tarefas de várias etapas

Multi-step tasks provide step-based task definition and execution for building, testing, and patching container images in the cloud. Task steps defined in a [YAML file](container-registry-tasks-reference-yaml.md) specify individual build and push operations for container images or other artifacts. Elas também podem definir a execução de um ou mais contêineres, com cada etapa usando o contêiner como seu ambiente de execução.

Por exemplo, você pode criar uma tarefa de várias etapa que automatiza o seguinte:

1. Compilar uma imagem do aplicativo Web
1. Executar o contêiner de aplicativo Web
1. Compilar uma imagem de teste do aplicativo Web
1. Run the web application test container, which performs tests against the running application container
1. Quando os testes são aprovados, compilar um pacote de arquivo morto de gráfico do Helm
1. Executar um `helm upgrade` usando o novo pacote de arquivo morto do gráfico do Helm

As tarefas de várias etapas permitem que você divida o build, a execução e o teste de uma imagem em etapas mais combináveis, com suporte para dependência entre etapas. Com as tarefas de várias etapas nas Tarefas do ACR, há um controle mais granular sobre os fluxos de trabalho de build de imagem, de teste e de aplicação de patch no sistema operacional e na estrutura.

Saiba mais sobre as tarefas de várias etapas em [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Executar tarefas de várias etapas de build, teste e patch nas Tarefas do ACR).

## <a name="context-locations"></a>Context locations

A tabela a seguir mostra alguns exemplos de locais de contexto com suporte para as Tarefas do ACR:

| Local do contexto | Descrição | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de arquivos local | Arquivos dentro de um diretório no sistema de arquivos local. | `/home/user/projects/myapp` |
| Branch mestre do GitHub | Arquivos dentro da ramificação principal (ou outro padrão) de um repositório GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramificação GitHub | Filial específica de um repositório GitHub.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Subpasta do GitHub | Arquivos dentro de uma subpasta em um repositório do GitHub. Example shows combination of a branch and subfolder specification. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| Azure DevOps subfolder | Files within a subfolder in an Azure repo. Example shows combination of branch and subfolder specification. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Tarball remoto | Arquivos em um arquivo compactado em um servidor remoto. | `http://remoteserver/myapp.tar.gz` |

## <a name="image-platforms"></a>Image platforms

By default, ACR Tasks builds images for the Linux OS and the amd64 architecture. Specify the `--platform` tag to build Windows images or Linux images for other architectures. Specify the OS and optionally a supported architecture in OS/architecture format (for example, `--platform Linux/arm`). For ARM architectures, optionally specify a variant in OS/architecture/variant format (for example, `--platform Linux/arm64/v8`):

| SISTEMA OPERACIONAL | Arquitetura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-logs"></a>View task logs

Each task run generates log output that you can inspect to determine whether the task steps ran successfully. If you use the [az acr build](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run), or [az acr task run](/cli/azure/acr/task#az-acr-task-run) command to trigger the task, log output for the task run is streamed to the console and also stored for later retrieval. When a task is automatically triggered, for example by a source code commit or a base image update, task logs are only stored. View the logs for a task run in the Azure portal, or use the [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) command.

By default, data and logs for task runs in a registry are retained for 30 days and then automatically purged. If you want to archive the data for a task run, enable archiving using the [az acr task update-run](/cli/azure/acr/task#az-acr-task-update-run) command. The following example enables archiving for the task run *cf11* in registry *myregistry*.

```azurecli
az acr task update-run --registry myregistry --run-id cf11 --no-archive false
```

## <a name="next-steps"></a>Próximos passos

When you're ready to automate container image builds and maintenance in the cloud, check out the [ACR Tasks tutorial series](container-registry-tutorial-quick-task.md).

Opcionalmente, instale a [Extensão do Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) e a extensão [Conta do Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabalhar com seus registros de contêiner do Azure. Efetue pull e push de imagens para um registro de contêiner do Azure ou execute Tarefas do ACR, tudo isso no Visual Studio Code.

<!-- LINKS - External -->
[base-alpine]: https://hub.docker.com/_/alpine/
[base-dotnet]: https://hub.docker.com/r/microsoft/dotnet/
[base-node]: https://hub.docker.com/_/node/
[base-windows]: https://hub.docker.com/r/microsoft/nanoserver/
[sample-archive]: https://github.com/Azure-Samples/acr-build-helloworld-node/archive/master.zip
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-pack-build]: /cli/azure/acr/pack#az-acr-pack-build
[az-acr-task]: /cli/azure/acr/task
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
