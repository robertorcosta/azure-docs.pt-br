---
title: Soluções VMware do Azure (AVS) – criar serviço AVS
description: Descreve como criar o serviço AVS no portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024816"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Criar o serviço de soluções do Azure VMware (AVS)

Para começar a usar as soluções do Azure VMware (AVS), crie o serviço de soluções do Azure VMware (AVS) no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR/28 para a sub-rede de gateway. Uma sub-rede de gateway é necessária por serviço AVS e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada para serviços de rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunique com o ambiente da AVS. As redes que se comunicam com o AVS incluem redes locais e redes virtuais do Azure.

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no [portal do Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure **serviços de sincronização automática**.
    ![de pesquisa de serviço AVS](media/create-cloudsimple-service-search.png)
3. Selecione **serviços de sincronização automática**.
4. Clique em **Adicionar** para criar um novo serviço.
    ![adicionar serviço AVS](media/create-cloudsimple-service-add.png)
5. Selecione a assinatura na qual você deseja criar o serviço AVS.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o gateway de serviço. Especifique uma sub-rede/28 que não se sobreponha a nenhuma de suas sub-redes locais, sub-redes do Azure ou sub-redes de AVS planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço AVS](media/create-cloudsimple-service.png)
9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="next-steps"></a>Próximos passos

* Saiba como [provisionar nós](create-nodes.md)
* Saiba como [criar uma nuvem privada de AVS](create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada AVS](quickstart-create-private-cloud.md)
