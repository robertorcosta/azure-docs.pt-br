---
title: Implantar hosts dedicados do Azure usando o portal do Azure
description: Implante VMs em hosts dedicados usando o portal do Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 195a19ef881f235ad8e42f23b53da9e667ef88d0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "79086774"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Implantar VMs em hosts dedicados usando o portal

Este artigo orienta como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMs). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Ubuntu Server 16.04 LTS** por Canonical, escolha **Criar**.
1. Na guia **noções básicas** , em **detalhes do projeto**, verifique se a assinatura correta está selecionada e, em seguida, selecione *MyDedicatedHostsRG* como o **grupo de recursos**. 
1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual** e escolha *Leste dos EUA* para **Localização**.
1. Em **Opções de disponibilidade** selecionar **zona de disponibilidade**, selecione *1* na lista suspensa.
1. Para o tamanho, selecione **alterar tamanho**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **Standard E2 v3**. Talvez seja necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Em **Conta de administrador**, selecione **Chave pública SSH**, digite seu nome de usuário e, em seguida, cole sua chave pública na caixa de texto. Remova os espaços em branco à esquerda ou à direita em sua chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. Em **regras de porta de entrada**  >  **portas públicas de entrada**, escolha **permitir portas selecionadas** e, em seguida, selecione **SSH (22)** na lista suspensa. 
1. Na parte superior da página, selecione a guia **avançado** e, na seção **host** , selecione *myhost* Group para o **grupo de hosts** e *myhost* para o **host**. 
    ![Selecionar grupo de hosts e host](./media/dedicated-hosts-portal/advanced.png)
1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.
1. Quando você vir a mensagem a validação foi aprovada, selecione **criar**.

Levará alguns minutos para que sua VM seja implantada.

## <a name="add-an-existing-vm"></a>Adicionar uma VM existente 

Você pode adicionar uma VM existente a um host dedicado, mas a VM deve primeiro ser Stop\Deallocated. Antes de mover uma VM para um host dedicado, verifique se a configuração da VM tem suporte:

- O tamanho da VM deve estar na mesma família de tamanho que o host dedicado. Por exemplo, se o host dedicado for DSv3, o tamanho da VM poderá ser Standard_D4s_v3, mas não poderá ser um Standard_A4_v2. 
- A VM precisa estar localizada na mesma região que o host dedicado.
- A VM não pode fazer parte de um grupo de posicionamento de proximidade. Remova a VM do grupo de posicionamento de proximidade antes de movê-la para um host dedicado. Para obter mais informações, consulte [mover uma VM para fora de um grupo de posicionamento de proximidade](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- A VM não pode estar em um conjunto de disponibilidade.
- Se a VM estiver em uma zona de disponibilidade, ela deverá ser a mesma zona de disponibilidade que o grupo de hosts. As configurações de zona de disponibilidade para a VM e o grupo de hosts devem corresponder.

Mova a VM para um host dedicado usando o [portal](https://portal.azure.com).

1. Abra a página da VM.
1. Selecione **parar** para STOP\DEALLOCATE a VM.
1. Selecione **configuração** no menu à esquerda.
1. Selecione um grupo de hosts e um host nos menus suspensos.
1. Quando terminar, selecione **salvar** na parte superior da página.
1. Depois que a VM tiver sido adicionada ao host, selecione **visão geral** no menu à esquerda.
1. Na parte superior da página, selecione **Iniciar** para reiniciar a VM.

## <a name="next-steps"></a>Próximas etapas

- Para mais informações, consulte a visão geral de [hosts dedicados](dedicated-hosts.md).

- Há um exemplo de modelo, [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter resiliência máxima em uma região.

- Você também pode implantar um host dedicado usando o [CLI do Azure](dedicated-hosts-cli.md).



