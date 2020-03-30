---
title: Atualizações de imagem base - Tarefas
description: Saiba mais sobre imagens base para imagens de contêiner de aplicativos e sobre como uma atualização de imagem base pode desencadear uma tarefa de Registro de Contêiner do Azure.
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 017c8f8a3a15896bd6e14a54136ba713e9f9c499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617925"
---
# <a name="about-base-image-updates-for-acr-tasks"></a>Sobre atualizações de imagem base para tarefas ACR

Este artigo fornece informações em segundo plano sobre atualizações para a imagem base de um aplicativo e como essas atualizações podem desencadear uma tarefa do Registro de Contêineres do Azure.

## <a name="what-are-base-images"></a>O que são imagens básicas?

Os arquivos docker que definem a maioria das imagens do contêiner especificam uma imagem pai a partir da qual a imagem é baseada, muitas vezes referida como sua *imagem base*. Imagens de base normalmente contêm o sistema operacional, por exemplo [Alpine Linux][base-alpine] ou [Windows Nano Server][base-windows], no qual o resto do camadas do contêiner são aplicadas. Elas também podem incluir estruturas de aplicativo, como [Node.js][base-node] ou [.NET Core][base-dotnet]. Essas imagens base são baseadas em imagens públicas a montante. Várias das suas imagens de aplicativo podem compartilhar uma imagem de base comum.

Uma imagem de base geralmente é atualizada pelo mantenedor de imagem para incluir novos recursos ou aprimoramentos para o sistema operacional ou da estrutura na imagem. Patches de segurança são outra causa comum para uma atualização da imagem base. Quando essas atualizações upstream ocorrerem, você também deve atualizar suas imagens base para incluir a correção crítica. Cada imagem de aplicativo deve também ser reconstruída para incluir essas correções upstream agora incluídas em sua imagem de base.

Em alguns casos, como uma equipe de desenvolvimento privado, uma imagem base pode especificar mais do que sO ou framework. Por exemplo, uma imagem base pode ser uma imagem de componente de serviço compartilhada que precisa ser rastreada. Os membros de uma equipe podem precisar rastrear essa imagem base para testes, ou precisam atualizar regularmente a imagem ao desenvolver imagens de aplicativos.

## <a name="track-base-image-updates"></a>Acompanhar atualizações de imagens base

As Tarefas do ACR incluem a capacidade de compilar imagens automaticamente para você quando a imagem base do contêiner for atualizada.

O ACR Tasks descobre dinamicamente dependências de imagem base quando constrói uma imagem de contêiner. Como resultado, ele pode detectar quando a imagem base de uma imagem de aplicativo é atualizada. Com uma tarefa de compilação pré-configurada, o ACR Tasks pode reconstruir automaticamente cada imagem de aplicativo que faz referência à imagem base. Com essa detecção e recriação automáticas, as Tarefas do ACR poupam o tempo e o esforço normalmente necessários para acompanhar e atualizar manualmente cada imagem de aplicativo que faz referência à imagem base atualizada.

## <a name="base-image-locations"></a>Locais de imagem base

Para builds de imagem com base em um Dockerfile, uma tarefa do ACR detecta dependências em imagens base nas seguintes localizações:

* O mesmo Registro de Contêiner do Azure em que a tarefa é executada
* Outro registro privado de contêineres do Azure na mesma ou em uma região diferente 
* Um repositório público no Docker Hub 
* Um repositório público no Registro de Contêiner da Microsoft

Se a imagem base `FROM` especificada na declaração residir em um desses locais, a tarefa ACR adicionará um gancho para garantir que a imagem seja reconstruída sempre que sua base for atualizada.

## <a name="additional-considerations"></a>Considerações adicionais

* **Imagens base para imagens de aplicativos** - Atualmente, uma tarefa aCR só rastreia atualizações de imagem base para imagens de aplicativos *(tempo de execução).* Elas não rastreiam atualizações de imagem base para imagens intermediárias (*buildtime*) usadas em Dockerfiles de vários estágios.  

* **Ativado por padrão** - Quando você cria uma tarefa ACR com o comando [az acr task create,][az-acr-task-create] por padrão, a tarefa é *habilitada* para ser acionada por uma atualização de imagem base. Isto é, a propriedade `base-image-trigger-enabled` é definida como True. Se desejar desabilitar esse comportamento da tarefa, atualize a propriedade para False. Por exemplo, execute o seguinte comando [az acr task update][az-acr-task-update]:

  ```azurecli
  az acr task update --myregistry --name mytask --base-image-trigger-enabled False
  ```

* **Acione para rastrear dependências** - Para habilitar uma tarefa de ACR para determinar e rastrear as dependências de uma imagem de contêiner - que incluem sua imagem base - você deve primeiro ativar a tarefa de construir a imagem **pelo menos uma vez**. Por exemplo, disparar a tarefa manualmente usando o comando [az acr task run][az-acr-task-run].

* **Etiqueta estável para imagem base** - Para ativar uma tarefa na atualização da imagem base, a imagem base deve ter uma tag *estável,* como `node:9-alpine`. Essa marcação é típica de uma imagem base que é atualizada com o sistema operacional e patches de estrutura para uma versão estável mais recente. Se a imagem base é atualizada com uma nova marca de versão, ela não dispara uma tarefa. Para obter mais informações sobre a marcação de imagens, confira as [diretrizes de práticas recomendadas](container-registry-image-tag-version.md). 

* **Outros gatilhos de tarefas** - Em uma tarefa desencadeada por atualizações de imagem base, você também pode habilitar gatilhos com base no [compromisso do código fonte](container-registry-tutorial-build-task.md) ou em um [cronograma](container-registry-tasks-scheduled.md). Uma atualização de imagem base também pode desencadear uma [tarefa de várias etapas](container-registry-tasks-multi-step.md).

## <a name="next-steps"></a>Próximas etapas

Veja os tutoriais a seguir para cenários para automatizar compilações de imagens de aplicativos depois que uma imagem base é atualizada:

* [Automatizar a compilação de imagens de contêiner quando uma imagem base é atualizada no mesmo registro](container-registry-tutorial-base-image-update.md)

* [Automatizar a compilação de imagens de contêiner quando uma imagem base é atualizada em um registro diferente](container-registry-tutorial-base-image-update.md)


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
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-update]: /cli/azure/acr/task#az-acr-task-update
[az-login]: /cli/azure/reference-index#az-login
[az-login-service-principal]: /cli/azure/authenticate-azure-cli

<!-- IMAGES -->
[quick-build-01-fork]: ./media/container-registry-tutorial-quick-build/quick-build-01-fork.png
[quick-build-02-browser]: ./media/container-registry-tutorial-quick-build/quick-build-02-browser.png
