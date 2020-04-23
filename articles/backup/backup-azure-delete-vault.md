---
title: Exclua um cofre do Microsoft Azure Recovery Services
description: Neste artigo, saiba como remover dependências e, em seguida, excluir um cofre do Azure Backup Recovery Services.
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 69fed6c53914ed7aa16b04b5311ec69966734f25
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025128"
---
# <a name="delete-an-azure-backup-recovery-services-vault"></a>Exclua um cofre de serviços de recuperação de backup do Azure

Este artigo descreve como excluir um cofre [do Azure Backup](backup-overview.md) Recovery Services. Ele contém instruções para remover dependências e, em seguida, excluir um cofre.

## <a name="before-you-start"></a>Antes de começar

Você não pode excluir um cofre de Serviços de Recuperação que tenha dependências, como servidores protegidos ou servidores de gerenciamento de backup, associados a ele.

- Os cofres que contêm dados de backup não podem ser excluídos (mesmo se você parou a proteção, mas reteve os dados de backup).

- Se você excluir um cofre que contém dependências, a seguinte mensagem será exibida:

  ![Exclua o erro do cofre.](./media/backup-azure-delete-vault/error.png)

- Se você excluir um item protegido no local de um portal que contém dependências, uma mensagem de aviso será exibida:

  ![Exclua o erro do servidor protegido.](./media/backup-azure-delete-vault/error-message.jpg)

- Se os itens de backup estiverem em estado de exclusão suave abaixo, a mensagem de aviso aparecerá e você terá que esperar até que eles sejam excluídos permanentemente. Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud).

   ![Exclua o erro do cofre.](./media/backup-azure-delete-vault/error-message-soft-delete.png)

- Cofres que registraram contas de armazenamento não podem ser excluídos. Para saber como cancelar o registro da conta, consulte [Cancelar o registro de uma conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account).
  
Para excluir o cofre, escolha o cenário que corresponda à sua configuração e siga as etapas recomendadas:

