---
title: Gerenciar e monitorar backups do AGENTE MARS
description: Saiba como gerenciar e monitorar backups de agentes do Microsoft Azure Recovery Services (MARS) usando o serviço de backup do Azure.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: a88ec4dc9283114e06eed424172dbb958850c2e9
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025094"
---
# <a name="manage-microsoft-azure-recovery-services-mars-agent-backups-by-using-the-azure-backup-service"></a>Gerencie backups de agentes do Microsoft Azure Recovery Services (MARS) usando o serviço de backup do Azure

Este artigo descreve como gerenciar arquivos e pastas que são copiados com o Microsoft Azure Recovery Services Agent.

## <a name="modify-a-backup-policy"></a>Modificar uma política de backup

Quando você modifica a política de backup, você pode adicionar novos itens, remover itens existentes do backup ou excluir que os arquivos sejam copiados usando configurações de exclusão.

- **Adicionar itens** use esta opção apenas para adicionar novos itens para fazer backup. Para remover itens existentes, use a opção **Remover itens** ou **configurações de exclusão.**  
- **Remova itens** use esta opção para remover itens de backup.
  - Use **configurações de exclusão** para remover todos os itens dentro de um volume em vez de **Remover itens**.
  - A limpeza de todas as seleções em um volume faz com que backups antigos dos itens sejam retidos conforme as configurações de retenção no momento do último backup, sem espaço para modificação.
  - Reselecionar esses itens, levar a um primeiro backup completo e novas alterações de diretiva não são aplicadas a backups antigos.
  - A desmarcação do volume inteiro retém o backup passado sem qualquer escopo para modificar a política de retenção.
- **As configurações de exclusão** usam essa opção para excluir itens específicos de serem copiados.

### <a name="add-new-items-to-existing-policy"></a>Adicionar novos itens à política existente

1. Em **Ações,** clique **em Agendar backup**.

    ![Agendar um backup do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

2. Na **guia Selecionar item de diretiva** e selecione Modificar o cronograma de backup para seus arquivos e **pastas** e clique em **Seguir**.

    ![Selecionar itens de política](./media/backup-azure-manage-mars/select-policy-items.png)

3. Em Modificar ou interromper a guia **de backup,** **selecione Fazer alterações em itens ou tempos de backup** e clique em **Seguir**.

    ![Modificar ou agendar backup](./media/backup-azure-manage-mars/modify-schedule-backup.png)

4. Na **guia Selecionar itens para backup,** clique em Adicionar **itens** para adicionar os itens que você deseja fazer backup.

    ![Modificar ou agendar itens adicionais de backup](./media/backup-azure-manage-mars/modify-schedule-backup-add-items.png)

5. Na janela **Selecionar itens,** selecione moscas ou pastas que deseja adicionar e clique em **OK**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-item.png)

6. Complete as etapas subseqüentes e clique **em Concluir** para concluir a operação.

### <a name="add-exclusion-rules-to-existing-policy"></a>Adicionar regras de exclusão à política existente

Você pode adicionar regras de exclusão para pular arquivos e pastas que você não quer fazer backup. Você pode fazer isso durante a definição de uma nova política ou modificando uma política existente.

1. No painel Ações, clique em **Agendar backup**. Vá para **Selecionar itens para backup** e clique em **Configurações de exclusão**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-exclusion-settings.png)

2. Em **Configurações de exclusão,** clique **em Adicionar exclusão**.

    ![Selecione os itens](./media/backup-azure-manage-mars/add-exclusion.png)

3. Em **Selecionar itens para excluir,** navegue pelos arquivos e pastas e selecione itens que deseja excluir e clique em **OK**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-items-exclude.png)

4. Por padrão, todas as **subpastas** dentro das pastas selecionadas são excluídas. Você pode alterar isso selecionando **Sim** ou **Não**. Você pode editar e especificar os tipos de arquivo para excluir conforme mostrado abaixo:

    ![Selecione os itens](./media/backup-azure-manage-mars/subfolders-type.png)

5. Complete as etapas subseqüentes e clique **em Concluir** para concluir a operação.

