---
title: Use o portal para notificações de manutenção
description: Veja as notificações de manutenção de máquinas virtuais em execução no Azure e inicie a manutenção de autoatendimento, usando o portal.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: 46fcc825ac49f0181ac74e9c3e2deaea577f3329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77115729"
---
# <a name="handling-planned-maintenance-notifications-using-the-portal"></a>Manuseio de notificações planejadas de manutenção usando o portal

**Este artigo se aplica a máquinas virtuais que executam o Linux e o Windows.**

Uma vez que uma onda [de manutenção planejada](maintenance-notifications.md) é agendada, você pode verificar se há uma lista de máquinas virtuais que são impactadas. 

É possível usar o portal do Azure e procurar as VMs agendadas para manutenção.

1. Faça login no [portal Azure](https://portal.azure.com).

2. Na navegação à esquerda, clique **em Máquinas Virtuais**.

3. No painel Máquinas Virtuais, selecione Editar o botão **colunas** para abrir a lista de colunas disponíveis.

4. Selecione e adicione as seguintes colunas:

   **Estado de manutenção**: Mostra o estado de manutenção da VM. Estes são os valores possíveis:
      
      | Valor | Descrição |
      |-------|-------------|
      | Comece agora | A VM está na janela de manutenção de autoatendimento, na qual você pode iniciar a manutenção por conta própria. Veja a seguir como iniciar a manutenção na VM. | 
      | Agendado | A VM está programada para manutenção sem a opção para iniciar a manutenção. Para saber mais sobre a janela de manutenção, selecione a janela de manutenção agendada neste modo de exibição ou clique na VM. | 
      | Já atualizado | A VM já está atualizada e nenhuma ação adicional é necessária neste momento. | 
      | Tente novamente mais tarde | Você iniciou a manutenção, mas ela apresentou falha. Você poderá usar a opção de manutenção de autoatendimento posteriormente. | 
      | Tente agora | Tente realizar a manutenção que foi iniciada automaticamente sem êxito. | 
      | - | Sua VM não faz parte de uma onda de manutenção planejada. |
      

   **Janela de manutenção de autoatendimento**: mostra a janela de tempo, quando é possível iniciar automaticamente a manutenção nas VMs.
   
   **Janela de manutenção agendada**: mostra a janela de tempo, quando o Azure fará a manutenção da VM. 



## <a name="notification-and-alerts-in-the-portal"></a>Notificação e alertas no portal

O Azure comunica uma agenda para manutenção planejada, enviando um email para o grupo de proprietário e os coadministradores de assinatura. Você pode adicionar outros destinatários e canais para essa comunicação com a criação de alertas de log de atividades do Azure. Para obter mais informações, consulte [Criar alertas do log de atividades em notificações de serviço](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

Certifique-se de definir o **tipo de evento** como manutenção **planejada**e **serviços** como **conjuntos de escala de máquinas virtuais** e/ou **máquinas virtuais.**

## <a name="start-maintenance-on-your-vm-from-the-portal"></a>Iniciar Manutenção na sua VM do portal

Ao examinar os detalhes da VM, você poderá ver mais detalhes relacionados à manutenção.  
Na parte superior do modo de exibição de detalhes VM, uma nova faixa de opções de notificação será adicionada se sua VM for incluída em uma onda de manutenção planejada. Além disso, uma nova opção é adicionada ao iniciar manutenção quando possível. 


Clique na notificação de manutenção para ver a página de manutenção com mais detalhes sobre a manutenção planejada. Nesse local, você pode **iniciar a manutenção** da VM.

Quando você inicia a manutenção, a máquina virtual passa pelo processo de manutenção e o respectivo status é atualizado para refletir o resultado dentro de alguns minutos.

Caso perca a janela de autoatendimento, você ainda poderá exibi-la quando a VM passar pela manutenção do Microsoft Azure. 


## <a name="next-steps"></a>Próximas etapas

Você também pode lidar com manutenção planejada usando o [Azure CLI](maintenance-notifications-cli.md) ou [PowerShell](maintenance-notifications-powershell.md).