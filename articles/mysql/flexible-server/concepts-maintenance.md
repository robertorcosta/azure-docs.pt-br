---
title: Manutenção agendada-banco de dados do Azure para MySQL-servidor flexível
description: Este artigo descreve o recurso de manutenção agendada no banco de dados do Azure para MySQL-servidor flexível.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 67529ce3546046eaba75bd3beacd7bb9b96445fe
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90933242"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Manutenção agendada no Banco de Dados do Azure para MySQL – Servidor Flexível

Banco de dados do Azure para MySQL-o servidor flexível executa manutenção periódica para manter seu banco de dados gerenciado seguro, estável e atualizado. Durante a manutenção, o servidor Obtém novos recursos, atualizações e patches.

> [!IMPORTANT]
> Banco de dados do Azure para MySQL-o servidor flexível está em versão prévia.

## <a name="select-a-maintenance-window"></a>Selecionar uma janela de manutenção

Você pode agendar a manutenção durante um dia específico da semana e uma janela de tempo dentro desse dia. Ou você pode permitir que o sistema escolha um dia e uma janela de tempo para você automaticamente. De qualquer forma, o sistema alertará cinco dias antes de executar qualquer manutenção. O sistema também avisará quando a manutenção for iniciada e quando for concluída com êxito.

As notificações sobre a manutenção agendada futura podem ser:

* Enviado por email para um endereço específico
* Enviado por email para uma função de Azure Resource Manager
* Enviado em uma mensagem de texto (SMS) para dispositivos móveis
* Enviado por push como uma notificação para um aplicativo do Azure
* Entregue como uma mensagem de voz

Ao especificar preferências para o agendamento de manutenção, você pode escolher um dia da semana e uma janela de tempo. Se você não especificar, o sistema escolherá os horários entre 23h e 7h no horário da região do servidor. Você pode definir agendas diferentes para cada servidor flexível em sua assinatura do Azure.

> [!IMPORTANT]
> Normalmente, há pelo menos 30 dias entre os eventos de manutenção agendados bem-sucedidos para um servidor.
>
> No entanto, no caso de uma atualização de emergência crítica, como uma vulnerabilidade grave, a janela de notificação pode ter menos de cinco dias. A atualização crítica pode ser aplicada ao seu servidor, mesmo se uma manutenção agendada bem-sucedida tiver sido realizada nos últimos 30 dias.

Você pode atualizar as configurações de agendamento a qualquer momento. Se houver uma manutenção agendada para seu servidor flexível e você atualizar as preferências de agendamento, o evento atual continuará conforme agendado e a alteração das configurações de agendamento entrará em vigor após sua conclusão bem-sucedida.

Em casos raros, o evento de manutenção pode ser cancelado pelo sistema ou pode falhar ao ser concluído com êxito. Nesse caso, o sistema criará uma notificação sobre o evento de manutenção cancelado ou com falha, respectivamente. A próxima tentativa de realizar a manutenção será agendada de acordo com as configurações de agendamento atuais e você receberá uma notificação sobre ela cinco dias de antecedência.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [alterar o agendamento de manutenção](how-to-maintenance-portal.md)
* Saiba como [obter notificações sobre a manutenção futura](/azure/service-health/service-notifications.md) usando a integridade do serviço do Azure
* Saiba como [configurar alertas sobre eventos de manutenção agendados futuros](/azure/service-health/resource-health-alert-monitor-guide.md)
