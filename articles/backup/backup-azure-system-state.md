---
title: Fazer backup de estado do sistema para o Windows Azure
description: Saiba como fazer backup do estado do sistema do Windows Server e/ou de computadores Windows para o Azure.
ms.topic: conceptual
ms.date: 05/23/2018
ms.openlocfilehash: 4319e03f9673baa2be01c1650ac1929204741087
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85611434"
---
# <a name="back-up-windows-system-state-to-azure"></a>Fazer backup de estado do sistema para o Windows Azure

Este artigo explica como fazer backup do estado do sistema Windows Server para o Azure. O objetivo é orientá-lo pelas noções básicas.

Se você quiser saber mais sobre o Backup do Azure, leia esta [visão geral](backup-overview.md).

Se não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) , que permitirá o acesso a qualquer serviço do Azure.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="set-storage-redundancy-for-the-vault"></a>Definir a redundância de armazenamento para o cofre

Quando você criar um cofre de Serviços de Recuperação, certifique-se de que a redundância de armazenamento esteja configurada da maneira desejada.

1. Na folha **Cofres dos Serviços de Recuperação**, clique no novo cofre.

    ![Selecionar o novo cofre da lista de cofres do Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-list.png)

    Quando você selecionar o cofre, a folha **Cofre de Serviços de Recuperação** será reduzida e a folha Configurações (*que tem o nome do cofre na parte superior*) e a folha de detalhes do cofre serão abertas.

    ![Exibir a configuração de armazenamento para um novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration-2.png)
2. Na folha de configurações do novo cofre, use o slide vertical para rolar para baixo até a seção Gerenciar e clique em **Infraestrutura de Backup**.
    A folha Infraestrutura de Backup é aberta.
3. Na folha Infraestrutura de Backup, clique em **Configuração de Backup** para abrir a folha **Configuração de Backup**.

    ![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/set-storage-configuration.png)
4. Escolha a opção de replicação de armazenamento adequada para o cofre.

    ![opções de configuração de armazenamento](./media/backup-try-azure-backup-in-10-mins/choose-storage-configuration.png)

    Por padrão, seu cofre tem armazenamento com redundância geográfica. Se você usar o Azure como um ponto de extremidade de armazenamento de backup principal, continue a usar **Georredundante**. Se você não usar o Azure como um ponto de extremidade de armazenamento de backup principal, escolha **Localmente redundante**, que reduz os custos de armazenamento do Azure. Leia mais sobre as opções de armazenamento [com redundância geográfica](../storage/common/storage-redundancy-grs.md) e [com redundância local](../storage/common/storage-redundancy-lrs.md) nesta [Visão geral de redundância de armazenamento](../storage/common/storage-redundancy.md).

Agora que você criou um cofre, configure-o para fazer backup do Estado do Sistema do Windows.

## <a name="configure-the-vault"></a>Configurar o cofre

1. Na folha do cofre dos Serviços de Recuperação (para o cofre recém-criado), na seção Introdução, clique em **Backup**, na folha **Introdução ao Backup**, selecione **Meta de Backup**.

    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

    A folha **Meta de Backup** será aberta.

    ![Abrir folha de meta backup](./media/backup-try-azure-backup-in-10-mins/backup-goal-blade.png)

2. No menu suspenso **Onde sua carga de trabalho é executada?**, selecione **Local**.

    Você escolhe **Local** porque o Windows Server ou o computador do Windows é uma máquina física que não está no Azure.

3. No menu do **que você deseja fazer backup?** , selecione estado do **sistema**e clique em **OK**.

    ![Configuração de arquivos e pastas](./media/backup-azure-system-state/backup-goal-system-state.png)

    Depois de clicar em OK, uma marca de seleção aparece ao lado de **Meta de backup** e a folha **Preparar infraestrutura** será aberta.

    ![Meta de backup configurada, prepare a infraestrutura em seguida](./media/backup-try-azure-backup-in-10-mins/backup-goal-configed.png)

