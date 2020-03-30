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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72330516"
---
# <a name="create-and-manage-schedules-for-classroom-labs-in-azure-lab-services"></a>Criar e gerenciar agendamentos para laboratórios de sala de aula do Azure Lab Services 
Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Os procedimentos a seguir fornecem as etapas para criar e gerenciar agendamentos para um laboratório de sala de aula: 

> [!IMPORTANT]
> O tempo de execução agendado de VMs não conta com a [cota alocada a um usuário](how-to-configure-student-usage.md#set-quotas-for-users). A cota é para o tempo fora dos horários agendados que um aluno passa em VMs. 

## <a name="set-a-schedule-for-the-lab"></a>Definir uma agenda para o laboratório
Crie um evento agendado para o laboratório, de modo que as VMs no laboratório sejam iniciadas/paradas automaticamente em horários específicos. A cota de usuário especificada anteriormente é o tempo adicional atribuído a cada usuário fora desse horário agendado. 

1. Alterne para a página **Agendamentos** e selecione **Adicionar evento agendado** na barra de ferramentas. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/add-schedule-button.png)
2. Confirme se **Padrão** está selecionado para o **Tipo de evento**. Selecione **Iniciar apenas** para especificar apenas a hora de início das VMs. Selecione **Parar apenas** para especificar apenas a hora de término das VMs. 
7. Na seção **Repetir**, selecione a agenda atual. 

    ![Botão Adicionar agendamento na página Agendamentos](../media/how-to-create-schedules/select-current-schedule.png)
5. Na caixa de diálogo **Repetir**, execute as seguintes etapas:
    1. Confirme se a opção **toda semana** está definida para o campo **Repetir**. 
    3. Especifique a **data de início**.
    4. Especifique a **hora de início** em que deseja iniciar as VMs.
    5. Especifique a **hora de término** em que as VMs devem ser desligadas. 
    6. Especifique o **fuso horário** para as horas de início e de término especificadas. 
    2. Selecione os dias nos quais deseja que a agenda entre em vigor. No exemplo a seguir, Segunda-Quinta foi selecionado. 
    8. Selecione **Salvar**. 

        ![Definir agenda de repetição](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Agora, na página **Adicionar evento agendado**, em **Observações (opcional)**, insira uma descrição ou observações para o agendamento. 
4. Na página **Adicionar evento agendado**, selecione **Salvar**. 

    ![Agendamento semanal](../media/how-to-create-schedules/add-schedule-page-weekly.png)

## <a name="view-schedules-in-calendar"></a>Exibir agendamentos no calendário
Você pode ver as datas e horas agendadas realçadas na exibição de calendário, conforme mostrado na imagem a seguir:

![Agendamentos na exibição de calendário](../media/how-to-create-schedules/schedules-calendar.png)

Selecione o botão **Hoje** no canto superior direito para alternar para a data atual no calendário. Selecione **seta para a esquerda** para alternar para a semana anterior e **seta para a direita** para alternar para a próxima semana no calendário. 

## <a name="edit-a-schedule"></a>Editar um agendamento
Ao selecionar um cronograma destacado no calendário, você verá botões para **editar** ou **excluir** o cronograma. 

![Página editar agendamento](../media/how-to-create-schedules/schedule-edit-button.png)

Na página de **eventos agendado editar,** você pode atualizar o cronograma e selecionar **Salvar**. 

## <a name="delete-a-schedule"></a>excluir uma agenda

1. Para excluir um horário, selecione um cronograma destacado no calendário e selecione o botão ícone de lixo (excluir):

    ![Botão excluir na barra de ferramentas](../media/how-to-create-schedules/schedule-delete-button.png)
2. Na caixa de diálogo **evento agendado Excluir,** selecione **Sim** para confirmar a exclusão. 



## <a name="next-steps"></a>Próximas etapas
Veja os artigos a seguir:

- [Como administrador, crie e gerencie contas de laboratório](how-to-manage-lab-accounts.md)
- [Como um proprietário de laboratório, crie e gerencie laboratórios](how-to-manage-classroom-labs.md)
- [Como um proprietário de laboratório, configure e controle o uso de um laboratório](how-to-configure-student-usage.md)
- [Como um usuário de laboratório, acesse os laboratórios de sala de aula](how-to-use-classroom-lab.md)
