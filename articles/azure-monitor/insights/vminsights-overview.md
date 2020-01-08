---
title: O que é o Azure Monitor para VMs (versão prévia)? | Microsoft Docs
description: Visão geral de Azure Monitor para VMs que monitora a integridade e o desempenho das VMs do Azure, além de descobrir e mapear automaticamente os componentes do aplicativo e suas dependências.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 17aa81c626c1bfa8a8a344552b7a0fe61dd85a7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365777"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>O que é o Azure Monitor para VMs (versão prévia)?

O Azure Monitor para VMs monitora as VMs (máquinas virtuais) e os conjuntos de dimensionamento de máquinas virtuais do Azure em escala. Ele analisa o desempenho e a integridade das VMs do Windows e do Linux e monitora os processos e as dependências de outros recursos e processos externos. 

Ele inclui suporte para monitoramento de desempenho e dependências de aplicativo para VMs que são hospedadas localmente ou em outro provedor de nuvem. Os principais recursos a seguir fornecem informações aprofundadas:

- **Gráficos de desempenho de tendência predefinidos**: Exibir métricas de desempenho de núcleo do sistema operacional da VM convidada.

- **Mapa de dependências**: exibe os componentes interconectados com a VM de vários grupos de recursos e assinaturas.  

>[!NOTE]
>Recentemente, [anunciamos alterações](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
) que estamos fazendo no recurso de integridade com base nos comentários que recebemos de nossos clientes de demonstração pública. Considerando o número de alterações que iremos fazer, vamos parar de oferecer o recurso de integridade para novos clientes. Os clientes existentes podem continuar a usar o recurso de integridade. Para obter mais detalhes, consulte nossas [perguntas frequentes sobre disponibilidade geral](vminsights-ga-release-faq.md).  

A integração com os logs do Azure Monitor oferece uma agregação e uma filtragem eficientes, e pode analisar as tendências de dados ao longo do tempo. Esse monitoramento abrangente da carga de trabalho não pode ser obtido sozinho com o Azure Monitor ou o Mapa do Serviço.  

Você pode exibir esses dados em uma única VM a partir da máquina virtual diretamente ou pode usar Azure Monitor para fornecer uma exibição agregada de suas VMs em que a exibição dá suporte aos modos de contexto de recurso do Azure ou de espaço de trabalho. Para obter mais informações, consulte [visão geral de modos de acesso](../platform/design-logs-deployment.md#access-mode).

![Perspectiva de insights de máquina virtual no portal do Azure](./media/vminsights-overview/vminsights-azmon-directvm.png)

O Azure Monitor para VMs pode fornecer desempenho e disponibilidade previsíveis de aplicativos vitais. Ele identifica gargalos de desempenho e problemas de rede. O Azure Monitor para VMs pode ajudar você a entender se um problema está relacionado a outras dependências.  

## <a name="data-usage"></a>Uso de dados

Quando você implanta o Azure Monitor para VMs, os dados coletados pelas VMs são ingeridos e armazenados no Azure Monitor. Os dados de desempenho e dependência coletados são armazenados em um espaço de trabalho Log Analytics. Com base nos preços publicados na [página de preços do Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/), o Azure Monitor para VMs é cobrado pelo seguinte:

- Os dados ingeridos e armazenados.
- As regras de alerta criadas.
- As notificações enviadas. 

O tamanho do log varia de acordo com os comprimentos de cadeia de caracteres de contadores de desempenho e pode aumentar com o número de discos lógicos e adaptadores de rede alocados para a VM. Se você já tiver um workspace e estiver coletando esses contadores, nenhum encargo duplicado será aplicado. Se você já estiver usando o Mapa do Serviço, a única alteração que você verá serão os dados de conexão adicionais enviados ao Azure Monitor.

## <a name="next-steps"></a>Próximos passos

Para entender os requisitos e os métodos que ajudam você a monitorar suas máquinas virtuais, examine [Implantar o Azure Monitor para VMs](vminsights-enable-overview.md).
