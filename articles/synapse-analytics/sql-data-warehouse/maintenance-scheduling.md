---
title: Cronogramas de manutenção para pool Synapse SQL
description: O agendamento de manutenção permite que os clientes planejem os eventos de manutenção programados necessários que o Azure Synapse Analytics usa para implementar novos recursos, upgrades e patches.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: f193580ca03d4b1805f3c044658a34f468f3f44f
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346555"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Use agendas de manutenção para gerenciar atualizações e manutenção de serviços

O recurso de cronograma de manutenção integra as Notificações de Manutenção Planejada do Serviço de Saúde, o Monitor de Verificação de Saúde de Recursos e o serviço de agendamento de manutenção para o pool Synapse SQL (data warehouse) dentro do Azure Synapse Analytics. 

Você deve usar o agendamento de manutenção para escolher uma janela de tempo quando for conveniente receber novos recursos, upgrades e patches. Você precisará escolher uma janela de manutenção primária e secundária dentro de um período de sete dias, cada janela deve estar dentro de intervalos de dias separados.

Por exemplo, você pode agendar uma janela primária de sábado das 22:00 às 01:00 e, em seguida, agendar uma janela secundária de quarta-feira das 19:00 às 22:00. Se a manutenção não puder ser realizada durante a janela de manutenção primária, ela tentará a manutenção novamente durante a janela de manutenção secundária. A manutenção do serviço pode ocorrer ocasionalmente durante as janelas primária e secundária. Para garantir a conclusão rápida de todas as operações de manutenção, os níveis de DW400c e de data warehouse inferiores poderiam concluir a manutenção fora de uma janela de manutenção designada.

Todas as instâncias de data warehouse recém-criadas terão um cronograma de manutenção definido pelo sistema aplicado durante a implantação. O agendamento poderá ser editado assim que a implantação for concluída.

Embora uma janela de manutenção possa ser entre três e oito horas, isso não significa que o data warehouse ficará offline durante toda a duração. A manutenção pode ocorrer a qualquer momento dentro dessa janela e você deve esperar uma única desconexão durante esse período com duração de ~5 -6 minutos à medida que o serviço implanta um novo código no seu data warehouse. DW400c e inferior podem sofrer múltiplas perdas breves na conectividade em vários momentos durante a janela de manutenção. Quando a manutenção for iniciada, todas as sessões ativas serão canceladas e as transações não comprometidas serão revertidas. Para minimizar o tempo de inatividade da instância, certifique-se de que o data warehouse não tenha transações de longa execução antes do período de manutenção escolhido.

Todas as operações de manutenção devem ser concluídas dentro das janelas de manutenção especificadas, a menos que sejamos obrigados a implantar uma atualização sensível ao tempo. Se o data warehouse estiver em pausa durante a manutenção agendada, ele será atualizado durante a operação de retomada. Você será notificado imediatamente após a conclusão da manutenção do seu data warehouse.

## <a name="alerts-and-monitoring"></a>Alertas e monitoramento

A integração com notificações do Service Health e o Monitor de verificação de integridade do recurso permite que os clientes se mantenham informados sobre a atividade de manutenção iminente. Essa automação aproveita o Azure Monitor. Você pode decidir como deseja ser notificado sobre eventos de manutenção iminentes. Além disso, você pode escolher quais fluxos automatizados o ajudarão a gerenciar o tempo de inatividade e minimizar o impacto operacional.

Uma notificação prévia de 24 horas precede todos os eventos de manutenção que não são para os níveis DWC400c e inferior.

> [!NOTE]
> Caso sejamnecessário implantar uma atualização crítica temporal, os tempos avançados de notificação podem ser significativamente reduzidos.

Se você recebeu uma notificação prévia de que a manutenção ocorrerá, mas a manutenção não pode ser realizada durante o período de tempo na notificação, você receberá uma notificação de cancelamento. A manutenção será retomada durante o próximo período de manutenção agendada.

Todos os eventos de manutenção ativos são exibidos na seção **Service Health - Planned Maintenance**. O histórico de integridade do serviço inclui um registro completo de eventos passados. Você pode monitorar a manutenção por meio do painel do portal de verificação do Funcionamento do Azure Health durante um evento ativo.

### <a name="maintenance-schedule-availability"></a>Disponibilidade do cronograma de manutenção

