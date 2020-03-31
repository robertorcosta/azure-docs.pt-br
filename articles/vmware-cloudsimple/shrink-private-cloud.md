---
title: Shrink Azure VMware Solution by CloudSimple Private Cloud
description: Descreve como encolher uma nuvem privada cloudsimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014259"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Encolher uma nuvem de nuvem simples nuvem privada

O CloudSimple oferece flexibilidade para encolher dinamicamente uma Nuvem Privada.  Uma Nuvem Privada consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nódulos. Ao encolher uma nuvem privada, você remove um nó do cluster existente ou exclui um cluster inteiro. 

## <a name="before-you-begin"></a>Antes de começar

As seguintes condições devem ser atendidas para o encolhimento de uma Nuvem Privada.  O cluster de gerenciamento (primeiro cluster) criado quando uma nuvem privada foi criada não pode ser excluído.

* Um cluster vSphere deve ter três nódulos.  Um cluster com apenas três nós não pode ser encolhido.
* O armazenamento total consumido não deve exceder a capacidade total após o encolhimento do cluster.
* Verifique se alguma regra do DRS (Distributed Resource Scheduler, agendamento de recursos distribuídos) impede o vMotion de uma máquina virtual.  Se as regras estiverem presentes, desabilite ou exclua as regras.  As regras do DRS incluem máquina virtual para hospedar regras de afinidade.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Reduzir uma nuvem privada

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos.**

3. Clique na Nuvem Privada que você deseja encolher

4. Na página de resumo, clique em **'Encolher**'

    ![Encolher nuvem privada](media/shrink-private-cloud.png)

5. Selecione o cluster que deseja encolher ou excluir. 

    ![Encolher nuvem privada - selecionar cluster](media/shrink-private-cloud-select-cluster.png)

6. Selecione **Remover um nó** ou Excluir todo o **cluster**. 

7. Verifique a capacidade do cluster

8. Clique **em Enviar** para encolher a Nuvem Privada.

O psiquiatra da Nuvem Privada começa.  Você pode monitorar o progresso nas tarefas.  O processo de encolhimento pode levar algumas horas dependendo dos dados, que precisam ser ressincronizados no vSAN.

> [!NOTE]
> 1. Se você encolher uma nuvem privada excluindo o último ou o único cluster no data center, o data center não será excluído.
> 2. Se ocorrer qualquer violação da regra drs, o nó não será removido do cluster e a descrição da tarefa mostra que a remoção de um nó violará as regras do DRS no cluster.    


## <a name="next-steps"></a>Próximas etapas

* [Consumir VMs do VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
