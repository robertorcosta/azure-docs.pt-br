---
title: Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação
description: Descreve como fazer backup de VMs do Azure em um cofre de serviços de recuperação usando o backup do Azure
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: dc47aa2b4da08a0fc2c9a91b4d547a0d19e1869a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173351"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Fazer backup de máquinas virtuais do Azure em um cofre dos Serviços de Recuperação

Este artigo descreve como fazer backup de VMs do Azure em um cofre de serviços de recuperação usando o serviço de [backup do Azure](backup-overview.md) .

Neste artigo, você aprenderá a:

> [!div class="checklist"]
>
> * Prepare VMs do Azure.
> * Crie um cofre.
> * Descubra VMs e configure uma política de backup.
> * Habilite o backup para VMs do Azure.
> * Executar o backup inicial.

> [!NOTE]
> Este artigo descreve como configurar um cofre e selecionar VMs para fazer backup. Isso é útil se você quiser fazer backup de várias VMs. Como alternativa, você pode [fazer backup de uma única VM do Azure](backup-azure-vms-first-look-arm.md) diretamente das configurações da VM.

## <a name="before-you-start"></a>Antes de começar

* [Examine](backup-architecture.md#architecture-built-in-azure-vm-backup) a arquitetura de backup da VM do Azure.
* [Saiba mais sobre](backup-azure-vms-introduction.md) backup de VM do Azure e a extensão de backup.
* [Examine a matriz de suporte antes de configurar o](backup-support-matrix-iaas.md) backup.

Além disso, há algumas coisas que você pode precisar fazer em algumas circunstâncias:

* **Instalar o agente de VM na VM: o**backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se sua VM foi criada a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se você criar uma VM personalizada ou migrar um computador local, talvez seja necessário [instalar o agente manualmente](#install-the-vm-agent).
* **Permitir acesso de saída explicitamente**: geralmente, você não precisa permitir explicitamente o acesso à rede de saída para uma VM do Azure para que ela se comunique com o backup do Azure. No entanto, algumas VMs podem enfrentar problemas de conexão, mostrando o erro **ExtensionSnapshotFailedNoNetwork** ao tentar se conectar. Se isso acontecer, você deverá [permitir explicitamente o acesso de saída](#explicitly-allow-outbound-access), para que a extensão de backup do Azure possa se comunicar com os endereços IP públicos do Azure para o tráfego de backup.

## <a name="create-a-vault"></a>Criar um cofre

 Um cofre armazena backups e pontos de recuperação criados ao longo do tempo e armazena as políticas de backup associadas às máquinas submetidas a backup. Crie um cofre da seguinte maneira:

1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Em pesquisa, digite **serviços de recuperação**. Em **Serviços**, clique em **cofres dos serviços de recuperação**.

     ![Pesquisar cofres dos serviços de recuperação](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png) <br/>

3. No menu **cofres dos serviços de recuperação** , clique em **+ Adicionar**.

     ![Criar Cofre de Serviços de Recuperação - etapa 2](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. Em **cofre dos serviços de recuperação**, digite um nome amigável para identificar o cofre.
    * O nome precisa ser exclusivo para a assinatura do Azure.
    * Ele pode conter caracteres de 2 a 50.
    * Ele deve começar com uma letra e pode conter apenas letras, números e hifens.
5. Selecione a assinatura do Azure, o grupo de recursos e a região geográfica na qual o cofre deve ser criado. Em seguida, clique em **Criar**.
    * Pode levar algum tempo para que o cofre seja criado.
    * Monitore as notificações de status na área superior direita do portal.

Depois que o cofre é criado, ele aparece na lista cofres dos serviços de recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

![Lista de cofres de backup](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

> [!NOTE]
> O serviço de backup do Azure cria um grupo de recursos separado (diferente do grupo de recursos da VM) para armazenar o instantâneo, com o formato de nomenclatura **AzureBackupRG_geography_number** (exemplo: AzureBackupRG_northeurope_1). Os dados nesse grupo de recursos serão mantidos durante a duração em dias, conforme especificado na seção *reter instantâneo de recuperação instantânea* da política de backup de máquina virtual do Azure.  A aplicação de um bloqueio a esse grupo de recursos pode causar falhas de backup.<br>
Esse grupo de recursos também deve ser excluído de qualquer restrição de nome/marca, uma vez que uma política de restrição bloquearia a criação de coleções de ponto de recurso novamente, causando falhas de backup.

### <a name="modify-storage-replication"></a>Modificar a replicação de armazenamento

Por padrão, os cofres usam o [grs (armazenamento com redundância geográfica)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for seu mecanismo de backup primário, recomendamos que você use GRS.
* Você pode usar o [armazenamento com redundância local (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para uma opção mais barata.

Modifique o tipo de replicação de armazenamento da seguinte maneira:

1. No novo cofre, clique em **Propriedades** na seção **configurações** .
2. Em **Propriedades**, em **configuração de backup**, clique em **Atualizar**.
3. Selecione o tipo de replicação de armazenamento e clique em **salvar**.

      ![Definir a configuração de armazenamento para o novo cofre](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > Não é possível modificar o tipo de replicação de armazenamento depois que o cofre é configurado e contém itens de backup. Se desejar fazer isso, você precisará recriar o cofre.

## <a name="apply-a-backup-policy"></a>Aplicar uma política de backup

Configure uma política de backup para o cofre.

1. No cofre, clique em **+ backup** na seção **visão geral** .

   ![Botão Backup](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. Em **meta de Backup** > **em que sua carga de trabalho está em execução?** selecione **Azure**. Em **o que você deseja fazer backup?** selecione a **máquina virtual** >  **OK**. Isso registra a extensão da VM no cofre.

   ![Painéis Backup e Meta de Backup](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. Na **Política de Backup**, escolha a política que você deseja associar ao cofre.
    * A política padrão faz o backup da VM uma vez por dia. Os backups diários são mantidos por 30 dias. Instantâneos de recuperação instantânea são mantidos por dois dias.
    * Se você não quiser usar a política padrão, selecione **criar nova**e crie uma política personalizada, conforme descrito no próximo procedimento.

      ![Política de backup padrão](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. Em **selecionar máquinas virtuais**, selecione as VMs que você deseja fazer backup usando a política. Em seguida, clique em **OK**.

   * As VMs selecionadas são validadas.
   * Você só pode escolher máquinas virtuais na mesma região que o cofre.
   * O backup das VMs só pode ser feito em um único cofre.

     ![Painel "Selecionar máquinas virtuais"](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. Em **backup**, clique em **habilitar backup**. Isso implantará a política no cofre e nas VMs e instalará a extensão de backup no agente da VM em execução na VM do Azure.

     ![Botão "Habilitar backup"](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

Depois de habilitar o backup:

* O serviço de Backup instala a extensão de backup independentemente de a VM estar em execução.
* Um backup inicial será executado de acordo com o agendamento de backup.
* Quando os backups são executados, observe que:
  * Uma VM em execução tem a maior chance de capturar um ponto de recuperação consistente com o aplicativo.
  * No entanto, mesmo que a VM seja desativada, o backup é feito. Essa VM é conhecida como VM offline. Nesse caso, o ponto de recuperação será consistente com falhas.

### <a name="create-a-custom-policy"></a>Criar uma política personalizada

Se você optou por criar uma nova política de backup, preencha as configurações de política.

1. Em **nome da política**, especifique um nome significativo.
2. Em **agendamento de backup**, especifique quando os backups devem ser feitos. Você pode fazer backups diários ou semanais para VMs do Azure.
3. Em **restauração instantânea**, especifique por quanto tempo deseja manter os instantâneos localmente para restauração instantânea.
    * Quando você restaura, os discos de VM de backup são copiados do armazenamento, pela rede para o local de armazenamento de recuperação. Com a restauração instantânea, você pode aproveitar os instantâneos armazenados localmente feitos durante um trabalho de backup, sem esperar que os dados de backup sejam transferidos para o cofre.
    * Você pode reter instantâneos para restauração instantânea entre um ou cinco dias. Dois dias é a configuração padrão.
4. Em **período de retenção**, especifique por quanto tempo deseja manter seus pontos de backup diários ou semanais.
5. Em **retenção do ponto de backup mensal**, especifique se deseja manter um backup mensal de seus backups diários ou semanais.
6. Clique em **OK** para salvar a política.

    ![Nova política de backup](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > O Backup do Azure não oferece suporte ao ajuste automático do relógio para alterações de horário de verão para backups de VMs do Azure. À medida que ocorrem alterações de tempo, modifique as políticas de backup manualmente, conforme necessário.

## <a name="trigger-the-initial-backup"></a>Disparar o backup inicial

O backup inicial será executado de acordo com o agendamento, mas você poderá executá-lo imediatamente da seguinte maneira:

1. No menu do cofre, clique em **Itens de backup**.
2. Em **itens de backup**, clique em **máquina virtual do Azure**.
3. Na lista **itens de backup** , clique nas reticências (...).
4. Clique em **Fazer backup agora**.
5. Em **fazer backup agora**, use o controle de calendário para selecionar o último dia em que o ponto de recuperação deve ser retido. Em seguida, clique em **OK**.
6. Monitorar as notificações do portal. Você pode monitorar o andamento do trabalho no painel do cofre > **Trabalhos de Backup** > **Em Andamento**. Dependendo do tamanho da VM, a criação do backup inicial pode demorar um pouco.

## <a name="verify-backup-job-status"></a>Verificar o status do trabalho de backup

Os detalhes do trabalho de backup para cada backup de VM consistem em duas fases, a fase de **instantâneo** seguida da fase **transferir dados para o cofre** .<br/>
A fase de instantâneo garante a disponibilidade de um ponto de recuperação armazenado junto com os discos para **restaurações instantâneas** e está disponível por um máximo de cinco dias, dependendo da retenção de instantâneo configurada pelo usuário. Transferir dados para o cofre cria um ponto de recuperação no cofre para retenção de longo prazo. A transferência de dados para o cofre só é iniciada depois que a fase de instantâneo é concluída.

  ![Status do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

Há duas **subtarefas** em execução no back-end, uma para o trabalho de backup de front-ends que pode ser verificada na folha detalhes do **trabalho de backup** , conforme indicado abaixo:

  ![Status do trabalho de backup](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

A fase **transferir dados para o cofre** pode levar vários dias para ser concluída, dependendo do tamanho dos discos, da rotatividade por disco e de vários outros fatores.

O status do trabalho pode variar dependendo dos seguintes cenários:

**Instantâneo** | **Transferir dados para o cofre** | **Status do trabalho**
--- | --- | ---
Concluído | Em andamento | Em andamento
Concluído | Ignorado | Concluído
Concluído | Concluído | Concluído
Concluído | Falha | Concluído com aviso
Falha | Falha | Falha

Agora, com esse recurso, para a mesma VM, dois backups podem ser executados em paralelo, mas, em qualquer fase (instantâneo, transferir dados para o cofre), apenas uma subtarefa pode ser executada. Assim, em cenários, um trabalho de backup em andamento resultou na falha do backup do dia seguinte, com essa funcionalidade de desacoplamento. Os backups do dia seguinte podem ter o instantâneo concluído ao **transferir dados para o cofre** ignorados se o trabalho de backup de um dia anterior estiver em estado de andamento.
O ponto de recuperação incremental criado no cofre irá capturar toda a rotatividade do último ponto de recuperação criado no cofre. Não há impacto no custo do usuário.

## <a name="optional-steps-install-agentallow-outbound"></a>Etapas opcionais (instalar agente/permitir saída)

### <a name="install-the-vm-agent"></a>Instalar o agente de VM

O Backup do Azure faz backup de VMs do Azure instalando uma extensão para o agente de VM do Azure em execução no computador. Se sua VM foi criada a partir de uma imagem do Azure Marketplace, o agente está instalado e em execução. Se você criar uma VM personalizada ou migrar um computador local, talvez seja necessário instalar o agente manualmente, conforme resumido na tabela.

**VM** | **Detalhes**
--- | ---
**Windows** | 1. [Baixe e instale](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) o arquivo MSI do agente.<br/><br/> 2. Instale com permissões de administrador no computador.<br/><br/> 3. Verifique a instalação. Em *C:\WindowsAzure\Packages* na VM, clique com o botão direito do mouse em **WaAppAgent. exe** > **Propriedades**. Na guia **detalhes** , a **versão do produto** deve ser 2.6.1198.718 ou superior.<br/><br/> Se você estiver atualizando o agente, certifique-se de que nenhuma operação de backup esteja em execução e [reinstale o agente](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
**Linux** | Instale o usando um RPM ou um pacote de DEB do repositório de pacotes de sua distribuição. Esse é o método preferencial para instalar e atualizar o agente Linux do Azure. Todos os [provedores de distribuição aprovados](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) integram o pacote do agente Linux do Azure em suas imagens e repositórios. O agente está disponível no [GitHub](https://github.com/Azure/WALinuxAgent), mas não recomendamos instalá-lo a partir daí.<br/><br/> Se você estiver atualizando o agente, verifique se nenhuma operação de backup está em execução e atualize os binários.

### <a name="explicitly-allow-outbound-access"></a>Permitir acesso de saída explicitamente

A extensão de backup em execução na VM precisa de acesso de saída para endereços IP públicos do Azure.

* Em geral, você não precisa permitir explicitamente o acesso de rede de saída para uma VM do Azure para que ela se comunique com o backup do Azure.
* Se você tiver dificuldades com as VMs conectadas ou se vir o erro **ExtensionSnapshotFailedNoNetwork** ao tentar se conectar, você deverá permitir o acesso explicitamente para que a extensão de backup possa se comunicar com os endereços IP públicos do Azure para o tráfego de backup. Os métodos de acesso são resumidos na tabela a seguir.

**Opção** | **Ação** | **Detalhes**
--- | --- | ---
**Configurar regras de NSG** | Permitir os [intervalos de IP do datacenter do Azure](https://www.microsoft.com/download/details.aspx?id=41653).<br/><br/> Em vez de permitir e gerenciar cada intervalo de endereços, você pode adicionar uma regra que permita o acesso ao serviço de backup do Azure usando uma [marca de serviço](backup-azure-arm-vms-prepare.md#set-up-an-nsg-rule-to-allow-outbound-access-to-azure). | [Saiba mais](../virtual-network/security-overview.md#service-tags) sobre marcas de serviço.<br/><br/> As marcas de serviços simplificam o gerenciamento de acesso e não incorrem em custos adicionais.
**Implantar um proxy** | Implante um servidor de proxy HTTP para rotear o tráfego. | Fornece acesso ao Azure por completo, não somente ao armazenamento.<br/><br/> É permitido o controle granular em relação às URLs de armazenamento.<br/><br/> Único ponto de acesso à Internet para VMs.<br/><br/> Custos adicionais para o proxy.
**Configurar o Firewall do Azure** | Permite o tráfego por meio do Firewall do Azure na VM, usando uma tag de FQDN para o serviço de Backup do Azure | Simples de usar se você tiver o Firewall do Azure configurado em uma sub-rede VNet.<br/><br/> Você não pode criar suas próprias marcas de FQDN ou modificar FQDNs em uma marca.<br/><br/> Se suas VMs do Azure tiverem discos gerenciados, talvez seja necessário abrir uma porta adicional (8443) nos firewalls.

#### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Estabelecer conectividade com NSG, por proxy ou por meio do firewall

##### <a name="set-up-an-nsg-rule-to-allow-outbound-access-to-azure"></a>Configurar uma regra NSG para permitir o acesso de saída ao Azure

Se um NSG gerencia o acesso à VM, permita o acesso de saída para o armazenamento de backup para os intervalos e as portas necessários.

1. Nas propriedades da VM > **rede**, selecione **Adicionar regra de porta de saída**.
2. Em **Adicionar regra de segurança de saída**, selecione **avançado**.
3. Em **Fonte**, escolha **VirtualNetwork**.
4. Em **intervalos de portas de origem**, insira um asterisco (*) para permitir o acesso de saída de qualquer porta.
5. Em **Destino**, escolha **Marca de Serviço**. Na lista, selecione **Storage.region**. A região é onde o cofre e as VMs das quais você deseja fazer backup estão localizados.
6. Em **Intervalos de portas de destino**, escolha a porta.
    * VM usando discos não gerenciados com conta de armazenamento não criptografada: 80
    * VM usando discos não gerenciados com a conta de armazenamento criptografada: 443 (configuração padrão)
    * VM usando discos gerenciados: 8443.
7. Em **Protocolo**, escolha **TCP**.
8. Em **Prioridade**, especifique um valor de prioridade menor que qualquer regra de negação maior.

   Se você tiver uma regra que negue acesso, a nova regra de permissão deverá ser maior. Por exemplo, se você tiver uma regra **Deny_All** definida com prioridade 1000, a nova regra deverá ser definida com um valor inferior a 1000.
9. Forneça um nome e uma descrição para a regra e selecione **OK**.

Você pode aplicar a regra de NSG para várias VMs para permitir acesso de saída. Este vídeo orienta você durante o processo.

>[!VIDEO https://www.youtube.com/embed/1EjLQtbKm1M]

##### <a name="route-backup-traffic-through-a-proxy"></a>Rotear o tráfego de backup por meio de um proxy

Você pode rotear o tráfego de backup por um proxy e, em seguida, fornecer o acesso de proxy aos intervalos necessários do Azure. Configure a VM de proxy para permitir o seguinte:

* A VM do Azure deve rotear todo o tráfego HTTP voltado para a Internet pública por meio do proxy.
* O proxy deve permitir o tráfego de entrada de VMs na rede virtual aplicável.
* O NSG **NSF-lockdown** precisa de uma regra que permita a saída do tráfego de Internet da VM do proxy.

###### <a name="set-up-the-proxy"></a>Configurar o proxy

Se você não tiver um proxy de conta do sistema, configure um da seguinte maneira:

1. Baixe o [PsExec](https://technet.microsoft.com/sysinternals/bb897553).
2. Execute **PsExec.exe -i -s cmd.exe** para executar o prompt de comando em uma conta do sistema.
3. Execute o navegador no contexto do sistema. Por exemplo, use **%ProgramFiles%\Internet Explorer\iexplore.exe** para o Internet Explorer.  
4. Defina as configurações de proxy.
   * Em máquinas do Linux:
     * Adicione esta linha ao arquivo **/etc/environment**:
       * **http_proxy = http:\/endereço IP/proxy nomedearquivo: porta proxy**
     * Adicione estas linhas ao arquivo **/etc/waagent.conf**:
         * **Endereço IP HttpProxy.Host=proxy**
         * **Porta HttpProxy.Port=proxy**
   * Em computadores Windows, nas configurações do navegador, especifique que um proxy deverá ser usado. Se estiver usando um proxy em uma conta de usuário, você poderá usar esse script para aplicar a configuração no nível de conta do sistema.

       ```powershell
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
      $obj = Get-ItemProperty -Path Registry::"HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
      Set-ItemProperty -Path Registry::"HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver

       ```

###### <a name="allow-incoming-connections-on-the-proxy"></a>Permitir conexões de entrada no proxy

Permite conexões de entrada nas configurações do proxy.

1. No firewall do Windows, abra o **Firewall do Windows com segurança avançada**.
2. Clique com o botão direito do mouse em **Regras de Entrada** > **Nova Regra**.
3. Em **tipo de regra**, selecione **personalizado** > **Avançar**.
4. Em **Programa**, escolha **Todos os Programas** > **Avançar**.
5. Em **protocolos e portas**:
   * Defina o tipo como **TCP**.
   * Defina **portas locais** para **portas específicas**.
   * Defina a **porta remota** para **todas as portas**.

6. Conclua o assistente e especifique um nome para a regra.

###### <a name="add-an-exception-rule-to-the-nsg-for-the-proxy"></a>Adicionar uma regra de exceção ao NSG para o proxy

No NSG **NSF-lockdown**, permita o tráfego de qualquer porta em 10.0.0.5 para qualquer endereço da Internet na porta 80 (HTTP) ou 443 (HTTPS).

O script do PowerShell a seguir fornece um exemplo de como habilitar o tráfego.
Em vez de permitir a saída para todos os endereços públicos da Internet, você pode especificar um intervalo de endereços IP (`-DestinationPortRange`) ou usar a marca de serviço Storage. Region.

```powershell
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

##### <a name="allow-firewall-access-with-an-fqdn-tag"></a>Permitir acesso de firewall com uma marca FQDN

Você pode configurar o Firewall do Azure para permitir o acesso de saída para o tráfego de rede para o backup do Azure.

* [Saiba como](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal) implantar o Firewall do Azure.
* [Leia sobre](https://docs.microsoft.com/azure/firewall/fqdn-tags) as marcas de FQDN.

## <a name="next-steps"></a>Próximas etapas

* Solucione quaisquer problemas com [agentes de VM do Azure](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) ou [backup de VM do Azure](backup-azure-vms-troubleshoot.md).
* [Restaurar](backup-azure-arm-restore-vms.md) VMs do Azure.