### <a name="remove-items-from-existing-policy"></a>Remover itens da política existente

1. No painel Ações, clique em **Agendar backup**. Ir para **Selecionar itens para backup**. Na lista, selecione os arquivos e pastas que deseja remover do cronograma de backup e clique **em Remover itens**.

    ![Selecione os itens](./media/backup-azure-manage-mars/select-items-remove.png)

    > [!NOTE]
    > Proceda com cautela quando remover completamente um volume da diretiva.  Se você precisar adicioná-lo novamente, então ele será tratado como um novo volume. O próximo backup programado executará um backup inicial (backup completo) em vez de backup incremental. Se você precisar remover e adicionar itens temporariamente mais tarde, então é recomendável usar **Configurações de Exclusões** em vez de **Remover itens** para garantir backup incremental em vez de backup completo.

2. Complete as etapas subseqüentes e clique **em Concluir** para concluir a operação.

## <a name="stop-protecting-files-and-folder-backup"></a>Pare de proteger arquivos e backup de pastas

Existem duas maneiras de parar de proteger o backup de arquivos e pastas:

- **Pare a proteção e retenha dados de backup**.
  - Essa opção impedirá todos os trabalhos de backup futuros de proteção.
  - O serviço de backup do Azure continuará a reter todos os pontos de recuperação existentes.  
  - Você poderá restaurar os dados de backup para pontos de recuperação não expirados.
  - Se você decidir retomar a proteção, então você pode usar a opção *Reativar o agendamento de backup.* Depois disso, os dados seriam retidos com base na nova política de retenção.
- **Pare a proteção e exclua dados de backup**.
  - Essa opção impedirá que todos os trabalhos de backup futuros protejam seus dados e excluam todos os pontos de recuperação.
  - Você receberá um e-mail de alerta de dados de backup com uma mensagem *Seus dados para este item de backup foram excluídos. Esses dados estarão temporariamente disponíveis por 14 dias, após os quais serão excluídos permanentemente* e a ação recomendada *Reproteger o item backup dentro de 14 dias para recuperar seus dados.*
  - Para retomar a proteção, reproteja-se dentro de 14 dias da operação de exclusão.

### <a name="stop-protection-and-retain-backup-data"></a>Parar a proteção e reter dados de backup

1. Abra o console de gerenciamento MARS, vá para o **painel Ações**e **selecione Agendamento backup**.

    ![Modificar ou interromper um backup programado.](./media/backup-azure-manage-mars/mars-actions.png)
1. Na página **Selecionar item de diretiva,** **selecione Modificar um cronograma de backup para seus arquivos e pastas** e clique em **Next**.

    ![Modificar ou interromper um backup programado.](./media/backup-azure-manage-mars/select-policy-item-retain-data.png)
1. Na página Modificar ou Interromper uma página **de backup agendada,** **selecione Pare de usar esse cronograma de backup, mas mantenha os backups armazenados até que um cronograma seja ativado novamente**. Em seguida, selecione **Avançar**.

    ![Modificar ou interromper um backup programado.](./media/backup-azure-manage-mars/stop-schedule-backup.png)
1. Em **Pausa, backup agendado** revise as informações e clique **em Concluir**.

    ![Modificar ou interromper um backup programado.](./media/backup-azure-manage-mars/pause-schedule-backup.png)
1. Em **Modificar processo de backup** verifique se a pausa de backup do cronograma está no status de sucesso e clique **perto** de terminar.

### <a name="stop-protection-and-delete-backup-data"></a>Parar a proteção e excluir dados de backup

1. Abra o console de gerenciamento MARS, vá para o painel **Ações** e selecione **Agendamento Backup**.
2. Na página Modificar ou Interromper uma página **de backup programada,** **selecione Parar de usar esse cronograma de backup e exclua todos os backups armazenados**. Em seguida, selecione **Avançar**.

    ![Modificar ou interromper um backup programado.](./media/backup-azure-delete-vault/modify-schedule-backup.png)

