---
title: Agendamentos de manutenção do Azure
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção programada necessários que o serviço do Azure SQL Data Warehouse usa para implantar novos recursos, atualizações e correções.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 07/16/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 91b202f8a5df841fa3d6aa1f0903999b395f8137
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686058"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Use agendas de manutenção para gerenciar atualizações e manutenção de serviços

Os agendamentos de manutenção agora estão disponíveis em todas as regiões do Azure SQL Data Warehouse. O recurso de agendamento de manutenção integra as notificações de manutenção planejada da integridade do serviço, Resource Health Monitor de verificação e o serviço de agendamento de manutenção do Azure SQL Data Warehouse.

Você usa o agendamento de manutenção para escolher uma janela de horário quando for conveniente receber novos recursos, atualizações e patches. Você escolhe uma janela de manutenção primária e secundária dentro de um período de sete dias. Para usar esse recurso, você precisará identificar uma janela primária e secundária dentro de intervalos de dia separado.

Por exemplo, você pode agendar uma janela primária de sábado 22:00 para domingo 01:00 e, em seguida, agendar uma janela secundária da quarta-feira 19:00 a 22:00. Se o SQL Data Warehouse não puder executar manutenção durante a janela de manutenção principal, ele tentará a manutenção novamente durante a janela de manutenção secundária. A manutenção do serviço pode ocorrer durante as janelas primária e secundária. Para garantir a conclusão rápida de todas as operações de manutenção, as camadas DW400 (c) e inferior data warehouse podem concluir a manutenção fora de uma janela de manutenção designada.

Todas as instâncias recém-criados do SQL Data Warehouse do Azure terão um agendamento de manutenção definida pelo sistema aplicada durante a implantação. O agendamento poderá ser editado assim que a implantação for concluída.

Cada janela de manutenção pode ter entre três e oito horas. A manutenção pode ocorrer a qualquer momento dentro da janela. Quando a manutenção for iniciada, todas as sessões ativas serão canceladas e as transações não confirmadas serão revertidas. Você deve esperar várias perdas curtas na conectividade, pois o serviço implanta novo código em seu data warehouse. Você será notificado imediatamente após a conclusão da manutenção do data warehouse.

 Todas as operações de manutenção devem terminar dentro das janelas de manutenção programadas. Nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas sem notificação prévia. Se o data warehouse estiver em pausa durante a manutenção agendada, ele será atualizado durante a operação de retomada. 

## <a name="alerts-and-monitoring"></a>Alertas e monitoramento

A integração com notificações do Service Health e o Monitor de verificação de integridade do recurso permite que os clientes se mantenham informados sobre a atividade de manutenção iminente. A nova automação aproveita o Azure Monitor. Você pode decidir como deseja ser notificado sobre eventos de manutenção iminentes. Além disso, você pode escolher quais fluxos automatizados ajudarão você a gerenciar o tempo de inatividade e minimizar o impacto operacional.

Uma notificação antecipada de 24 horas precede todos os eventos de manutenção que não são para as camadas DWC400c e inferior. Para minimizar o tempo de inatividade da instância, certifique-se de que o data warehouse não tenha transações de longa execução antes do período de manutenção escolhido.

> [!NOTE]
> No caso de ser necessário implantar uma atualização crítica de tempo, os tempos de notificação avançados podem ser reduzidos significativamente.

Se você recebeu uma notificação prévia de que a manutenção ocorrerá, mas o SQL Data Warehouse não pode executar manutenção durante esse período, você receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção agendada.

Todos os eventos de manutenção ativos são exibidos na seção **Service Health - Planned Maintenance**. O histórico de integridade do serviço inclui um registro completo de eventos passados. Você pode monitorar a manutenção por meio do painel do portal de verificação do Funcionamento do Azure Health durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade do cronograma de manutenção

Mesmo que o agendamento de manutenção não esteja disponível em sua região selecionada, você poderá visualizar e editar sua programação de manutenção a qualquer momento. Quando o agendamento de manutenção estiver disponível em sua região, o agendamento identificado ficará imediatamente ativo em seu data warehouse.

