---
title: Opções de energia do console serial azure | Microsoft Docs
description: Opções de energia vm disponíveis no Console Serial Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: 11c2549d7282bae5654ede1ac34d63a43cd0f059
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451191"
---
# <a name="power-options-available-from-the-azure-serial-console"></a>Opções de energia disponíveis no Console Serial Do Azure

O Azure Serial Console fornece várias ferramentas poderosas para o gerenciamento de energia em seu conjunto de escala de VM ou máquina virtual. Essas opções de gerenciamento de energia podem ser confusas para alguns, por isso esta é uma visão geral de cada ferramenta e seu caso de uso pretendido.

Recurso do console serial | Descrição | Caso de uso
:----------------------|:------------|:---------
Reiniciar a VM | Uma reinicialização graciosa da instância de conjunto de escala supérflua ou de máquina virtual. Esta operação é o mesmo que chamar o recurso de reinicialização disponível na página Visão Geral. | Na maioria dos casos, essa opção deve ser sua primeira ferramenta na tentativa de reiniciar sua VM. Sua conexão serial console sofrerá uma breve interrupção e será retomada automaticamente assim que a VM for reiniciada.
Reiniciar VM | Um ciclo de energia forte da sua escala de VM ou máquina virtual definida pela plataforma Azure. | Esta opção é usada para reiniciar imediatamente seu sistema operacional, independentemente do estado atual. Uma vez que esta operação não é graciosa, há um risco de perda de dados ou corrupção. Não há interrupção na conexão Serial Console, o que pode ser útil para enviar comandos no início do tempo de inicialização (por exemplo, chegar ao GRUB em um VM Linux ou modo de segurança em uma VM windows).
SysRq - Reinicialização (b) | Um pedido do sistema para forçar um convidado a reiniciar. | Esse recurso só é aplicável aos sistemas operacionais Linux e requer que [o SysRq seja habilitado](./serial-console-nmi-sysrq.md#system-request-sysrq) no sistema operacional. Se o sistema operacional estiver configurado corretamente para o SysRq, este comando fará com que o sistema operacional seja reiniciado.
NMI (Interrupção não mascarada) | Um comando de interrupção, que será entregue ao sistema operacional | Esta operação está disponível para VMs [Windows](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) e [Linux,](./serial-console-nmi-sysrq.md#non-maskable-interrupt-nmi) e requer que o NMI seja ativado. O envio de um NMI normalmente fará com que seu sistema operacional se desemmente. Você pode configurar seu sistema operacional para criar um arquivo de despejo e, em seguida, reiniciar ao receber o NMI, que pode ser útil na depuração de baixo nível.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Console Serial Azure para VMs Linux](./serial-console-linux.md)
* Saiba mais sobre o [Console Serial Azure para VMs Windows](./serial-console-windows.md)