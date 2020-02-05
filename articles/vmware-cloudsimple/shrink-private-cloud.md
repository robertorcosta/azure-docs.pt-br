---
title: Reduzir a nuvem privada do Azure VMware Solutions (AVS)
description: Descreve como reduzir uma nuvem privada de AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014259"
---
# <a name="shrink-an-avs-private-cloud"></a>Reduzir uma nuvem privada de AVS

A AVS fornece a flexibilidade para reduzir dinamicamente uma nuvem privada de AVS. Uma nuvem privada de AVS consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nós. Ao reduzir uma nuvem privada de AVS, você remove um nó do cluster existente ou exclui um cluster inteiro. 

## <a name="before-you-begin"></a>Antes de começar

As condições a seguir devem ser atendidas antes da redução de uma nuvem privada de AVS. O cluster de gerenciamento (primeiro cluster) é criado quando a nuvem privada da AVS foi criada. Ele não pode ser excluído.

* Um cluster vSphere deve ter três nós. Um cluster com apenas três nós não pode ser reduzido.
* O armazenamento total consumido não deve exceder a capacidade total depois de reduzir o cluster.
* Verifique se alguma regra do Distributed Resource Scheduler (DRS) impede o vMotion de uma máquina virtual. Se houver regras, desabilite ou exclua as regras. As regras do DRS incluem a máquina virtual para hospedar regras de afinidade.


## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Reduzindo uma nuvem privada de AVS

1. [Acesse o portal da AVS](access-cloudsimple-portal.md).

2. Abra a página **recursos** .

3. Clique na nuvem privada da AVS que você deseja reduzir

4. Na página Resumo, clique em **reduzir**.

    ![Reduzir a nuvem privada da AVS](media/shrink-private-cloud.png)

5. Selecione o cluster que você deseja reduzir ou excluir. 

    ![Reduzir a nuvem privada de AVS-selecionar cluster](media/shrink-private-cloud-select-cluster.png)

6. Selecione **remover um nó** ou **excluir o cluster inteiro**. 

7. Verificar a capacidade do cluster

8. Clique em **Enviar** para reduzir a nuvem privada de AVS.

A redução da nuvem privada da AVS é iniciada. Você pode monitorar o progresso em tarefas. O processo de redução pode levar algumas horas, dependendo dos dados, que precisam ser ressincronizados no vSAN.

> [!NOTE]
> 1. Se você reduzir uma nuvem privada excluindo o último ou o único cluster no datacenter, o datacenter não será excluído.
> 2. Se ocorrer uma violação de regra de DRS, o nó não será removido do cluster e a descrição da tarefa mostrará que a remoção de um nó violará as regras do DRS no cluster.    


## <a name="next-steps"></a>Próximos passos

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre as [nuvens privadas da AVS](cloudsimple-private-cloud.md)