Cenário | Etapas para remover dependências para excluir o cofre |
-- | --
Tenho arquivos e pastas no local protegidos usando o agente de backup do Azure, fazendo backup do Azure | Execute as etapas em [Excluir itens de backup do console de gerenciamento MARS](#delete-backup-items-from-the-mars-management-console)
Tenho máquinas no local que são protegidas usando MABS (Microsoft Azure Backup Server) ou DPM (System Center Data Protection Manager) para o Azure | Execute as etapas em [Excluir itens de backup do console de gerenciamento MABS](#delete-backup-items-from-the-mabs-management-console)
Eu protegi itens na nuvem (por exemplo, uma máquina virtual laaS ou um compartilhamento de arquivos Azure)  | Execute as etapas em [Excluir itens protegidos na nuvem](#delete-protected-items-in-the-cloud)
Eu protegi itens tanto no local quanto na nuvem | Execute as etapas em todas as seções a seguir, na seguinte ordem: <br> 1. [Exclua itens protegidos na nuvem](#delete-protected-items-in-the-cloud)<br> 2. [Exclua itens de backup do console de gerenciamento MARS](#delete-backup-items-from-the-mars-management-console) <br> 3. [Exclua itens de backup do console MABS Management](#delete-backup-items-from-the-mabs-management-console)
Eu não tenho nenhum item protegido no local ou nuvem; no entanto, eu ainda estou recebendo o erro de exclusão do cofre | Execute as etapas no [cofre Excluir serviços de recuperação usando o Azure Resource Manager](#delete-the-recovery-services-vault-by-using-azure-resource-manager) <br><br> Certifique-se de que não há contas de armazenamento registradas no cofre. Para saber como cancelar o registro da conta, consulte [Cancelar o registro de uma conta de armazenamento](manage-afs-backup.md#unregister-a-storage-account).

## <a name="delete-protected-items-in-the-cloud"></a>Excluir itens protegidos na nuvem

Primeiro, leia a seção **[Antes de iniciar](#before-you-start)** para entender as dependências e o processo de exclusão do cofre.

Para interromper a proteção e excluir os dados de backup, execute as seguintes etapas:

1. A partir do portal, vá para o **cofre dos Serviços de Recuperação**e, em seguida, vá para itens de **backup**. Em seguida, escolha os itens protegidos na nuvem (por exemplo, Azure Virtual Machines, Azure Storage [o serviço Azure Files], ou SQL Server em Máquinas Virtuais Azure).

    ![Selecione o tipo de backup.](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Clique com o botão direito do mouse para selecionar o item de backup. Dependendo se o item de backup está protegido ou não, o menu exibe o painel **Stop Backup** ou o painel **'Excluir dados de backup'.**

    - Se o painel **'Parar backup'** for exibido, **selecione Excluir dados** de backup no menu suspenso. Digite o nome do item de backup (este campo é sensível a maiúsculas e minúsculas) e selecione uma razão no menu suspenso. Insira seus comentários, se tiver algum. Em seguida, **selecione Stop backup**.

        ![O painel Stop Backup.](./media/backup-azure-delete-vault/stop-backup-item.png)

    - Se o painel **Excluir dados de backup** for exibido, digite o nome do item de backup (este campo é sensível a maiúsculas e minúsculas) e selecione uma razão no menu suspenso. Insira seus comentários, se tiver algum. Em seguida, selecione **Excluir**.

         ![O painel Excluir dados de backup.](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

3. Verifique o ícone ![ **Notificação:** O ícone notificação.](./media/backup-azure-delete-vault/messages.png) Após o término do processo, o serviço exibe a seguinte mensagem: *Parar o backup e excluir dados de backup para "* Item de backup *".* *Concluída com sucesso a operação.*
4. Selecione **Atualizar** no menu **Itens de backup** para garantir que o item de backup foi excluído.

      ![A página Excluir itens de backup.](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Excluir itens protegidos no local

Primeiro, leia a seção **[Antes de iniciar](#before-you-start)** para entender as dependências e o processo de exclusão do cofre.

1. No menu do painel do cofre, selecione **Infra-estrutura de backup**.
2. Dependendo do cenário local, escolha uma das seguintes opções:

      - Para MARS, selecione **Servidores protegidos** e, em seguida, **Azure Backup Agent**. Em seguida, selecione o servidor que deseja excluir.

        ![Para MARS, selecione seu cofre para abrir seu painel.](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - Para MABS ou DPM, selecione **Servidores de gerenciamento de backup**. Em seguida, selecione o servidor que deseja excluir.

          ![Para O MABS, selecione seu cofre para abrir seu painel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. O painel **Excluir** aparece com uma mensagem de aviso.

     ![O painel de exclusão.](./media/backup-azure-delete-vault/delete-protected-server.png)

     Revise a mensagem de aviso e as instruções na caixa de seleção de consentimento.
    > [!NOTE]
    >- Se o servidor protegido estiver sincronizado com os serviços do Azure e os itens de backup existirem, a caixa de seleção de consentimento exibirá o número de itens de backup dependentes e o link para exibir os itens de backup.
    >- Se o servidor protegido não estiver sincronizado com os serviços do Azure e os itens de backup existirem, a caixa de seleção de consentimento exibirá apenas o número de itens de backup.
    >- Se não houver itens de backup, a caixa de seleção de consentimento pedirá a exclusão.

4. Selecione a caixa de seleção de consentimento e, em seguida, **selecione Excluir**.

5. Verifique **Notification** o ![ícone Notificação](./media/backup-azure-delete-vault/messages.png)excluir dados de backup . Após o término da operação, o serviço exibe a mensagem: *Interrompendo o backup e excluindo dados de backup para "Item de backup".* *Concluída com sucesso a operação.*
6. Selecione **Atualizar** no menu **Itens de backup** para garantir que o item de backup seja excluído.

Depois que esse processo terminar, você pode excluir os itens de backup do console de gerenciamento:

- [Exclua itens de backup do console de gerenciamento MARS](#delete-backup-items-from-the-mars-management-console)
- [Exclua itens de backup do console de gerenciamento MABS](#delete-backup-items-from-the-mabs-management-console)

### <a name="delete-backup-items-from-the-mars-management-console"></a>Exclua itens de backup do console de gerenciamento MARS

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

### <a name="delete-backup-items-from-the-mabs-management-console"></a>Exclua itens de backup do console de gerenciamento MABS

Existem dois métodos que você pode usar para excluir itens de backup do console de gerenciamento MABS.

#### <a name="method-1"></a>Método 1

Para interromper a proteção e excluir dados de backup, faça as seguintes etapas:

1. Abra o console do administrador do DPM e selecione **Proteção** na barra de navegação.
2. No painel de exibição, selecione o membro do grupo de proteção que você deseja remover. Clique com o botão direito do mouse para selecionar a opção **Stop Protection of Group Members.**
3. Na caixa de diálogo **Stop Protection,** **selecione Excluir dados protegidos**e selecione a caixa de seleção **de armazenamento excluído on-line.** Em seguida, **selecione Stop Protection**.

    ![Selecione Excluir dados protegidos no painel Stop Protection.](./media/backup-azure-delete-vault/delete-storage-online.png)

    O status do membro protegido altera para *réplica inativa disponível*.

4. Clique com o botão direito do mouse no grupo de proteção inativa e **selecione Remover proteção inativa**.

    ![Remova a proteção inativa.](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Na janela **Excluir proteção inativa,** selecione a caixa de seleção **de armazenamento on-line e** selecione **OK**.

    ![Exclua o armazenamento on-line.](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

#### <a name="method-2"></a>Método 2

Abra o console **de gerenciamento Do MABS.** Em **Select método de proteção de dados,** limpe a caixa de seleção i want **on-line protection.**

  ![Selecione o método de proteção de dados.](./media/backup-azure-delete-vault/data-protection-method.png)

Depois de excluir os itens de backup no local, siga os próximos passos do portal.

## <a name="delete-the-recovery-services-vault"></a>Excluir o Cofre de Serviços de Recuperação

1. Quando todas as dependências forem **removidas,** role até o painel Essentials no menu do cofre.
2. Verifique se não há itens de backup, servidores de gerenciamento de backup ou itens replicados listados. Se os itens ainda aparecerem no cofre, consulte a seção [Antes de iniciar.](#before-you-start)

3. Quando não houver mais itens no cofre, **selecione Excluir** no painel do cofre.

    ![Selecione Excluir no painel do cofre.](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Selecione **Sim** para verificar se deseja excluir o cofre. O cofre foi apagado. O portal retorna ao menu de serviço **Novo.**

## <a name="delete-the-recovery-services-vault-by-using-powershell"></a>Exclua o cofre dos Serviços de Recuperação usando o PowerShell

Primeiro, leia a seção **[Antes de iniciar](#before-you-start)** para entender as dependências e o processo de exclusão do cofre.

Para interromper a proteção e excluir os dados de backup:

- Se você estiver usando sql no backup de VMs do Azure e ativado a proteção automática para instâncias SQL, primeiro desative a proteção automática.

    ```PowerShell
        Disable-AzRecoveryServicesBackupAutoProtection
           [-InputItem] <ProtectableItemBase>
           [-BackupManagementType] <BackupManagementType>
           [-WorkloadType] <WorkloadType>
           [-PassThru]
           [-VaultId <String>]
           [-DefaultProfile <IAzureContextContainer>]
           [-WhatIf]
           [-Confirm]
           [<CommonParameters>]
    ```

  [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupautoprotection?view=azps-2.6.0) sobre como desativar a proteção de um item protegido pelo Azure Backup.

- Pare a proteção e exclua dados para todos os itens protegidos por backup na nuvem (ex. laaS VM, Compartilhamento de arquivos Azure etc.):

    ```PowerShell
       Disable-AzRecoveryServicesBackupProtection
       [-Item] <ItemBase>
       [-RemoveRecoveryPoints]
       [-Force]
       [-VaultId <String>]
       [-DefaultProfile <IAzureContextContainer>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
    ```

    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-2.6.0&viewFallbackFrom=azps-2.5.0) sobre a proteção desativa da desativação de um item protegido por backup.

- Para arquivos e pastas no local protegidos usando o backup do Azure Backup Agent (MARS) no Azure, use o seguinte comando PowerShell para excluir os dados de backup de cada módulo MARS PowerShell:

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Poste onde o seguinte prompt apareceria:

    *Backup do Microsoft Azure Tem certeza de que deseja remover essa política de backup? Os dados de backup excluídos serão retidos por 14 dias. Após esse tempo, os dados de backup serão excluídos permanentemente. <br/> [Y] Sim [A] Sim para Todos [N] Não [L] Não a Todos [S] Suspender [?] Ajuda (padrão é "Y"):*

- Para máquinas locais protegidas usando MABS (Microsoft Azure Backup Server) ou DPM para Azure (System Center Data Protection Manager), use o seguinte comando para excluir os dados de backup no Azure.

    ```powershell
    Get-OBPolicy | Remove-OBPolicy -DeleteBackup -SecurityPIN <Security Pin>
    ```

    Poste onde o seguinte prompt apareceria:

   *Backup do Microsoft Azure* Tem certeza de que deseja remover essa política de backup? Os dados de backup excluídos serão retidos por 14 dias. Após esse tempo, os dados de backup serão excluídos permanentemente. <br/>
   [Y] Sim [A] Sim para Todos [N] Não [L] Não a Todos [S] Suspender [?] Ajuda (padrão é "Y"):*

Depois de excluir os dados de backup, desregistre quaisquer contêineres e servidores de gerenciamento no local.

- Para arquivos e pastas no local protegidos usando o Azure Backup Agent (MARS) fazendo backup no Azure:

    ```PowerShell
    Unregister-AzRecoveryServicesBackupContainer
              [-Container] <ContainerBase>
              [-PassThru]
              [-VaultId <String>]
              [-DefaultProfile <IAzureContextContainer>]
              [-WhatIf]
              [-Confirm]
              [<CommonParameters>]
    ```

    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sobre como cancelar o registro de um Servidor Windows ou outro contêiner no cofre.

- Para máquinas locais protegidas usando MABS (Microsoft Azure Backup Server) ou DPM para Azure (System Center Data Protection Manage:

    ```PowerShell
        Unregister-AzRecoveryServicesBackupManagementServer
          [-AzureRmBackupManagementServer] <BackupEngineBase>
          [-PassThru]
          [-VaultId <String>]
          [-DefaultProfile <IAzureContextContainer>]
          [-WhatIf]
          [-Confirm]
          [<CommonParameters>]
    ```

    [Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/unregister-azrecoveryservicesbackupcontainer?view=azps-2.6.0) sobre como cancelar o registro de um contêiner de gerenciamento de backup do cofre.

Depois de excluir permanentemente os dados de backup e descadastrar todos os contêineres, continue a excluir o cofre.

Para excluir um cofre dos Serviços de Recuperação:

   ```PowerShell
       Remove-AzRecoveryServicesVault
      -Vault <ARSVault>
      [-DefaultProfile <IAzureContextContainer>]
      [-WhatIf]
      [-Confirm]
      [<CommonParameters>]
   ```

[Saiba mais](https://docs.microsoft.com/powershell/module/az.recoveryservices/remove-azrecoveryservicesvault) sobre a exclusão de um cofre de serviços de recuperação.

## <a name="delete-the-recovery-services-vault-by-using-cli"></a>Exclua o cofre dos Serviços de Recuperação usando cli

Primeiro, leia a seção **[Antes de iniciar](#before-you-start)** para entender as dependências e o processo de exclusão do cofre.

> [!NOTE]
> Atualmente, o Azure Backup CLI suporta o gerenciamento de apenas backups do Azure VM, de modo que o seguinte comando para excluir o cofre funciona apenas se o cofre contiver backups do Azure VM. Não é possível excluir um cofre usando o Cli de backup do Azure, se o cofre contiver qualquer item de backup do tipo que não seja as VMs do Azure.

Para excluir o cofre de serviços de recuperação existente, execute o abaixo:

- Para interromper a proteção e excluir os dados de backup

    ```azurecli
    az backup protection disable --container-name
                             --item-name
                             [--delete-backup-data {false, true}]
                             [--ids]
                             [--resource-group]
                             [--subscription]
                             [--vault-name]
                             [--yes]
    ```

    Para obter mais informações, consulte este [artigo](/cli/azure/backup/protection#az-backup-protection-disable).

- Exclua um cofre de serviços de recuperação existente:

    ```azurecli
    az backup vault delete [--force]
                       [--ids]
                       [--name]
                       [--resource-group]
                       [--subscription]
                       [--yes]
    ```

    Para obter mais informações, consulte este [artigo](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest)

## <a name="delete-the-recovery-services-vault-by-using-azure-resource-manager"></a>Exclua o cofre dos serviços de recuperação usando o Azure Resource Manager

Essa opção de excluir o cofre dos Serviços de Recuperação é recomendada somente se todas as dependências forem removidas e você ainda estiver recebendo o erro de *exclusão*do Cofre . Experimente todas ou todas as dicas a seguir:

- No painel **Essentials** no menu do cofre, verifique se não há itens de backup, servidores de gerenciamento de backup ou itens replicados listados. Se houver itens de backup, consulte a seção [Antes de iniciar.](#before-you-start)
- Tente [excluir o cofre do portal](#delete-the-recovery-services-vault) novamente.
- Se todas as dependências forem removidas e você ainda estiver recebendo o *erro de exclusão*do Cofre, use a ferramenta ARMClient para executar as seguintes etapas (após a nota).

1. Vá para [chocolatey.org](https://chocolatey.org/) para baixar e instalar chocolatey. Em seguida, instale o ARMClient executando o seguinte comando:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Faça login na sua conta do Azure e execute o seguinte comando:

    `ARMClient.exe login [environment name]`

3. No portal Azure, reúna o ID de assinatura e o nome do grupo de recursos para o cofre que você deseja excluir.

Para obter mais informações sobre o comando ARMClient, consulte [ARMClient README](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-the-azure-resource-manager-client-to-delete-a-recovery-services-vault"></a>Use o cliente do Azure Resource Manager para excluir um cofre de Serviços de Recuperação

1. Execute o seguinte comando usando seu ID de assinatura, nome do grupo de recursos e nome do cofre. Se você não tiver nenhuma dependência, o cofre será excluído quando você executar o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```

2. Se o cofre não estiver vazio, você receberá a seguinte mensagem de erro: *O cofre não pode ser excluído, pois existem recursos existentes neste cofre.* Para remover um item ou contêiner protegido dentro de um cofre, execute o seguinte comando:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. No portal Azure, certifique-se de que o cofre seja excluído.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais sobre os cofres dos Serviços de Recuperação](backup-azure-recovery-services-vault-overview.md)<br/>
[Saiba mais sobre o monitoramento e gerenciamento de cofres dos Serviços de Recuperação](backup-azure-manage-windows-server.md)
