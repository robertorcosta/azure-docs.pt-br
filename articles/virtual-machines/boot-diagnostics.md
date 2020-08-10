---
title: Diagnóstico de inicialização do Azure
description: Visão geral do diagnóstico de inicialização do Azure e diagnóstico de inicialização gerenciada
services: virtual-machines
ms.service: virtual-machines
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9ffd5a6397fa01efcf9aece93333dcb5e5b418cc
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042944"
---
# <a name="azure-boot-diagnostics"></a>Diagnóstico de inicialização do Azure

O diagnóstico de inicialização é um recurso de depuração para VMs (máquinas virtuais) do Azure que permite o diagnóstico de falhas de inicialização da VM. O diagnóstico de inicialização permite que um usuário observe o estado de sua VM durante a inicialização coletando informações de log serial e capturas de tela.

## <a name="boot-diagnostics-storage-account"></a>Conta de armazenamento de diagnóstico de inicialização
Por padrão, o diagnóstico de inicialização é habilitado para todas as VMs criadas usando o portal do Azure e utiliza uma conta de armazenamento gerenciado. Ao usar uma conta de armazenamento gerenciado, os usuários têm uma melhoria significativa no tempo de implantação da VM. Por esse motivo, recomendamos que os clientes usem o diagnóstico de inicialização com uma conta de armazenamento gerenciada para todas as VMs.

> [!NOTE]
> Os clientes do Azure não serão cobrados pelo armazenamento ao optar por usar o diagnóstico de inicialização com uma conta de armazenamento gerenciada até 1º de outubro de 2020.

Uma experiência de diagnóstico de inicialização alternativa é usar uma conta de armazenamento personalizada. Um usuário pode criar uma nova conta de armazenamento ou usar uma existente. Para obter mais informações sobre contas de armazenamento personalizadas, consulte [visão geral da conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-overview).

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-portal.png" alt-text="Captura de tela para mostrar como habilitar o diagnóstico de inicialização":::

## <a name="boot-diagnostics-view"></a>Exibição de diagnóstico de inicialização
Localizado na folha máquina virtual, a opção diagnóstico de inicialização está na seção *suporte e solução de problemas* no portal do Azure. A seleção de diagnósticos de inicialização exibirá uma captura de tela e informações de log serial. O log serial contém mensagens de kernel e a captura de tela é um instantâneo do estado atual de suas VMs. Com base em se a VM que está executando o Windows ou Linux determinar a aparência esperada da captura de tela. Para o Windows, os usuários verão um plano de fundo de área de trabalho e para Linux, os usuários verão um prompt de logon.

:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-linux.png" alt-text="Captura de tela do diagnóstico de inicialização do Linux":::
:::image type="content" source="./media/boot-diagnostics/boot-diagnostics-windows.png" alt-text="Captura de tela do diagnóstico de inicialização do Windows":::


## <a name="limitations"></a>Limitações
- O diagnóstico de inicialização só está disponível para VMs Azure Resource Manager (ARM). 
- O diagnóstico de inicialização não oferece suporte a contas de armazenamento Premium, se uma conta de armazenamento Premium for usada para os usuários de diagnóstico de inicialização receberem um `StorageAccountTypeNotSupported` erro ao iniciar a VM. 
- Atualmente, o console serial do Azure não dá suporte a uma conta de armazenamento gerenciada para o diagnóstico de inicialização.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre o [console serial do Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-overview) e como usar o diagnóstico de inicialização para [solucionar problemas de máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics).
