---
title: Exclua uma solução Azure VMware por CloudSimple Private Cloud
description: Descreve como excluir uma nuvem privada cloudsimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024748"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Exclua uma nuvemde nuvem simples nuvem privada

O CloudSimple oferece a flexibilidade para excluir uma Nuvem Privada.  Uma Nuvem Privada consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nódulos. Quando você excluir uma Nuvem Privada, todos os clusters serão excluídos.

## <a name="before-you-begin"></a>Antes de começar

A exclusão de uma nuvem privada exclui toda a Nuvem Privada.  Todos os componentes da Nuvem Privada serão excluídos.  Se você quiser manter qualquer um dos dados, certifique-se de fazer backup dos dados para armazenamento no local ou armazenamento Azure.

Os componentes de uma Nuvem Privada incluem:

* Nódulos cloudsimple
* Máquinas virtuais
* VLANs/sub-redes
* Todos os dados do usuário armazenados na Nuvem Privada
* Todos os anexos de regras de firewall a uma VLAN/Sub-rede

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Excluir uma nuvem privada

1. [Acesse o portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos.**

3. Clique na Nuvem Privada que deseja excluir

4. Na página de resumo, clique em **Excluir**.

    ![Excluir nuvem privada](media/delete-private-cloud.png)

5. Na página de confirmação, digite o nome da Nuvem Privada e clique em **Excluir**. 

    ![Excluir nuvem privada - confirmar](media/delete-private-cloud-confirm.png)

A Nuvem Privada está marcada para exclusão.  O processo de exclusão começa após três horas e exclui a Nuvem Privada.

> [!CAUTION]
> Os nós devem ser excluídos após a exclusão da Nuvem Privada.  A medição dos nós continuará até que os nós sejam excluídos da sua assinatura.

## <a name="next-steps"></a>Próximas etapas

* [Excluir nós](delete-nodes.md)
