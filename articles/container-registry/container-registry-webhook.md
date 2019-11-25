---
title: Webhooks to respond to registry actions
description: Learn how to use webhooks to trigger events when push or pull actions occur in your registry repositories.
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 5e6fd2d9f4c7727365a8e2fe3893aafebfeb7bd4
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454363"
---
# <a name="using-azure-container-registry-webhooks"></a>Como usar webhooks do Registro de Contêiner do Azure

Um registro de contêiner do Azure armazena e gerencia imagens de contêiner privadas do Docker, de forma semelhante a como o Docker Hub armazena imagens públicas do Docker. It can also host repositories for [Helm charts](container-registry-helm-repos.md) (preview), a packaging format to deploy applications to Kubernetes. É possível usar webhooks para disparar eventos quando certas ações ocorrem em um dos repositórios do Registro. Webhooks podem responder a eventos no nível do registro ou podem ter o escopo reduzido para a marca de um repositório específico. With a  [geo-replicated](container-registry-geo-replication.md) registry, you configure each webhook to respond to events in a specific regional replica.

Para obter detalhes sobre solicitações de webhook, consulte [Azure Container Registry webhook schema reference](container-registry-webhook-reference.md) (Referência de esquema de webhook do Registro de Contêiner do Azure).

## <a name="prerequisites"></a>Pré-requisitos

* Registro de Contêiner do Azure – Crie um Registro de contêiner em sua assinatura do Azure. Por exemplo, use o [Portal do Azure](container-registry-get-started-portal.md) ou a [CLI do Azure](container-registry-get-started-azure-cli.md). The [Azure Container Registry SKUs](container-registry-skus.md) have different webhooks quotas.
* CLI do Docker – para configurar o computador local como um host do Docker e acessar os comandos da CLI do Docker, instale o [Mecanismo do Docker](https://docs.docker.com/engine/installation/).

## <a name="create-webhook---azure-portal"></a>Create webhook - Azure portal

1. Entre no [portal do Azure](https://portal.azure.com).
1. Navegue até o Registro de contêiner no qual você deseja criar um webhook.
1. Under **Services**, select **Webhooks**.
1. Selecione **Adicionar** na barra de ferramentas do webhook.
1. Preencha o formulário *Criar webhook* com as seguintes informações:

| Value | Descrição |
|---|---|
| Webhook name | O nome que você deseja dar ao webhook. It may contain only letters and numbers, and must be 5-50 characters in length. |
| Location | For a [geo-replicated](container-registry-geo-replication.md) registry, specify the Azure region of the registry replica. 
| URI de serviço | O URI para que o webhook deveria enviar notificações POST. |
| Cabeçalhos personalizados | Cabeçalhos que você deseja passar junto com a solicitação POST. Eles devem estar no formato "chave: valor". |
| Ações de gatilho | Ações que disparam o webhook. Actions include image push, image delete, Helm chart push, Helm chart delete, and image quarantine. You can choose one or more actions to trigger the webhook. |
| Status | O status do webhook depois que ele é criado. Ele é habilitado por padrão. |
| Escopo | O escopo no qual o webhook funciona. If not specified, the scope is for all events in the registry. It can be specified for a repository or a tag by using the format "repository:tag", or "repository:*" for all tags under a repository. |

Formulário de webhook de exemplo:

![Interface do usuário de criação de webhook ACR no portal do Azure](./media/container-registry-webhook/webhook.png)

## <a name="create-webhook---azure-cli"></a>Create webhook - Azure CLI

Para criar um webhook usando a CLI do Azure, use o comando [az acr webhook create](/cli/azure/acr/webhook#az-acr-webhook-create). The following command creates a webhook for all image delete events in the registry *mycontainerregistry*:

```azurecli-interactive
az acr webhook create --registry mycontainerregistry --name myacrwebhook01 --actions delete --uri http://webhookuri.com
```

## <a name="test-webhook"></a>Testar webhook

### <a name="azure-portal"></a>Portal do Azure

Prior to using the webhook, you can test it with the **Ping** button. O ping envia uma solicitação POST genérica para o ponto de extremidade especificado e registra a resposta. Usar o recurso de ping pode ajudá-lo a verificar se você configurou corretamente o webhook.

1. Selecione o webhook que você deseja testar.
2. Na barra de ferramentas superior, selecione **Ping**.
3. Verifique a resposta do ponto de extremidade na coluna **STATUS HTTP**.

![Interface do usuário de criação de webhook ACR no portal do Azure](./media/container-registry-webhook/webhook-02.png)

### <a name="azure-cli"></a>Azure CLI

Para testar um webhook ACR com a CLI do Azure, use o comando [az acr webhook ping](/cli/azure/acr/webhook#az-acr-webhook-ping).

```azurecli-interactive
az acr webhook ping --registry mycontainerregistry --name myacrwebhook01
```

Para ver os resultados, use o comando [az acr webhook list-events](/cli/azure/acr/webhook).

```azurecli-interactive
az acr webhook list-events --registry mycontainerregistry08 --name myacrwebhook01
```

## <a name="delete-webhook"></a>Excluir webhook

### <a name="azure-portal"></a>Portal do Azure

Cada webhook pode ser excluído selecionando-se o webhook e, em seguida, o botão **Excluir** no Portal do Azure.

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az acr webhook delete --registry mycontainerregistry --name myacrwebhook01
```

## <a name="next-steps"></a>Próximos passos

### <a name="webhook-schema-reference"></a>Referência do esquema do Webhook

Para obter detalhes sobre o formato e propriedades das cargas do evento JSON emitidas pelo Registro de Contêiner do Azure, consulte a referência de esquema do webhook:

[Referência de esquema de webhook do Registro de Contêiner do Azure](container-registry-webhook-reference.md)

### <a name="event-grid-events"></a>Eventos da Grade de Eventos

Além dos eventos do webhook do registro nativo discutidos neste artigo, o Registro de Contêiner do Azure pode emitir eventos para a Grade de Eventos:

[Início Rápido: enviar eventos de registro de contêiner para a Grade de Eventos](container-registry-event-grid-quickstart.md)
