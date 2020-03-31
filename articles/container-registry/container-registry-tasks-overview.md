---
title: Visão geral das Tarefas do ACR
description: Uma introdução ao ACR Tasks, um conjunto de recursos no Azure Container Registry que fornece construção, gerenciamento e patches de imagens de contêiner seguros e automatizados na nuvem.
ms.topic: article
ms.date: 01/22/2020
ms.openlocfilehash: 4fda57c1d7c866f2e6f72b04d75e53f91e995baf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087278"
---
# <a name="automate-container-image-builds-and-maintenance-with-acr-tasks"></a>Automatize a construção e manutenção de imagens de contêineres com tarefas ACR

Os contêineres fornecem novos níveis de virtualização, isolando as dependências de aplicativo e desenvolvedor dos requisitos de infraestrutura e operacionais. O que permanece, no entanto, é a necessidade de abordar como essa virtualização de aplicativos é gerenciada e corrigida ao longo do ciclo de vida do contêiner.

## <a name="what-is-acr-tasks"></a>O que são as Tarefas do ACR?

As **Tarefas do ACR** são um pacote de recursos do Registro de Contêiner do Azure. Ele fornece a construção de imagens de contêineres baseadas em nuvem para [plataformas,](#image-platforms) incluindo Linux, Windows e ARM, e pode automatizar [o sistema operacional e o patch de estrutura](#automate-os-and-framework-patching) para seus contêineres Docker. O ACR Tasks não apenas estende seu ciclo de desenvolvimento dentro do loop para a nuvem com compilações de imagens de contêiner demanda, mas também permite compilações automatizadas desencadeadas por atualizações de código-fonte, atualizações para a imagem base de um contêiner ou temporizadores. Por exemplo, com os gatilhos de atualização de imagem base, você pode automatizar o fluxo de trabalho de correção do sistema operacional e do aplicativo, mantendo ambientes seguros e aderindo aos princípios dos contêineres imutáveis.

## <a name="task-scenarios"></a>Cenários de tarefas

O ACR Tasks suporta vários cenários para construir e manter imagens de contêineres e outros artefatos. Veja as seções a seguir neste artigo para obter detalhes.

* **[Tarefa rápida](#quick-task)** - Construa e empurre uma única imagem de contêiner para um registro de contêiner demanda, no Azure, sem precisar de uma instalação local do Docker Engine. Pense em `docker build`, `docker push` na nuvem.
* **Tarefas acionadas automaticamente** - Habilite um ou mais *gatilhos* para construir uma imagem:
  * **[Acione na atualização do código-fonte](#trigger-task-on-source-code-update)** 
  * **[Gatilho na atualização de imagem base](#automate-os-and-framework-patching)** 
  * **[Acionar em um cronograma](#schedule-a-task)** 
* **[Tarefa em várias etapas](#multi-step-tasks)** - Amplie o recurso de compilação e pressão de imagem única das tarefas ACR com fluxos de trabalho baseados em vários passos e vários contêineres. 

Cada Tarefa ACR tem um [contexto de código fonte](#context-locations) associado - a localização de um conjunto de arquivos de origem usados para construir uma imagem de contêiner ou outro artefato. Os contextos de exemplo incluem um repositório Git ou um sistema de arquivos local.

As tarefas também podem aproveitar as variáveis de [execução,](container-registry-tasks-reference-yaml.md#run-variables)para que você possa reutilizar definições de tarefas e padronizar tags para imagens e artefatos.

## <a name="quick-task"></a>Tarefa rápida

O ciclo de desenvolvimento de loop interno, o processo iterativo de escrever código, compilar e testar o aplicativo antes de confirmar no controle do código-fonte, é realmente o início do gerenciamento de ciclo de vida do contêiner.

Antes que você confirme sua primeira linha de código, o recurso [tarefas rápidas](container-registry-tutorial-quick-task.md) das Tarefas do ACR pode fornecer uma experiência de desenvolvimento integrada ao descarregar os builds de imagem de contêiner no Azure. Com as tarefas rápidas, você pode verificar suas definições de build automatizadas e detectar possíveis problemas antes de confirmar o código.

Usando o formato `docker build` familiar, o comando [az acr build][az-acr-build] na CLI do Azure usa um [contexto](#context-locations) (o conjunto de arquivos a ser compilado), envia-o às Tarefas do ACR e, por padrão, envia a imagem de build por push ao seu registro após a conclusão.

Para uma introdução, consulte o quickstart para [construir e executar uma imagem de contêiner](container-registry-quickstart-task-cli.md) no Registro de Contêineres do Azure.  

O recurso Tarefas de ACR foi projetado como uma primitiva do ciclo de vida do contêiner. Por exemplo, é possível integrar as Tarefas do ACR à sua solução de CI/CD. Executando [az login][az-login] com uma [entidade de serviço][az-login-service-principal], sua solução CI/CD pode enviar os comandos [az acr build][az-acr-build] para iniciar compilações da imagem.

Saiba como usar as tarefas rápidas no primeiro tutorial das Tarefas do ACR, [Compilar imagens de contêiner na nuvem com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-quick-task.md).

> [!TIP]
> Se você quiser construir e empurrar uma imagem diretamente do código-fonte, sem um Arquivo Docker, o Azure Container Registry fornece o comando [az acr pack build][az-acr-pack-build] (visualização). Esta ferramenta constrói e empurra uma imagem do código-fonte do aplicativo usando [buildpacks nativos da nuvem](https://buildpacks.io/).

## <a name="trigger-task-on-source-code-update"></a>Acione a tarefa na atualização do código-fonte

Acione uma tarefa de compilação de imagem de contêiner ou de várias etapas quando o código é comprometido, ou uma solicitação de tração é feita ou atualizada, para um repositório público ou privado do Git no GitHub ou no Azure DevOps. Por exemplo, configure uma tarefa de compilação com a tarefa Azure CLI command [az acr,][az-acr-task-create] especificando um repositório Git e, opcionalmente, um ramo e um arquivo Docker. Quando sua equipe atualiza o código no repositório, um webhook criado pelo ACR Tasks aciona uma compilação da imagem do contêiner definida no repo. 

O ACR Tasks suporta os seguintes gatilhos quando você define um repo do Git como o contexto da tarefa:

| Gatilho | Habilitado por padrão |
| ------- | ------------------ |
| Commit | Sim |
| Solicitação de pull | Não |

Para configurar um gatilho de atualização de código fonte, você precisa fornecer à tarefa um token de acesso pessoal (PAT) para definir o webhook no repo gitHub ou azure DevOps público ou privado.

> [!NOTE]
> Atualmente, as Tarefas do ACR não dão suporte a gatilhos de solicitação de pull ou commit em Repos do GitHub Enterprise.

Saiba como disparar builds na confirmação do código-fonte no segundo tutorial das Tarefas do ACR, [Automatizar builds de imagem de contêiner com as Tarefas do Registro de Contêiner do Azure](container-registry-tutorial-build-task.md).

## <a name="automate-os-and-framework-patching"></a>Automatizar sistema operacional e aplicação de patch de estrutura

O poder das Tarefas ACR para realmente melhorar o fluxo de trabalho de construção de contêineres vem de sua capacidade de detectar uma atualização para uma *imagem base*. Uma característica da maioria das imagens de contêiner, uma imagem base é uma imagem pai na qual uma ou mais imagens de aplicativos são baseadas. As imagens base geralmente contêm o sistema operacional e, às vezes, as estruturas de aplicação. 

Você pode configurar uma tarefa ACR para rastrear uma dependência de uma imagem base quando ela constrói uma imagem de aplicativo. Quando a imagem base atualizada é empurrada para o seu registro ou uma imagem base é atualizada em um repo público, como no Docker Hub, o ACR Tasks pode criar automaticamente qualquer imagem de aplicativo com base nela.
Com essa detecção e recriação automáticas, as Tarefas do ACR poupam o tempo e o esforço normalmente necessários para acompanhar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

Saiba mais sobre [os gatilhos de atualização de imagem base](container-registry-tasks-base-images.md) para tarefas ACR. E aprenda a ativar uma compilação de imagem quando uma imagem base é empurrada para um registro de contêiner no tutorial [Automate imagem de contêiner é construída quando uma imagem base é atualizada em um registro de contêiner do Azure](container-registry-tutorial-base-image-update.md)

## <a name="schedule-a-task"></a>Agendar uma tarefa

Opcionalmente, agende uma tarefa configurando um ou mais *gatilhos do temporizador* quando você criar ou atualizar a tarefa. Agendar uma tarefa é útil para executar cargas de trabalho de contêineres em um cronograma definido ou executar operações de manutenção ou testes em imagens empurradas regularmente para o seu registro. Para obter detalhes, consulte [Executar uma tarefa ACR em um cronograma definido](container-registry-tasks-scheduled.md).

## <a name="multi-step-tasks"></a>Tarefas de várias etapas

As tarefas de várias etapas fornecem uma definição de tarefas com base em etapa e execução para criar, testar e aplicar patches em imagens de contêiner na nuvem. As etapas de tarefa definidas em um [arquivo YAML](container-registry-tasks-reference-yaml.md) especificam operações individuais de compilação e pressão para imagens de contêiner ou outros artefatos. Elas também podem definir a execução de um ou mais contêineres, com cada etapa usando o contêiner como seu ambiente de execução.

Por exemplo, você pode criar uma tarefa de várias etapa que automatiza o seguinte:

1. Compilar uma imagem do aplicativo Web
1. Executar o contêiner de aplicativo Web
1. Compilar uma imagem de teste do aplicativo Web
1. Execute o recipiente de teste de aplicação web, que realiza testes contra o recipiente de aplicativo em execução
1. Quando os testes são aprovados, compilar um pacote de arquivo morto de gráfico do Helm
1. Executar um `helm upgrade` usando o novo pacote de arquivo morto do gráfico do Helm

As tarefas de várias etapas permitem que você divida o build, a execução e o teste de uma imagem em etapas mais combináveis, com suporte para dependência entre etapas. Com as tarefas de várias etapas nas Tarefas do ACR, há um controle mais granular sobre os fluxos de trabalho de build de imagem, de teste e de aplicação de patch no sistema operacional e na estrutura.

Saiba mais sobre as tarefas de várias etapas em [Run multi-step build, test, and patch tasks in ACR Tasks](container-registry-tasks-multi-step.md) (Executar tarefas de várias etapas de build, teste e patch nas Tarefas do ACR).

## <a name="context-locations"></a>Localizações de contexto

A tabela a seguir mostra alguns exemplos de locais de contexto com suporte para as Tarefas do ACR:

| Local do contexto | Descrição | Exemplo |
| ---------------- | ----------- | ------- |
| Sistema de arquivos local | Arquivos dentro de um diretório no sistema de arquivos local. | `/home/user/projects/myapp` |
| Branch mestre do GitHub | Arquivos dentro do ramo mestre (ou outro padrão) de um repositório público ou privado do GitHub.  | `https://github.com/gituser/myapp-repo.git` |
| Ramificação GitHub | Filial específica de um repo GitHub público ou privado.| `https://github.com/gituser/myapp-repo.git#mybranch` |
| Subpasta do GitHub | Arquivos dentro de uma subpasta em um repo GitHub público ou privado. Exemplo mostra combinação de uma especificação de ramo e subpasta. | `https://github.com/gituser/myapp-repo.git#mybranch:myfolder` |
| GitHub comete | Compromisso específico em um repo GitHub público ou privado. Exemplo mostra combinação de hash (SHA) e especificação de subpasta. | `https://github.com/gituser/myapp-repo.git#git-commit-hash:myfolder` |
| Subpasta Azure DevOps | Arquivos dentro de uma subpasta em um repo Azure público ou privado. Exemplo mostra combinação de especificação de ramo e subpasta. | `https://dev.azure.com/user/myproject/_git/myapp-repo#mybranch:myfolder` |
| Tarball remoto | Arquivos em um arquivo compactado em um servidor remoto. | `http://remoteserver/myapp.tar.gz` |

> [!NOTE]
> Ao usar um repo Git privado como um contexto para uma tarefa, você precisa fornecer um token de acesso pessoal (PAT).

## <a name="image-platforms"></a>Plataformas de imagem

Por padrão, o ACR Tasks constrói imagens para o Sistema Operacional Linux e a arquitetura amd64. Especifique a `--platform` tag para construir imagens do Windows ou imagens Linux para outras arquiteturas. Especifique o Sistema Operacional e, opcionalmente, uma `--platform Linux/arm`arquitetura suportada no formato S/arquitetura (por exemplo, ). Para arquiteturas ARM, especificar opcionalmente uma variante no `--platform Linux/arm64/v8`formato OS/arquitetura/variante (por exemplo, ):

| Sistema operacional | Arquitetura|
| --- | ------- | 
| Linux | amd64<br/>arm<br/>arm64<br/>386 |
| Windows | amd64 |

## <a name="view-task-output"></a>Exibir saída da tarefa

Cada execução de tarefa gera saída de log que você pode inspecionar para determinar se as etapas da tarefa foram executadas com sucesso. Quando você aciona uma tarefa manualmente, a saída de log para a execução da tarefa é transmitida para o console e também armazenada para recuperação posterior. Quando uma tarefa é acionada automaticamente, por exemplo, por um compromisso de código-fonte ou uma atualização de imagem base, os logs de tarefas são armazenados apenas. Veja os logs executados no portal Azure ou use o comando [az acr task logs.](/cli/azure/acr/task#az-acr-task-logs)

Veja mais sobre [como visualizar e gerenciar registros de tarefas](container-registry-tasks-logs.md).

## <a name="next-steps"></a>Próximas etapas

Quando estiver pronto para automatizar a construção e a manutenção de imagens de contêineres na nuvem, confira a [série de tutoriais Tarefas ACR](container-registry-tutorial-quick-task.md).

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
