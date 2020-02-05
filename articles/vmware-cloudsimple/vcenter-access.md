---
title: Soluções VMware do Azure (AVS)-cliente vSphere de acesso
description: Descreve como acessar o vCenter de sua nuvem privada da AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ad018ea89b194d42ab1867a0569725c4c3680f7d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022657"
---
# <a name="access-your-avs-private-cloud-vcenter-portal"></a>Acessar seu portal de vCenter privado de nuvem privada da AVS

Você pode iniciar seu portal do vCenter de nuvem privada da AVS no portal portal do Azure ou AVS. o portal do vCenter permite que você gerencie a infraestrutura do VMware na sua nuvem privada de AVS.

## <a name="before-you-begin"></a>Antes de começar

A conexão de rede deve ser estabelecida e a resolução de nomes DNS deve ser habilitada para acessar o portal do vCenter. Você pode estabelecer a conexão de rede com sua nuvem privada da AVS usando qualquer uma das opções a seguir.

* [Conectar-se do local para a AVS usando o ExpressRoute](on-premises-connection.md)
* [Configurar uma conexão VPN para sua nuvem privada da AVS](set-up-vpn.md)

Para configurar a resolução de nomes DNS de seus componentes da sua nuvem privada do VMware Infrastructure, consulte [Configurar o DNS para resolução de nomes para acesso ao vCenter de nuvem privada da AVS de estações de trabalho locais](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Acesse o vCenter de portal do Azure

Você pode iniciar o portal do vCenter da sua nuvem privada da AVS a partir de portal do Azure.

1. Selecione **Todos os serviços**.

2. Procure **serviços de sincronização automática**.

3. Selecione o serviço AVS da sua nuvem privada de AVS à qual você deseja se conectar.

4. Na página **visão geral** , clique em **Exibir nuvens do VMware AVS privadas**

    ![Visão geral do serviço AVS](media/cloudsimple-service-overview.png)

5. Selecione a nuvem privada da AVS na lista de nuvens privadas da AVS e clique em **Iniciar vSphere cliente**.

    ![Iniciar cliente do vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-avs-portal"></a>Acessar o vCenter no portal da AVS

Você pode iniciar o portal do vCenter da sua nuvem privada da AVS no portal da AVS.

1. Acesse seu [portal da AVS](access-cloudsimple-portal.md).

2. Nos **recursos** , selecione a nuvem privada da AVS que você deseja acessar e clique em **iniciar o cliente do vSphere**.

    ![Iniciar vSphere cliente-recursos](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Você também pode iniciar o portal do vCenter na tela de resumo da sua nuvem privada de AVS.

    ![Iniciar vSphere cliente-Resumo](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Próximos passos

* [Criar e gerenciar VLANs/sub-redes para suas nuvens privadas da AVS](create-vlan-subnet.md)
* [Modelo de permissão de nuvem privada da AVS do VMware vCenter](learn-private-cloud-permissions.md)