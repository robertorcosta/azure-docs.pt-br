---
title: Console Serial Azure | Microsoft Docs
description: O Console Serial Azure permite que você se conecte à Sua VM quando SSH ou RDP não estiverem disponíveis.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267008"
---
# <a name="azure-serial-console"></a>Azure Serial Console

O Console Serial no portal Azure fornece acesso a um console baseado em texto para máquinas virtuais (VMs) e instâncias de conjunto de escala de máquinas virtuais executando o Linux ou o Windows. Esta conexão serial conecta-se à porta serial ttyS0 ou COM1 da instância de conjunto de escala de VM ou máquina virtual, fornecendo acesso independente do estado de rede ou do sistema operacional. O console serial só pode ser acessado usando o portal Azure e é permitido apenas para aqueles usuários que têm uma função de acesso de Contribuinte ou superior ao conjunto de escala sem vm ou máquina virtual.

O Console Serial funciona da mesma maneira para VMs e instâncias de conjunto de escala de máquinavirtual. Neste documento, todas as menções às VMs incluirão implicitamente instâncias de conjunto de escala de máquina virtual, a menos que seja indicado de outra forma.

> [!NOTE]
> O Console Serial está geralmente disponível em regiões globais do Azure e em visualização pública no Governo Azure. Ainda não está disponível na nuvem do Azure China.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Pré-requisitos para acessar o Console Serial Azure
Para acessar o Console Serial na instância de conjunto de escala sinuosa ou de máquina virtual, você precisará do seguinte:

- Os diagnósticos de inicialização devem ser ativados para a VM
- Uma conta de usuário que usa autenticação de senha deve existir dentro da VM. Você pode criar um usuário baseado em senha com a função [de redefinição](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) de senha da extensão de acesso à VM. Selecione **Redefinir senha** na seção **Suporte + solução de problemas**.
- A conta acessando o Console Serial deve ter [função de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) tanto para a VM quanto para a conta de armazenamento de diagnóstico de [inicialização](boot-diagnostics.md)

> [!NOTE]
> Implantações clássicas não são suportadas. A instância de conjunto de escala de vm ou de máquina virtual deve usar o modelo de implantação do Azure Resource Manager.

## <a name="get-started-with-the-serial-console"></a>Comece com o Console Serial
O console serial para VMs e conjunto de escalade máquina virtual é acessível apenas através do portal Azure:

### <a name="serial-console-for-virtual-machines"></a>Console serial para máquinas virtuais
O console serial para VMs é tão simples quanto clicar no **console Serial** dentro da seção Suporte + Solução **de problemas** no portal Azure.
  1. Abra o [portal Azure.](https://portal.azure.com)

  1. Navegue por **todos os recursos** e selecione uma Máquina Virtual. A página de visão geral da VM é aberta.

  1. Role para baixo até a seção **Support + troubleshooting** e selecione **Console serial**. Um novo painel com o console serial abre e inicia a conexão.

     ![Janela do Console Serial Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console serial para conjuntos de escala de máquinavirtual
Serial Console está disponível para conjuntos de escala de máquinavirtual, acessíveis em cada instância dentro do conjunto de escalas. Você terá que navegar até a instância individual de um conjunto de escala de máquina virtual antes de ver o botão **de console Serial.** Se o conjunto de escalada da máquina virtual não tiver diagnósticos de inicialização ativados, certifique-se de atualizar o modelo de conjunto de escala da máquina virtual para habilitar diagnósticos de inicialização e, em seguida, atualizar todas as instâncias para o novo modelo, a fim de acessar o console serial.
  1. Abra o [portal Azure.](https://portal.azure.com)

  1. Navegue por **todos os recursos** e selecione um conjunto de escala de máquina virtual. A página de visão geral do conjunto de escalas de máquina virtual é aberta.

  1. Navegue até **Instâncias**

  1. Selecione uma instância de conjunto de escala de máquina virtual

  1. Na seção **Suporte + solução de problemas,** selecione Console **Serial**. Um novo painel com o console serial abre e inicia a conexão.

     ![Conjunto de escala de máquina virtual Linux Serial Console](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Usos avançados para console serial
Além do acesso do console à Sua VM, você também pode usar o Azure Serial Console para o seguinte:
* Enviando um [comando de solicitação de sistema para sua VM](./serial-console-nmi-sysrq.md)
* Enviando uma [interrupção não mascarada para sua VM](./serial-console-nmi-sysrq.md)
* Reinicializar graciosamente [ou forçar o seu VM](./serial-console-power-options.md)


## <a name="next-steps"></a>Próximas etapas
A documentação adicional do Console Serial está disponível na barra lateral.
- Mais informações estão disponíveis para [Serial Console para VMs Linux](./serial-console-linux.md).
- Mais informações estão disponíveis para [console serial para VMs Windows](./serial-console-windows.md).
