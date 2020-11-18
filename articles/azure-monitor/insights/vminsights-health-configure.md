---
title: Configurar o monitoramento na integridade do convidado do Azure Monitor para VMs (versão prévia)
description: Descreve como modificar o monitoramento padrão para Azure Monitor para VMs integridade de convidado (versão prévia) usando o portal do Azure.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: f41a43e76993a03554d32fc7f3ce3149848989a9
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686619"
---
# <a name="configure-monitoring-in-azure-monitor-for-vms-guest-health-preview"></a>Configurar o monitoramento na integridade do convidado do Azure Monitor para VMs (versão prévia)
Azure Monitor para VMs integridade de convidado permite que você exiba a integridade de uma máquina virtual conforme definido por um conjunto de medidas de desempenho que são amostradas em intervalos regulares. Este artigo descreve como você pode modificar o monitoramento padrão usando o portal do Azure. Ele também descreve os conceitos fundamentais dos monitores necessários para [Configurar o monitoramento usando uma regra de coleta de dados](vminsights-health-configure-dcr.md).

## <a name="open-monitor-configuration"></a>Abrir configuração do monitor
Abra a configuração do monitor bin The portal do Azure selecionando o monitor e, em seguida, a guia **Configuration** .

[![Configuração de detalhes do monitor](media/vminsights-health-overview/monitor-details-configuration.png)](media/vminsights-health-overview/monitor-details-configuration.png#lightbox)

## <a name="enable-or-disable-monitors"></a>Habilitar ou desabilitar monitores
Os monitores de unidade e os monitores agregados têm uma configuração de **status do monitor de integridade** que permite habilitar ou desabilitar o monitor. Quando um monitor é habilitado, sua integridade é exibida e usada para definir a integridade da VM. Quando um monitor é desabilitado, sua integridade não é calculada e não é usada para definir a integridade da VM.

| Configuração | Descrição |
|:---|:---|
| habilitado | O monitor é habilitado independentemente da configuração de seu pai. |
| Desabilitado | O monitor é desabilitado independentemente da configuração de seu pai. |
| Igual ao pai | O monitor será habilitado ou desabilitado dependendo da configuração de seu pai. |

Quando um monitor é desabilitado, todos os critérios são mostrados como não disponíveis, conforme mostrado no exemplo a seguir.

![Monitor desabilitado](media/vminsights-health-configure/disabled-monitor.png)

## <a name="enable-or-disable-virtual-machine"></a>Habilitar ou desabilitar a máquina virtual
Você pode desabilitar o monitoramento de uma VM para interromper temporariamente todos os monitores. Você pode desabilitar o monitoramento de uma VM, por exemplo, quando estiver executando a manutenção nela.

| Configuração | Descrição |
|:---|:---|
| habilitado  | O estado de integridade do computador é exibido. |
| Desabilitado | O estado de integridade do computador é mostrado como **desabilitado**. Os alertas não são criados. |

## <a name="health-state-logic"></a>Lógica do estado de integridade
A lógica do estado de integridade para um monitor de unidade define os critérios para definir seu estado de integridade. Você pode especificar quantos Estados de integridade um monitor tem e o limite de como cada Estado de integridade é calculado.

![Critérios de integridade de exemplo](media/vminsights-health-configure/sample-health-criteria.png)

Os monitores agregados não especificam nenhuma lógica de estado de integridade. Seu estado de integridade é definido pelos monitores de unidade sob eles de acordo com seu acúmulo de integridade.

Monitores de unidade têm dois ou três Estados de integridade. Cada um sempre terá um estado íntegro e, opcionalmente, um estado de aviso, um estado crítico ou ambos. Os Estados de aviso e crítico exigem critérios exclusivos que definem quando o monitor é definido como esse estado. O estado íntegro não tem critérios, pois esse estado é definido quando os critérios para os outros Estados não são atendidos.

No exemplo a seguir, a utilização da CPU está definida para os seguintes Estados de integridade:

- Crítico se for maior que 90%.
- Aviso se for maior ou igual a 80%.
- Íntegro se for inferior a 80%. Isso é implícito, pois é a condição em que nenhuma das outras condições se aplica.

## <a name="next-steps"></a>Próximas etapas

- [Configure monitores em escala usando regras de coleta de dados.](vminsights-health-configure-dcr.md)