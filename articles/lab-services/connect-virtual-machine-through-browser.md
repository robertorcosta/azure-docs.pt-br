---
title: Conecte-se às suas máquinas virtuais através de um navegador - Azure | Microsoft Docs
description: Aprenda a se conectar às suas máquinas virtuais através de um navegador.
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
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974268"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Conecte-se às suas máquinas virtuais através de um navegador 

O DevTest Labs [integra-se ao Azure Bastion](https://docs.microsoft.com/azure/bastion/), que permite que você se conecte às suas máquinas virtuais através de um navegador. Para obter informações sobre como habilitar esse recurso no DevTest Labs, consulte [Habilitar a conexão do navegador em máquinas virtuais de laboratório](enable-browser-connection-lab-virtual-machines.md).

Uma vez que a *conexão do Navegador* esteja ativada, os usuários de laboratório podem acessar máquinas virtuais através de um navegador.  

## <a name="create-a-lab-virtual-machine"></a>Crie uma máquina virtual de laboratório

Primeiro você precisa criar a máquina virtual de laboratório dentro de uma rede virtual que tenha Bastion configurado nela. Selecione a segunda **sub-rede** que você criou, não a AzureBastionSubnet. Você pode selecionar uma rede virtual durante a criação de máquinas virtuais indo para a guia **Configurações Avançadas.**

![Criar máquina virtual](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Inicie a máquina virtual em um navegador

Uma vez que a máquina virtual é criada, você pode lançá-la em um navegador clicando no botão *de conexão do Navegador* e digitando seu nome de usuário e senha para a máquina.  

![Lançamento em um navegador](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Próximas etapas

[Adicionar uma VM a um laboratório no Azure DevTest Labs](devtest-lab-add-vm.md)
