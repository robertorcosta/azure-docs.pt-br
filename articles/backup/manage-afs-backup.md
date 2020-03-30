---
title: Gerenciar backups de compartilhamento de arquivos do Azure
description: Este artigo descreve tarefas comuns para gerenciar e monitorar os compartilhamentos de arquivos do Azure que são apoiados pelo Azure Backup.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: cb764fa441c063328dc350cf26f42c5bc7a0ca99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247651"
---
# <a name="manage-azure-file-share-backups"></a>Gerenciar backups de compartilhamento de arquivos do Azure

Este artigo descreve tarefas comuns para gerenciar e monitorar os compartilhamentos de arquivos do Azure que são apoiados pelo [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview). Você aprenderá a fazer tarefas de gerenciamento no cofre dos Serviços de Recuperação.

## <a name="monitor-jobs"></a>Monitorar trabalhos

Quando você aciona uma operação de backup ou restauração, o serviço de backup cria um trabalho para rastreamento. Você pode monitorar o progresso de todos os trabalhos na página **Trabalhos de Backup**.

Para abrir a página **Trabalhos de Backup**:

1. Abra o cofre serviços de recuperação usado para configurar backup para seus compartilhamentos de arquivos. No **painel Visão geral,** selecione **Trabalhos de backup** na seção **Monitoramento.**

   ![Trabalhos de backup na seção de monitoramento](./media/manage-afs-backup/backup-jobs.png)

1. Depois de selecionar **OK,** o painel **Trabalhos de backup** lista o status de todos os trabalhos. Selecione o nome da carga de trabalho que corresponde ao compartilhamento de arquivos que deseja monitorar.

   ![Nome da carga de trabalho](./media/manage-afs-backup/workload-name.png)

## <a name="create-a-new-policy"></a>Criar uma nova política

Você pode criar uma nova diretiva para fazer backup de compartilhamentos de arquivos Do Azure na seção Políticas de **backup** do cofre Serviços de recuperação. Todas as políticas criadas quando você configurou backup para compartilhamentos de arquivos aparecem com o **Tipo de diretiva** como Compartilhamento de arquivos do **Azure**.

Para exibir as políticas de backup existentes:

1. Abra o cofre serviços de recuperação usado para configurar o backup para o compartilhamento de arquivos. No menu cofre Serviços de recuperação, selecione **Políticas de backup** na seção **Gerenciar.** Todas as políticas de backup configuradas no cofre aparecem.

   ![Todas as políticas de backup](./media/manage-afs-backup/all-backup-policies.png)

1. Para exibir políticas específicas para **o Compartilhamento de arquivos do Azure,** selecione **Azure File Share** na lista de baixa no canto superior direito.

   ![Selecione o compartilhamento de arquivos do Azure](./media/manage-afs-backup/azure-file-share.png)

Para criar uma nova política de backup:

1. No painel **de políticas de backup,** selecione **+ Adicione**.

   ![Nova política de backup](./media/manage-afs-backup/new-backup-policy.png)

1. No **painel Adicionar,** selecione **Azure File Share** como o **Tipo de diretiva**. O **painel de diretiva de backup** para compartilhamento de arquivos do **Azure é** aberto. Especifique o nome da diretiva, a freqüência de backup e o intervalo de retenção para os pontos de recuperação. Depois de definir a política, selecione **OK**.

   ![Defina a política de backup](./media/manage-afs-backup/define-backup-policy.png)

## <a name="modify-policy"></a>Modificar a política

Você pode modificar uma diretiva de backup para alterar a freqüência de backup ou o intervalo de retenção.

Para modificar uma política:

1. Abra o cofre serviços de recuperação usado para configurar o backup para o compartilhamento de arquivos. No menu cofre Serviços de recuperação, selecione **Políticas de backup** na seção **Gerenciar.** Todas as políticas de backup configuradas no cofre aparecem.

   ![Todas as políticas de backup no cofre](./media/manage-afs-backup/all-backup-policies-modify.png)

