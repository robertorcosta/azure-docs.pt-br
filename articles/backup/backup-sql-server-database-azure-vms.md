---
title: Fazer backup de bancos de dados do SQL Server nas VMs do Azure
description: Neste artigo, saiba como fazer backup de bancos de dados SQL Server em máquinas virtuais do Azure com o Azure Backup.
ms.reviewer: vijayts
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 887f15deed74330cf132e0574d166c074d2c7cad
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685723"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer backup de bancos de dados do SQL Server nas VMs do Azure

Os bancos de dados SQL Server são cargas de trabalho críticas que requerem um RPO (Objetivo de ponto de recuperação baixo) e retenção a longo prazo. Você pode fazer backup dos bancos de dados SQL Server em execução em máquinas virtuais (VMs) do [Azure usando o Azure Backup](backup-overview.md).

Este artigo mostra como fazer backup de um banco de dados do SQL Server que está sendo executado em um Azure VM para um cofre de Serviços de Recuperação de Backup do Azure.

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Criar e configurar um cofre.
> * Descubra bancos de dados e configure backups.
> * Configurar a proteção automática para bancos de dados.

>[!NOTE]
>**Exclusão suave para servidor SQL no Azure VM e soft delete for SAP HANA in Azure VM workloads** now available in preview.<br>
>Para se inscrever para a pré-visualização, escreva para nós emAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de um banco de dados do SQL Server, verifique os seguintes critérios:

