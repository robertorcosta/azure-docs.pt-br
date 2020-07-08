---
title: Conectar-se às suas máquinas virtuais por meio de um navegador – Azure | Microsoft Docs
description: Saiba como se conectar às suas máquinas virtuais por meio de um navegador.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b1e50a15673199a4bf95956ae66e6b58d6d41d0e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483730"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Conectar-se às suas máquinas virtuais por meio de um navegador 

O DevTest Labs integra-se à [bastiões do Azure](https://docs.microsoft.com/azure/bastion/), que permite que você se conecte às suas máquinas virtuais por meio de um navegador. Para obter informações sobre como habilitar esse recurso no DevTest Labs, consulte [habilitar conexão do navegador em máquinas virtuais do laboratório](enable-browser-connection-lab-virtual-machines.md).

Quando o *navegador conectar* estiver habilitado, os usuários do laboratório poderão acessar as máquinas virtuais por meio de um navegador.  

## <a name="create-a-lab-virtual-machine"></a>Criar uma máquina virtual do laboratório

Primeiro, você precisa criar a máquina virtual do laboratório em uma rede virtual que tenha a bastiões configurada nela. Selecione a segunda **sub-rede** que você criou, não o AzureBastionSubnet. Você pode selecionar uma rede virtual durante a criação da máquina virtual acessando a guia **Configurações avançadas** .

![Criar máquina virtual](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Iniciar máquina virtual em um navegador

Depois que a máquina virtual for criada, você poderá iniciá-la em um navegador clicando no botão *conectar do navegador* e inserindo seu nome de usuário e senha para o computador.  

![Iniciar em um navegador](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Próximas etapas

[Adicionar uma VM a um laboratório no Azure DevTest Labs](devtest-lab-add-vm.md)
