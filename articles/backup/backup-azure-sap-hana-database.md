---
title: Faça backup de um banco de dados SAP HANA para o Azure com backup do Azure
description: Neste artigo, aprenda a fazer backup de um banco de dados SAP HANA para máquinas virtuais Do Azure com o serviço de backup do Azure.
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: deedd4d2553b3b06f76f698fdb2425a8d3878d23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248054"
---
# <a name="back-up-sap-hana-databases-in-azure-vms"></a>Fazer backup de bancos de dados do SAP HANA em VMs do Azure

Os bancos de dados SAP HANA são cargas de trabalho críticas que requerem um RPO (Objetivo de ponto de recuperação baixo) e retenção a longo prazo. Você pode fazer backup de bancos de dados SAP HANA em execução em máquinas virtuais (VMs) do [Azure usando o Azure Backup](backup-overview.md).

Este artigo mostra como fazer backup de bancos de dados SAP HANA que estão sendo executados em VMs Azure para um cofre de Serviços de Recuperação de Backup do Azure.

Neste artigo, você aprenderá a:
> [!div class="checklist"]
>
> * Criar e configurar um cofre
> * Descobrir bancos de dados
> * Configurar backups
> * Execute um trabalho de backup demanda

>[!NOTE]
>**Exclusão suave para servidor SQL no Azure VM e soft delete for SAP HANA in Azure VM workloads** now available in preview.<br>
>Para se inscrever para a pré-visualização, escreva para nós emAskAzureBackupTeam@microsoft.com

## <a name="prerequisites"></a>Pré-requisitos

