---
title: Fazer backup e restaurar VMs Azure criptografados
description: Descreve como fazer backup e restaurar VMs Azure criptografados com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 98febe9f91cb4b71d546300d4e65ade073d19e67
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461762"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>Fazer backup e restaurar a VM Azure criptografada

Este artigo descreve como fazer backup e restaurar máquinas virtuais (VMs) do Windows ou Linux Azure com discos criptografados usando o serviço de backup do [Azure.](backup-overview.md)

Se você quiser saber mais sobre como o Azure Backup interage com as VMs do Azure antes de começar, revise esses recursos:

- [Revise](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup do Azure VM.
- [Saiba mais sobre](backup-azure-vms-introduction.md) Backup do Azure VM e a extensão azure Backup.

## <a name="encryption-support"></a>Suporte à criptografia

O Azure Backup suporta backup de VMs do Azure que têm seus discos de sO/dados criptografados com a Ade (ADE). O ADE usa o BitLocker para criptografia de VMs do Windows e o recurso dm-crypt para VMs Linux. O ADE se integra ao Azure Key Vault para gerenciar chaves e segredos de criptografia de disco. Key Vault Key Encryption Keys (KEKs) pode ser usado para adicionar uma camada adicional de segurança, criptografando segredos de criptografia antes de escrevê-los no Key Vault.

O Azure Backup pode fazer backup e restaurar As VMs do Azure usando o ADE com e sem o aplicativo Azure AD, conforme resumido na tabela a seguir.

**Tipo de disco VM** | **ADE (BEK/dm-cript)** | **ADE e KEK**
--- | --- | ---
**Gerenciado** | Sim | Sim
**Gerenciado**  | Sim | Sim

- Saiba mais sobre [ADE,](../security/azure-security-disk-encryption-overview.md) [Key Vault](../key-vault/general/overview.md)e [KEKs](https://docs.microsoft.com/azure/virtual-machine-scale-sets/disk-encryption-key-vault#set-up-a-key-encryption-key-kek).
- Leia a [FAQ](../security/azure-security-disk-encryption-faq.md) para criptografia de disco Azure VM.

### <a name="limitations"></a>Limitações

- Você pode fazer backup e restaurar VMs criptografadas dentro da mesma assinatura e região.
- O Azure Backup suporta VMs criptografados usando chaves autônomas. Qualquer chave que faça parte de um certificado usado para criptografar uma VM não é suportada no momento.
- Você pode fazer backup e restaurar VMs criptografadas dentro da mesma assinatura e região que o cofre de backup dos serviços de recuperação.
- As VMs criptografadas não podem ser recuperadas no nível de arquivo/pasta. Você precisa recuperar toda a VM para restaurar arquivos e pastas.
- Ao restaurar uma VM, você não pode usar a opção [de substituir VM existente](backup-azure-arm-restore-vms.md#restore-options) para VMs criptografadas. Essa opção só é suportada para discos gerenciados não criptografados.

## <a name="before-you-start"></a>Antes de começar

Antes de começar, faça o seguinte:

1. Certifique-se de ter um ou mais VMs [Windows](../security/azure-security-disk-encryption-windows.md) ou [Linux](../virtual-machines/linux/disk-encryption-overview.md) com ADE ativado.
2. [Revise a matriz de suporte](backup-support-matrix-iaas.md) para backup do Azure VM
3. [Crie](backup-azure-arm-vms-prepare.md#create-a-vault) um cofre de backup de serviços de recuperação se você não tiver um.
4. Se você habilitar criptografia para VMs que já estão habilitadas para backup, basta fornecer ao Backup permissões para acessar o Key Vault para que os backups possam continuar sem interrupções. [Saiba mais](#provide-permissions) sobre como atribuir essas permissões.

Além disso, há algumas coisas que você pode precisar fazer em algumas circunstâncias:

- **Instale o agente VM no VM**: O Backup Do Azure faz backup das VMs do Azure instalando uma extensão para o agente Azure VM em execução na máquina. Se a VM foi criada a partir de uma imagem de mercado do Azure, o agente será instalado e executado. Se você criar uma VM personalizada ou migrar uma máquina no local, talvez seja necessário [instalar o agente manualmente](backup-azure-arm-vms-prepare.md#install-the-vm-agent).

## <a name="configure-a-backup-policy"></a>Configurar uma política de backup

1. Se você ainda não criou um cofre de backup dos Serviços de Recuperação, siga [estas instruções](backup-azure-arm-vms-prepare.md#create-a-vault)
2. Abra o cofre no portal e selecione **Backup** na seção **Como Começar.**

    ![Folha Backup](./media/backup-azure-vms-encryption/select-backup.png)

3. Em **Backup objetivo** > **Onde sua carga de trabalho está sendo executado?** selecione **Azure**.
4. Em **Que você deseja fazer backup?** selecione Máquina >  **Virtual****OK**.

      ![Folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. Na **política de backup** > **Escolha a diretiva de backup,** selecione a política que deseja associar ao cofre. Em seguida, clique em **OK**.
    - Uma política de backup especifica quando os backups são feitos e quanto tempo eles são armazenados.
    - Os detalhes da política padrão estão listados no menu suspenso.

    ![Abrir a folha Cenário](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. Se você não quiser usar a diretiva padrão, selecione **Criar novo**e crie uma [diretiva personalizada](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. Escolha as VMs criptografadas que deseja fazer backup usando a diretiva select e selecione **OK**.

      ![Selecionar VMs criptografadas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Se você estiver usando o Azure Key Vault, na página do cofre, você verá uma mensagem de que o Azure Backup precisa de acesso somente de leitura às chaves e segredos no Cofre de Chaves.

    - Se você receber esta mensagem, nenhuma ação é necessária.

        ![Acesse OK](./media/backup-azure-vms-encryption/access-ok.png)

    - Se você receber esta mensagem, você precisa definir permissões conforme descrito no [procedimento abaixo](#provide-permissions).

        ![Aviso de acesso](./media/backup-azure-vms-encryption/access-warning.png)

9. Clique **em Ativar backup** para implantar a diretiva de backup no cofre e habilitar o backup para as VMs selecionadas.

## <a name="trigger-a-backup-job"></a>Disparar um trabalho de backup

O backup inicial será executado de acordo com o cronograma, mas você pode executá-lo imediatamente da seguinte forma:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **Itens de backup,** clique **em Azure Virtual Machine**.
3. Na lista **Itens de backup,** clique nas elipses (...).
4. Clique em **Fazer backup agora**.
5. Em **Backup Now,** use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
6. Monitorar as notificações do portal. Você pode monitorar o progresso do trabalho no painel do cofre > **backup jobs** > **em andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.

## <a name="provide-permissions"></a>Fornecer permissões

O Azure VM precisa de acesso somente à leitura para fazer backup das chaves e segredos, juntamente com as VMs associadas.

- Seu Key Vault está associado ao inquilino Azure AD da assinatura do Azure. Se você é um **usuário membro,** o Azure Backup adquire acesso ao Key Vault sem mais ações.
- Se você é um **usuário convidado,** você deve fornecer permissões para o Azure Backup acessar o cofre-chave.

Para definir permissões:

1. No portal Azure, selecione **Todos os serviços**e procure por **cofres Chave.**
2. Selecione o cofre de chaves associado à VM criptografada que você está fazendo backup.
3. Selecione **políticas de acesso** > **Adicionar novas**.
4. Selecione **Selecionar principal**e, em seguida, digite **gerenciamento de backup**.
5. Selecione **O serviço de gerenciamento** > **de backup Select**.

    ![Seleção de serviço de backup](./media/backup-azure-vms-encryption/select-backup-service.png)

6. Em **Adicionar política** > de acesso**Configurar a partir do modelo (opcional)**, selecione Backup do **Azure**.
    - As permissões necessárias para **Permissões de chave** e **Permissões de segredo** são preenchidas.
    - Se a VM estiver criptografada **usando apenas BEK,** remova a seleção para **permissões de chave,** pois você só precisa de permissões para segredos.

    ![Seleção de backup do Azure](./media/backup-azure-vms-encryption/select-backup-template.png)

7. Clique em **OK**. **O Serviço de Gerenciamento de Backup** é adicionado às políticas de **acesso**.

    ![Políticas de acesso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. Clique **em Salvar** para fornecer o Backup do Azure com as permissões.

## <a name="restore-an-encrypted-vm"></a>Restaurar uma VM criptografada

Você restaura VMs criptografadas da seguinte forma:

1. [Restaure o disco da VM](backup-azure-arm-restore-vms.md#restore-disks).
2. Recriar a instância da máquina virtual fazendo um dos seguintes:
    1. Use o modelo gerado durante a operação de restauração para personalizar as configurações da VM e acione a implantação da VM. [Saiba mais](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. Crie uma nova VM a partir dos discos restaurados usando o PowerShell. [Saiba mais](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Para VMs Linux, reinstale a extensão ADE para que os discos de dados estejam abertos e montados.

## <a name="next-steps"></a>Próximas etapas

Se você encontrar qualquer problema, revise estes artigos:

- [Erros comuns](backup-azure-vms-troubleshoot.md) ao fazer backup e restaurar VMs Azure criptografadas.
- [Problemas de extensão de backup/agente de VM do Azure.](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md)
