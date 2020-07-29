---
title: O que é o Azure Monitor para VMs?
description: Visão geral do Azure Monitor para VMs, que monitora a integridade e o desempenho das VMs do Azure e descobre e mapeia automaticamente os componentes do aplicativo e suas dependências.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2020
ms.openlocfilehash: f9ad39b88ad2212ea2cdceb40e61fbc0a2d1a764
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87320486"
---
# <a name="what-is-azure-monitor-for-vms"></a>O que é o Azure Monitor para VMs?

O Azure Monitor para VMs monitora o desempenho e a integridade de suas máquinas virtuais e conjuntos de dimensionamento de máquinas virtuais, incluindo seus processos e dependências em execução em outros recursos. Ele pode ajudar a fornecer desempenho previsível e disponibilidade de aplicativos vitais identificando gargalos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado a outras dependências.

O Azure Monitor para VMs dá suporte aos sistemas operacionais Windows e Linux no seguinte:

- Máquinas virtuais do Azure
- Conjuntos de dimensionamento de máquinas virtuais do Azure
- Máquinas virtuais híbridas conectadas com o arco do Azure
- Máquinas virtuais locais
- Máquinas virtuais hospedadas em outro ambiente de nuvem
  


>[!NOTE]
>Recentemente, [anunciamos alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que estamos fazendo no recurso de integridade com base nos comentários que recebemos de nossos clientes de demonstração pública. Considerando o número de alterações que iremos fazer, vamos parar de oferecer o recurso de integridade para novos clientes. Os clientes existentes podem continuar a usar o recurso de integridade. Para obter mais detalhes, consulte nossas [perguntas frequentes sobre disponibilidade geral](vminsights-ga-release-faq.md).  


O Azure Monitor para VMs armazena seus dados em logs de Azure Monitor, o que permite que ele forneça agregação e filtragem poderosas e analise as tendências de dados ao longo do tempo. Você pode exibir esses dados em uma única VM da máquina virtual diretamente ou pode usar Azure Monitor para fornecer uma exibição agregada de várias VMs.

![Perspectiva de insights de máquina virtual no portal do Azure](media/vminsights-overview/vminsights-azmon-directvm.png)


## <a name="pricing"></a>Preços
Não há custo direto para Azure Monitor para VMs, mas você é cobrado por sua atividade no espaço de trabalho Log Analytics. Com base nos preços publicados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), o Azure Monitor para VMs é cobrado pelo seguinte:

- Dados ingeridos de agentes e armazenados no espaço de trabalho.
- Regras de alerta com base em dados de log e de integridade.
- Notificações enviadas de regras de alerta.

O tamanho do log varia de acordo com os comprimentos de cadeia de caracteres de contadores de desempenho e pode aumentar com o número de discos lógicos e adaptadores de rede alocados para a VM. Se você já estiver usando Mapa do Serviço, a única alteração que você verá é os dados de desempenho adicionais que são enviados para o `InsightsMetrics` tipo de dados Azure monitor.


## <a name="configuring-azure-monitor-for-vms"></a>Configurando Azure Monitor para VMs
As etapas para configurar Azure Monitor para VMs são as seguintes. Siga cada link para obter orientações detalhadas sobre cada etapa:

- [Criar Log Analytics espaço de trabalho.](vminsights-configure-workspace.md#create-log-analytics-workspace)
- [Adicione a solução VMInsights ao espaço de trabalho.](vminsights-configure-workspace.md#add-vminsights-solution-to-workspace)
- [Instale agentes na máquina virtual e no conjunto de dimensionamento de máquinas virtuais a ser monitorado.](vminsights-enable-overview.md)



## <a name="next-steps"></a>Próximas etapas

- Consulte [implantar Azure monitor para VMs](vminsights-enable-overview.md) para obter os requisitos e métodos que permitem o monitoramento de suas máquinas virtuais.