Consulte os [pré-requisitos](tutorial-backup-sap-hana-db.md#prerequisites) e [o que o script de pré-registro faz](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) para configurar o banco de dados para backup.

### <a name="set-up-network-connectivity"></a>Configurar a conectividade de rede

Para todas as operações, a VM do SAP HANA exige a conectividade com endereços IP públicos do Azure. As operações de VM (descoberta de banco de dados, configuração de backups, agendamento de backups, restauração de pontos de recuperação etc.) falham sem conectividade com endereços IP públicos do Azure.

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

**Implante um servidor proxy HTTP para direcionar o tráfego**. Quando você faz backup de um banco de dados do SAP HANA em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento ao Backup do Azure e dados ao Armazenamento do Azure. A extensão de backup também usa o Azure AD para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. As extensões são o único componente que está configurado para acesso à Internet pública.

As opções de conectividade incluem as seguintes vantagens e desvantagens:

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custos adicionais | É complexa de ser gerenciada porque os intervalos de endereços IP mudam com o tempo <br/><br/> Fornece acesso a todo o Azure, não somente ao Armazenamento do Azure
Usar marcas de serviço do NSG | Mais fácil de ser gerenciada, pois as alterações de intervalo são mescladas automaticamente <br/><br/> Sem custos adicionais <br/><br/> | Pode ser usada somente com NSGs <br/><br/> Fornece acesso a todo o serviço
Usar marcas de FQDN do Firewall do Azure | Mais fácil de ser gerenciada, pois os FQDNs necessários são gerenciados automaticamente | Pode ser usada somente com o Firewall do Azure
Usar um proxy HTTP | É permitido o controle granular no proxy das URLs de armazenamento <br/><br/> Único ponto de acesso à Internet para VMs <br/><br/> Não está sujeita a alterações de endereços IP do Azure | Custos adicionais para execução de uma VM com o software de proxy

#### <a name="private-endpoints"></a>Pontos finais privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-the-databases"></a>Descobrir os bancos de dados

1. No cofre, em **Introdução**, clique em **Backup**. Em **Em que local sua carga de trabalho é executada?**, selecione **SAP HANA em VM do Azure**.
2. Clique em **Iniciar Descoberta**. Isso inicia a descoberta de VMs do Linux desprotegidas na região do cofre.

   * Após a descoberta, VMs desprotegidos aparecem no portal, listados por nome e grupo de recursos.
   * Se uma VM não estiver listada como esperado, verifique se já está em backup em um cofre.
   * Várias VMs podem ter o mesmo nome, mas pertencem a diferentes grupos de recursos.

3. Em **Selecionar Máquinas Virtuais**, clique no link para baixar o script que fornece permissões para que o serviço de Backup do Azure acesse as VMs SAP HANA para descoberta de banco de dados.
4. Execute o script em cada VM hospedando bancos de dados SAP HANA que você deseja fazer backup.
5. Depois de executar o script nas VMs, em **Selecionar Máquinas Virtuais,** selecione as VMs. Em seguida, clique em **Descobrir BDs**.
6. O Backup do Azure descobre todos os bancos de dados do SAP HANA na VM. Durante a descoberta, o Backup do Azure registra a VM com o cofre e instala uma extensão na VM. Nenhum agente é instalado no banco de dados.

    ![Descubra os bancos de dados SAP HANA](./media/backup-azure-sap-hana-database/hana-discover.png)

## <a name="configure-backup"></a>Configurar o backup  

Agora habilite o backup.

1. Na etapa 2, clique **em Configurar backup**.

    ![Configurar backup](./media/backup-azure-sap-hana-database/configure-backup.png)
2. Em **Selecionar itens para fazer backup,** selecione todos os bancos de dados que deseja proteger > **OK**.

    ![Selecionar itens para fazer backup](./media/backup-azure-sap-hana-database/select-items.png)
3. Em **Política de backup** > **Escolha a diretiva de backup,** crie uma nova política de backup para os bancos de dados, de acordo com as instruções abaixo.

    ![Escolher política de backup](./media/backup-azure-sap-hana-database/backup-policy.png)
4. Depois de criar a diretiva, no menu **Backup,** clique **em Ativar backup**.

    ![Habilite o backup](./media/backup-azure-sap-hana-database/enable-backup.png)
5. Acompanhe o progresso da configuração de backup na área de **Notificações** do portal.

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

* Uma política é criada no nível do cofre.
* Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.

Especifique as configurações de política da seguinte maneira:

1. Em **Nome da política**, insira um nome para a nova política.

   ![Digite o nome da política](./media/backup-azure-sap-hana-database/policy-name.png)
2. Em **Política de Backup Completo**, selecione uma **Frequência de Backup** escolhendo **Diária** ou **Semanal**.
   * **Diário**: Selecione a hora e o fuso horário em que o trabalho de backup começa.
       * Você deve executar um backup completo. Você não pode desligar essa opção.
       * Clique em **Backup Completo** para exibir a política.
       * Você não pode criar backups diferenciais para backups diários completos.
   * **Semanal**: Selecione o dia da semana, hora e fuso horário em que o trabalho de backup é executado.

   ![Selecione a freqüência de backup](./media/backup-azure-sap-hana-database/backup-frequency.png)

3. Em **Período de Retenção**, defina as configurações de retenção para o backup completo.
    * Por padrão, todas as opções são selecionadas. Limpe todos os limites de faixa de retenção que você não deseja usar e defina aqueles que você faz.
    * O período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é de sete dias.
    * Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    * O backup para um dia específico é marcado e mantido com base na configuração e no período de retenção semanal.
    * Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.

4. No menu de **Política de Backup Completo**, clique em **OK** para aceitar as configurações.
5. Selecione **backup diferencial** para adicionar uma política diferencial.
6. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.
    * No máximo, você pode acionar um backup diferencial por dia.
    * Backups diferenciais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.

    ![Política de backup diferencial](./media/backup-azure-sap-hana-database/differential-backup-policy.png)

    > [!NOTE]
    > Atualmente, backups incrementais não são compatíveis.

7. Clique em **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
8. Selecione **Backup de Log** para adicionar uma política de backup de log transacional.
    * Em **Log Backup,** selecione **Ativar**.  Isso não pode ser desabilitado, pois o SAP HANA gerencia todos os backups de log.
    * Defina os controles de freqüência e retenção.

    > [!NOTE]
    > Os backups de log só começam a fluir depois que um backup completo bem-sucedido é concluído.

9. Clique em **OK** para salvar a política e retornar para o menu principal da **Política de backup**.
10. Depois de terminar de definir a política de backup, clique em **OK**.

> [!NOTE]
> Cada backup de log é acorrentado ao backup completo anterior para formar uma cadeia de recuperação. Este backup completo será mantido até que a retenção do último backup de log tenha expirado. Isso pode significar que o backup completo é retido por um período extra para garantir que todos os registros possam ser recuperados. Vamos supor que o usuário tenha um backup completo semanal, diferencial diário e registros de 2 horas. Todos eles estão retidos por 30 dias. Mas, a inteira semanal pode ser realmente limpa/excluída somente após o próximo backup completo estar disponível, ou seja, depois de 30 + 7 dias. Um backup semanal completo acontece em 16 de novembro. De acordo com a política de retenção, ela deve ser mantida até 16 de dezembro. O último backup de log para este completo acontece antes do próximo programado completo, em 22 de novembro. Até o dia 22 de dezembro, o 16 de novembro completo não poderá ser excluído. Assim, o 16 de Novembro completo é mantido até 22 de dezembro.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Os backups são executados de acordo com o cronograma da apólice. Você pode executar um backup demanda da seguinte forma:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **Itens de backup,** selecione a VM executando o banco de dados SAP HANA e clique **em Backup agora**.
3. Em **Backup Now,** use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser mantido. Em seguida, clique em **OK**.
4. Monitorar as notificações do portal. Você pode monitorar o progresso do trabalho no painel do cofre > **backup jobs** > **em andamento**. Dependendo do tamanho do seu banco de dados, a criação do backup inicial pode demorar um pouco.

## <a name="run-sap-hana-studio-backup-on-a-database-with-azure-backup-enabled"></a>Execute o backup do SAP HANA Studio em um banco de dados com o Azure Backup ativado

Se você quiser fazer um backup local (usando o HANA Studio) de um banco de dados que está sendo feito backup com o Azure Backup, faça o seguinte:

1. Aguarde os backups completos ou logpara o banco de dados terminar. Verifique o status no SAP HANA Studio / Cockpit.
2. Desabilite backups de log e defina o catálogo de backup para o sistema de arquivos para banco de dados relevante.
3. Para fazer isso, clique duas vezes em **Systemdb** > **Configuration** > **Select Database** > **Filter (Log)**.
4. Definir **enable_auto_log_backup** para **No**.
5. Definir **log_backup_using_backint** para **Falso**.
6. Faça um backup completo sob demanda do banco de dados.
7. Aguarde o backup completo e o backup do catálogo para terminar.
8. Reverter as configurações anteriores de volta para aquelas para o Azure:
    * Definir **enable_auto_log_backup** para **Sim**.
    * Definir **log_backup_using_backint** para **True**.

## <a name="next-steps"></a>Próximas etapas

* Saiba como [restaurar bancos de dados do SAP HANA em execução em VMs do Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)
* Saiba como [gerenciar bancos de dados SAP HANA que são backup usando o Azure Backup](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)
