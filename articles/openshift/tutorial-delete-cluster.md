---
title: Tutorial – Excluir um cluster do Red Hat OpenShift no Azure
description: Neste tutorial, saiba como excluir um cluster do Red Hat OpenShift no Azure usando a CLI do Azure
author: jimzim
ms.author: jzim
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: c335236a2b0b05f03bef1ebef37f1129a5d0352b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "76278768"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>Tutorial: Excluir um cluster do Red Hat OpenShift no Azure

Este é o fim do tutorial. Ao terminar de testar o cluster de exemplo, veja como excluí-lo e os seus recursos associados, assim você não será cobrado por algo que não está usando.

Na terceira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Excluir um cluster do Red Hat OpenShift no Azure

Nesta série de tutoriais, você aprenderá a:
> [!div class="checklist"]
> * [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md)
> * [Dimensionar um cluster do Red Hat OpenShift no Azure](tutorial-scale-cluster.md)
> * Excluir um cluster do Red Hat OpenShift no Azure

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Crie um cluster, seguindo o tutorial [Criar um cluster do Red Hat OpenShift no Azure](tutorial-create-cluster.md).

## <a name="step-1-sign-in-to-azure"></a>Etapa 1: Entrar no Azure

Se você estiver executando a CLI do Azure localmente, execute `az login` para entrar no Azure.

```bash
az login
```

Caso tenha acesso a várias assinaturas, execute `az account set -s {subscription ID}` substituindo `{subscription ID}` pela assinatura que você deseja usar.

## <a name="step-2-delete-the-cluster"></a>Etapa 2: Excluir o cluster

Abra um terminal Bash e defina a variável CLUSTER_NAME com o nome do seu cluster:

```bash
CLUSTER_NAME=yourclustername
```

Agora exclua seu cluster:

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

Você será perguntado se deseja excluir o cluster. Depois de confirmar com `y`, demorará alguns minutos para excluir o cluster. Quando o comando for concluído, todo o Grupo de recursos e todos os recursos dentro dele, incluindo o cluster, serão excluídos.

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Excluir o cluster usando o portal do Azure

Como alternativa, você pode excluir o grupo de recursos associado do seu cluster por meio do portal do Azure online. O nome do grupo de recursos é o mesmo nome do seu cluster.

Atualmente, o recurso `Microsoft.ContainerService/openShiftManagedClusters` que é criado quando você cria o cluster está oculto no portal do Azure. Na exibição `Resource group`, marque `Show hidden types` para exibir o grupo de recursos.

![Captura de tela da caixa de seleção de tipo oculto](./media/aro-portal-hidden-type.png)

Excluir o grupo de recursos excluirá todos os recursos relacionados criados com um cluster do Red Hat OpenShift no Azure.

## <a name="next-steps"></a>Próximas etapas

Nesta parte do tutorial, você aprendeu a:
> [!div class="checklist"]
> * Excluir um cluster do Red Hat OpenShift no Azure

Saiba mais sobre como usar o OpenShift com a [documentação oficial do Red Hat OpenShift](https://docs.openshift.com/aro/welcome/index.html)
