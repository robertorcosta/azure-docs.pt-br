---
title: Criar agendamento para laboratórios de salas de aula no Azure Lab Services | Microsoft Docs
description: Saiba como criar agendamentos para laboratórios de sala de aula no Azure Lab Services para que as VMs nos laboratórios inicializem e desliguem em um horário especificado.
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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 4e3cf302437c3e4954ac977ac3f4ff6b2021a760
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330516"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerenciar agendamentos para laboratórios de sala de aula do Azure Lab Services 
Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Os procedimentos a seguir fornecem as etapas para criar e gerenciar agendamentos para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de execução agendado de VMs não conta com a [cota alocada a um usuário](how-to-configure-student-usage.md#set-quotas-for-users). A cota é para o tempo fora dos horários agendados que um aluno passa em VMs. 

## <a name="set-a-schedule-for-the-lab"></a>Definir uma agenda para o laboratório
Crie um evento agendado para o laboratório para que as VMs no laboratório sejam iniciadas/interrompidas automaticamente em horários específicos. A cota de usuário especificada anteriormente é o tempo adicional atribuído a cada usuário fora deste horário agendado. 

1. Alterne para a página **agendas** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/add-schedule-button.png)
2. Confirme se **padrão** está selecionado como o **tipo de evento**. Selecione **Iniciar somente** para especificar apenas a hora de início para as VMs. Você seleciona **parar somente** para especificar apenas a hora de parada para as VMs. 
7. Na seção **repetir** , selecione o agendamento atual. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **repetir** , execute as seguintes etapas:
    1. Confirme se **todas as semanas** estão definidas para o campo de **repetição** . 
    3. Especifique a **data de início**.
    4. Especifique a **hora de início** em que você deseja que as VMs sejam iniciadas.
    5. Especifique a **hora de parada** em que as VMs devem ser desligadas. 
    6. Especifique o **fuso horário** para os horários de início e de término especificados. 
    2. Selecione os dias nos quais você deseja que o agendamento entre em vigor. No exemplo a seguir, segunda-feira-quinta-feira está selecionado. 
    8. Clique em **Salvar**. 

        ![Definir agendamento de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **Adicionar evento agendado** , para **observações (opcional)** , insira qualquer descrição ou nota para o agendamento. 
4. Na página **Adicionar evento agendado** , selecione **salvar**. 

    ![Agendamento semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Exibir agendamentos no calendário
Você pode ver as datas e horas agendadas realçadas na exibição de calendário, conforme mostrado na imagem a seguir:

![Agendamentos na exibição de calendário](../media/how-to-create-schedules/schedules-calendar.png)

Selecione o botão **Hoje** no canto superior direito para alternar para a data atual no calendário. Selecione **seta para a esquerda** para alternar para a semana anterior e **seta para a direita** para alternar para a próxima semana no calendário. 

## <a name="edit-a-schedule"></a>Editar um agendamento
Ao selecionar uma agenda realçada no calendário, você verá botões para **Editar** ou **excluir** a agenda. 

![Página editar agendamento](../media/how-to-create-schedules/schedule-edit-button.png)

Na página **Editar evento agendado** , você pode atualizar a agenda e selecionar **salvar**. 

## <a name="delete-a-schedule"></a>excluir uma agenda

1. Para excluir uma agenda, selecione uma agenda realçada no calendário e selecione o botão de Lixeira (excluir):

    ![Botão excluir na barra de ferramentas](../media/how-to-create-schedules/schedule-delete-button.png)
2. Na caixa de diálogo **excluir evento agendado** , selecione **Sim** para confirmar a exclusão. 



## <a name="next-steps"></a>Próximos passos
Confira os seguintes artigos:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
