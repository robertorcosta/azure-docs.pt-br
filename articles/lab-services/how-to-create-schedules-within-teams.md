---
title: Criar Azure Lab Services agendas dentro das equipes
description: Saiba como criar agendas de serviços de laboratório dentro das equipes.
ms.topic: article
ms.date: 10/07/2020
ms.openlocfilehash: 5578a2f63a4d6f58998a54560299a4356ed65997
ms.sourcegitcommit: ba7fafe5b3f84b053ecbeeddfb0d3ff07e509e40
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91946592"
---
# <a name="create-and-manage-lab-services-schedules-within-teams"></a>Criar e gerenciar agendas de serviços de laboratório dentro de equipes

Agendamentos permitem que você configure um laboratório de sala de aula, de modo que as VMs no laboratório iniciam e desligam automaticamente em um horário especificado. Você pode definir um agendamento único ou recorrente. Os procedimentos a seguir fornecem as etapas para criar e gerenciar agendamentos para um laboratório de sala de aula: 

Veja como as agendas afetam as máquinas virtuais do laboratório: 

- A máquina virtual de modelo não está incluída em agendas. 
- Somente as máquinas virtuais atribuídas são iniciadas. Isso significa que, se um computador não for reivindicado por um usuário final (aluno), o computador não será iniciado nas horas agendadas. 
- Todas as máquinas virtuais (se declaradas por um usuário ou não) são interrompidas com base na agenda do laboratório. 

> [!IMPORTANT]
> O tempo de execução agendado de VMs não conta com a cota alocada a um usuário. A cota é para o tempo fora dos horários agendados que um aluno passa em VMs. 

## <a name="createeditdelete-a-schedule-for-the-lab"></a>Criar/editar/excluir uma agenda para o laboratório

Crie um evento agendado para o laboratório, de modo que as VMs no laboratório sejam iniciadas/paradas automaticamente em horários específicos. A cota de usuário especificada anteriormente é o tempo adicional atribuído a cada usuário fora desse horário agendado. 

1. Alterne para a página **agendas** e selecione **Adicionar evento agendado** na barra de ferramentas (na parte superior/esquerda da janela). 
1. Defina os seguintes parâmetros para a agenda:
    1. Confirme se **Padrão** está selecionado para o **Tipo de evento**. Selecione **Iniciar apenas** para especificar apenas a hora de início das VMs. Selecione **Parar apenas** para especificar apenas a hora de término das VMs. 
    1. Especifique a **data de início**.
    1. Especifique a **hora de início** em que deseja iniciar as VMs.
    1. Especifique a **hora de término** em que as VMs devem ser desligadas. 
    1. Especifique o **fuso horário** para as horas de início e de término especificadas. 
    1. Na seção **repetir** , selecione **todas as semanas** ou **nunca**. 
    1. Para **observações (opcional)**, insira qualquer descrição ou nota para o agendamento. 
1. Clique em **Salvar**. 

### <a name="view-schedules-in-calendar"></a>Exibir agendamentos no calendário

Você pode ver as datas e os horários agendados realçados no calendário. Selecione o botão **Hoje** no canto superior direito para alternar para a data atual no calendário. Selecione **seta para a esquerda** para alternar para a semana anterior e **seta para a direita** para alternar para a próxima semana no calendário. 

### <a name="edit-a-schedule"></a>Editar um agendamento

Ao selecionar uma agenda realçada no calendário, você verá botões para **Editar** ou **excluir** a agenda. 

Na página **Editar evento agendado** , você pode atualizar a agenda e selecionar **salvar**. 

### <a name="delete-a-schedule"></a>excluir uma agenda

1. Para excluir uma agenda, selecione uma agenda realçada no calendário e selecione o botão de Lixeira (excluir):
1. Na caixa de diálogo **excluir evento agendado** , selecione **Sim** para confirmar a exclusão. 

## <a name="automatic-shutdown-and-disconnect-settings"></a>Configurações de desligamento e desconexão automáticas

Na parte superior da página, você verá um link para as configurações de **desligamento** automático.

Você pode habilitar vários recursos de controle de custos de desligamento automático para prevenir de forma proativa custos adicionais quando as máquinas virtuais não estiverem sendo usadas ativamente. A combinação dos seguintes três recursos automáticos de desligamento e desconexão captura a maioria dos casos em que os usuários deixam acidentalmente suas máquinas virtuais em execução:
 
- Desconectar automaticamente os usuários de máquinas virtuais que o sistema operacional considera ocioso.
- Desligar automaticamente as máquinas virtuais quando os usuários se desconectarem.
- Desligar automaticamente as máquinas virtuais que são iniciadas, mas os usuários não se conectam.

Para obter mais detalhes, clique no ícone de *informações* ao lado de opções nas configurações.

## <a name="next-steps"></a>Próximas etapas

Veja os artigos a seguir:

- [Visão geral do uso de Azure Lab Services nas equipes](lab-services-within-teams-overview.md)
- [Comece e crie um laboratório de serviços de laboratório das equipes](how-to-get-started-create-lab-within-teams.md)
- [Gerenciar listas de usuários do Lab Services de equipes](how-to-manage-user-lists-within-teams.md)
- [Gerenciar um pool de VMs no Lab Services das equipes](how-to-manage-vm-pool-within-teams.md)
- [Acessar uma VM (exibição de aluno) no Lab Services das equipes](how-to-access-vm-for-students-within-teams.md)