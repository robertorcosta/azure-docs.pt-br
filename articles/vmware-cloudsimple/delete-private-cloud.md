---
title: Excluir uma nuvem privada da AVS (soluções VMware do Azure)
description: Descreve como excluir uma nuvem privada de AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 77cbfb19c3861bac517142f7491e6b1a5fb4ca27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024748"
---
# <a name="delete-an-avs-private-cloud"></a>Excluir uma nuvem privada de AVS

A AVS fornece a flexibilidade para excluir uma nuvem privada de AVS. Uma nuvem privada de AVS consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nós. Quando você exclui uma nuvem privada de AVS, todos os clusters serão excluídos.

## <a name="before-you-begin"></a>Antes de começar

A exclusão de uma nuvem privada de AVS exclui toda a nuvem privada da AVS. Todos os componentes da nuvem privada da AVS serão excluídos. Se você quiser manter qualquer um dos dados, certifique-se de ter feito backup dos dados no armazenamento local ou no armazenamento do Azure.

Os componentes de uma nuvem privada de AVS incluem:

* Nós da AVS
* Máquinas virtuais
* VLANs/sub-redes
* Todos os dados de usuário armazenados na nuvem privada da AVS
* Todos os anexos de regra de firewall para uma VLAN/sub-rede

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-an-avs-private-cloud"></a>Excluir uma nuvem privada de AVS

1. [Acesse o portal da AVS](access-cloudsimple-portal.md).

2. Abra a página **recursos** .

3. Clique na nuvem privada da AVS que você deseja excluir

4. Na página Resumo, clique em **excluir**.

    ![Excluir a nuvem privada de AVS](media/delete-private-cloud.png)

5. Na página confirmação, insira o nome da nuvem privada da AVS e clique em **excluir**. 

    ![Excluir a nuvem privada de AVS-confirmar](media/delete-private-cloud-confirm.png)

A nuvem privada da AVS é marcada para exclusão. O processo de exclusão é iniciado após três horas e exclui a nuvem privada da AVS.

> [!CAUTION]
> Os nós devem ser excluídos após a exclusão da nuvem privada da AVS. A medição de nós continuará até que os nós sejam excluídos da sua assinatura.

## <a name="next-steps"></a>Próximos passos

* [Excluir nós](delete-nodes.md)
