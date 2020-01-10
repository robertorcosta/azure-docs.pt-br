---
title: Implantar hosts dedicados do Azure usando o portal do Azure
description: Implante VMs em hosts dedicados usando o portal do Azure.
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: c8e2ac929b3285b0ba122928485b423e34dc8f4f
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75835125"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Implantar VMs em hosts dedicados usando o portal

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMS). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Criar uma VM

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Ubuntu Server 16.04 LTS** por Canonical, escolha **Criar**.
1. Na guia **noções básicas** , em **detalhes do projeto**, verifique se a assinatura correta está selecionada e, em seguida, selecione *MyDedicatedHostsRG* como o **grupo de recursos**. 
1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual** e escolha *Leste dos EUA* para **Localização**.
1. Em **Opções de disponibilidade** selecionar **zona de disponibilidade**, selecione *1* na lista suspensa.
1. Para o tamanho, selecione **alterar tamanho**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **Standard E2 v3**. Talvez seja necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Em **Conta de administrador**, selecione **Chave pública SSH**, digite seu nome de usuário e, em seguida, cole sua chave pública na caixa de texto. Remova os espaços em branco à esquerda ou à direita em sua chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. Em **regras de porta de entrada** > **portas de entrada públicas**, escolha **permitir portas selecionadas** e, em seguida, selecione **SSH (22)** na lista suspensa. 
1. Na parte superior da página, selecione a guia **avançado** e, na seção **host** , selecione *myhost* Group para o **grupo de hosts** e *myhost* para o **host**. 
    ![selecionar grupo de hosts e host](./media/dedicated-hosts-portal/advanced.png)
1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.
1. Quando você vir a mensagem a validação foi aprovada, selecione **criar**.

Levará alguns minutos para que sua VM seja implantada.

## <a name="next-steps"></a>Próximos passos

- Para obter mais informações, consulte a visão geral dos [hosts dedicados](dedicated-hosts.md) .

- Há um modelo de exemplo, encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter máxima resiliência em uma região.

- Você também pode implantar um host dedicado usando o [CLI do Azure](dedicated-hosts-cli.md).



