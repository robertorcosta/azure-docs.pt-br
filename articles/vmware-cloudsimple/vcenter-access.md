---
title: Solução Azure VMware por CloudSimple - Cliente access vSphere
description: Descreve como acessar o vCenter da sua Nuvem Privada.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022657"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Acesse seu portal private cloud vCenter

Você pode lançar seu portal Private Cloud vCenter a partir do portal Azure ou do portal CloudSimple.  O portal vCenter permite gerenciar a infra-estrutura VMware em sua Nuvem Privada.

## <a name="before-you-begin"></a>Antes de começar

A conexão de rede deve ser estabelecida e a resolução do nome DNS deve ser ativada para acessar o portal vCenter.  Você pode estabelecer conexão de rede com sua Nuvem Privada usando qualquer uma das opções abaixo.

* [Conecte-se de on-premises ao CloudSimple usando expressroute](on-premises-connection.md)
* [Configure uma conexão VPN com sua Nuvem Privada Cloud Simple](set-up-vpn.md)

Para configurar a resolução de nomes DNS de seus componentes de infra-estrutura Private Cloud VMware, consulte [Configurar dNS para resolução de nomes para acesso ao Private Cloud vCenter a partir de estações de trabalho no local](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Faça login no portal Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Acesse o portal vCenter do Azure

Você pode lançar o portal vCenter do seu Private Cloud a partir do portal Azure.

1. Selecione **Todos os serviços**.

2. Procure por **Serviços CloudSimple**.

3. Selecione o serviço CloudSimple da sua Nuvem Privada ao qual deseja se conectar.

4. Na **página Visão geral,** clique **em Exibir nuvens privadas vMware**

    ![Visão geral do serviço CloudSimple](media/cloudsimple-service-overview.png)

5. Selecione a Nuvem Privada na lista de Nuvens Privadas e clique em **Iniciar o cliente vSphere**.

    ![Inicie o cliente vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Acesse o vCenter do portal CloudSimple

Você pode lançar o portal vCenter do seu Private Cloud a partir do portal CloudSimple.

1. Acesse seu [portal CloudSimple](access-cloudsimple-portal.md).

2. A partir dos **Recursos** selecione a Nuvem Privada, que você deseja acessar e clique em **Launch vSphere Client**.

    ![Iniciar vSphere Client - Recursos](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Você também pode iniciar o portal vCenter a partir da tela de resumo da sua Nuvem Privada.

    ![Iniciar cliente vSphere - Resumo](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Próximas etapas

* [Crie e gerencie VLANs/sub-redes para suas nuvens privadas](create-vlan-subnet.md)
* [Modelo de permissão cloud simple private cloud do VMware vCenter](learn-private-cloud-permissions.md)