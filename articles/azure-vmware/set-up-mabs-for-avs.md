---
title: Configurar Servidor de Backup do Azure para a solução VMware do Azure
description: Configure seu ambiente de solução do Azure VMware para fazer backup de máquinas virtuais usando Servidor de Backup do Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: c56ebaff6b08f3d6586dfe025fdb2a5bfc708fa4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84816851"
---
# <a name="set-up-azure-backup-server-for-azure-vmware-solution"></a>Configurar Servidor de Backup do Azure para a solução VMware do Azure

Servidor de Backup do Azure é um sistema de backup e recuperação empresarial robusto que contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre). Durante a visualização da solução Azure VMware, você pode configurar somente o backup em nível de máquina virtual (VM) usando Servidor de Backup do Azure. 

Servidor de Backup do Azure pode armazenar dados de backup em:

- **Disco**: para armazenamento de curto prazo, servidor de backup do Azure faz backup de dados em pools de disco.
- **Azure**: para armazenamento de curto e longo prazo externo, servidor de backup do Azure dados armazenados em pools de discos podem passar por backup para a nuvem Microsoft Azure usando o backup do Azure.

Quando ocorrerem paralisações e os dados de origem não estiverem disponíveis, você poderá usar Servidor de Backup do Azure para restaurar dados para a origem ou um local alternativo facilmente. Dessa forma, se os dados originais estiverem indisponíveis devido a problemas planejados ou inesperados, você poderá restaurar dados facilmente para um local alternativo.

Neste artigo, ajudamos você a preparar seu ambiente de solução do Azure VMware para fazer backup de VMs usando Servidor de Backup do Azure. Nós o orientaremos pelas etapas para: 

> [!div class="checklist"]
> * Determine o tipo de disco de VM recomendado e o tamanho a ser usado.
> * Crie um cofre dos serviços de recuperação que armazena os pontos de recuperação.
> * Defina a replicação de armazenamento para um cofre dos serviços de recuperação.
> * Adicionar armazenamento a Servidor de Backup do Azure.

## <a name="supported-vmware-features"></a>Recursos do VMware com suporte

- **Backup sem agente:** Servidor de Backup do Azure não exige que um agente seja instalado no servidor vCenter ou ESXi para fazer backup da máquina virtual. Em vez disso, forneça apenas o endereço IP ou FQDN (nome de domínio totalmente qualificado) e as credenciais de entrada usadas para autenticar o servidor VMware com o Servidor de Backup do Azure.
- **Backup integrado à nuvem:** Servidor de Backup do Azure protege as cargas de trabalho em disco e na nuvem. O fluxo de trabalho de backup e recuperação do Servidor de Backup do Azure ajuda a gerenciar a retenção de longo prazo e o backup externo.
- **Detectar e proteger VMs gerenciadas pelo vCenter:** Servidor de Backup do Azure detecta e protege as VMs implantadas em um vCenter ou em um servidor ESXi. Servidor de Backup do Azure também detecta VMs gerenciadas pelo vCenter para que você possa proteger grandes implantações.
- **Autoproteção em nível de pasta:** o vCenter permite organizar suas VMs em pastas de VM. O Servidor de Backup do Azure detecta essas pastas e você pode usá-la para proteger as VMs no nível da pasta, que inclui todas as subpastas. Ao proteger pastas, Servidor de Backup do Azure não apenas protege as VMs nessa pasta, mas também protege as VMs adicionadas posteriormente. O Servidor de Backup do Azure detecta novas VMs diariamente e as protege automaticamente. Conforme você organiza suas VMs em pastas recursivas, Servidor de Backup do Azure detecta e protege automaticamente as novas VMs implantadas nas pastas recursivas.
- **Servidor de backup do Azure continua a proteger as VMs do VMotion no cluster:** À medida que as VMs são vMotion para balanceamento de carga no cluster, Servidor de Backup do Azure automaticamente detecta e continua a proteção da VM.
- **Recuperar os arquivos necessários mais rápido:** Servidor de Backup do Azure pode recuperar arquivos ou pastas de uma VM do Windows sem recuperar toda a VM.

## <a name="limitations"></a>Limitações

