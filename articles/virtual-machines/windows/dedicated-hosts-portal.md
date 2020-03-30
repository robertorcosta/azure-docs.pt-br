---
title: Implantar hosts dedicados do Azure usando o portal
description: Implante VMs para hosts dedicados usando o portal.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.openlocfilehash: 3d014014b540e5ea5959483427dec4b239ceaf7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476783"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Implantar VMs para hosts dedicados usando o portal

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) ao Azure para hospedar suas máquinas virtuais (VMs). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Criar uma máquina virtual

1. Escolha **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na página **Nova,** em **Popular,** selecione **O Datacenter do Windows Server 2016**.
1. Na guia **Noções** Básicas, em **Detalhes do Projeto,** certifique-se de que a assinatura correta está selecionada e, em seguida, selecione *myDedicatedHostsRG* como o **grupo Recurso**. 
1. Em **Detalhes da instância**, digite *myVM* para o **Nome da máquina virtual** e escolha *Leste dos EUA* para **Localização**.
1. Em **Opções de Disponibilidade** **selecione Zona disponibilidade,** selecione *1* na queda.
1. Para o tamanho, selecione **Alterar tamanho**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **o Standard E2s v3**. Você pode precisar limpar o filtro para ver todos os tamanhos disponíveis.
1. Em **conta de administrador,** forneça um nome de usuário, como *azureuser* e uma senha. A senha deve ter no mínimo 12 caracteres e atender a [requisitos de complexidade definidos](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. Em **Regras de portas de entrada**, escolha **Permitir portas selecionadas** e, em seguida, selecione **RDP (3389)** na lista suspensa.
1. Na parte superior da página, selecione a guia **Avançado** e na seção **Hospedar,** selecione o **grupo** *myHostGroup* for Host e *myHost* for the **Host**. 
    ![Selecione grupo de host e host](./media/dedicated-hosts-portal/advanced.png)
1. Deixe os padrões restantes e, em seguida, selecione o botão **Examinar + criar** na parte inferior da página.
1. Quando você vir a mensagem que a validação passou, selecione **Criar**.

## <a name="add-an-existing-vm"></a>Adicione uma VM existente 

Você pode adicionar uma VM existente a um host dedicado, mas a VM deve primeiro ser Stop\Deallocated. Antes de mover uma VM para um host dedicado, certifique-se de que a configuração da VM seja suportada:

- O tamanho da VM deve estar na mesma família do tamanho do host dedicado. Por exemplo, se o seu host dedicado for DSv3, então o tamanho da VM pode ser Standard_D4s_v3, mas não poderia ser um Standard_A4_v2. 
- A VM precisa estar localizada na mesma região do host dedicado.
- A VM não pode fazer parte de um grupo de colocação de proximidade. Remova a VM do grupo de colocação de proximidade antes de movê-la para um host dedicado. Para obter mais informações, consulte [Mover uma VM de um grupo de colocação de proximidade](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- A VM não pode estar em um conjunto de disponibilidade.
- Se a VM estiver em uma zona de disponibilidade, ela deve ser a mesma zona de disponibilidade do grupo de host. As configurações de zona de disponibilidade para a VM e o grupo de host devem coincidir.

Mova a VM para um host dedicado usando o [portal](https://portal.azure.com).

1. Abra a página para a VM.
1. Selecione **Parar** para stop\dealocar o VM.
1. Selecione **Configuração** no menu esquerdo.
1. Selecione um grupo de host e um host nos menus suspensos.
1. Quando terminar, **selecione Salvar** no topo da página.
1. Depois que a VM tiver sido adicionada ao host, selecione **Visão geral** no menu esquerdo.
1. Na parte superior da página, **selecione Iniciar** para reiniciar a VM.

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações, consulte a visão geral dos [hosts dedicados.](dedicated-hosts.md) 

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas quanto domínios de falha para máxima resiliência em uma região.

- Você também pode implantar um host dedicado usando [o Azure PowerShell](dedicated-hosts-powershell.md).
