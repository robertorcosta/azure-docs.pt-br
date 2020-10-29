---
title: Tutorial – Excluir um cluster do Red Hat OpenShift no Azure
description: Neste tutorial, saiba como excluir um cluster do Red Hat OpenShift no Azure usando a CLI do Azure
author: sakthi-vetrivel
ms.custom: fasttrack-edit
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 254a9737b805aeeae2008923a8178cd971602132
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92677754"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Excluir um cluster do Red Hat OpenShift 4 no Azure

Neste tutorial, parte três de três, um cluster do Red Hat OpenShift no Azure que executa o OpenShift 4 é excluído. Você aprenderá como:

> [!div class="checklist"]
> * Excluir um cluster do Red Hat OpenShift no Azure


## <a name="before-you-begin"></a>Antes de começar

Nos tutoriais anteriores, um cluster do Red Hat OpenShift no Azure foi criado e conectado usando o console Web do OpenShift. Se você ainda não realizou essas etapas e gostaria de acompanhar, comece com o [Tutorial 1 – Criar um cluster do Red Hat OpenShift no Azure 4.](tutorial-create-cluster.md)

Se você optar por instalar e usar a CLI localmente, este tutorial exigirá a execução da CLI do Azure versão 2.6.0 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

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
RESOURCEGROUP=yourresourcegroup
```

Usando esses valores, exclua o cluster:

```bash
az aro delete --resource-group $RESOURCEGROUP --name $CLUSTER
```

Depois, você deverá confirmar se deseja excluir o cluster. Depois de confirmar com `y`, demorará alguns minutos para excluir o cluster. Quando o comando for concluído, todo o grupo de recursos e todos os recursos dentro dele, incluindo o cluster, serão excluídos.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Excluir um cluster do Red Hat OpenShift 4 no Azure

Saiba mais sobre como usar o OpenShift com a [documentação oficial do Red Hat OpenShift](https://www.openshift.com/try)