Mesmo que o agendamento de manutenção não esteja disponível em sua região selecionada, você poderá visualizar e editar sua programação de manutenção a qualquer momento. Quando o agendamento de manutenção estiver disponível em sua região, o cronograma identificado se tornará imediatamente ativo em seu pool Synapse SQL.

## <a name="view-a-maintenance-schedule"></a>Exibir um agendamento de manutenção 

Por padrão, todas as instâncias de data warehouse recém-criadas têm uma janela de manutenção primária e secundária de oito horas aplicada durante a implantação. Como indicado acima, você pode alterar as janelas assim que a implantação estiver concluída. Nenhuma manutenção ocorrerá fora das janelas de manutenção especificadas sem notificação prévia.

Para visualizar o cronograma de manutenção aplicado ao seu pool Synapse SqL, complete as seguintes etapas:

1.    Faça login no [portal Azure](https://portal.azure.com/).
2.    Selecione o pool Synapse SQL que você deseja visualizar. 
3.    O pool Synapse SQL selecionado é aberto na lâmina de visão geral. O cronograma de manutenção aplicado ao data warehouse aparece abaixo do **cronograma de manutenção.**

![Folha de visão geral](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Alterar um agendamento de manutenção 

Um agendamento de manutenção pode ser atualizado ou alterado a qualquer momento. Se a instância selecionada estiver passando por um ciclo de manutenção ativa, as configurações serão salvas. Elas estarão ativas durante o próximo período de manutenção identificado. [Saiba mais](../../service-health/resource-health-overview.md) sobre como monitorar seu data warehouse durante um evento de manutenção ativa. 

## <a name="identifying-the-primary-and-secondary-windows"></a>Identificando as janelas principais e secundárias

As janelas principais e secundárias devem ter intervalos de dia separados. Um exemplo é uma janela principal de terça-feira – quinta-feira e um secundário da janela de domingo-sábado.

Para alterar o cronograma de manutenção do seu pool Synapse SQL, complete as seguintes etapas:
1.    Faça login no [portal Azure](https://portal.azure.com/).
2.    Selecione o pool Synapse SQL que deseja atualizar. A página será aberta na folha de visão geral. 
3.    Abra a página para as configurações do cronograma de manutenção selecionando o link **resumo do cronograma de manutenção** na lâmina da visão geral. Ou, selecione a opção **Agendamento de Manutenção** no menu de recursos do lado esquerdo.  

    ![Opções da folha Visão geral](./media/maintenance-scheduling/maintenance-change-option.png)

4. Identifique o intervalo de dia preferencial para a janela de manutenção primário, usando as opções na parte superior da página. Essa seleção determina se a janela principal ocorrerá em um dia da semana ou no final de semana. Sua seleção irá atualizar os valores de lista suspensa. Durante a visualização, algumas regiões podem ainda não dar suporte ao conjunto completo de opções disponíveis de**Dia**.

   ![Folha de configurações de manutenção](./media/maintenance-scheduling/maintenance-settings-page.png)

5. Escolha suas janelas de manutenção preferencial de primário e secundário, usando as caixas de lista suspensa:
   - **Dia**: Dia preferido para realizar a manutenção durante a janela selecionada.
   - **Hora de início**: hora de início preferencial para a janela de manutenção.
   - **Janela de tempo**: Duração preferencial da janela de tempo.

   A área do **Resumo de agendamento** na parte inferior da folha é atualizada com base nos valores que você selecionou. 
  
6. Selecione **Salvar**. Será exibida uma mensagem confirmando que seu novo agendamento agora está ativo. 

   Se você estiver salvando um agendamento em uma região que não dá suporte a agendamento de manutenção, a seguinte mensagem será exibida. As configurações são salvas e se tornam ativas quando o recurso estiver disponível em sua região selecionada.    

   ![Mensagem sobre a disponibilidade de região](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais](../../azure-monitor/platform/alerts-metric.md) sobre como criar, exibir e gerenciar alertas usando o Azure Monitor.
- [Saiba mais](../..//azure-monitor/platform/alerts-log-webhook.md) sobre ações de webhook para regras de alerta do log.
- [Saiba mais](../..//azure-monitor/platform/action-groups.md) Criando e Gerenciar Grupo de ações.
- [Saiba mais](../../service-health/service-health-overview.md) sobre a Integridade do Serviço do Azure.
