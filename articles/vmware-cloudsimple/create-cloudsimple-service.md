---
title: Solução Azure VMware by CloudSimple - Crie serviço CloudSimple
description: Descreve como criar o serviço CloudSimple no portal Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024816"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Crie a solução Azure VMware pelo serviço CloudSimple

Para começar com a Solução Azure VMware pela CloudSimple, crie o serviço Azure VMware pelo cloudsimple no portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco /28 CIDR para a sub-rede gateway. Uma sub-rede gateway é necessária por serviço CloudSimple e é exclusiva da região em que é criada. A sub-rede gateway é usada para serviços de rede de borda e requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais Do Zure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no [portal Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
    ![Pesquisar serviço cloudsimple](media/create-cloudsimple-service-search.png)
3. Selecione **Serviços cloudsimple**.
4. Clique **em Adicionar** para criar um novo serviço.
    ![Adicionar serviço CloudSimple](media/create-cloudsimple-service-add.png)
5. Selecione a assinatura onde deseja criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique **em Criar novo**.
7. Digite o nome para identificar o serviço.
8. Digite o CIDR para o gateway de serviço. Especifique uma sub-rede /28 que não se sobreponha a nenhuma de suas sub-redes no local, sub-redes Azure ou sub-redes CloudSimple planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço CloudSimple](media/create-cloudsimple-service.png)
9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [prover os nódulos](create-nodes.md)
* Aprenda a [criar uma nuvem privada](create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
