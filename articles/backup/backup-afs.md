---
title: Fazer backup de compartilhamentos de arquivo do Azure no portal do Azure
description: Saiba como usar o portal Azure para fazer backup de compartilhamentos de arquivos do Azure no cofre serviços de recuperação
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c1dea6925bad96be178f875567077fafa4db9326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938111"
---
# <a name="back-up-azure-file-shares-in-a-recovery-services-vault"></a>Faça backup das ações de arquivos do Azure em um cofre de Serviços de Recuperação

Este artigo explica como usar o portal Azure para fazer backup de [compartilhamentos de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction).

Neste artigo, você aprenderá a:

* Crie um cofre dos Serviços de Recuperação.
* Descubra compartilhamentos de arquivos e configure backups.
* Execute um trabalho de backup demanda para criar um ponto de restauração.

## <a name="prerequisites"></a>Pré-requisitos

* Identifique ou crie um [cofre de Serviços de Recuperação](#create-a-recovery-services-vault) na mesma região que a conta de armazenamento que hospeda o compartilhamento de arquivos.
* Certifique-se de que o compartilhamento de arquivos está presente em um dos tipos de conta de [armazenamento suportados.](#limitations-for-azure-file-share-backup-during-preview)

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Limitações de backup do compartilhamento de arquivos do Azure durante a versão prévia

A cópia de segurança para compartilhamentos de arquivos do Azure está em versão prévia. Há suporte para compartilhamentos de arquivos do Azure em contas de armazenamento de uso geral v1 e de uso geral v2. Aqui estão as limitações para fazer backup das ações de arquivos do Azure:

* O suporte para backup de compartilhamentos de arquivos Azure em contas de armazenamento com replicação ZRS [(Zone-redundant e redundante de armazenamento)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-zrs) está atualmente limitado a [essas regiões](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#in-which-geos-can-i-back-up-azure-file-shares).
* O Azure Backup atualmente suporta a configuração de backups diários programados de compartilhamentos de arquivos Do Azure.
* A quantidade máxima de backups agendados por dia é de um.
* A quantidade máxima de backups sob demanda por dia é de quatro.
* Use [bloqueios de recursos](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) na conta de armazenamento para evitar a exclusão acidental de backups no cofre dos Serviços de Recuperação.
* Não exclua instantâneos criados pelo Azure Backup. A exclusão de instantâneos pode resultar em perda de pontos de recuperação ou falhas de restauração.
* Não exclua os compartilhamentos de arquivos protegidos pelo Azure Backup. A solução atual exclui todos os instantâneos tirados pelo Azure Backup depois que o compartilhamento de arquivos é excluído, de modo que todos os pontos de restauração serão perdidos.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam [armazenamento geo-redundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for seu principal mecanismo de backup, recomendamos que você use GRS.
* Você pode usar [o LRS (Locally Redundante Storage, armazenamento localmente redundante)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) como uma opção de baixo custo.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** na seção **Configurações.**

1. Na página **Propriedades,** em **Configuração de backup,** selecione **Atualizar**.

1. Selecione o tipo de replicação de armazenamento e selecione **Salvar**.

    ![Configuração de backup de atualização](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Você não pode modificar o tipo de replicação de armazenamento depois que o cofre estiver configurado e contiver itens de backup. Se você quiser fazer isso, você precisa recriar o cofre.
>

## <a name="discover-file-shares-and-configure-backup"></a>Descubra compartilhamentos de arquivos e configure backup

1. No [portal Azure,](https://portal.azure.com/)abra o cofre serviços de recuperação que deseja usar para fazer backup do compartilhamento de arquivos.

1. No painel de **cofre dos Serviços de Recuperação,** selecione **+Backup**.

   ![Cofre dos Serviços de Recuperação](./media/backup-afs/recovery-services-vault.png)

    a. Em **Backup Goal**, defina Onde **Azure**sua carga de trabalho está **sendo executado?**

    ![Escolha o Compartilhamento de arquivos do Azure como objetivo de backup](./media/backup-afs/backup-goal.png)

    b.  Em **Que você deseja fazer backup?**, selecione **Azure File Share** na lista de desímpara das partes de baixo.

    c.  Selecione **Backup** para registrar a extensão de compartilhamento de arquivos do Azure no cofre.

    ![Selecione Backup para associar o compartilhamento de arquivos do Azure com o cofre](./media/backup-afs/register-extension.png)

1. Depois de selecionar **Backup,** o painel **Backup** é aberto e solicita que você selecione uma conta de armazenamento a partir de uma lista de contas de armazenamento com suporte descobertas. Ou estão associados a este cofre ou estão presentes na mesma região que o cofre, mas ainda não estão associados a nenhum cofre dos Serviços de Recuperação.

   ![Escolher conta de armazenamento](./media/backup-afs/select-storage-account.png)

1. Na lista de contas de armazenamento descobertas, selecione uma conta e selecione **OK**. O Azure pesquisa a conta de armazenamento para compartilhamentos de arquivos que podem ser backup. Se você adicionou recentemente seus compartilhamentos de arquivos e não os verá na lista, permita algum tempo para que as partes de arquivo apareçam.

    ![Descobrindo compartilhamentos de arquivos](./media/backup-afs/discovering-file-shares.png)

1. Na lista **'Compartilhamentos de arquivos',** selecione um ou mais dos compartilhamentos de arquivos que deseja fazer backup. Selecione **OK**.

1. Depois de escolher as partes do arquivo, o **menu Backup** muda para a **diretiva Backup**. A partir deste menu, selecione uma política de backup existente ou crie uma nova. Em seguida, **selecione Ativar backup**.

    ![Selecionar a Política de backup](./media/backup-afs/select-backup-policy.png)

Depois de definir uma política de backup, um instantâneo das partes do arquivo é tirado na hora agendada. O ponto de recuperação também é mantido para o período escolhido.

## <a name="create-an-on-demand-backup"></a>Criar um backup sob demanda

Ocasionalmente, você pode querer gerar um instantâneo de backup ou ponto de recuperação, fora dos horários programados na diretiva de backup. Uma razão comum para gerar um backup demanda é logo após você configurar a diretiva de backup. Com base no cronograma da política de backup, pode ser horas ou dias até que um instantâneo seja tirado. Para proteger seus dados até que a política de backup seja ativada, inicie um backup sob demanda. A criação de um backup demanda é frequentemente necessária antes de fazer alterações planejadas em seus compartilhamentos de arquivos.

### <a name="create-a-backup-job-on-demand"></a>Crie um trabalho de backup demanda

1. Abra o cofre dos Serviços de Recuperação que você usou para fazer backup do seu compartilhamento de arquivos. No **painel Visão geral,** selecione **Itens de backup** na seção Itens **protegidos.**

   ![Selecione itens de backup](./media/backup-afs/backup-items.png)

1. Depois de selecionar **itens de backup,** um novo painel que lista todos os **tipos de gerenciamento de backup** aparece ao lado do painel Visão **Geral.**

   ![Lista de tipos de gerenciamento de backup](./media/backup-afs/backup-management-types.png)

1. Na lista **de tipos de gerenciamento de backup,** selecione **Azure Storage (Arquivos Azure)**. Você verá uma lista de todas as partes de arquivo e as contas de armazenamento correspondentes apoiadas usando este cofre.

   ![Itens de backup do Azure Storage (Azure Files)](./media/backup-afs/azure-files-backup-items.png)

1. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivos que deseja. Os detalhes **do item de backup** aparecem. No menu **Itens de backup,** selecione **Backup agora**. Como esse trabalho de backup é demanda, não há política de retenção associada ao ponto de recuperação.

   ![Selecione Backup agora](./media/backup-afs/backup-now.png)

1. O **painel Backup Now** é aberto. Especifique até que dia deseja manter o ponto de recuperação escolhendo o último dia. Você pode ter uma retenção máxima de 10 anos para um backup demanda.

   ![Escolha a data de retenção](./media/backup-afs/retention-date.png)

1. Selecione **OK** para confirmar o trabalho de backup demanda que é executado.

1. Monitore as notificações do portal para manter um controle da conclusão do trabalho de backup. Você pode monitorar o progresso do trabalho no painel do cofre. Selecione **trabalhos de backup** > **em andamento**.

## <a name="next-steps"></a>Próximas etapas

Saiba como:
* [Restaurar compartilhamentos de arquivos Do Zure](restore-afs.md)
* [Gerenciar backups de compartilhamento de arquivos do Azure](manage-afs-backup.md)