1. Identifique ou crie um [cofre de Serviços de Recuperação](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) na mesma região e assinatura que a VM hospedando a instância do SQL Server.
2. Verifique se a VM tem [conectividade de rede.](backup-sql-server-database-azure-vms.md#establish-network-connectivity)
3. Certifique-se de que os bancos de dados do SQL Server sigam as [diretrizes de nomeação do banco de dados para o Backup do Azure](#database-naming-guidelines-for-azure-backup).
4. Verifique se você não tem nenhuma outra solução de backup habilitada para o banco de dados. Desabilite todos os outros backups do SQL Server antes de fazer backup do banco de dados.

> [!NOTE]
> Você pode habilitar o Backup do Azure para uma VM Azure e também para um banco de dados SQL Server em execução na VM sem conflito.

### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, um VM do SQL Server requer conectividade com endereços IP públicos do Azure. As operações de VM (descoberta de banco de dados, configuração de backups, agendamento de backups, restauração de pontos de recuperação etc.) falham sem conectividade com endereços IP públicos do Azure.

Estabeleça a conectividade usando uma das seguintes opções:

#### <a name="allow-the-azure-datacenter-ip-ranges"></a>Permitir os intervalos de IP do datacenter do Azure

Essa opção permite os [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no arquivo baixado. Para acessar um NSG (grupo de segurança de rede), use o cmdlet Set-AzureNetworkSecurityRule. Se a lista de destinatários confiáveis só incluir IPs específicos da região, você também precisará atualizar a lista de destinatários confiáveis quanto à marca de serviço do Azure AD (Azure Active Directory) para habilitar a autenticação.

#### <a name="allow-access-using-nsg-tags"></a>Permitir acesso usando marcas NSG

Se você usar um NSG para restringir a conectividade, deverá usar a marca de serviço AzureBackup para permitir o acesso de saída ao Backup do Azure. Além disso, você deverá permitir a conectividade para autenticação e transferência de dados usando [regras](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) para o Azure AD e o Armazenamento do Azure. Isso pode ser feito no portal do Azure ou por meio do PowerShell.

Para criar uma regra usando o portal:

  1. Em **Todos os Serviços**, acesse **Grupos de segurança de rede** e selecione o grupo de segurança de rede.
  2. Selecione **Regras de segurança de saída** em **Configurações**.
  3. Selecione **Adicionar**. Insira todos os detalhes necessários para criar uma regra, conforme descrito em [Configurações da regra de segurança](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group#security-rule-settings). Verifique se a opção **Destino** está definida como **Marca de Serviço** e se **Marca de serviço de destino** está definida como **AzureBackup**.
  4. Clique em **Adicionar** para salvar a regra de segurança de saída recém-criada.

Para criar uma regra usando o PowerShell:

 1. Adicione as credenciais de conta do Azure e atualize as nuvens nacionais<br/>
      `Add-AzureRmAccount`<br/>

 2. Selecione a assinatura do NSG<br/>
      `Select-AzureRmSubscription "<Subscription Id>"`

 3. Selecione o NSG<br/>
    `$nsg = Get-AzureRmNetworkSecurityGroup -Name "<NSG name>" -ResourceGroupName "<NSG resource group name>"`

 4. Adicione a permissão da regra de saída à marca de serviço do Backup do Azure<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureBackupAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureBackup" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 5. Adicione a permissão da regra de saída à marca de serviço do Armazenamento<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "StorageAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "Storage" -DestinationPortRange 443 -Description "Allow outbound traffic to Azure Backup service"`

 6. Adicione a permissão da regra de saída à marca de serviço AzureActiveDirectory<br/>
    `Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg -Name "AzureActiveDirectoryAllowOutbound" -Access Allow -Protocol * -Direction Outbound -Priority <priority> -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix "AzureActiveDirectory" -DestinationPortRange 443 -Description "Allow outbound traffic to AzureActiveDirectory service"`

 7. Salve o NSG<br/>
    `Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg`

**Permitir o acesso usando marcas do Firewall do Azure**. Se estiver usando o Firewall do Azure, crie uma regra de aplicativo usando a marca de [FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags) do AzureBackup. Isso permite o acesso de saída ao Backup do Azure.

**Implante um servidor proxy HTTP para direcionar o tráfego**. Quando você faz backup de um banco de dados SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o Azure Backup e dados para o Azure Storage. A extensão de backup também usa o Azure AD para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. Não há domínios curinga em uso com o Azure Backup para adicionar à lista de permitir suas regras de proxy. Você precisará usar as faixas de IP públicas para esses serviços fornecidos pelo Azure. As extensões são o único componente que está configurado para acesso à Internet pública.

As opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custos adicionais | É complexa de ser gerenciada porque os intervalos de endereços IP mudam com o tempo <br/><br/> Fornece acesso a todo o Azure, não somente ao Armazenamento do Azure
Usar marcas de serviço do NSG | Mais fácil de ser gerenciada, pois as alterações de intervalo são mescladas automaticamente <br/><br/> Sem custos adicionais <br/><br/> | Pode ser usada somente com NSGs <br/><br/> Fornece acesso a todo o serviço
Usar marcas de FQDN do Firewall do Azure | Mais fácil de ser gerenciada, pois os FQDNs necessários são gerenciados automaticamente | Pode ser usada somente com o Firewall do Azure
Usar um proxy HTTP | Único ponto de acesso à Internet para VMs <br/> | Custos adicionais para execução de uma VM com o software de proxy <br/> Sem endereços FQDN publicados, as regras de permitir estarão sujeitas a alterações de endereço IP do Azure

#### <a name="private-endpoints"></a>Pontos finais privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

### <a name="database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomeação de banco de dados para backup do Azure

Evite usar os seguintes elementos em nomes de banco de dados:

* Espaços de trilha e liderança
* Pontos de exclamação em trilha (!)
* Fechando suportes quadrados (])
* Ponto e vírgula ';'
* Barra para frente '/'

Aliasing está disponível para caracteres sem suporte, mas recomendamos evitá-los. Para obter informações, consulte [Noções básicas sobre o modelo de dados do serviço Tabela](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

>[!NOTE]
>A operação **Configurar proteção** para bancos de dados com caracteres especiais como "+" ou "&" em seu nome não é suportada. Você pode alterar o nome do banco de dados ou ativar **o Auto Protection,** que pode proteger com sucesso esses bancos de dados.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados do SQL Server

Como descobrir bancos de dados em execução em uma VM:

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos Serviços de Recuperação usado para fazer backup do banco de dados.

2. No painel de **cofre dos Serviços de Recuperação,** selecione **Backup**.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Em **Backup Goal**, defina Onde **Azure**sua carga de trabalho está **sendo executado?**

4. Em **Do que você deseja fazer backup**, selecione **SQL Server em uma VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Backup Goal** > **Discover DBs em VMs,** selecione **Iniciar o Discovery** para procurar VMs desprotegidas na assinatura. Essa pesquisa pode demorar um pouco, dependendo do número de VMs desprotegidas na assinatura.

   * As VMs não protegidas devem ser exibidas na lista após a descoberta, listadas por nome e grupo de recursos.
   * Se uma VM não estiver listada como você espera, veja se ela já está em backup em um cofre.
   * Várias VMs podem ter o mesmo nome, mas pertencerão a diferentes grupos de recursos.

     ![O backup está pendente durante a pesquisa por BDs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VM, selecione a VM que executa o banco de dados do SQL Server > **Descobrir BDs**.

7. Rastrear a descoberta do banco de dados em **Notificações**. O tempo necessário para essa ação depende do número de bancos de dados vm. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Backup do Azure descobre todos os bancos de dados do SQL Server na VM. Durante a descoberta, ocorrem os seguintes elementos em segundo plano:

    * O Azure Backup registra a VM com o cofre para backup da carga de trabalho. Todos os bancos de dados na VM registrada podem ser copiados apenas para este cofre.
    * O Backup do Azure instala a extensão AzureBackupWindowsWorkload na VM. Nenhum agente está instalado em um banco de dados SQL.
    * O Backup do Azure cria a conta de serviço NT Service\AzureWLBackupPluginSvc na VM.
      * Todas as operações de backup e restauração usam a conta de serviço.
      * Nt Service\AzureWLBackupPluginSvc requer permissões SQL sysadmin. Todas as VMs do SQL Server criadas no Marketplace vêm com o SqlIaaAExtension instalado. A extensão AzureBackupWindowsWorkload usa a SQLIaaSExtension para obter automaticamente as permissões necessárias.
    * Se você não criou o VM do Marketplace ou se você estiver no SQL 2008 e 2008 R2, a VM pode não ter o SqlIaaAExtension instalado e a operação de detecção falha com a mensagem de erro UserErrorSQLNoSysAdminMembership. Para corrigir este problema, siga as instruções em [Set VM permissões](backup-azure-sql-database.md#set-vm-permissions).

        ![Selecionar a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar o backup  

1. Em **Backup Goal** > **Passo 2: Configure backup,** selecione Configurar **backup**.

   ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Em Selecionar itens para backup, você verá todos os grupos de disponibilidade registrados e **instâncias autônomas**do SQL Server. Selecione a seta à esquerda de uma linha para expandir a lista de todos os bancos de dados desprotegidos nessa instância ou o grupo de disponibilidade Always On.  

    ![Exibindo todas as Instâncias do SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Escolha todos os bancos de dados que deseja proteger e selecione **OK**.

   ![Proteção do banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Backup do Azure define um número máximo de bancos de dados em um trabalho de backup como 50.

     * Para proteger mais de 50 bancos de dados, configure vários backups.
     * Para [ativar](#enable-auto-protection) a instância inteira ou o grupo de disponibilidade Always On, na lista de isto de suspensão **AUTOPROTECT,** selecione **ON**e selecione **OK**.

    > [!NOTE]
    > O recurso [de proteção automática](#enable-auto-protection) não só permite a proteção em todos os bancos de dados existentes de uma só vez, mas também protege automaticamente quaisquer novos bancos de dados adicionados a essa instância ou ao grupo de disponibilidade.  

4. Selecione **OK** para abrir a **diretiva de backup**.

    ![Habilite a proteção automática para o grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

5. Na **diretiva Backup,** escolha uma diretiva e selecione **OK**.

   * Selecione a diretiva padrão como HourlyLogBackup.
   * Escolher uma política de backup existente criada anteriormente para SQL.
   * Definir uma nova política baseada no seu período de retenção e o RPO.

     ![Selecionar a Política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Em **Backup**, escolha **Habilitar backup**.

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhe o progresso da configuração na área **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
* Quando você cria uma política de backup, um backup completo diário é o padrão.
* Você poderá adicionar um backup diferencial, mas somente se configurar backups completos para que ocorram semanalmente.
* Saiba mais sobre [diferentes tipos de políticas de backup](backup-architecture.md#sql-server-backup-types).

Para criar uma política de backup:

1. No cofre, selecione **Adicionar políticas de backup** > **.**
2. Em **Adicionar,** selecione **SQL Server no Azure VM** para definir o tipo de diretiva.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **Nome da política**, insira um nome para a nova política.
4. Em **Política de backup completo**, selecione uma **Frequência de Backup**. Escolha **diariamente** ou **semanalmente.**

   * Para **Diária**, selecione a hora e fuso horário quando o trabalho de backup começar.
   * Para **Semanal**, selecione o dia da semana, a hora e o fuso horário do início do trabalho de backup.
   * Execute um backup completo, porque você não pode desativar a opção **Backup completo.**
   * Selecione **Backup completo** para exibir a diretiva.
   * Você não pode criar backups diferenciais para backups diários completos.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Em **RETENTION RANGE,** todas as opções são selecionadas por padrão. Limpe os limites de faixa de retenção que você não deseja e, em seguida, defina os intervalos a serem usados.

    * O período mínimo de retenção para qualquer tipo de backup (completo, diferencial e log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    * O backup para um dia específico é marcado e retido com base na faixa de retenção semanal e na configuração de retenção semanal.
    * As faixas de retenção mensal e anual se comportam de forma semelhante.

       ![Configuração de intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu de **política de Backup Completo**, clique em **OK** para aceitar as configurações.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Configurações do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Abrir o menu da política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.

    * Você pode acionar apenas um backup diferencial por dia.
    * Backups diferenciais podem ser retidos por até 180 dias. Para maior retenção, use backups completos.

9. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

10. Para adicionar uma política de backup de log transacional, selecione **Backup de Log**.
11. Em **Backup de Log**, selecione **Habilitar** e, em seguida, defina os controles de retenção e frequência. Os backups de log podem ocorrer tão frequentemente quanto a cada 15 minutos e podem ser retidos por até 35 dias.
12. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

    ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No **menu de diretiva Backup,** escolha ativar ou não a **compactação de backup SQL.** Esta opção está desabilitada por padrão. Se ativado, o SQL Server enviará um fluxo de backup compactado para o VDI.  Observe que o Azure Backup substitui os padrões de nível de ocorrência com a cláusula COMPRESSION /NO_COMPRESSION, dependendo do valor deste controle.

14. Depois de concluir as edições à política de backup, selecione **OK**.

> [!NOTE]
> Cada backup de log é acorrentado ao backup completo anterior para formar uma cadeia de recuperação. Este backup completo será mantido até que a retenção do último backup de log tenha expirado. Isso pode significar que o backup completo é retido por um período extra para garantir que todos os registros possam ser recuperados. Vamos supor que o usuário tenha um backup completo semanal, diferencial diário e registros de 2 horas. Todos eles estão retidos por 30 dias. Mas, a inteira semanal pode ser realmente limpa/excluída somente após o próximo backup completo estar disponível, ou seja, depois de 30 + 7 dias. Um backup semanal completo acontece em 16 de novembro. De acordo com a política de retenção, ela deve ser mantida até 16 de dezembro. O último backup de log para este completo acontece antes do próximo programado completo, em 22 de novembro. Até o dia 22 de dezembro, o 16 de novembro completo não poderá ser excluído. Assim, o 16 de Novembro completo é mantido até 22 de dezembro.

## <a name="enable-auto-protection"></a>Habilitar a proteção automática  

Você pode habilitar a proteção automática para fazer backup automático de todos os bancos de dados existentes e futuros para uma instância autônoma do SQL Server ou para um grupo de disponibilidade Always On.

* Não há limite no número de bancos de dados que você pode selecionar para proteção automática de uma só vez.
* Você não pode proteger ou excluir seletivamente bancos de dados de proteção em uma instância no momento em que você habilitar a proteção automática.
* Se a sua instância já incluir alguns bancos de dados protegidos, eles permanecerão protegidos sob suas respectivas políticas, mesmo depois de você ativar a proteção automática. Todos os bancos de dados desprotegidos adicionados posteriormente terão apenas uma única política que você definir no momento de habilitar a proteção automática, listada em **Configure Backup**. No entanto, você pode alterar a política associada a um banco de dados protegido automaticamente mais tarde.  

Para habilitar a proteção automática:

  1. Em **Itens para backup**, selecione a instância na qual deseja habilitar a proteção automática.
  2. Selecione a lista de parada em **AUTOPROTECT,** escolha **ON**e selecione **OK**.

      ![Habilite a proteção automática no grupo de disponibilidade](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. O backup é configurado para todos os bancos de dados juntos e pode ser acompanhado em **Trabalhos de Backup**.

Se você precisar desativar a proteção automática, selecione o nome da ocorrência em **Configurar backup**e, em seguida, **selecione Desativar autoprotect** para a ocasião. Todos os bancos de dados continuarão sendo backup, mas os bancos de dados futuros não serão protegidos automaticamente.

![Desativar a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

## <a name="next-steps"></a>Próximas etapas

Saiba como:

* [Restaurar bancos de dados SQL Server de backup](restore-sql-database-azure-vm.md)
* [Gerenciar bancos de dados SQL Server de backup](manage-monitor-sql-database-backup.md)
