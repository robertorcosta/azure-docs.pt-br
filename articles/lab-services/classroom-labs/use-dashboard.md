---
title: Use o painel de instrumentos para um laboratório de sala de aula no Azure Lab Services | Microsoft Docs
description: Aprenda a usar o painel de controle para um laboratório de sala de aula no Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 363e5705e9ccf4aa207ff7e5cafb615bc01bc7d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77538933"
---
# <a name="dashboard-for-classroom-labs"></a>Painel para laboratórios de sala de aula
Este artigo descreve a visão do painel de um laboratório de sala de aula no Azure Lab Services. 

![Painel](../media/use-dashboard/dashboard.png)

## <a name="costs-and-billing-tile"></a>Custos e telhas de faturamento
Este azulejo fornece os seguintes detalhes de estimativa de custo:

| Configuração | Valor | 
| ------- | ----- | 
| Horário de cotas | O número máximo de horas que um usuário pode usar a VM fora do horário programado. |
| Horário sinuoso | Horas que serão incorridas com base no cronograma definido no laboratório. Esse valor só está disponível se houver uma data de/para data definida em todos os eventos da programação. |
| Horas/usuário | A soma dos horários de cotas e horários programados. |
| Usuários máximos | Número máximo de usuários no laboratório com base em todas as máquinas virtuais a serem reclamadas. |
| Horas x usuários | Horas/usuário multiplicados pelo número de usuários. |
| Cota ajustada | A soma das horas de cota adicionadas a usuários específicos. |
| Total de horas * $/hora | O custo por hora com base no tamanho da VM selecionado. Isso é baseado no pagamento regular como você vai preço. |
| Custo total estimado | Este é o preço máximo para este laboratório com base nas configurações atuais. |

## <a name="template-tile"></a>Ladrilho de modelo
Você vê as seguintes informações sobre este azulejo:

- A data em que o modelo foi criado 
- A data em que o modelo foi publicado pela última vez 

Ele também tem um link para navegar até a página **Modelo** onde você pode [gerenciar o modelo VM](how-to-create-manage-template.md) para a classe. 

## <a name="virtual-machine-pool-tile"></a>Azulejo de piscina de máquina virtual

Você vê as seguintes informações sobre este azulejo:

- Número de máquinas virtuais atribuídas aos alunos (usuários)
- Número de máquinas virtuais que ainda não foram atribuídas aos alunos

Ele também tem um link para navegar até a página **do pool de máquinas virtuais** onde você pode gerenciar o pool de [máquinas virtuais](how-to-set-virtual-machine-passwords.md) no laboratório. 

## <a name="users-tile"></a>Ladrilho de usuários

Você vê as seguintes informações sobre este azulejo:

- Número de usuários cadastrados na classe
- Número de usuários que são adicionados ao laboratório, mas não registrados na classe 

Ele também tem um link para navegar até a página **Usuários** onde você pode [gerenciar os usuários](how-to-configure-student-usage.md) para o laboratório. 

## <a name="schedules-tile"></a>Agenda ladrilho
Você vê os eventos agendados atuais para o laboratório na telha. Ele também tem um link para navegar até a página **Agendar** onde você pode [criar e gerenciar horários](how-to-create-schedules.md). O azulejo mostra detalhes para apenas dois eventos agendados e o número de eventos programados restantes para o laboratório. 

![Eventos agendados](../media/use-dashboard/scheduled-events.png)

