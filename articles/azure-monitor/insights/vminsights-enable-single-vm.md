---
title: Habilitar Azure Monitor para VMs no portal do Azure
description: Saiba como avaliar Azure Monitor para VMs em uma única máquina virtual do Azure ou em um conjunto de dimensionamento de máquinas virtuais.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507051"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>Habilitar Azure Monitor para VM única ou VMSS no portal do Azure
Este artigo descreve como habilitar Azure Monitor para VMs para uma única máquina virtual ou conjunto de dimensionamento de máquinas virtuais usando o portal do Azure. Esse procedimento pode ser usado para o seguinte:

- Máquina virtual do Azure
- Conjunto de dimensionamento de máquinas virtuais do Azure
- Máquina do Arc do Azure

Antes de começar, examine os [pré-requisitos](vminsights-enable-overview.md) e verifique se sua assinatura e seus recursos atendem aos requisitos.  

## <a name="enable-azure-monitor-for-vms"></a>Habilitar o Azure Monitor para VMs

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **máquinas virtuais**, **conjuntos de dimensionamento**de máquinas virtuais ou **computadores – Arc do Azure**.

1. Selecione um recurso na lista.

1. Na seção **monitoramento** do menu, selecione **insights** e, em seguida, **habilitar**. O exemplo a seguir mostra uma máquina virtual do Azure, mas o menu é semelhante para o Azure VMSS ou o arco do Azure.

    ![Habilitar o Azure Monitor para VMs em uma VM](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Se a VM ainda não estiver conectada a um espaço de trabalho Log Analytics, você será solicitado a selecionar uma. Se você ainda não tiver [criado um espaço de trabalho](../../azure-monitor/learn/quick-create-workspace.md), poderá selecionar um padrão para o local onde a VM ou a VMSS está implantada na assinatura. Esse espaço de trabalho será criado e configurado se ele ainda não existir.

2. Você receberá mensagens de status conforme a configuração é executada.

    >[!NOTE]
    >Se você usar um modelo de atualização manual para seu conjunto de dimensionamento, atualize as instâncias para concluir a instalação. Você pode iniciar as atualizações na página **instâncias** , na seção **configurações** .

    ![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Próximas etapas

* Para exibir dependências de aplicativo descobertas, consulte [usar o mapa de Azure monitor para VMs](vminsights-maps.md). 
* Para identificar afunilamentos, a utilização geral e o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