- O pacote cumulativo de atualizações 1 para Servidor de Backup do Azure v3 deve ser instalado.
- Não é possível fazer backup de instantâneos do usuário antes do primeiro Servidor de Backup do Azure backup. Depois que Servidor de Backup do Azure concluir o primeiro backup, você poderá fazer backup de instantâneos do usuário.
- Servidor de Backup do Azure não pode proteger VMs VMware com discos de passagem e mapeamentos de dispositivo brutos físicos (pRDMs).
- Servidor de Backup do Azure não pode detectar nem proteger o VMware vApps.

Para configurar Servidor de Backup do Azure para a solução VMware do Azure, você deve concluir as seguintes etapas:

- Configure os pré-requisitos e o ambiente.
- Crie um cofre dos Serviços de Recuperação.
- Baixe e instale o Servidor de Backup do Azure.
- Adicionar armazenamento a Servidor de Backup do Azure.

### <a name="deployment-architecture"></a>Arquitetura de implantação

O Servidor de Backup do Azure é implantado como uma VM de IaaS (infraestrutura como serviço) do Azure para proteger as VMs de solução do Azure VMware.

:::image type="content" source="media/avs-backup/deploy-mabs-avs-diagram.png" alt-text="Arquitetura de implantação AVS" border="false":::

## <a name="prerequisites-for-the-azure-backup-server-environment"></a>Pré-requisitos para o ambiente do Servidor de Backup do Azure

Considere as recomendações nesta seção ao instalar Servidor de Backup do Azure em seu ambiente do Azure.

### <a name="azure-virtual-network"></a>Rede Virtual do Azure

Certifique-se de [Configurar a rede para sua nuvem privada do VMware no Azure](tutorial-configure-networking.md).

### <a name="determine-the-size-of-the-virtual-machine"></a>Determinar o tamanho da máquina virtual

Você deve criar uma máquina virtual do Windows na rede virtual que você criou na etapa anterior. Ao escolher um servidor para executar o Servidor de Backup do Azure, comece com uma imagem da galeria do Windows Server 2019 datacenter. O tutorial [criar sua primeira máquina virtual do Windows na portal do Azure o](../virtual-machines/windows/quick-create-portal.md) ajudará a começar com a VM recomendada no Azure, mesmo que você nunca tenha usado o Azure.

A tabela a seguir resume o número máximo de cargas de trabalho protegidas para cada tamanho de máquina virtual Servidor de Backup do Azure. As informações são baseadas no desempenho e em testes de escala internos com valores canônicos para o tamanho da carga de trabalho e a variação. O tamanho real da carga de trabalho pode ser maior, mas deve ser acomodado pelos discos anexados à máquina virtual Servidor de Backup do Azure.

| Máximo de cargas de trabalho protegidas | Tamanho médio da carga de trabalho | Variação de carga de trabalho média (diariamente) | IOPS de armazenamento mínimo | Tipo/tamanho de disco recomendado      | Tamanho de VM recomendado |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Variação de 5% NET                   | 2\.000             | HDD Standard (8 TB ou acima do tamanho por disco)  | A4V2       |
| 40                      | 150 GB                | Variação de 10% NET                  | 4\.500             | SSD Premium * (1 TB ou acima do tamanho por disco) | DS3_V2     |
| 60                      | 200 GB                | Variação de 10% NET                  | 10.500            | SSD Premium * (8 TB ou acima do tamanho por disco) | DS3_V2     |

* Para obter o IOPs necessário, use discos mínimos recomendados ou de tamanho superior. Discos de tamanho menor oferecem IOPs inferior.

> [!NOTE]
> O Servidor de Backup do Azure foi projetado para ser executado em um servidor dedicado de finalidade única. Não é possível instalar o Servidor de Backup do Azure em um computador que:
> * É executado como um controlador de domínio.
> * Tem a função de servidor de aplicativos instalada.
> * É um servidor de gerenciamento de System Center Operations Manager.
> * Executa o Exchange Server.
> * É um nó de um cluster.

### <a name="disks-and-storage"></a>Discos e armazenamento

Servidor de Backup do Azure requer discos para instalação, que inclui arquivos do sistema, arquivos de instalação, software de pré-requisito, arquivos de banco de dados e discos dedicados para o pool de armazenamento.

