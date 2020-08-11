---
title: Diagnóstico de inicialização do Azure
description: Visão geral do diagnóstico de inicialização do Azure e diagnóstico de inicialização gerenciada
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: e2ba5d909a3aa43921f52295d2f7216aac76bc32
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88067079"
---
# <a name="azure-boot-diagnostics"></a>Diagnóstico de inicialização do Azure

O diagnóstico de inicialização é um recurso de depuração para VMs (máquinas virtuais) do Azure que permite o diagnóstico de falhas de inicialização da VM. O diagnóstico de inicialização permite que um usuário observe o estado de sua VM durante a inicialização coletando informações de log serial e capturas de tela.

## <a name="boot-diagnostics-view"></a>Exibição de diagnóstico de inicialização
Localizado na folha máquina virtual, a opção diagnóstico de inicialização está na seção *suporte e solução de problemas* no portal do Azure. A seleção de diagnósticos de inicialização exibirá uma captura de tela e informações de log serial. O log serial contém mensagens de kernel e a captura de tela é um instantâneo do estado atual de suas VMs. Com base em se a VM que está executando o Windows ou Linux determinar a aparência esperada da captura de tela. Para o Windows, os usuários verão um plano de fundo de área de trabalho e para Linux, os usuários verão um prompt de logon.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Captura de tela do diagnóstico de inicialização do Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Captura de tela do diagnóstico de inicialização do Windows":::


## <a name="limitations"></a>Limitações
- O diagnóstico de inicialização só está disponível para VMs Azure Resource Manager. 
- O diagnóstico de inicialização não oferece suporte a contas de armazenamento Premium, se uma conta de armazenamento Premium for usada para os usuários de diagnóstico de inicialização receberem um `StorageAccountTypeNotSupported` erro ao iniciar a VM. 

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [console serial do Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) e como usar o diagnóstico de inicialização para [solucionar problemas de máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