## <a name="view-a-maintenance-schedule"></a>Exibir um agendamento de manutenção 

### <a name="portal"></a>Portal

Por padrão, todas as instâncias do Azure SQL Data Warehouse criadas recentemente têm uma janela de manutenção de primários e secundários de oito horas aplicada durante a implantação. Conforme indicado acima, você pode alterar o Windows assim que a implantação for concluída. Nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas sem notificação prévia.

Para exibir o agendamento de manutenção que foi aplicado ao seu data warehouse, conclua as seguintes etapas:

1.  Entre no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja exibir. 
3.  O data warehouse selecionado abre a folha de visão geral. O agendamento de manutenção que é aplicado ao data warehouse aparece abaixo do **agendamento de manutenção**.

![Folha de visão geral](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Alterar um agendamento de manutenção 

### <a name="portal"></a>Portal
Um agendamento de manutenção pode ser atualizado ou alterado a qualquer momento. Se a instância selecionada estiver passando por um ciclo de manutenção ativa, as configurações serão salvas. Elas estarão ativas durante o próximo período de manutenção identificado. [Saiba mais](https://docs.microsoft.com/azure/service-health/resource-health-overview) sobre como monitorar seu data warehouse durante um evento de manutenção ativa. 

### <a name="identifying-the-primary-and-secondary-windows"></a>Identificando as janelas principais e secundárias

As janelas principais e secundárias devem ter intervalos de dia separados. Um exemplo é uma janela principal de terça-feira – quinta-feira e um secundário da janela de domingo-sábado.

Para alterar o agendamento de manutenção para seu data warehouse, conclua as seguintes etapas:
1.  Entre no [Portal do Azure](https://portal.azure.com/).
2.  Selecione o data warehouse que você deseja atualizar. A página será aberta na folha de visão geral. 
3.  Abra a página de configurações de agendamento de manutenção, selecionando o link do **Resumo de agendamento de manutenção (visualização)** na folha de visão geral. Ou, selecione a opção **Agendamento de Manutenção** no menu de recursos do lado esquerdo.  

    ![Opções da folha Visão geral](media/sql-data-warehouse-maintenance-scheduling/maintenance-change-option.png)

4. Identifique o intervalo de dia preferencial para a janela de manutenção primário, usando as opções na parte superior da página. Essa seleção determina se a janela principal ocorrerá em um dia da semana ou no final de semana. Sua seleção irá atualizar os valores de lista suspensa. Durante a visualização, algumas regiões podem ainda não dar suporte ao conjunto completo de opções disponíveis de**Dia**.

   ![Folha de configurações de manutenção](media/sql-data-warehouse-maintenance-scheduling/maintenance-settings-page.png)

5. Escolha suas janelas de manutenção preferencial de primário e secundário, usando as caixas de lista suspensa:
   - **Dia**: dia preferencial para executar a manutenção durante a janela selecionada.
   - **Hora de início**: hora de início preferencial para a janela de manutenção.
   - **Janela de tempo**: duração preferencial da sua janela de tempo.

   A área do **Resumo de agendamento** na parte inferior da folha é atualizada com base nos valores que você selecionou. 
  
6. Selecione **Salvar**. Será exibida uma mensagem confirmando que seu novo agendamento agora está ativo. 

   Se você estiver salvando um agendamento em uma região que não dá suporte a agendamento de manutenção, a seguinte mensagem será exibida. As configurações são salvas e se tornam ativas quando o recurso estiver disponível em sua região selecionada.    

   ![Mensagem sobre a disponibilidade de região](media/sql-data-warehouse-maintenance-scheduling/maintenance-notactive-toast.png)

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) sobre ações de webhook para regras de alerta do log.
- [Saiba mais](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) Criando e Gerenciar Grupo de ações.
- [Saiba mais](https://docs.microsoft.com/azure/service-health/service-health-overview) sobre a Integridade do Serviço do Azure.