1. Para exibir políticas específicas para um compartilhamento de arquivos do Azure, selecione **Azure File Share** na lista de paradas no canto superior direito. Selecione a política de backup que deseja modificar.

   ![Compartilhamento de arquivos Azure para modificar](./media/manage-afs-backup/azure-file-share-modify.png)

1. O painel **de programação** abre. Edite o **cronograma de backup** e a faixa de **retenção** conforme necessário e **selecione Salvar**. Você verá uma mensagem "Atualizar em progresso" no painel. Depois que as alterações de diretiva forem atualizadas com sucesso, você verá a mensagem "Atualizou com sucesso a diretiva de backup".

   ![Salvar a política modificada](./media/manage-afs-backup/save-policy.png)

## <a name="stop-protection-on-a-file-share"></a>Interromper a proteção em um compartilhamento de arquivo

Há duas maneiras de interromper a proteção de compartilhamentos de arquivos do Azure:

* Interrompa todos os trabalhos de backup futuros e *exclua todos os pontos de recuperação*.
* Pare todos os trabalhos de backup futuros, mas *deixe os pontos de recuperação*.

Pode haver um custo associado à saída dos pontos de recuperação no armazenamento, porque os instantâneos subjacentes criados pelo Azure Backup serão mantidos. A vantagem de deixar os pontos de recuperação é que você pode restaurar o compartilhamento de arquivos mais tarde. Para obter informações sobre o custo de deixar os pontos de recuperação, confira os [detalhes de preços](https://azure.microsoft.com/pricing/details/backup/). Se você decidir excluir todos os pontos de recuperação, você não poderá restaurar o compartilhamento de arquivos.

Para parar a proteção de um compartilhamento de arquivos do Azure:

1. Abra o cofre Serviços de recuperação que contém os pontos de recuperação do compartilhamento de arquivos. Selecione **Itens de backup** na seção Itens **protegidos.** A lista de tipos de itens de backup é exibida.

   ![Itens de Backup](./media/manage-afs-backup/backup-items.png)

1. Na lista **Tipo de Gerenciamento de Backup**, selecione **Armazenamento do Azure (Arquivos do Azure)**. A **lista Itens de backup (Azure Storage (Azure Files))** é exibida.

   ![Selecione armazenamento azure (arquivos azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **Itens de backup (Azure Storage (Azure Files)),** selecione o item de backup para o qual deseja interromper a proteção.

1. Selecione a opção **Parar de backup.**

   ![Selecionar Parar Backup](./media/manage-afs-backup/stop-backup.png)

1. No painel **Parar backup,** selecione **Reter dados de backup** ou excluir dados de **backup**. Em seguida, **selecione Stop backup**.

    ![Selecione Reter dados de backup ou excluir dados de backup](./media/manage-afs-backup/retain-or-delete-backup-data.png)

## <a name="resume-protection-on-a-file-share"></a>Retomar a proteção em um compartilhamento de arquivo

Se a opção **Reter dados de backup** foi selecionada quando a proteção para o compartilhamento de arquivos foi interrompida, é possível retomar a proteção. Se a opção **Excluir dados de backup** foi selecionada, a proteção para o compartilhamento de arquivos não poderá ser retomada.

Para retomar a proteção para o compartilhamento de arquivos Do Zure:

1. Abra o cofre Serviços de recuperação que contém os pontos de recuperação do compartilhamento de arquivos. Selecione **Itens de backup** na seção Itens **protegidos.** A lista de tipos de itens de backup é exibida.

   ![Itens de backup para currículo](./media/manage-afs-backup/backup-items-resume.png)

1. Na lista **Tipo de Gerenciamento de Backup**, selecione **Armazenamento do Azure (Arquivos do Azure)**. A **lista Itens de backup (Azure Storage (Azure Files))** é exibida.

   ![Lista de armazenamento azure (arquivos Azure)](./media/manage-afs-backup/azure-storage-azure-files.png)

1. Na lista **Itens de backup (Azure Storage (Azure Files)),** selecione o item de backup para o qual deseja retomar a proteção.

1. Selecione a opção **De backup Currículo.**

   ![Selecione Retomar backup](./media/manage-afs-backup/resume-backup.png)

1. O painel **de política** de backup é aberto. Selecione uma política de sua escolha para retomar o backup.

1. Depois de selecionar uma política de backup, **selecione Salvar**. Você verá uma mensagem "Atualização em Andamento" no portal. Depois que o backup for retomado com sucesso, você verá a mensagem "Política de backup atualizada com sucesso para o Compartilhamento de arquivos protegido do Azure".

   ![Política de backup atualizada com sucesso](./media/manage-afs-backup/successfully-updated.png)

## <a name="delete-backup-data"></a>Excluir dados de backup

Você pode excluir o backup de um compartilhamento de arquivos durante o trabalho **de backup Stop** ou a qualquer momento após interromper a proteção. Pode ser benéfico esperar dias ou até semanas antes de excluir os pontos de recuperação. Quando você exclui dados de backup, você não pode escolher pontos de recuperação específicos para excluir. Se você decidir excluir seus dados de backup, você excluirá todos os pontos de recuperação associados ao compartilhamento de arquivos.

O procedimento a seguir pressupõe que a proteção foi interrompida para o compartilhamento de arquivos.

Para excluir dados de backup do compartilhamento de arquivos do Azure:

1. Depois que o trabalho de backup é interrompido, as opções **de backup do Resume** e excluir dados de **backup** estão disponíveis no painel de backup **do Item.** Selecione a opção **Excluir dados de backup.**

   ![Excluir dados de backup](./media/manage-afs-backup/delete-backup-data.png)

1. O painel **Excluir dados de backup** é aberto. Digite o nome do compartilhamento de arquivos para confirmar a exclusão. Opcionalmente, forneça mais informações nas caixas **Razão** ou **Comentários.** Depois de ter certeza sobre a exclusão dos dados de backup, **selecione Excluir**.

   ![Confirmar dados de exclusão](./media/manage-afs-backup/confirm-delete-data.png)

## <a name="unregister-a-storage-account"></a>Cancelar o registro de uma conta de armazenamento

Para proteger seus compartilhamentos de arquivos em uma determinada conta de armazenamento, usando um cofre de serviços de recuperação diferente, primeira [proteção stop para todos os compartilhamentos de arquivos](#stop-protection-on-a-file-share) nessa conta de armazenamento. Em seguida, não registre a conta do cofre de serviços de recuperação atual usado para proteção.

O procedimento a seguir pressupõe que a proteção foi interrompida para todos os compartilhamentos de arquivos na conta de armazenamento que você deseja cancelar o registro.

Para cancelar o registro da conta de armazenamento:

1. Abra o cofre dos Serviços de Recuperação onde sua conta de armazenamento está registrada.
1. No **painel Visão geral,** selecione a opção **Infra-estrutura de backup** na seção **Gerenciar.**

   ![Selecionar Infraestrutura de Backup](./media/manage-afs-backup/backup-infrastructure.png)

1. O painel **de infra-estrutura** de backup é aberto. Selecione **Contas de armazenamento** na seção Contas de armazenamento do **Azure.**

   ![Selecionar contas de armazenamento](./media/manage-afs-backup/storage-accounts.png)

1. Depois de selecionar **Contas de armazenamento,** uma lista de contas de armazenamento registradas no cofre é exibida.
1. Clique com o botão direito do mouse na conta de armazenamento que deseja cancelar o registro e selecione **Descadastramento**.

   ![Selecione 'Não registrar'](./media/manage-afs-backup/select-unregister.png)

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte Backup de [compartilhamentos de arquivos Azure](https://docs.microsoft.com/azure/backup/troubleshoot-azure-files).
