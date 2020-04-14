---
title: Restaurar VMs usando o portal Azure
description: Restaure uma máquina virtual do Azure de um ponto de recuperação usando o portal do Azure
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: b9cdb187aa3b8750bead8e81ad6d0ee50dcb3d6c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254913"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Como restaurar os dados do Azure VM no portal Azure

Este artigo descreve como restaurar os dados da VM do Azure a partir dos pontos de recuperação armazenados em cofres dos Serviços de Recuperação do [Backup do Azure](backup-overview.md).

## <a name="restore-options"></a>Restaurar opções

O Backup do Azure proporciona várias maneiras de restaurar uma VM.

**Opção de restauração** | **Detalhes**
--- | ---
**Criar uma nova VM** | Cria e coloca rapidamente uma VM básica em funcionamento a partir de um ponto de restauração.<br/><br/> Você pode especificar um nome para a VM, selecionar o grupo de recursos e a rede virtual (VNet) na qual ele será colocado e especificar uma conta de armazenamento para a VM restaurada. A nova VM deve ser criada na mesma região da VM de origem.
**Restaurar disco** | Restaura um disco VM, que pode ser usado para criar uma nova VM.<br/><br/> O Backup do Azure fornece um modelo para ajudá-lo a personalizar e criar uma VM. <br/><br> O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações de VM personalizadas e criar uma VM.<br/><br/> Os discos são copiados para o Grupo de recursos especificado.<br/><br/> Como alternativa, você pode anexar o disco a uma VM existente ou criar uma nova VM usando o PowerShell.<br/><br/> Essa opção é útil se você quiser personalizar a VM, adicionar configurações que não existiam no momento do backup ou adicionar configurações que devem ser definidas usando o modelo ou o PowerShell.
**Substituir existente** | Você pode restaurar um disco e usá-lo para substituir um disco na VM existente.<br/><br/> A VM atual deve existir. Se foi excluído, esta opção não pode ser usada.<br/><br/> O Azure Backup tira um instantâneo da VM existente antes de substituir o disco e armazena-o no local de preparação especificado. Os discos existentes conectados à VM são substituídos pelo ponto de restauração selecionado.<br/><br/> O instantâneo é copiado para o cofre e retido de acordo com a política de retenção. <br/><br/> Após a operação do disco de substituição, o disco original é retido no grupo de recursos. Você pode optar por excluir manualmente os discos originais se eles não forem necessários. <br/><br/>A substituição existente é suportada para VMs gerenciadas não criptografadas, incluindo VMs [criadas usando imagens personalizadas](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/). Não é suportado para VMs clássicos.<br/><br/> Se o ponto de restauração tem mais ou menos discos do que a VM atual, o número de discos no ponto de restauração reflete apenas a configuração da VM.<br><br> Substituir o existente não é suportado por VMs por recursos vinculados (como [identidade gerenciada atribuída pelo usuário](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) ou Key [Vault)](https://docs.microsoft.com/azure/key-vault/key-vault-overview)porque o aplicativo cliente de backup não tem permissões nesses recursos durante a execução da restauração.
**Região Transversal (região secundária)** | A restauração da Região Transversal pode ser usada para restaurar as VMs do Azure na região secundária, que é uma [região emparelhada azure.](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)<br><br> Você pode restaurar todas as VMs do Azure para o ponto de recuperação selecionado se o backup for feito na região secundária.<br><br> Este recurso está disponível para as opções abaixo:<br> * [Criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) <br> * [Restaurar discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> No momento, não suportamos a opção [Substituir discos existentes.](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks)<br><br> Permissões<br> A operação de restauração na região secundária pode ser realizada por admins de backup e admins de aplicativos.

> [!NOTE]
> Você também pode recuperar arquivos e pastas específicos em uma VM do Azure. [Saiba mais](backup-azure-restore-files-from-vm.md).

## <a name="storage-accounts"></a>Contas de armazenamento

Alguns detalhes sobre contas de armazenamento:

- **Criar VM**: Quando você criar uma nova VM, a VM será colocada na conta de armazenamento especificada.
- **Restaurar disco**: Quando você restaura um disco, o disco é copiado para a conta de armazenamento especificada. O trabalho de restauração gera um modelo que você pode baixar e usar para especificar configurações personalizadas de VM. Este modelo é colocado na conta de armazenamento especificada.
- **Substitua o disco**: Quando você substitui um disco em uma VM existente, o Azure Backup tira um instantâneo da VM existente antes de substituir o disco. O instantâneo é armazenado no local de preparação (conta de armazenamento) especificado. Esta conta de armazenamento é usada para armazenar temporariamente o instantâneo durante o processo de restauração, e recomendamos que você crie uma nova conta para fazer isso, que pode ser facilmente removida depois.
- **Localização da conta de**armazenamento : A conta de armazenamento deve estar na mesma região do cofre. Apenas essas contas são exibidas. Se não houver contas de armazenamento no local, você precisa criar uma.
- **Tipo de armazenamento**: O armazenamento blob não é suportado.
- **Redundância de armazenamento**: O ZRS (Zone redundante Storage, armazenamento redundante da zona) não é suportado. As informações de replicação e redundância da conta são mostradas entre parênteses após o nome da conta.
- **Armazenamento premium:**
  - Ao restaurar VMs não premium, as contas de armazenamento premium não são suportadas.
  - Ao restaurar VMs gerenciadas, as contas de armazenamento premium configuradas com regras de rede não são suportadas.

## <a name="before-you-start"></a>Antes de começar

Para restaurar uma VM (criar uma nova VM), certifique-se de que você tenha as [permissões corretas](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) de controle de acesso baseado em função (RBAC) para a operação Restaurar VM.

Se você não tiver permissões, poderá [restaurar um disco](#restore-disks)e, depois que o disco for restaurado, poderá usar o [modelo](#use-templates-to-customize-a-restored-vm) gerado como parte da operação de restauração para criar uma nova VM.

## <a name="select-a-restore-point"></a>Selecione um ponto de restauração

1. No cofre associado à VM que deseja restaurar, clique **em Backup itemS** > **Azure Virtual Machine**.
2. Clique em uma VM. Por padrão, os pontos de recuperação dos últimos 30 dias são exibidos no painel da VM. Você pode exibir pontos de recuperação com mais de 30 dias ou filtrar para encontrar pontos de recuperação com base em datas, intervalos de tempo e diferentes tipos de consistência de instantâneos.
3. Para restaurar a VM, clique em **Restaurar VM**.

    ![Ponto de restauração](./media/backup-azure-arm-restore-vms/restore-point.png)

4. Selecione um ponto de restauração para usar na recuperação.

## <a name="choose-a-vm-restore-configuration"></a>Escolha uma configuração de restauração de VM

1. Na **Configuração de restauração**, selecione uma opção de restauração:
    - **Criar novo**: Use esta opção se quiser criar uma nova VM. Você pode criar uma VM com configurações simples ou restaurar um disco e criar uma VM personalizada.
    - **Substitua a existente**: Use esta opção se quiser substituir discos em uma VM existente.

        ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. Especifique as configurações para sua opção de restauração selecionada.

## <a name="create-a-vm"></a>Criar uma máquina virtual

Como uma das [opções de restauração](#restore-options), você pode criar uma VM rapidamente com configurações básicas a partir de um ponto de restauração.

1. Na **configuração** > Restaurar**Crie um novo** > **tipo de restauração,** selecione **Criar uma máquina virtual**.
2. Em **nome da máquina Virtual,** especifique uma VM que não existe na assinatura.
3. No **Grupo de recursos**, selecione um grupo de recursos existente para a nova máquina virtual ou crie um novo com um nome exclusivo globalmente. Se você atribuir um nome que já existe, o Azure atribui ao grupo o mesmo nome da VM.
4. Em **Rede virtual**, selecione a rede virtual na qual a VM será colocada. Todas as redes virtuais associadas à assinatura são exibidas. Escolher a sub-rede. A primeira sub-rede será selecionada por padrão.
5. Em **Local de armazenamento,** especifique a conta de armazenamento da VM. [Saiba mais](#storage-accounts).

    ![Assistente de configuração de restauração](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. Em **Restaurar configuração**, selecione **OK**. Em **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.

## <a name="restore-disks"></a>Restaurar discos

Como uma das [opções de restauração](#restore-options), você pode criar um disco a partir de um ponto de restauração. Em seguida, com o disco, você pode fazer o seguinte:

- Use o modelo gerado durante a operação de restauração para personalizar as configurações e disparar a implantação da VM. Você edita as configurações de modelo padrão e envia o modelo para implantação da VM.
- [Anexe discos restaurados](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) a uma VM existente.
- [Crie uma nova VM](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) a partir dos discos restaurados usando o PowerShell.

1. Na **configuração** > Restaurar**Crie um novo** > **tipo de restauração,** selecione **Restaurar discos**.
2. No **Grupo de recursos**, selecione um grupo de recursos existente para os discos recuperados ou crie um novo com um nome exclusivo globalmente.
3. Em **Conta de armazenamento**, especifique a conta para a qual copiar os VHDs. [Saiba mais](#storage-accounts).

    ![Configuração de recuperação concluída](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. Em **Restaurar configuração**, selecione **OK**. Em **Restaurar**, clique em **Restaurar** para disparar a operação de restauração.

Quando sua máquina virtual usa discos gerenciados e você seleciona a opção **Criar máquina virtual,** o Azure Backup não usa a conta de armazenamento especificada. No caso de **Restaurar discos** e **Restauração Instantânea,** a conta de armazenamento é usada apenas para armazenar o modelo. Os discos gerenciados são criados no grupo de recursos especificado.
Quando sua máquina virtual usa discos não gerenciados, eles são restaurados como blobs para a conta de armazenamento.

### <a name="use-templates-to-customize-a-restored-vm"></a>Usar modelos para personalizar uma VM restaurada

Depois de restaurar o disco, use o modelo gerado como parte da operação de restauração para personalizar e criar uma nova VM:

1. Abra **Detalhes do Trabalho de Restauração** para o trabalho relevante.

2. Em **Detalhes do Trabalho de Restauração**, selecione **Implantar Modelo** para iniciar a implantação de modelo.

    ![Detalhamento do trabalho de restauração](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. Para personalizar a configuração da VM fornecida no modelo, clique em **Editar modelo**. Se você quiser adicionar mais personalizações, clique em **Editar parâmetros**.
    - [Saiba mais](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) sobre como implantar recursos a partir de um modelo personalizado.
    - [Saiba mais](../azure-resource-manager/templates/template-syntax.md) sobre como criar modelos.

   ![Carregar a implantação de modelo](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. Insira os valores personalizados para a VM, aceite os **Termos e Condições** e clique em **Comprar**.

   ![Enviar a implantação de modelo](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>Substituir discos existentes

Como uma das [opções de restauração](#restore-options), você pode substituir um disco de VM existente pelo ponto de restauração selecionado. [Analise](#restore-options) todas as opções de restauração.

1. Em **Restaurar configuração**, clique em **Substituir existente**.
2. Em **Tipo de Restauração**, selecione **Substituir disco**. Este é o ponto de restauração que será usado para substituir discos de VM existentes.
3. Em **Local de preparação,** especifique onde os instantâneos dos discos gerenciados atuais devem ser salvos durante o processo de restauração. [Saiba mais](#storage-accounts).

   ![Restaurar o Assistente de configuração de substituir existente](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>Restauração da região cruzada

Como uma das opções de [restauração,](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)o CRR (Cross Region Restore, restauração de regiões cruzadas) permite restaurar as VMs do Azure em uma região secundária, que é uma região emparelhada do Azure.

Para embarcar no recurso durante a pré-visualização, leia a [seção Antes de Começar](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore).

Para ver se o CRR está habilitado, siga as instruções em [Configurar restauração de região cruzada](backup-create-rs-vault.md#configure-cross-region-restore)

### <a name="view-backup-items-in-secondary-region"></a>Exibir itens de backup na região secundária

Se o CRR estiver habilitado, você poderá visualizar os itens de backup na região secundária.

1. A partir do portal, vá para **o cofre** > do Recovery Services Itens de**backup**
2. Clique **em Região Secundária** para ver os itens na região secundária.

![Máquinas virtuais na região secundária](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![Selecione Região Secundária](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>Restauração na região secundária

A experiência do usuário de restauração da região secundária será semelhante à experiência do usuário de restauração da região primária. Ao configurar detalhes na lâmina restaurar configuração para configurar sua restauração, você será solicitado a fornecer apenas parâmetros de região secundária.

![Escolha a VM para restaurar](./media/backup-azure-arm-restore-vms/sec-restore.png)

![Folha Selecionar ponto de restauração](./media/backup-azure-arm-restore-vms/sec-rp.png)

![Configuração de restauração](./media/backup-azure-arm-restore-vms/rest-config.png)

![Iniciar a restauração em notificação de progresso](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- Para restaurar e criar uma VM, consulte [Criar uma VM](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm).
- Para restaurar como um disco, consulte [Restaurar discos](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks).

>[!NOTE]
>Depois que a restauração é acionada e na fase de transferência de dados, o trabalho de restauração não pode ser cancelado.

### <a name="monitoring-secondary-region-restore-jobs"></a>Monitoramento da região secundária restaura empregos

1. A partir do portal, vá para **Recovery Services vault** > **Backup Jobs**
2. Clique **em Região Secundária** para ver os itens na região secundária.

![Trabalhos de backup filtrados](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>Restaurar VMs com configurações especiais

Há vários cenários comuns nos quais você pode precisar restaurar VMs.

**Cenário** | **Orientação**
--- | ---
**Restaurar VMs usando Benefícios de uso híbrido** | Se uma VM do Windows usa o [licenciamento do Benefício de uso híbrido (HUB)](../virtual-machines/windows/hybrid-use-benefit-licensing.md), restaure os discos e crie uma nova VM usando o modelo fornecido (com **Tipo de Licença** definido como **Windows_Server**) ou o PowerShell.  Essa configuração também pode ser aplicada após a criação da VM.
**Restaurar VMs durante um desastre de datacenter do Azure** | Se o cofre usa GRS e o datacenter principal da VM ficar inativo, o Backup do Azure oferece suporte à restauração de VMs de backup no datacenter emparelhado. Você seleciona uma conta de armazenamento no datacenter emparelhado e a restaura normalmente. O Azure Backup usa o serviço de computação na região emparelhada para criar a VM restaurada. [Saiba mais](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md) sobre a resiliência do datacenter.
**Restaurar VM do controlador de domínio único no domínio único** | Restaure a VM como qualquer outra VM. Observe que:<br/><br/> De uma perspectiva de Diretório Ativo, a VM do Azure é como qualquer outra VM.<br/><br/> O DSRM (Modo de Restauração dos Serviços de Diretório) também está disponível, portanto, todos os cenários de recuperação do Active Directory são viáveis. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps) sobre considerações de backup e restauração para controladores de domínio virtualizados.
**Restaurar várias VMs do controlador de domínio em domínio único** | Se outros controladores de domínio no mesmo domínio puderem ser alcançados pela rede, o controlador de domínio poderá ser restaurado como qualquer VM. Se for o último controlador de domínio restante no domínio, ou uma recuperação em uma rede isolada for executada, use uma [recuperação de floresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restaurar vários domínios em uma floresta** | Recomendamos uma [recuperação de floresta](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery).
**Restauração bare-metal** | A principal diferença entre VMs do Azure e hipervisores locais é que não há nenhum console de VM disponível no Azure. Um console é necessário para determinados cenários, como a recuperação usando um backup do tipo BMR (recuperação bare-metal). No entanto, a restauração de VM do cofre é uma substituição completa para a BMR.
**Restaurar VMs com configurações de rede especiais** | Configurações especiais de rede incluem VMs usando balanceamento de carga interno ou externo, usando vários NICS ou vários endereços IP reservados. Restaure essas VMs usando a [opção de disco de restauração](#restore-disks). Essa opção faz uma cópia dos VHDs na conta de armazenamento especificada e, em seguida, você pode criar uma VM com um balanceador de carga [interno](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) ou [externo,](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) [vários NICS](../virtual-machines/windows/multiple-nics.md)ou [vários endereços IP reservados,](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)de acordo com sua configuração.
**Grupo de Segurança de Rede (NSG) em NIC/Subnet** | O backup do Azure VM suporta backup e restauração de informações do NSG no nível vnet, sub-net e NIC.
**VMs com fixação de zona** | O Azure Backup suporta backup e restauração de VMs com zonas fixas. [Saiba mais](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>Acompanhar a operação de restauração

Após disparar a operação de restauração, o serviço de backup cria um trabalho para acompanhamento. O Backup do Azure exibe notificações sobre o trabalho no portal. Se eles não estiverem visíveis, selecione o símbolo **Notificações** e selecione **Exibir todas as Vagas** para ver o Status do processo de restauração.

![Restauração disparada](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 Acompanhe a restauração da seguinte forma:

1. Para ver as operações do trabalho, clique no hiperlink de notificações. Como alternativa, no cofre, clique em **Trabalhos de Backup** e, em seguida, clique na VM relevante.

    ![Lista de VMs em um cofre](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. Para monitorar o progresso da restauração, clique em qualquer tarefa de restauração com o status **In-progress**. Isso exibe a barra de progresso, que exibe informações sobre o progresso da restauração:

    - **Tempo estimado de restauração**: Inicialmente fornece o tempo necessário para concluir a operação de restauração. À medida que a operação progride, o tempo gasto reduz e chega a zero quando a operação de restauração é concluída.
    - **Porcentagem de restauração**. Mostra a porcentagem concluída da operação de restauração.
    - **Número de bytes transferidos**: Se você está restaurando criando uma nova VM, ele mostra os bytes que foram transferidos contra o número total de bytes a serem transferidos.

## <a name="post-restore-steps"></a>Etapas pós-restauração

Há vários itens a observar após a restauração de uma VM:

- Extensões presentes durante a configuração do backup serão instaladas, mas não habilitadas. Se encontrar um problema, reinstale as extensões.
- Se a VM de backup tiver um endereço IP estático, a VM restaurada terá um endereço IP dinâmico para evitar conflitos. Você pode [adicionar um endereço IP estático à VM restaurada](https://docs.microsoft.com/powershell/module/az.network/set-aznetworkinterfaceipconfig?view=azps-3.5.0#description).
- Uma VM restaurada não tem um conjunto de disponibilidade. Se você usar a opção de disco de restauração, você poderá [especificar um conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md) quando criar uma VM a partir do disco usando o modelo fornecido ou o PowerShell.
- Se você usar uma distribuição Linux baseada em inicialização da nuvem, como o Ubuntu, a senha será bloqueada após a restauração por motivos de segurança. Use uma extensão VMAccess na VM restaurada para [redefinir a senha](../virtual-machines/linux/reset-password.md). Recomendamos o uso de chaves SSH nessas distribuições, para que você não precise redefinir a senha após a restauração.
- Se você não conseguir acessar a VM uma vez restaurada devido à VM ter relações quebradas com o controlador de domínio, siga as etapas abaixo para trazer a VM:
  - Anexar disco do SISTEMA OPERACIONAL como um disco de dados a uma VM recuperada.
  - Instale manualmente o agente VM se o Azure Agent não for responsivo seguindo este [link](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
  - Permitir acesso ao Console Serial na VM para permitir acesso à linha de comando à VM

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - Quando a VM for reconstruída, use o portal Azure para redefinir a conta e a senha do administrador local
  - Use acesso ao console Serial e CMD para desfazer vm do domínio

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- Uma vez que a VM seja desacompanhada e reiniciada, você poderá fazer um RDP para VM com sucesso com credenciais de admin local e recolocar a VM de volta ao domínio com sucesso.

## <a name="backing-up-restored-vms"></a>Fazer backup de VMs restauradas

- Se você tiver restaurado uma VM no mesmo grupo de recursos e com o mesmo nome que a VM do backup original, o backup continuará na VM após a restauração.
- Se você tiver restaurado a VM para outro grupo de recursos ou tiver especificado um nome diferente para a VM restaurada, é preciso configurar o backup para a VM restaurada.

## <a name="next-steps"></a>Próximas etapas

- Se você tiver dificuldades durante o processo de restauração, [verifique](backup-azure-vms-troubleshoot.md#restore) os problemas e erros comuns.
- Depois de restaurar a VM, aprenda mais sobre o [gerenciamento de máquinas virtuais](backup-azure-manage-vms.md).
