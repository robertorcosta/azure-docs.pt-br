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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549090"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Habilite a conexão do navegador em máquinas virtuais de laboratório 

O DevTest Labs [integra-se ao Azure Bastion](https://docs.microsoft.com/azure/bastion/), que permite que você se conecte às suas máquinas virtuais através de um navegador. Primeiro você precisa ativar a conexão do navegador em máquinas virtuais de laboratório.

Como proprietário de um laboratório, você pode habilitar o acesso a todas as máquinas virtuais de laboratório através de um navegador. Não é preciso um cliente, agente ou software adicional. O Azure Bastion fornece conectividade RDP/SSH segura e perfeita para suas máquinas virtuais diretamente no portal Azure via TLS. Quando você se conecta via Azure Bastion, suas máquinas virtuais não precisam de um endereço IP público. Para obter mais informações, veja [o que é O Bastião do Azure?](../bastion/bastion-overview.md)

> [!NOTE]
> Ativar a conexão do navegador em máquinas virtuais de laboratório está em pré-visualização.

Este artigo mostra como ativar a conexão do navegador em máquinas virtuais de laboratório.

## <a name="prerequisites"></a>Pré-requisitos 
Ou implante um host Bastion na rede virtual **(OR)** do seu laboratório existente conecte seu laboratório com um VNet configurado por Bastion. 

Para saber como implantar um host Bastion em um VNet, consulte [Criar um host Azure Bastion (Preview)](../bastion/bastion-create-host-portal.md). Ao criar o host Bastion, selecione a rede virtual do laboratório. 

Para saber como conectar seu laboratório com um VNet configurado pelo Bastion, consulte [Configurar uma rede virtual no Azure DevTest Labs](devtest-lab-configure-vnet.md). Selecione o VNet que tem o host Bastion implantado e a **AzureBastionSubnet** nele. Aqui estão as etapas detalhadas: 

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione **Todos os serviços** no menu de navegação à esquerda. 
1. Selecione **DevTest Labs** da lista. 
1. Na lista de laboratórios, selecione *seu laboratório.* 

    > [!NOTE]
    > Azure Bastion está atualmente em pré-visualização. Limita-se às seguintes regiões: Oeste dos EUA, Leste dos EUA, Europa Ocidental, Centro-Sul dos EUA, Austrália Oriental e Japão Leste. Então, crie um laboratório em uma dessas regiões se seu laboratório não estiver em uma delas. 
1. Selecione **Configuração e políticas** na seção **Configurações** no menu esquerdo. 
1. Selecione **redes virtuais**.
1. Selecione **Adicionar** na barra de ferramentas. 
1. Selecione o **VNet** que tem o host Bastion implantado. 
1. Selecione a **sub-rede: AzureBastionSubnet**. 

    ![Sub-rede](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Selecione Usar na opção **de criação de máquinas virtuais.** 
1. Selecione **Salvar** na barra de ferramentas. 
1. Se você tem um VNet antigo para o laboratório, remova-o selecionando **...*  e **Remover**. 

## <a name="enable-browser-connection"></a>Habilite a conexão do navegador 

Uma vez que você tenha um Bastion configurado VNet dentro do laboratório, como um proprietário de laboratório, você pode habilitar a conexão do navegador em máquinas virtuais de laboratório.

Para permitir que o navegador se conecte em máquinas virtuais de laboratório, siga estas etapas:

1. No portal Azure, navegue até *seu laboratório.*
1. Selecione **Configuração e políticas**.
1. Em **Configurações,** selecione **Conexão do navegador (Visualização)**.

![Habilite a conexão do navegador](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Próximas etapas
Veja o artigo a seguir para saber como se conectar às suas VMs usando um navegador: [Conecte-se às suas máquinas virtuais através de um navegador](connect-virtual-machine-through-browser.md)