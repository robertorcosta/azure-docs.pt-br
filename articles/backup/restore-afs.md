---
title: Restaurar compartilhamentos de arquivos Do Zure
description: Saiba como usar o portal Azure para restaurar um compartilhamento inteiro de arquivos ou arquivos específicos a partir de um ponto de restauração criado pelo Azure Backup.
ms.topic: conceptual
ms.date: 01/12/2020
ms.openlocfilehash: c22078ebd89f5f6f8299e1424d4d9e21edce8b92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586802"
---
# <a name="restore-azure-file-shares"></a>Restaurar compartilhamentos de arquivos Do Zure

Este artigo explica como usar o portal Azure para restaurar um compartilhamento inteiro de arquivos ou arquivos específicos a partir de um ponto de restauração criado pelo [Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview).

Neste artigo, você aprenderá a:

* Restaurar um compartilhamento completo de arquivos Azure.
* Restaurar arquivos ou pastas individuais.
* Rastreie o estado da operação de restauração.

## <a name="steps-to-perform-a-restore-operation"></a>Etapas para executar uma operação de restauração

Para realizar uma operação de restauração, siga estas etapas.

### <a name="select-the-file-share-to-restore"></a>Selecione o compartilhamento de arquivos para restaurar

1. No [portal Azure,](https://portal.azure.com/)abra o cofre serviços de recuperação usado para configurar o backup para o compartilhamento de arquivos.

1. No painel visão geral, selecione **Itens de backup** na seção Itens **protegidos.**

    ![Selecione itens de backup](./media/restore-afs/backup-items.png)

1. Depois de selecionar **itens de backup,** um novo painel que lista todos os tipos de gerenciamento de backup é aberto ao lado do painel de visão geral.

    ![Tipos de gerenciamento de backup](./media/restore-afs/backup-management.png)

1. No painel **Itens de backup,** em **Tipo de Gerenciamento de Backup,** selecione **Azure Storage (Arquivos Azure)**. Você verá uma lista de todas as partes de arquivo e suas contas de armazenamento correspondentes apoiadas usando este cofre.

    ![Lista de todos os compartilhamentos de arquivos](./media/restore-afs/file-shares.png)

1. Na lista de compartilhamentos de arquivos do Azure, selecione o compartilhamento de arquivos para o qual deseja executar a operação de restauração.

### <a name="full-share-recovery"></a>Recuperação total das ações

Você pode usar esta opção de restauração para restaurar o compartilhamento completo de arquivos no local original ou em um local alternativo.

1. Selecione a opção **Restaurar compartilhar** no painel Itens **de backup** que aparece depois que você selecionou o compartilhamento de arquivos para restaurar na etapa 5 da [seção Selecionar o compartilhamento de arquivos para restaurar](#select-the-file-share-to-restore) a seção.

   ![Selecione Compartilhar restauração](./media/restore-afs/restore-share.png)

1. Depois de selecionar **Restaurar compartilhar,** o painel **Restaurar** será aberto com um menu Ponto de **restauração** que exibe uma lista de pontos de restauração disponíveis para o compartilhamento de arquivos selecionado.

1. Selecione o ponto de restauração que deseja usar para executar a operação de restauração e selecione **OK**.

    ![Folha Selecionar ponto de restauração](./media/restore-afs/restore-point.png)

1. Depois de selecionar **OK,** o menu **restaurar** painel muda para **restaurar o local**. Em **Restaurar localização,** especifique onde ou como restaurar os dados. Selecione uma das duas opções a seguir:

    * **Localização original**: Restaurar o compartilhamento completo de arquivos para o mesmo local que a fonte original.
    * **Localização alternativa**: Restaure o compartilhamento completo de arquivos para um local alternativo e mantenha o compartilhamento original do arquivo como está.

#### <a name="restore-to-the-original-location"></a>Restaurar para o local original

1. Selecione **Local Original** como **destino de recuperação**e selecione se deve pular ou substituir se houver conflitos. Depois de fazer a seleção apropriada, selecione **OK**.

    ![Selecione Local Original](./media/restore-afs/original-location.png)

1. Selecione **Restaurar** para iniciar a operação de restauração.

    ![Selecione Restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. Selecione **Local Alternativo** como destino **de recuperação**.
1. Selecione a conta de armazenamento de destino onde deseja restaurar o conteúdo de backup da lista de desistência da **conta** de armazenamento.
1. A lista de **seleção de compartilhamento** de arquivos exibe as ações de arquivo presentes na conta de armazenamento selecionada na etapa 2. Selecione o compartilhamento de arquivos onde deseja restaurar o conteúdo de backup.
1. Na **caixa Nome da pasta,** especifique um nome de pasta que deseja criar no compartilhamento de arquivos de destino com o conteúdo restaurado.
1. Selecione se deve pular ou substituir se houver conflitos.
1. Depois de inserir os valores apropriados em todas as caixas, selecione **OK**.

    ![Selecione local alternativo](./media/restore-afs/alternate-location.png)

1. Selecione **Restaurar** para iniciar a operação de restauração.

    ![Selecione Restaurar para iniciar](./media/restore-afs/click-restore.png)

### <a name="item-level-recovery"></a>Recuperação em nível de item

Você pode usar esta opção de restauração para restaurar arquivos ou pastas individuais no local original ou em um local alternativo.

1. Selecione a opção **Recuperação de arquivos** no painel De itens de **backup** que aparece depois que você selecionou o compartilhamento de arquivos para restaurar na etapa 5 da [seção Selecionar o compartilhamento de arquivos para restaurar.](#select-the-file-share-to-restore)

    ![Selecione recuperação de arquivos](./media/restore-afs/file-recovery.png)

1. Depois de selecionar **Recuperação de arquivos,** o painel **Restaurar** será aberto com um menu de ponto **de restauração** que exibe uma lista de pontos de restauração disponíveis para o compartilhamento de arquivos selecionado.

1. Selecione o ponto de restauração que deseja usar para executar a operação de restauração e selecione **OK**.

    ![Folha Selecionar ponto de restauração](./media/restore-afs/restore-point.png)

1. Depois de selecionar **OK,** o menu de restauração muda para **Restaurar local**. Em **Restaurar localização,** especifique onde ou como restaurar os dados. Selecione uma das duas opções a seguir:

    * **Localização original**: Restaurar arquivos ou pastas selecionados para o mesmo compartilhamento de arquivo que a fonte original.
    * **Localização alternativa**: Restaurar arquivos ou pastas selecionados para um local alternativo e manter o conteúdo original do compartilhamento de arquivos como está.

#### <a name="restore-to-the-original-location"></a>Restaurar para o local original

1. Selecione **Local Original** como **destino de recuperação**e selecione se deve pular ou substituir se houver conflitos.

    ![Localização original para recuperação em nível de item](./media/restore-afs/original-location-item-level.png)

1. Escolha **Selecionar arquivo** para selecionar os arquivos ou pastas que deseja restaurar.

    ![Escolha Selecionar arquivo](./media/restore-afs/select-file.png)

1. Depois de escolher **'Selecionar arquivo '''''''''** Um painel de compartilhamento de arquivos exibe o conteúdo do ponto de recuperação de compartilhamento de arquivos selecionado para restaurar.

1. Selecione a caixa de seleção que corresponde ao arquivo ou pasta que deseja restaurar e escolha **Selecionar**.

    ![Selecione arquivo ou pasta](./media/restore-afs/select-file-folder.png)

1. Repita as etapas 2 a 4 para selecionar vários arquivos ou pastas para restaurar.
1. Depois de selecionar todos os itens que deseja restaurar, selecione **OK**.

    ![Depois de selecionar todos os itens para restaurar, selecione OK](./media/restore-afs/after-selecting-items.png)

1. Selecione **Restaurar** para iniciar a operação de restauração.

    ![Selecione Restaurar para iniciar](./media/restore-afs/click-restore.png)

#### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. Selecione **Local Alternativo** como destino **de recuperação**.
1. Selecione a conta de armazenamento de destino onde deseja restaurar o conteúdo de backup da lista de desistência da **conta** de armazenamento.
1. A lista de **seleção de compartilhamento** de arquivos exibe as ações de arquivo presentes na conta de armazenamento selecionada na etapa 2. Selecione o compartilhamento de arquivos onde deseja restaurar o conteúdo de backup.
1. Na **caixa Nome da pasta,** especifique um nome de pasta que deseja criar no compartilhamento de arquivos de destino com o conteúdo restaurado.
1. Selecione se deve pular ou substituir se houver conflitos.
1. Escolha **Selecionar arquivo** para selecionar os arquivos ou pastas que deseja restaurar.

    ![Selecione itens para restaurar para local alternativo](./media/restore-afs/restore-to-alternate-location.png)

1. Quando você escolhe **Selecionar Arquivo**, um painel de compartilhamento de arquivo exibe o conteúdo do ponto de recuperação de compartilhamento de arquivos selecionado para restaurar.
1. Selecione a caixa de seleção que corresponde ao arquivo ou pasta que deseja restaurar e escolha **Selecionar**.

    ![Selecione o destino de recuperação](./media/restore-afs/recovery-destination.png)

1. Repita as etapas 6 a 8 para selecionar vários arquivos ou pastas para restauração.
1. Depois de selecionar todos os itens que deseja restaurar, selecione **OK**.

    ![Selecione OK depois de selecionar todos os arquivos](./media/restore-afs/after-selecting-all-items.png)

1. Selecione **Restaurar** para iniciar a operação de restauração.

## <a name="track-a-restore-operation"></a>Acompanhe uma operação de restauração

Após disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O Backup do Azure exibe notificações sobre o trabalho no portal. Para visualizar as operações para o trabalho, selecione o hiperlink notificações.

![Selecione o hiperlink de notificações](./media/restore-afs/notifications-link.png)

Você também pode monitorar o progresso de restauração do cofre dos Serviços de Recuperação:

1. Abra o cofre dos Serviços de Recuperação de onde você desencadeou a operação de restauração.
1. No painel visão geral, selecione **Trabalhos de backup** na seção **Monitoramento** para ver o status das operações em execução contra diferentes cargas de trabalho.

    ![Selecione trabalhos de backup](./media/restore-afs/backup-jobs.png)

1. Selecione o nome da carga de trabalho que corresponde ao seu compartilhamento de arquivos para exibir mais detalhes sobre a operação de restauração, como **Dados Transferidos** e **Número de Arquivos Restaurados**.

    ![Veja detalhes restaurados](./media/restore-afs/restore-details.png)

## <a name="next-steps"></a>Próximas etapas

* Saiba como [gerenciar backups de compartilhamento de arquivos do Azure](manage-afs-backup.md).
