---
title: Implantar hosts dedicados do Azure usando o portal do Azure
description: Implante VMs em hosts dedicados usando o portal do Azure.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/26/2019
ms.author: cynthn
ms.openlocfilehash: aa19c343e003bf1cd55e3d12b18e595113a7189e
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833936"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Implantar VMs em hosts dedicados usando o portal

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMS). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Criar uma VM

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na página **Novo**, em **Popular**, selecione **Datacenter do Windows Server 2016**.
1. Na guia **noções básicas** , em **detalhes do projeto**, verifique se a assinatura correta está selecionada e, em seguida, selecione *MyDedicatedHostsRG* como o **grupo de recursos**. 
1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual** e escolha *Leste dos EUA* para **Localização**.
1. Em **Opções de disponibilidade** selecionar **zona de disponibilidade**, selecione *1* na lista suspensa.
1. Para o tamanho, selecione **alterar tamanho**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **Standard E2 v3**. Talvez seja necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Em **Conta de administrador**, forneça um nome de usuário, como *azureuser* e uma senha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Em **Regras de portas de entrada**, escolha **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** na lista suspensa.
1. Na parte superior da página, selecione a guia **avançado** e, na seção **host** , selecione *myhost* Group para o **grupo de hosts** e *myhost* para o **host**. 
    ![selecionar grupo de hosts e host](./media/dedicated-hosts-portal/advanced.png)
1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.
1. Quando você vir a mensagem a validação foi aprovada, selecione **criar**.


## <a name="next-steps"></a>Próximos passos

- Para obter mais informações, consulte a visão geral dos [hosts dedicados](dedicated-hosts.md) . 

- Há um modelo de exemplo, encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter máxima resiliência em uma região.

- Você também pode implantar um host dedicado usando o [Azure PowerShell](dedicated-hosts-powershell.md).