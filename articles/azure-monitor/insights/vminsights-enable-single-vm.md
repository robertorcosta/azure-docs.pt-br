---
title: Habilite o Monitor Do Azure para VMs no portal Azure
description: Aprenda a avaliar o Monitor Azure para VMs em uma única máquina virtual Azure ou em um conjunto de escala de máquina virtual.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480701"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>Habilite o Monitor Do Azure para VMs no portal Azure

Este artigo descreve como ativar o Azure Monitor para VMs em um pequeno número de máquinas virtuais (VMs) do Azure usando o portal Azure. Seu objetivo é monitorar suas VMs e descobrir quaisquer problemas de desempenho ou disponibilidade. 

Antes de começar, revise os [pré-requisitos](vminsights-enable-overview.md) e certifique-se de que sua assinatura e recursos atendam aos requisitos.  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>Habilite o monitoramento de uma única VM Azure
Para habilitar o monitoramento da VM do Azure:

1. Faça login no [portal Azure](https://portal.azure.com).

1. Selecione **Máquinas Virtuais do Microsoft Azure**.

1. Na lista, selecione uma VM.

1. Na página VM, na seção **Monitoramento,** selecione **Insights** e, em seguida, **Habilitar**.

    ![Habilitar o Azure Monitor para VMs em uma VM](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. Na página **Integração de Insights do Azure Monitor**, caso você tenha um espaço de trabalho do Log Analytics existente na mesma assinatura, selecione-o na lista suspensa.  

    A lista selecione previamente o workspace e a localização padrão em que a VM está implantada na assinatura. 

    >[!NOTE]
    >Para criar um workspace do Log Analytics para armazenar os dados de monitoramento da VM, confira [Criar um workspace do Log Analytics](../../azure-monitor/learn/quick-create-workspace.md). Seu workspace do Log Analytics deve pertencer a uma das [regiões com suporte](vminsights-enable-overview.md#log-analytics).

6. Você receberá mensagens de status à medida que a configuração for executada.

    ![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>Habilite o monitoramento de um único conjunto de escala de máquina virtual

Para habilitar o monitoramento do conjunto de escalas da máquina virtual do Azure:

1. Faça login no [portal Azure](https://portal.azure.com).

2. Selecione **conjuntos de escala de máquinavirtual**.

3. Na lista, selecione um conjunto de escala de máquina virtual.

4. Na página de set de escala de máquina virtual, na seção **Monitoramento,** selecione **Insights** e, em seguida, **Habilitar**.

5. Na página **Insights,** se você quiser usar um espaço de trabalho do Log Analytics existente, selecione-o na lista de itens de si.

    A lista pré-seleciona o espaço de trabalho padrão e o local para o que a VM está implantada na assinatura. 

    ![Habilite o Monitor Azure para VMs para um conjunto de escala de máquina virtual](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >Para criar um novo espaço de trabalho do Log Analytics para armazenar os dados de monitoramento do conjunto de escalas de máquinavirtual, consulte [Criar um espaço de trabalho do Log Analytics](../learn/quick-create-workspace.md). Seu workspace do Log Analytics deve pertencer a uma das [regiões com suporte](vminsights-enable-overview.md#log-analytics).

6. Você receberá mensagens de status à medida que a configuração for executada.

    >[!NOTE]
    >Se você usar um modelo de atualização manual para o seu conjunto de escala, atualize as instâncias para concluir a configuração. Você pode iniciar as atualizações da página **'Instâncias',** na seção **Configurações.**
    
    ![Habilitar o Azure Monitor para o processamento da implantação do monitoramento das VMs](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

Agora que você habilitou o monitoramento para o conjunto de escala de VM ou máquina virtual, as informações de monitoramento estão disponíveis para análise no Monitor Azure para VMs. 

## <a name="next-steps"></a>Próximas etapas

* Para visualizar as dependências de aplicativos descobertas, consulte [Use Azure Monitor for VMs Map](vminsights-maps.md). 
* Para identificar gargalos, utilização geral e desempenho da VM, consulte Exibir o desempenho do VM do [Azure](vminsights-performance.md).
