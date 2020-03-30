---
title: O que é o Azure Monitor para VMs?
description: Visão geral do Monitor Azure para VMs que monitora a saúde e o desempenho das VMs do Azure, além de descobrir e mapear automaticamente os componentes do aplicativo e suas dependências.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/11/2020
ms.openlocfilehash: 6f65ba96f768a4b88aa13c073cc66920b8c5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480480"
---
# <a name="what-is-azure-monitor-for-vms"></a>O que é o Azure Monitor para VMs?

O Azure Monitor para VMs monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos. Ele inclui suporte para monitorar o desempenho e as dependências de aplicativos para VMs hospedados no local ou em outro provedor de nuvem. Os principais recursos fornecem informações detalhadas:

- **Gráficos de desempenho de tendências pré-definidos**: Exibir métricas de desempenho do núcleo do sistema operacional VM convidado.

- **Mapa de dependência**: Exibe os componentes interconectados com a VM de vários grupos de recursos e assinaturas.  

>[!NOTE]
>[Recentemente, anunciamos mudanças](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que estamos fazendo para o recurso Saúde com base no feedback que recebemos de nossos clientes de visualização pública. Dado o número de mudanças que faremos, vamos parar de oferecer o recurso Saúde para novos clientes. Os clientes existentes podem continuar a usar o recurso de saúde. Para obter mais detalhes, consulte o nosso [FAQ de Disponibilidade Geral.](vminsights-ga-release-faq.md)  

A integração com o Azure Monitor Logs oferece uma poderosa agregação e filtragem, permitindo que o Azure Monitor para VMs analise tendências de dados ao longo do tempo. Você pode visualizar esses dados diretamente em uma Única VM da máquina virtual, ou pode usar o Azure Monitor para fornecer uma visão agregada de suas VMs onde a exibição suporta modos de contexto de recursos do Azure ou de contexto de espaço de trabalho. Para obter mais informações, consulte [a visão geral dos modos de acesso](../platform/design-logs-deployment.md#access-mode).

![Perspectiva de insights de máquina virtual no portal do Azure](media/vminsights-overview/vminsights-azmon-directvm.png)

O Azure Monitor para VMs pode fornecer desempenho e disponibilidade previsíveis de aplicativos vitais. Ele identifica gargalos de desempenho e problemas de rede e também pode ajudá-lo a entender se um problema está relacionado a outras dependências.  

## <a name="data-usage"></a>Uso de dados

Quando você implanta o Azure Monitor para VMs, os dados coletados pelas VMs são ingeridos e armazenados no Azure Monitor. Os dados de desempenho e dependência coletados são armazenados em um espaço de trabalho do Log Analytics. Com base nos preços publicados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), o Azure Monitor para VMs é cobrado pelo seguinte:

- Os dados ingeridos e armazenados.
- As regras de alerta criadas.
- As notificações enviadas. 

O tamanho do registro varia de acordo com os comprimentos das cordas dos contadores de desempenho, e pode aumentar com o número de discos lógicos e adaptadores de rede alocados na VM. Se você já tiver um workspace e estiver coletando esses contadores, nenhum encargo duplicado será aplicado. Se você já estiver usando o Mapa de Serviço, a única alteração que você verá são os dados adicionais de conexão enviados ao Azure Monitor.

## <a name="next-steps"></a>Próximas etapas

Para entender os requisitos e os métodos que ajudam você a monitorar suas máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-enable-overview.md).
