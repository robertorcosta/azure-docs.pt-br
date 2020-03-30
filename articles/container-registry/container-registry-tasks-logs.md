---
title: Exibir logs de execução de tarefas - Tarefas
description: Como visualizar e gerenciar logs de execução gerados por Tarefas ACR.
ms.topic: article
ms.date: 03/09/2020
ms.openlocfilehash: f7098f470a3f8a0cdac019f4bf8eb8fe14330337
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246962"
---
# <a name="view-and-manage-task-run-logs"></a>Exibir e gerenciar logs de execução de tarefas

Cada tarefa executada nas [tarefas do Registro de Contêiner do Azure](container-registry-tasks-overview.md) gera saída de log que você pode inspecionar para determinar se as etapas da tarefa foram executadas com sucesso. 

Este artigo explica como visualizar e gerenciar registros de execução de tarefas.

## <a name="view-streamed-logs"></a>Exibir logs transmitidos

Quando você aciona uma tarefa manualmente, a saída de log é transmitida diretamente para o console. Por exemplo, quando você aciona uma tarefa manualmente usando a [compilação az acr](/cli/azure/acr#az-acr-build), [az acr run](/cli/azure/acr#az-acr-run)ou [az acr task run](/cli/azure/acr/task#az-acr-task-run) command, você vê a saída de log transmitida para o console. 

O seguinte comando [sample az acr run](/cli/azure/acr#az-acr-run) aciona manualmente uma tarefa que executa um contêiner retirado do mesmo registro:

```azurecli
az acr run --registry mycontainerregistry1220 \
  --cmd '$Registry/samples/hello-world:v1' /dev/null
```

Log transmitido:

```console
Queued a run with ID: cf4
Waiting for an agent...
2020/03/09 20:30:10 Alias support enabled for version >= 1.1.0, please see https://aka.ms/acr/tasks/task-aliases for more information.
2020/03/09 20:30:10 Creating Docker network: acb_default_network, driver: 'bridge'
2020/03/09 20:30:10 Successfully set up Docker network: acb_default_network
2020/03/09 20:30:10 Setting up Docker configuration...
2020/03/09 20:30:11 Successfully set up Docker configuration
2020/03/09 20:30:11 Logging in to registry: mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Successfully logged into mycontainerregistry1220azurecr.io
2020/03/09 20:30:12 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2020/03/09 20:30:12 Launching container with name: acb_step_0
Unable to find image 'mycontainerregistry1220azurecr.io/samples/hello-world:v1' locally
v1: Pulling from samples/hello-world
Digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e888a
Status: Downloaded newer image for mycontainerregistry1220azurecr.io/samples/hello-world:v1

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]

2020/03/09 20:30:13 Successfully executed container: acb_step_0
2020/03/09 20:30:13 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 1.180081)

Run ID: cf4 was successful after 5s
```

## <a name="view-stored-logs"></a>Exibir registros armazenados 

As lojas azure Container Registry executam registros para todas as tarefas. Você pode visualizar logs de execução armazenados no portal Azure. Ou, use o comando [az acr task logs](/cli/azure/acr/task#az-acr-task-logs) para exibir um log selecionado. Por padrão, os registros são retidos por 30 dias.

Se uma tarefa for acionada automaticamente, por exemplo, por uma atualização de código-fonte, acessar os logs armazenados é a *única* maneira de visualizar os logs executados. Os gatilhos automáticos da tarefa incluem confirmações de código-fonte ou solicitações de puxar, atualizações de imagem base e gatilhos de temporizador.

Para visualizar os logs executados no portal:

1. Navegue até o registro do contêiner.
1. Em **Serviços,** selecione **Executa tarefas** > **Runs**.
1. Selecione um **ID de execução** para exibir o status de execução e executar logs. O registro contém as mesmas informações de um registro transmitido, se um for gerado.

![Exibir portal de login de execução de tarefas](./media/container-registry-tasks-logs/portal-task-run-logs.png)

Para exibir um log usando o Cli do Azure, execute [os logs de tarefas do AZ ACR](/cli/azure/acr/task#az-acr-task-logs) e especifique um ID de execução, um nome de tarefa ou uma criação de imagem específica por uma tarefa de compilação. Se um nome de tarefa for especificado, o comando mostrará o registro da última execução criada.

O exemplo a seguir produz o log para execução com ID *cf4*:

```azurecli
az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4
```

## <a name="alternative-log-storage"></a>Armazenamento de log alternativo

Você pode querer armazenar logs de execução de tarefas em um sistema de arquivos local ou usar uma solução alternativa de arquivamento, como o Azure Storage.

Por exemplo, crie um diretório *de tasklogs* local e redirecione a saída de logs de [tarefa sac az acr](/cli/azure/acr/task#az-acr-task-logs) para um arquivo local:

```azurecli
mkdir ~/tasklogs

az acr task logs --registry mycontainerregistry1220 \
  --run-id cf4 > ~/tasklogs/cf4.log
```

Você também pode salvar arquivos de log locais no Azure Storage. Por exemplo, use o [Azure CLI](../storage/blobs/storage-quickstart-blobs-cli.md), o [portal Azure](../storage/blobs/storage-quickstart-blobs-portal.md)ou outros métodos para carregar arquivos em uma conta de armazenamento.


## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [as tarefas de registro de contêineres do Azure](container-registry-tasks-overview.md)

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