3. Na página **Stop a Scheduled Backup,** selecione **Concluir**.

    ![Pare um reforço programado.](./media/backup-azure-delete-vault/stop-schedule-backup.png)
4. Você é solicitado a inserir um PIN de segurança (número de identificação pessoal), que você deve gerar manualmente. Para isso, entre primeiro no portal Azure.
5. Ir para **propriedades de configurações do cofre** > dos**serviços de** > **recuperação**.
6. Em **Security PIN,** selecione **Gerar**. Copie este PIN. O PIN é válido por apenas cinco minutos.
7. No console de gerenciamento, cole o PIN e selecione **OK**.

    ![Gerar um PIN de segurança.](./media/backup-azure-delete-vault/security-pin.png)

8. Na página **Modificar backup progress,** a seguinte mensagem aparece: *Os dados de backup excluídos serão retidos por 14 dias. Após esse tempo, os dados de backup serão excluídos permanentemente.*  

    ![Exclua a infra-estrutura de backup.](./media/backup-azure-delete-vault/deleted-backup-data.png)

Depois de excluir os itens de backup no local, siga os próximos passos do portal.

## <a name="re-enable-protection"></a>Reativar a proteção

Se você parou a proteção enquanto retém dados e decidiu retomar a proteção, então você pode reativar o cronograma de backup usando a diretiva de backup modificada.

1. Em **Ações** selecione **Agendamento de backup**.
1. Selecione **Reativar o cronograma de backup. Você também pode modificar itens de backup ou tempos** e clicar em **Next**.<br>

    ![Exclua a infra-estrutura de backup.](./media/backup-azure-manage-mars/re-enable-policy-next.png)
1. Em **Selecionar itens para backup,** clique em **Avançar**.

    ![Exclua a infra-estrutura de backup.](./media/backup-azure-manage-mars/re-enable-next.png)
1. Em **Especificar cronograma de backup,** especifique o cronograma de backup e clique **em Next**.
1. Em **Selecionar política de retenção,** especifique a duração de retenção e clique em **Seguir**.
1. Finalmente, na tela **Confirmação,** revise os detalhes da política e clique **em Concluir**.

## <a name="re-generate-passphrase"></a>Re-gerar senha

Uma senha é usada para criptografar e descriptografar dados durante o backup ou restauração de sua máquina local ou local usando o agente MARS de ou para o Azure. Se você perdeu ou esqueceu a senha, então você pode regenerar a senha (desde que sua máquina ainda esteja registrada no Cofre de Serviços de Recuperação e o backup esteja configurado) seguindo estas etapas:

- A partir do console de agente MARS, vá para as propriedades **Ações Pane** > **Change** >. Em seguida, vá para **a guia Criptografia**.<br>
- Selecione **Caixa de seleção 'Alterar senha'.**<br>
- Digite uma nova senha ou clique **em Gerar senha**.
- Clique **em Procurar** para salvar a nova senha.

    ![Gerar senha.](./media/backup-azure-manage-mars/passphrase.png)
- Clique em **OK** para aplicar as alterações.  Se o [recurso de segurança estiver](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#enable-security-features) ativado no portal Azure para o Cofre de Serviços de Recuperação, você será solicitado a entrar no PIN de segurança. Para receber o PIN, siga os passos listados neste [artigo](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#authentication-to-perform-critical-operations).<br>
- Cole o PIN de segurança do portal e clique em **OK** para aplicar as alterações.<br>

    ![Gerar senha.](./media/backup-azure-manage-mars/passphrase2.png)
- Certifique-se de que a senha é salva com segurança em um local alternativo (diferente da máquina de origem), de preferência no Cofre de Chaves Do Azure. Acompanhe todas as frases-senhas se você tiver várias máquinas sendo apoiadas com os agentes mars.

## <a name="next-steps"></a>Próximas etapas

- Para obter informações sobre cenários e limitações suportadas, consulte a [Matriz de Suporte para o Agente MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent).
- Saiba mais sobre [o comportamento de retenção de políticas de backup sob demanda](backup-windows-with-mars-agent.md#set-up-on-demand-backup-policy-retention-behavior).
