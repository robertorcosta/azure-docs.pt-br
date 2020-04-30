---
title: Tutorial – Excluir um cluster do Red Hat OpenShift no Azure
description: Neste tutorial, saiba como excluir um cluster do Red Hat OpenShift no Azure usando a CLI do Azure
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 2de60b90eb6fb75ef013a2fd8785f1b8b616fba6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232131"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: excluir um cluster do Azure Red Hat OpenShift 4

Neste tutorial, parte três de três, um cluster do Azure Red Hat OpenShift que executa o OpenShift 4 é excluído. Você aprenderá como:

> [!div class="checklist"]
> * Excluir um cluster do Red Hat OpenShift no Azure


## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um cluster do Azure Red Hat OpenShift foi criado e conectado usando o console Web do OpenShift. Se você ainda não realizou essas etapas e gostaria de acompanhar, comece com [o tutorial 1-criar um cluster do Azure Red Hat Openshift 4.](tutorial-create-cluster.md)

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá que você esteja executando o CLI do Azure versão 2.0.75 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Se você estiver executando a CLI do Azure localmente, execute `az login` para entrar no Azure.

```bash
az login
```

Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

## <a name="delete-the-cluster"></a>Excluir o cluster

Nos tutoriais anteriores, as variáveis a seguir foram definidas. 

```bash
CLUSTER=yourclustername
RESOURCE_GROUP=yourresourcegroup
```

Usando esses valores, exclua o cluster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Em seguida, será solicitado que você confirme se deseja excluir o cluster. Depois de confirmar com `y`, demorará alguns minutos para excluir o cluster. Quando o comando for concluído, todo o grupo de recursos e todos os recursos dentro dele, incluindo o cluster, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Excluir um cluster do Azure Red Hat OpenShift 4

Saiba mais sobre como usar o OpenShift com a [documentação oficial do Red Hat OpenShift](https://www.openshift.com/try)