| Requisito                      | Tamanho recomendado  |
|----------------------------------|-------------------------|
| Instalação do Servidor de Backup do Azure                | Local de instalação: 3 GB<br />Unidade dos arquivos do banco de dados: 900 MB<br />Unidade do sistema: 1 GB para SQL Server instalação<br /><br />Você também precisará de espaço para Servidor de Backup do Azure copiar o catálogo de arquivos para um local de instalação temporário ao arquivar.      |
| Disco para o pool de armazenamento<br />(Usa volumes básicos, não pode estar em um disco dinâmico) | De duas a três vezes o tamanho dos dados protegidos.<br />Para obter um cálculo detalhado do armazenamento, consulte [DPM planejador de capacidade](https://www.microsoft.com/download/details.aspx?id=54301).   |

Para saber como anexar um novo disco de dados gerenciado a uma VM do Azure existente, consulte [anexar um disco de dados gerenciado a uma VM do Windows usando o portal do Azure](../virtual-machines/windows/attach-managed-disk-portal.md).

> [!NOTE]
> Um único Servidor de Backup do Azure tem um limite flexível de 120 TB para o pool de armazenamento.

### <a name="store-backup-data-on-local-disk-and-in-azure"></a>Armazenar dados de backup no disco local e no Azure

O armazenamento de dados de backup no Azure reduz a infraestrutura de backup na VM Servidor de Backup do Azure. Para recuperação operacional (backup), o Servidor de Backup do Azure armazena dados de backup em discos do Azure anexados à VM. Depois que os discos e o espaço de armazenamento são anexados à VM, Servidor de Backup do Azure gerencia o armazenamento para você. A quantidade de armazenamento de dados de backup depende do número e do tamanho dos discos anexados a cada VM do Azure. Cada tamanho da VM do Azure tem um número máximo de discos que podem ser anexados. Por exemplo, a2 é quatro discos, a3 tem oito discos e A4 é 16 discos. Novamente, o tamanho e o número de discos determinam a capacidade total do pool de armazenamento de backup.

> [!IMPORTANT]
> Você *não* deve reter os dados de recuperação operacional em discos anexados servidor de backup do Azure por mais de cinco dias. Se os dados tiverem mais de cinco dias, armazene-os em um cofre dos serviços de recuperação.

Para armazenar dados de backup no Azure, crie ou use um cofre dos Serviços de Recuperação. Ao se preparar para fazer backup da carga de trabalho do Servidor de Backup do Azure, você [configura o cofre dos serviços de recuperação](#create-a-recovery-services-vault). Uma vez configurado, cada vez que um trabalho de backup online é executado, um ponto de recuperação é criado no cofre. Cada cofre dos serviços de recuperação contém até 9.999 pontos de recuperação. Dependendo do número de pontos de recuperação criados e de quanto tempo eles são retidos, você pode reter os dados de backup por muitos anos. Por exemplo, você pode criar pontos de recuperação mensais e mantê-los por cinco anos.

> [!IMPORTANT]
> Se você enviar dados de backup para o Azure ou mantê-los localmente, deverá registrar Servidor de Backup do Azure com um cofre dos serviços de recuperação.

### <a name="scale-deployment"></a>Implantação de escala

Se você quiser dimensionar a implantação, terá as seguintes opções:

- **Escalar verticalmente**: Aumente o tamanho do servidor de backup do Azure VM de uma série para DS3 Series e aumente o armazenamento local.
- **Dados de descarregamento**: envie dados mais antigos para o Azure e retenha apenas os dados mais recentes no armazenamento anexado à máquina servidor de backup do Azure.
- **Escalar horizontalmente**: Adicione mais máquinas servidor de backup do Azure para proteger as cargas de trabalho.

### <a name="net-framework"></a>.NET Framework

A VM deve ter .NET Framework 3,5 SP1 ou superior instalado.

### <a name="join-a-domain"></a>Ingressar em um domínio

A VM Servidor de Backup do Azure deve ser unida a um domínio e um usuário de domínio com privilégios de administrador na VM deve instalar Servidor de Backup do Azure.

Embora não haja suporte no momento da visualização, Servidor de Backup do Azure implantados em uma VM do Azure podem fazer backup de cargas de trabalho nas VMs na solução do Azure VMware. As cargas de trabalho devem estar no mesmo domínio para habilitar a operação de backup.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos serviços de recuperação é uma entidade de armazenamento que armazena os pontos de recuperação criados ao longo do tempo. Ele também contém políticas de backup associadas a itens protegidos.

1. Entre na sua assinatura no [Portal do Azure](https://portal.azure.com/).

1. No menu à esquerda, selecione **todos os serviços**.

   ![Selecionar Todos os serviços](../backup/media/backup-create-rs-vault/click-all-services.png)

1. Na caixa de diálogo **todos os serviços** , insira **serviços de recuperação** e selecione **cofres dos serviços de recuperação** na lista.

   ![Insira e escolha os cofres dos Serviços de Recuperação](../backup/media/backup-create-rs-vault/all-services.png)

   A lista de cofres de Serviços de Recuperação na assinatura aparecerá.

1. No painel **cofres dos serviços de recuperação** , selecione **Adicionar**.

   ![Adicionar um cofre dos Serviços de Recuperação](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

   A caixa de diálogo **Cofre dos Serviços de Recuperação** é aberta.

1. Insira valores para o **nome**, a **assinatura**, o **grupo de recursos**e o **local**.

   ![Configurar o cofre de Serviços de Recuperação](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

   - **Nome**: digite um nome amigável para identificar o cofre. O nome deve ser exclusivo para a assinatura do Azure. Especifique um nome que tenha pelo menos dois, mas não mais de 50 caracteres. O nome deve começar com uma letra e consistir apenas em letras, números e hifens.
   - **Assinatura**: Escolha a assinatura a ser usada. Se você for um membro de apenas uma assinatura, verá esse nome. Se você não tem certeza de qual assinatura usar, utilize a assinatura padrão (sugerida). Só haverá múltiplas opções se a sua conta corporativa ou de estudante estiver associada a várias assinaturas do Azure.
   - **Grupo de recursos**: Use um grupo de recursos existente ou crie um novo. Para ver a lista de grupos de recursos disponíveis em sua assinatura, selecione **Usar existente** e, em seguida, selecione um recurso na lista suspensa. Para criar um novo grupo de recursos, selecione **Criar novo** e insira o nome.
   - **Localização**: Selecione a região geográfica para o cofre. Para criar um cofre para proteger as máquinas virtuais da solução Azure VMware, o cofre *deve* estar na mesma região que a nuvem privada da solução Azure VMware.

1. Quando você estiver pronto para criar o cofre dos Serviços de Recuperação, clique em **Criar**.

   ![Criar o cofre dos Serviços de Recuperação](../backup/media/backup-create-rs-vault/click-create-button.png)

   Pode levar um tempo para criar o cofre dos Serviços de Recuperação. Monitore as notificações de status na área **notificações** no canto superior direito do Portal. Depois que o cofre tiver sido criado, ele aparecerá na lista de cofres dos Serviços de Recuperação. Se você não encontrar seu cofre, selecione **Atualizar**.

   ![Atualizar a lista de cofres de backup](../backup/media/backup-create-rs-vault/refresh-button.png)

## <a name="set-storage-replication"></a>Definir replicação de armazenamento

A opção de replicação de armazenamento permite que você escolha entre o armazenamento com redundância geográfica (o padrão) e o armazenamento com redundância local. O armazenamento com redundância geográfica copia os dados em sua conta de armazenamento para uma região secundária, o que torna seus dados duráveis. O armazenamento com redundância local é uma opção mais barata que não é tão durável. Para saber mais sobre as opções de armazenamento com redundância geográfica e com redundância local, confira [redundância de armazenamento do Azure](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Alterar a configuração do **tipo de replicação de armazenamento com redundância local/redundância geográfica** para um cofre dos serviços de recuperação deve ser feito antes de configurar os backups no cofre. Depois de configurar os backups, a opção para modificá-lo é desabilitada e você não pode alterar o tipo de replicação de armazenamento.

1. Em **cofres dos serviços de recuperação**, selecione o novo cofre. 

1. Em **Configurações**, selecione **Propriedades**. Em **configuração de backup**, selecione **Atualizar**.

1. Selecione o tipo de replicação de armazenamento e selecione **salvar**.

   ![Definir a configuração de armazenamento para o novo cofre](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## <a name="download-and-install-the-software-package"></a>Baixar e instalar o pacote de software

Siga as etapas nesta seção para baixar, extrair e instalar o pacote de software.

### <a name="download-the-software-package"></a>Baixar o pacote de software

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Se você já tiver um cofre dos serviços de recuperação aberto, continue para a próxima etapa. Se você não tiver um cofre dos serviços de recuperação aberto, mas estiver no portal do Azure, no menu principal, selecione **procurar**.

   1. Na lista de recursos, digite **Serviços de Recuperação**.

   1. Quando você começa a digitar, a lista é filtrada com base em sua entrada. Quando você vir a opção **cofres de Serviços de Recuperação**, selecione-a.

   ![Criar cofre de serviços de recuperação-etapa 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

1. Na lista de cofres de Serviços de Recuperação, selecione um cofre.

   O painel de cofres selecionados será aberto.

   ![Abrir painel do cofre](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

   A opção **configurações** é aberta por padrão. Se estiver fechado, selecione **configurações** para abri-lo.

   ![Abrir opção de configurações do cofre](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1. Selecione **backup** para abrir o assistente de **introdução** .

   ![O backup abre Introdução assistente](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1. Na janela que é aberta, faça o seguinte:

   1. No menu **onde sua carga de trabalho está em execução?** , selecione **local**.

      :::image type="content" source="media/avs-backup/deploy-mabs-on-premises-workload.png" alt-text="Onde sua carga de trabalho está em execução?":::

   1. No menu do **que você deseja fazer backup?** , selecione as cargas de trabalho que você deseja proteger usando servidor de backup do Azure.

   1. Selecione **preparar infraestrutura** para baixar e instalar servidor de backup do Azure e as credenciais do cofre.

      :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure.png" alt-text="Preparar a infraestrutura":::

1. Na janela **preparar infraestrutura** que é aberta, faça o seguinte:

   1. Selecione o link de **Download** para instalar o servidor de backup do Azure.

   1. Baixe as credenciais do cofre selecionando a caixa de seleção **já baixado ou usando a última servidor de backup do Azure instalação** e, em seguida, selecione **baixar**. Você usa as credenciais do cofre durante o registro de Servidor de Backup do Azure para o cofre dos serviços de recuperação. Os links levam você ao centro de download, no qual você baixa o pacote de software.

   :::image type="content" source="media/avs-backup/deploy-mabs-prepare-infrastructure2.png" alt-text="Preparar a infraestrutura-Servidor de Backup do Azure":::

1. Na página de download, selecione todos os arquivos e selecione **Avançar**.

   > [!NOTE]
   > Você deve baixar todos os arquivos para a mesma pasta. Como o tamanho do download dos arquivos juntos é maior que 3 GB, pode levar até 60 minutos para que o download seja concluído. 

   ![Selecionar arquivos no centro de download](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### <a name="extract-the-software-package"></a>Extrair o pacote de software

Se você baixou o pacote de software para um servidor diferente, copie os arquivos para a máquina virtual que você criou para implantar Servidor de Backup do Azure.

> [!WARNING]
> Pelo menos 4 GB de espaço livre são necessários para a extração dos arquivos de instalação.

1. Depois de baixar todos os arquivos, clique duas vezes em **MicrosoftAzureBackupInstaller.exe** para abrir o assistente de instalação do **backup do Microsoft Azure** e selecione **Avançar**.

1. Selecione o local para extrair os arquivos e selecione **Avançar**.

1. Selecione **extrair** para iniciar o processo de extração.

   ![Assistente de instalação do Backup do Microsoft Azure](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1. Depois de extraído, selecione a opção para **executar setup.exe** e, em seguida, selecione **concluir**.

> [!TIP]
> Você também pode localizar o arquivo de setup.exe da pasta onde você extraiu o pacote de software.

### <a name="install-the-software-package"></a>Instalar o pacote de software

1. Na janela instalação, em **instalar**, selecione **backup do Microsoft Azure** para abrir o assistente de instalação.

   ![Botão de instalação do assistente de instalação do Backup do Microsoft Azure](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1. Na tela de **boas-vindas** , selecione **Avançar** para continuar na página **verificações de pré-requisitos** .

1. Selecione **verificar novamente** para determinar se os pré-requisitos de hardware e software para servidor de backup do Azure foram atendidos. Se for atendido com êxito, selecione **Avançar**.

   ![Verificação de pré-requisitos de Servidor de Backup do Azure](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1. O pacote de instalação do Servidor de Backup do Azure vem agrupado com os binários de SQL Server apropriados que são necessários. Ao iniciar uma nova instalação do Servidor de Backup do Azure, selecione a opção **Instalar nova instância do SQL Server com esta configuração** . Em seguida, selecione **verificar e instalar**.

   ![Verificação de SQL Server de Servidor de Backup do Azure](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

   > [!NOTE]
   > Se você quiser usar sua própria instância de SQL Server, as versões de SQL Server com suporte são SQL Server 2014 SP1 ou superior, 2016 e 2017. Todas as versões do SQL Server devem ser Standard ou Enterprise de 64 bits. Servidor de Backup do Azure não funciona com uma instância de SQL Server remota. A instância usada pelo Servidor de Backup do Azure precisa ser local. Se você usar uma instância de SQL Server existente para Servidor de Backup do Azure, a instalação só dará suporte ao uso de *instâncias nomeadas* do SQL Server.

   Se ocorrer uma falha com uma recomendação para reiniciar o computador, faça isso e selecione **verificar novamente**. Se houver algum problema de configuração SQL Server, reconfigure o SQL Server de acordo com as diretrizes de SQL Server. Em seguida, tente instalar ou atualizar Servidor de Backup do Azure usando a instância existente do SQL Server.

   **Configuração manual**

   Ao usar sua própria instância do SQL Server, certifique-se de adicionar Builtin\administradores à função sysadmin ao banco de dados mestre.

   **Configuração do SSRS com o SQL Server 2017**

   Ao usar sua própria instância do SQL Server 2017, você deve configurar o SQL Server 2017 Reporting Services (SSRS) manualmente. Após a configuração do SSRS, verifique se a propriedade **IsInitialized** do SSRS está definida como **true**. Quando essa propriedade é definida como **true**, o servidor de backup do Azure pressupõe que o SSRS já está configurado e ignora a configuração do SSRS.

   Para verificar o status da configuração do SSRS, execute o seguinte comando:

   ```powershell
   $configset =Get-WmiObject –namespace 
   "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class 
   MSReportServer_ConfigurationSetting -ComputerName localhost

   $configset.IsInitialized
   ```

   Use os seguintes valores para a configuração do SSRS:

   * **Conta de serviço**: **usar conta interna** deve ser **serviço de rede**.
   * **URL do serviço Web**: o **diretório Virtual** deve ser **ReportServer_ \<SQLInstanceName> **.
   * **Banco de dados**: **DatabaseName** deve ser **reportserver \<SQLInstanceName> $**.
   * **URL do portal da Web**: o **diretório Virtual** deve ser **Reports_ \<SQLInstanceName> **.

   [Saiba mais](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) sobre a configuração do SSRS.

   > [!NOTE]
   > Os [termos do Microsoft Online Services](https://www.microsoft.com/licensing/product-licensing/products) (OST) governam o licenciamento para SQL Server usado como banco de dados para servidor de backup do Azure. De acordo com o OST, SQL Server agrupadas com Servidor de Backup do Azure podem ser usadas somente como o banco de dados para Servidor de Backup do Azure.

1. Depois que a instalação for bem-sucedida, selecione **Avançar**.

1. Forneça um local para a instalação de arquivos do Backup do Microsoft Azure Server e selecione **Avançar**.

   > [!NOTE]
   > O local de rascunho é necessário para o backup no Azure. Verifique se o local temporário é de pelo menos 5% dos dados planejados para fazer backup na nuvem. Para proteção de disco, discos separados precisam ser configurados após a conclusão da instalação. Para obter mais informações sobre pools de armazenamento, consulte [configurar pools de armazenamento e armazenamento em disco](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

   ![Configurações de instalação do Backup do Microsoft Azure](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1. Forneça uma senha forte para contas de usuário locais restritas e selecione **Avançar**.

   ![Backup do Microsoft Azure configurações de segurança da instalação](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1. Selecione se deseja usar Microsoft Update para verificar se há atualizações e selecione **Avançar**.

   > [!NOTE]
   > É recomendável ter Windows Update redirecionar para Microsoft Update, que oferece atualizações de segurança e importantes para o Windows e outros produtos, como Servidor de Backup do Azure.

   ![Backup do Microsoft Azure instalação Microsoft Update aceitar](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1. Examine o **Resumo das configurações**e selecione **instalar**.

   A instalação acontece em fases. A primeira fase instala o agente de Serviços de Recuperação do Microsoft Azure e a segunda fase verifica a conectividade com a Internet. Se a conectividade com a Internet estiver disponível, você poderá continuar com a instalação. Caso contrário, você deve fornecer detalhes de proxy para se conectar à Internet. A fase final verifica o software de pré-requisito. Se ele não estiver instalado, qualquer software ausente será instalado junto com o agente de Serviços de Recuperação do Microsoft Azure.

1. Selecione **procurar** para localizar suas credenciais do cofre para registrar o computador no cofre dos serviços de recuperação e, em seguida, selecione **Avançar**.

1. Escolha uma frase secreta para criptografar ou descriptografar os dados enviados entre o Azure e seu local.

   > [!TIP]
   > Você pode gerar automaticamente uma senha ou fornecer sua própria senha mínima de 16 caracteres.

1. Insira o local para salvar a frase secreta e, em seguida, selecione **Avançar** para registrar o servidor.

   > [!IMPORTANT]
   > Salve a frase secreta em um local seguro que não seja o servidor local. É altamente recomendável usar Azure Key Vault para armazenar a frase secreta.

   Depois que a instalação do agente de Serviços de Recuperação do Microsoft Azure for concluída, a etapa de instalação passará para a instalação e a configuração de SQL Server e os componentes de Servidor de Backup do Azure.

   ![Backup do Microsoft Azure instalação da instalação](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1. Após a conclusão da etapa de instalação, selecione **fechar**.

### <a name="install-update-rollup-1"></a>Instalar o pacote cumulativo de atualizações 1

A instalação do pacote cumulativo de atualizações 1 para Servidor de Backup do Azure v3 é obrigatória antes que você possa proteger as cargas de trabalho. Para exibir a lista de correções de bugs e as instruções de instalação do Servidor de Backup do Azure v3 pacote cumulativo de atualizações 1, consulte o artigo [4534062](https://support.microsoft.com/en-us/help/4534062/)da base de dados de conhecimento.

## <a name="add-storage-to-azure-backup-server"></a>Adicionar armazenamento ao Servidor de Backup do Azure

O Servidor de Backup do Azure v3 oferece suporte a Armazenamento de Backup Moderno que oferece:

-  Economia de armazenamento de 50%.
-  Backups que são três vezes mais rápidos.
-  Armazenamento mais eficiente.
-  Armazenamento com reconhecimento de carga de trabalho.

### <a name="volumes-in-azure-backup-server"></a>Volumes no Servidor de Backup do Azure

Adicione os discos de dados com a capacidade de armazenamento necessária para o Servidor de Backup do Azure máquina virtual, se ainda não tiver sido adicionado.

Servidor de Backup do Azure v3 só aceita volumes de armazenamento. Quando você adiciona um volume, o Servidor de Backup do Azure formata o volume para o ReFS (sistema de arquivos resiliente), que Armazenamento de Backup Moderno requer.

### <a name="add-volumes-to-azure-backup-server-disk-storage"></a>Adicionar volumes a Servidor de Backup do Azure armazenamento em disco

1. No painel **Gerenciamento** , examine novamente o armazenamento e, em seguida, selecione **Adicionar**. 

1. Selecione os volumes disponíveis para adicionar ao pool de armazenamento. 

1. Depois de adicionar os volumes disponíveis, dê a eles um nome amigável para ajudá-lo a gerenciá-los. 

1. Selecione **OK** para formatar esses volumes para ReFS para que servidor de backup do Azure possa usar os benefícios de armazenamento de backup moderno.

![Adicionar volumes disponíveis](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

## <a name="next-steps"></a>Próximas etapas

Continue no próximo tutorial para saber como configurar o backup de VMs VMware em execução na solução VMware do Azure usando Servidor de Backup do Azure.

> [!div class="nextstepaction"]
> [Configurar o backup de VMs de solução do Azure VMware](backup-avs-vms-with-mabs.md)

