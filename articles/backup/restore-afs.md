---
title: Restaurar compartilhamentos de arquivos do Azure
description: Saiba como usar o portal do Azure para restaurar um compartilhamento de arquivos inteiro ou arquivos específicos de um ponto de restauração criado pelo backup do Azure.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77586802"
---
# <a name="restore-azure-file-shares"></a>Restaurar compartilhamentos de arquivos do Azure

Este artigo explica como usar o portal do Azure para restaurar um compartilhamento de arquivos inteiro ou arquivos específicos de um ponto de restauração criado pelo [backup do Azure](https://docs.microsoft.com/azure/backup/backup-overview).

Neste artigo, você aprenderá a:

* Restaure um compartilhamento de arquivos completo do Azure.
* Restaurar arquivos ou pastas individuais.
* Acompanhe o status da operação de restauração.

## <a name="steps-to-perform-a-restore-operation"></a>Etapas para executar uma operação de restauração

Para executar uma operação de restauração, siga estas etapas.

### <a name="select-the-file-share-to-restore"></a>Selecione o compartilhamento de arquivos a ser restaurado

1. No [portal do Azure](https://portal.azure.com/), abra o cofre dos serviços de recuperação que você usou para configurar o backup para o compartilhamento de arquivos.

1. No painel Visão geral, selecione **itens de backup** na seção **itens protegidos** .

    ![Selecionar itens de backup](./media/restore-afs/backup-items.png)

1. Depois de selecionar **itens de backup**, um novo painel que lista todos os tipos de gerenciamento de backup é aberto ao lado do painel Visão geral.

    ![Tipos de gerenciamento de backup](./media/restore-afs/backup-management.png)

1. No painel **itens de backup** , em **tipo de gerenciamento de backup**, selecione **armazenamento do Azure (arquivos do Azure)**. Você verá uma lista de todos os compartilhamentos de arquivos e suas contas de armazenamento correspondentes com backup usando esse cofre.

    ![Lista de todos os compartilhamentos de arquivos](./media/restore-afs/file-shares.png)

1. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivos para o qual você deseja executar a operação de restauração.

### <a name="full-share-recovery"></a>Recuperação de compartilhamento completa

Você pode usar essa opção de restauração para restaurar o compartilhamento de arquivos completo no local original ou em um local alternativo.

1. Selecione a opção **restaurar compartilhamento** no painel **item de backup** que aparece depois que você selecionou o compartilhamento de arquivos a ser restaurado na etapa 5 da seção [selecionar o compartilhamento de arquivos a ser restaurado](#select-the-file-share-to-restore) .

   ![Selecione restaurar compartilhamento](./media/restore-afs/restore-share.png)

1. Depois de selecionar **restaurar compartilhamento**, o painel **restaurar** é aberto com um menu de **ponto de restauração** que exibe uma lista de pontos de restauração disponíveis para o compartilhamento de arquivos selecionado.

1. Selecione o ponto de restauração que você deseja usar para executar a operação de restauração e selecione **OK**.

    ![Folha Selecionar ponto de restauração](./media/restore-afs/restore-point.png)

1. Depois de selecionar **OK**, o menu do painel **restaurar** muda para o **local de restauração**. Em **local de restauração**, especifique onde ou como restaurar os dados. Selecione uma das duas opções a seguir:

    * **Local original**: restaure o compartilhamento de arquivos completo para o mesmo local que a origem original.
    * **Local alternativo**: restaure o compartilhamento de arquivos completo para um local alternativo e mantenha o compartilhamento de arquivos original como está.

#### <a name="restore-to-the-original-location"></a>Restaurar no local original

1. Selecione **local original** como o **destino de recuperação**e selecione se deseja ignorar ou substituir se houver conflitos. Depois de fazer a seleção apropriada, selecione **OK**.

    ![Selecionar local original](./media/restore-afs/original-location.png)

1. Selecione **restaurar** para iniciar a operação de restauração.

    ![Selecione restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. Selecione o **local alternativo** como o **destino de recuperação**.
1. Selecione a conta de armazenamento de destino na qual você deseja restaurar o conteúdo de backup da lista suspensa **conta de armazenamento** .
1. A lista suspensa **selecionar compartilhamento de arquivos** exibe os compartilhamentos de arquivos presentes na conta de armazenamento que você selecionou na etapa 2. Selecione o compartilhamento de arquivos no qual você deseja restaurar o conteúdo de backup.
1. Na caixa **nome da pasta** , especifique um nome de pasta que você deseja criar no compartilhamento de arquivos de destino com o conteúdo restaurado.
1. Selecione se deseja ignorar ou substituir se houver conflitos.
1. Depois de inserir os valores apropriados em todas as caixas, selecione **OK**.

    ![Selecionar local alternativo](./media/restore-afs/alternate-location.png)

1. Selecione **restaurar** para iniciar a operação de restauração.

    ![Selecione restaurar para iniciar](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Recuperação em nível de item

Você pode usar essa opção de restauração para restaurar arquivos ou pastas individuais no local original ou em um local alternativo.

1. Selecione a opção **recuperação de arquivo** no painel Item de **backup** que aparece depois que você selecionou o compartilhamento de arquivos a ser restaurado na etapa 5 da seção [selecionar o compartilhamento de arquivos a ser restaurado](#select-the-file-share-to-restore) .

    ![Selecionar recuperação de arquivo](./media/restore-afs/file-recovery.png)

1. Depois de selecionar a **recuperação de arquivo**, o painel **restaurar** é aberto com um menu de ponto de **restauração** que exibe uma lista de pontos de restauração disponíveis para o compartilhamento de arquivos selecionado.

1. Selecione o ponto de restauração que você deseja usar para executar a operação de restauração e selecione **OK**.

    ![Folha Selecionar ponto de restauração](./media/restore-afs/restore-point.png)

1. Depois de selecionar **OK**, o menu do painel restaurar muda para o **local de restauração**. Em **local de restauração**, especifique onde ou como restaurar os dados. Selecione uma das duas opções a seguir:

    * **Local original**: restaure os arquivos ou pastas selecionados para o mesmo compartilhamento de arquivos que a origem original.
    * **Local alternativo**: restaure os arquivos ou pastas selecionados em um local alternativo e mantenha o conteúdo do compartilhamento de arquivos original como está.

#### <a name="restore-to-the-original-location"></a>Restaurar no local original

1. Selecione **local original** como o **destino de recuperação**e selecione se deseja ignorar ou substituir se houver conflitos.

    ![Local original para recuperação em nível de item](./media/restore-afs/original-location-item-level.png)

1. Escolha **Selecionar arquivo** para selecionar os arquivos ou as pastas que você deseja restaurar.

    ![Escolha Selecionar arquivo](./media/restore-afs/select-file.png)

1. Depois que você escolher **Selecionar arquivo**, um painel Compartilhamento de arquivos exibirá o conteúdo do ponto de recuperação de compartilhamento de arquivos selecionado para restauração.

1. Marque a caixa de seleção que corresponde ao arquivo ou à pasta que você deseja restaurar e escolha **selecionar**.

    ![Selecionar arquivo ou pasta](./media/restore-afs/select-file-folder.png)

1. Repita as etapas de 2 a 4 para selecionar vários arquivos ou pastas para restauração.
1. Depois de selecionar todos os itens que deseja restaurar, selecione **OK**.

    ![Depois de selecionar todos os itens a serem restaurados, selecione OK](./media/restore-afs/after-selecting-items.png)

1. Selecione **restaurar** para iniciar a operação de restauração.

    ![Selecione restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. Selecione o **local alternativo** como o **destino de recuperação**.
1. Selecione a conta de armazenamento de destino na qual você deseja restaurar o conteúdo de backup da lista suspensa **conta de armazenamento** .
1. A lista suspensa **selecionar compartilhamento de arquivos** exibe os compartilhamentos de arquivos presentes na conta de armazenamento que você selecionou na etapa 2. Selecione o compartilhamento de arquivos no qual você deseja restaurar o conteúdo de backup.
1. Na caixa **nome da pasta** , especifique um nome de pasta que você deseja criar no compartilhamento de arquivos de destino com o conteúdo restaurado.
1. Selecione se deseja ignorar ou substituir se houver conflitos.
1. Escolha **Selecionar arquivo** para selecionar os arquivos ou as pastas que você deseja restaurar.

    ![Selecionar itens para restaurar para o local alternativo](./media/restore-afs/restore-to-alternate-location.png)

1. Quando você escolhe **Selecionar arquivo**, um painel Compartilhamento de arquivos exibe o conteúdo do ponto de recuperação de compartilhamento de arquivos selecionado para restauração.
1. Marque a caixa de seleção que corresponde ao arquivo ou à pasta que você deseja restaurar e escolha **selecionar**.

    ![Selecionar destino de recuperação](./media/restore-afs/recovery-destination.png)

1. Repita as etapas de 6 a 8 para selecionar vários arquivos ou pastas para restauração.
1. Depois de selecionar todos os itens que deseja restaurar, selecione **OK**.

    ![Selecione OK depois de selecionar todos os arquivos](./media/restore-afs/after-selecting-all-items.png)

1. Selecione **restaurar** para iniciar a operação de restauração.

## <a name="track-a-restore-operation"></a>Rastrear uma operação de restauração

Após disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O Backup do Azure exibe notificações sobre o trabalho no portal. Para exibir as operações do trabalho, selecione o hiperlink notificações.

![Selecionar hiperlink de notificações](./media/restore-afs/notifications-link.png)

Você também pode monitorar o progresso da restauração no cofre dos serviços de recuperação:

1. Abra o cofre dos serviços de recuperação de onde você disparou a operação de restauração.
1. No painel Visão geral, selecione **trabalhos de backup** na seção **monitoramento** para ver o status das operações em execução em diferentes cargas de trabalho.

    ![Selecionar trabalhos de backup](./media/restore-afs/backup-jobs.png)

1. Selecione o nome da carga de trabalho que corresponde ao seu compartilhamento de arquivos para exibir mais detalhes sobre a operação de restauração, como **dados transferidos** e **número de arquivos restaurados**.

    ![Consulte os detalhes restaurados](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [gerenciar backups de compartilhamento de arquivos do Azure](manage-afs-backup.md).
