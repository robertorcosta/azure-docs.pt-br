---
title: Criar uma entidade de serviço de integração habilitada para Azure Arc (versão prévia)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Criar uma entidade de serviço de integração habilitada para Azure Arc '
keywords: Kubernetes, Arc, Azure, contêineres
ms.openlocfilehash: 3c95c6bb85c7c1bc097b7751a560a658863c0afd
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.contentlocale: pt-BR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725594"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Criar uma entidade de serviço de integração habilitada para Azure Arc (versão prévia)

## <a name="overview"></a>Visão geral

É possível usar as entidades de serviço que têm uma atribuição de função com privilégios limitados para integração de clusters do Kubernetes ao Azure Arc. Isso é útil em pipelines de integração contínua e implantação contínua (CI/CD), como Azure Pipelines e GitHub Actions.

As etapas a seguir fornecem instruções sobre o uso de entidades de serviço para integração de clusters do Kubernetes ao Azure Arc.

## <a name="create-a-new-service-principal"></a>Crie uma nova Entidade de Serviço

Crie uma nova Entidade de Serviço com um nome informativo. Observe que esse nome deve ser exclusivo para o locatário do Azure Active Directory:

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Saída:**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Atribuir permissões

Depois de criar a nova Entidade de Serviço, atribua a função "Azure Arc para Integração do Kubernetes" à entidade recém-criada. Essa é uma função interna do Azure com permissões limitadas, que só permite que a entidade de segurança registre clusters no Azure. A entidade de segurança não pode atualizar, excluir ou modificar outros clusters ou recursos na assinatura.

Dadas as capacidades limitadas, os clientes podem facilmente reutilizar essa entidade para integrar vários clusters.

As permissões podem ser ainda mais limitadas passando o argumento `--scope` apropriado ao atribuir a função. Isso permite que os clientes restrinjam o registro do cluster. Os cenários a seguir são compatíveis com vários parâmetros `--scope`:

| Recurso  | Argumento `scope`| Efeito |
| ------------- | ------------- | ------------- |
| Subscription | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | A entidade de serviço pode registrar qualquer cluster em um Grupo de Recursos existente na assinatura fornecida |
| Grupo de recursos | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | A entidade de serviço __só__ pode registrar clusters no Grupo de Recursos `myGroup` |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Saída:**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Usar a Entidade de Serviço com a CLI do Azure

Referencie a Entidade de Serviço recém-criada:

```console
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Próximas etapas

* [Usar a Azure Policy para controlar a configuração do cluster](./use-azure-policy.md)
