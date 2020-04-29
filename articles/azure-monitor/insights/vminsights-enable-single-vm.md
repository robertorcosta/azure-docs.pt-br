---
title: Habilitar Azure Monitor para VMs no portal do Azure
description: Saiba como avaliar Azure Monitor para VMs em uma única máquina virtual do Azure ou em um conjunto de dimensionamento de máquinas virtuais.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79480701"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Habilitar Azure Monitor para VMs no portal do Azure

Este artigo descreve como habilitar Azure Monitor para VMs em um pequeno número de VMs (máquinas virtuais) do Azure usando o portal do Azure. Seu objetivo é monitorar suas VMs e descobrir problemas de desempenho ou disponibilidade. 

Antes de começar, examine os [pré-requisitos](vminsights-enable-overview.md) e verifique se sua assinatura e seus recursos atendem aos requisitos.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Habilitar o monitoramento para uma única VM do Azure
Para habilitar o monitoramento de sua VM do Azure:

1. Entre no [portal do Azure](https://portal.azure.com).

1. Selecione **Máquinas Virtuais do Microsoft Azure**.

1. Na lista, selecione uma VM.

1. Na página VM, na seção **monitoramento** , selecione **insights** e, em seguida, **habilitar**.

    ![Habilitar o Azure Monitor para VMs em uma VM](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Na página **Integração de Insights do Azure Monitor**, caso você tenha um espaço de trabalho do Log Analytics existente na mesma assinatura, selecione-o na lista suspensa.  

    A lista selecione previamente o workspace e a localização padrão em que a VM está implantada na assinatura. 

    >[!NOTE]
    >Para criar um workspace do Log Analytics para armazenar os dados de monitoramento da VM, confira [Criar um workspace do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Seu workspace do Log Analytics deve pertencer a uma das [regiões com suporte](vminsights-enable-overview.md#log-analytics).

6. Você receberá mensagens de status conforme a configuração é executada.

    ![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Habilitar o monitoramento para um único conjunto de dimensionamento de máquinas virtuais

Para habilitar o monitoramento do conjunto de dimensionamento de máquinas virtuais do Azure:

1. Entre no [portal do Azure](https://portal.azure.com).

2. Selecione **conjuntos de dimensionamento de máquinas virtuais**.

3. Na lista, selecione um conjunto de dimensionamento de máquinas virtuais.

4. Na página conjunto de dimensionamento de máquinas virtuais, na seção **monitoramento** , selecione **insights** e, em seguida, **habilitar**.

5. Na página **insights** , se você quiser usar um espaço de trabalho log Analytics existente, selecione-o na lista suspensa.

    A lista seleciona o espaço de trabalho padrão e o local em que a VM é implantada na assinatura. 

    ![Habilitar Azure Monitor para VMs para um conjunto de dimensionamento de máquinas virtuais](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Para criar um novo espaço de trabalho Log Analytics para armazenar os dados de monitoramento do conjunto de dimensionamento de máquinas virtuais, consulte [criar um espaço de trabalho log Analytics](../learn/quick-create-workspace.md). Seu workspace do Log Analytics deve pertencer a uma das [regiões com suporte](vminsights-enable-overview.md#log-analytics).

6. Você receberá mensagens de status conforme a configuração é executada.

    >[!NOTE]
    >Se você usar um modelo de atualização manual para seu conjunto de dimensionamento, atualize as instâncias para concluir a instalação. Você pode iniciar as atualizações na página **instâncias** , na seção **configurações** .
    
    ![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Agora que você habilitou o monitoramento para sua VM ou conjunto de dimensionamento de máquinas virtuais, as informações de monitoramento estão disponíveis para análise no Azure Monitor para VMs. 

## <a name="next-steps"></a>Próximas etapas

* Para exibir dependências de aplicativo descobertas, consulte [usar o mapa de Azure monitor para VMs](vminsights-maps.md). 
* Para identificar afunilamentos, a utilização geral e o desempenho da VM, consulte [Exibir o desempenho da VM do Azure](vminsights-performance.md).
