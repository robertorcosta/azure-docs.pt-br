---
title: Habilite a conexão do navegador em máquinas virtuais Azure DevTest Labs | Microsoft Docs
description: O DevTest Labs agora se integra ao Azure Bastion, como proprietário do laboratório, você pode habilitar o acesso a todas as máquinas virtuais de laboratório através de um navegador.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970747"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Habilite a conexão do navegador em máquinas virtuais de laboratório 
O DevTest Labs [integra-se ao Azure Bastion](https://docs.microsoft.com/azure/bastion/), que permite que você se conecte às suas máquinas virtuais através de um navegador. Primeiro você precisa ativar a conexão do navegador em máquinas virtuais de laboratório.

Como proprietário de um laboratório, você pode habilitar o acesso a todas as máquinas virtuais de laboratório através de um navegador. Não é preciso um cliente, agente ou software adicional. O Azure Bastion fornece conectividade RDP/SSH segura e perfeita para suas máquinas virtuais diretamente no portal Azure sobre SSL. Quando você se conecta via Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, veja [o que é O Bastião do Azure?](../bastion/bastion-overview.md)


Este artigo mostra como ativar a conexão do navegador em máquinas virtuais de laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
Ou implante um host Bastion na rede virtual **(OR)** do seu laboratório existente conecte seu laboratório com uma rede virtual configurada pelo Bastion. 

Para saber como implantar um host Bastion em uma rede virtual, consulte [Criar um host Azure Bastion](../bastion/bastion-create-host-portal.md). Ao criar o host Bastion, selecione a rede virtual do laboratório. 

Primeiro, você precisa criar uma segunda sub-rede na rede virtual Bastion porque o AzureBastionSubnet não permite a criação de recursos não-Bastion nela. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Criar uma segunda sub-rede na rede virtual Bastion
Você não pode criar VMs de laboratório em uma sub-rede do Azure Bastion. Crie outra sub-rede dentro da rede virtual Bastion, conforme mostrado na imagem a seguir:

![Segunda sub-rede na rede virtual Azure Bastion](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Habilite a criação de VM na sub-rede
Agora, habilite a criação de VMs nesta sub-rede seguindo estas etapas: 

1. Faça login no [portal Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Na lista de laboratórios, selecione *seu laboratório.* 

    > [!NOTE]
    > O Azure Bastion está agora disponível nas seguintes regiões: Oeste dos EUA, Leste dos EUA, Europa Ocidental, Centro-Sul dos EUA, Austrália Oriental e Japão Leste. Então, crie um laboratório em uma dessas regiões se seu laboratório não estiver em uma delas. 
    
1. Selecione **Configuração e políticas** na seção **Configurações** no menu esquerdo. 
1. Selecione **redes virtuais**.
1. Selecione **Adicionar** na barra de ferramentas. 
1. Selecione a **rede virtual** que tem o host Bastion implantado. 
1. Selecione a sub-rede para VMs, não **a AzureBastionSubnet**, a outra que você criou anteriormente. Feche a página e reabra-a se você não ver a sub-rede na lista na parte inferior. 

    ![Habilite a criação de VM na sub-rede](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Selecione Usar na opção **de criação de máquinas virtuais.** 
1. Selecione **Salvar** na barra de ferramentas. 
1. Se você tem uma rede virtual antiga para o laboratório, remova-a selecionando **...*  e **Remover**. 

## <a name="enable-browser-connection"></a>Habilite a conexão do navegador 

Uma vez que você tenha uma rede virtual configurada bastião dentro do laboratório, como proprietário de laboratório, você pode habilitar a conexão do navegador em máquinas virtuais de laboratório.

Para permitir que o navegador se conecte em máquinas virtuais de laboratório, siga estas etapas:

1. No portal Azure, navegue até *seu laboratório.*
1. Selecione **Configuração e políticas**.
1. Em **Configurações,** selecione **Conexão do navegador**. Se você não ver essa opção, feche a página **De configuração de políticas** e reabra-a. 

    ![Habilite a conexão do navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Próximas etapas
Veja o artigo a seguir para saber como se conectar às suas VMs usando um navegador: [Conecte-se às suas máquinas virtuais através de um navegador](connect-virtual-machine-through-browser.md)