4. Na folha **Preparar infraestrutura**, clique em **Baixar agente do Windows Server ou Windows Client**.

    ![Preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

    Se você estiver usando o Windows Server Essential, opte por baixar o agente para o Windows Server Essential. Um menu pop-up solicitará que você execute ou salve MARSAgentInstaller.exe.

    ![Diálogo MARSAgentInstaller](./media/backup-try-azure-backup-in-10-mins/mars-installer-run-save.png)

5. Clique em **Salvar** no menu pop-up de download.

    Por padrão, o arquivo **MARSagentinstaller.exe** será salvo em sua pasta Downloads. Quando o instalador for concluído, será exibido um pop-up perguntando se você deseja executar o instalador ou abrir a pasta.

    ![Preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/mars-installer-complete.png)

    Você não precisa instalar o agente ainda. Você poderá instalar o agente depois de baixar as credenciais do cofre.

6. Na folha **Preparar infraestrutura**, clique em **Baixar**.

    ![baixar as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

    As credenciais do cofre são baixadas para a pasta Downloads. Após o término do download das credenciais do cofre, você verá um pop-up perguntando se deseja abrir ou salvar as credenciais. Clique em **Save** (Salvar). Se você clicar acidentalmente em **Abrir**, deixe a caixa de diálogo que tenta abrir as credenciais do cofre falhar. Não é possível abrir as credenciais do cofre. Vá para a próxima etapa. As credenciais do cofre estão na pasta Downloads.

    ![o download das credenciais do cofre foi concluído](./media/backup-try-azure-backup-in-10-mins/vault-credentials-downloaded.png)
   > [!NOTE]
   > As credenciais do cofre devem ser salvas apenas em um local do Windows Server no qual você pretende usar o agente.
   >

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="install-and-register-the-agent"></a>Instalar e registrar o agente

> [!NOTE]
> A habilitação do backup pelo portal do Azure ainda não está disponível. Use o agente de Serviços de Recuperação do Microsoft Azure para fazer backup do Estado do Sistema do Windows Server.
>

1. Localize e clique duas vezes no **MARSagentinstaller.exe** na pasta Downloads (ou em outro local salvo).

    O instalador fornece uma série de mensagens, pois extrai, instala e registra o agente dos Serviços de Recuperação.

    ![execute as credenciais do instalador do agente dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/mars-installer-registration.png)

2. Conclua o Assistente de Instalação do Agente do Serviços de Recuperação do Microsoft Azure. Para concluir o assistente, você precisa fazer o seguinte:

   * Escolher um local para a instalação e a pasta de cache.
   * Fornecer as informações de seu servidor proxy se você usar um servidor proxy para conectar-se à Internet.
   * Forneça os detalhes do seu nome de usuário e de sua senha se usar um proxy autenticado.
   * Forneça as credenciais do cofre baixado
   * Salve a senha de criptografia em um local seguro.

     > [!NOTE]
     > Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Salve o arquivo em um local seguro. Isso é necessário para restaurar um backup.
     >
     >

Agora, o agente está instalado e seu computador está registrado no cofre. Você está pronto para configurar e agendar o backup.

## <a name="back-up-windows-server-system-state"></a>Fazer backup do Estado do Sistema do Windows Server

O backup inicial inclui duas tarefas:

* Agendar o backup
* Fazer backup do Estado do Sistema pela primeira vez

Para concluir o backup inicial, use o agente dos Serviços de Recuperação do Microsoft Azure.

> [!NOTE]
> Você pode fazer backup do Estado do Sistema no Windows Server 2008 R2 por meio do Windows Server 2016. O Backup do Estado do Sistema não tem suporte em SKUs do cliente. O Estado do Sistema não é mostrado como uma opção para clientes do Windows ou máquinas do Windows Server 2008 SP2.
>
>

### <a name="to-schedule-the-backup-job"></a>Para agendar o trabalho de backup

1. Abra o agente dos Serviços de Recuperação do Microsoft Azure Você pode localizá-lo pesquisando no seu computador por **Backup do Microsoft Azure**.

    ![Inicialize o agente dos Serviços de Recuperação do Azure](./media/backup-try-azure-backup-in-10-mins/snap-in-search.png)

2. O agente dos Serviços de Recuperação, clique em **Agendar Backup**.

    ![Agendar um backup do Windows Server](./media/backup-try-azure-backup-in-10-mins/schedule-first-backup.png)

3. Na página de Introdução do Assistente de Agendamento de Backup, clique em **Avançar**.

4. Na tela Selecionar Itens para Backup, clique em **Adicionar Itens**.

5. Selecione **Estado do sistema** e, em seguida, clique em **OK**.

6. Clique em **Próximo**.

7. Selecione a frequência de Backup necessária e a política de retenção para os backups de Estado do Sistema nas próximas páginas.

8. Na página Confirmação, examine as informações e clique em **Concluir**.

9. Depois que o assistente terminar de criar o agendamento de backup, clique em **Fechar**.

### <a name="to-back-up-windows-server-system-state-for-the-first-time"></a>Para fazer backup do Estado do Sistema do Windows Server pela primeira vez

1. Certifique-se de que não existem atualizações pendentes para o Windows Server que exigem uma reinicialização.

2. No Agente dos Serviços de Recuperação, clique em **Fazer Backup Agora** para concluir a propagação inicial pela rede.

    ![Backup do Windows Server agora](./media/backup-try-azure-backup-in-10-mins/backup-now.png)

3. Selecione **Estado do Sistema** na tela **Selecionar Item de Backup** exibida e clique em **Avançar**.

4. Na página Confirmação, examine as configurações que o Assistente Fazer Backup Agora usará para fazer backup do computador. Em seguida, clique em **Fazer Backup**.

5. Clique em **Fechar** para fechar o assistente. Se você fechar o assistente antes da conclusão do processo de backup, o assistente continuará a ser executado em segundo plano.
    > [!NOTE]
    > O agente MARS dispara o/VERIFYONLY do SFC como parte das verificações anteriores antes de todos os backups de estado do sistema. Isso é para garantir que os arquivos submetidos a backup como parte do estado do sistema tenham as versões corretas correspondentes à versão do Windows. Saiba mais sobre o verificador de arquivos do sistema (SFC) neste [artigo](https://docs.microsoft.com/windows-server/administration/windows-commands/sfc).
    >

Depois que o backup inicial for concluído, o status **Trabalho concluído** aparecerá no Console de backup.

  ![IR completo](./media/backup-try-azure-backup-in-10-mins/ircomplete.png)

## <a name="questions"></a>Dúvidas?

Se você tiver dúvidas ou gostaria de ver algum recurso incluído, [envie-nos seus comentários](https://feedback.azure.com/forums/258995-azure-backup).

## <a name="next-steps"></a>Próximas etapas

* Obtenha mais detalhes sobre o [backup de computadores que usam o Windows](backup-windows-with-mars-agent.md).
* Agora que você fez backup do Estado do Sistema do Windows Server, [gerencie seus cofres e servidores](backup-azure-manage-windows-server.md).
* Se você precisar restaurar um backup, use este artigo para [restaurar os arquivos para um computador que usa o Windows](backup-azure-restore-windows-server.md).
