---
title: SQL Server FCI com compartilhamento de arquivos premium - Azure Virtual Machines
description: Este artigo explica como criar uma instância de cluster de failover do SQL Server usando um compartilhamento de arquivos premium em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303215"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configure uma instância de cluster de failover do SQL Server com compartilhamento de arquivos premium em máquinas virtuais do Azure

Este artigo explica como criar uma instância de cluster de failover do SQL Server (FCI) em máquinas virtuais do Azure usando um [compartilhamento de arquivos premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Os compartilhamentos de arquivos premium são compartilhadores de arquivos com suporte a SSD e consistentemente de baixa latência que são totalmente suportados para uso com instâncias de cluster failover para o SQL Server 2012 ou posterior no Windows Server 2012 ou posterior. As ações de arquivos premium dão maior flexibilidade, permitindo que você redimensione e dimensione um compartilhamento de arquivos sem qualquer tempo de inatividade.


## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e ter no lugar antes de começar.

Você deve ter uma compreensão operacional dessas tecnologias:

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de cluster de falha do servidor SQL](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a se ter em conta é que em um cluster de failover Azure IaaS VM, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede Azure tem redundância física que torna nics adicionais e sub-redes desnecessárias em um cluster de hóspedes Azure IaaS VM. O relatório de validação de cluster avisará que os nós são acessíveis apenas em uma única rede. Você pode ignorar este aviso nos clusters failover Do Azure IaaS VM.

Você também deve ter uma compreensão geral dessas tecnologias:

- [Compartilhamento de arquivos premium do Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupos de recursos do Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, as instâncias de cluster de failover do SQL Server em máquinas virtuais do Azure só são suportadas com o modo de [gerenciamento leve](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) do [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md). Para mudar do modo de extensão completa para leve, exclua o recurso **SQL Virtual Machine** para as VMs correspondentes e registre-os no provedor de recursos SQL VM no modo leve. Ao excluir o recurso **SQL Virtual Machine** usando o portal Azure, **limpe a caixa de seleção ao lado da Máquina Virtual correta**. A extensão completa suporta recursos como backup automatizado, patches e gerenciamento avançado de portais. Esses recursos não funcionarão para VMs SQL depois que o agente for reinstalado no modo de gerenciamento leve.

Os compartilhamentos de arquivos premium fornecem IOPS e todas as capacidades que atenderão às necessidades de muitas cargas de trabalho. Para cargas de trabalho intensivas em IO, considere as instâncias de [cluster failover do SQL Server com o Storage Spaces Direct,](virtual-machines-windows-portal-sql-create-failover-cluster.md)com base em discos premium gerenciados ou discos ultra.  

Verifique a atividade IOPS do seu ambiente e verifique se os compartilhamentos de arquivos premium fornecerão o IOPS que você precisa antes de iniciar uma implantação ou migração. Use os contadores de disco do Monitor de Desempenho do Windows para monitorar o total de iOPS (Transferências de Disco/segundo) e o throughput (Disk Bytes/segundo) necessários para os arquivos SQL Server Data, Log e Temp DB.

Muitas cargas de trabalho têm estourado IO, por isso é uma boa idéia verificar durante períodos de uso pesado e observar tanto o IOPS máximo e o IOPS médio. As ações de arquivos premium fornecem IOPS com base no tamanho da ação. As ações de arquivos premium também fornecem estouro suplementar que permite que você estoure seu IO para triplicar o valor da linha de base por até uma hora.

Para obter mais informações sobre o desempenho do compartilhamento de arquivos premium, consulte [Níveis de desempenho de compartilhamento de arquivos](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licenciamento e preços

Em máquinas virtuais do Azure, você pode licenciar o SQL Server usando imagens de VM pay-as-you-go (PAYG) ou trazer sua própria licença (BYOL). O tipo de imagem que você escolhe afeta a forma como você é cobrado.

Com o licenciamento pay-as-you-go, uma instância de cluster failover (FCI) do SQL Server em máquinas virtuais Azure incorre em encargos para todos os nós da FCI, incluindo os nós passivos. Para mais informações, consulte [Preços de Máquinas Virtuais do SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se você tiver contrato corporativo com garantia de software, você pode usar um nó FCI passivo gratuito para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens BYOL VM e use a mesma licença nos nós ativos e passivos do FCI. Para obter mais informações, consulte [Contrato Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar o licenciamento pay-as-you-go e BYOL para sql server em máquinas virtuais Azure, consulte [Comece com VMs SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [Preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Fluxo de arquivos

O filestream não é suportado para um cluster failover com um compartilhamento de arquivos premium. Para usar o filestream, implante seu cluster usando [o Storage Spaces Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de completar as etapas deste artigo, você já deve ter:

- Uma assinatura do Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta de usuário de domínio que tem permissões para criar objetos em máquinas virtuais do Azure e no Active Directory.
- Uma conta de usuário de domínio para executar o serviço SQL Server e que você pode fazer login na máquina virtual com quando estiver montando o compartilhamento de arquivos.  
- Uma rede virtual e uma sub-rede do Azure com espaço de endereço IP suficiente para esses componentes:
   - Duas máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede Azure, apontando para os controladores de domínio.
- Um [compartilhamento de arquivos premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) a ser usado como unidade agrupada, com base na cota de armazenamento do seu banco de dados para seus arquivos de dados.
- Se você estiver no Windows Server 2012 R2 ou mais antigo, você precisará de outro compartilhamento de arquivo para usar como testemunha de compartilhamento de arquivos, uma vez que testemunhas na nuvem são suportadas para o Windows 2016 e mais novas. Você pode usar outro compartilhamento de arquivos do Azure, ou pode usar um compartilhamento de arquivos em uma máquina virtual separada. Se você vai usar outro compartilhamento de arquivos do Azure, você pode montá-lo com o mesmo processo que para o compartilhamento de arquivos premium usado para sua unidade clustered. 

Com esses pré-requisitos no lugar, você pode começar a construir seu cluster failover. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-the-virtual-machines"></a>Passo 1: Crie as máquinas virtuais

1. Faça login no [portal Azure](https://portal.azure.com) com sua assinatura.

1. [Criar um conjunto de disponibilidade do Azure](../tutorial-availability-sets.md).

   O conjunto de disponibilidade agrupa máquinas virtuais em domínios de falha e domínios de atualização. Ele garante que seu aplicativo não seja afetado por pontos únicos de falha, como o interruptor de rede ou a unidade de alimentação de um rack de servidores.

   Se você não criou o grupo de recursos para suas máquinas virtuais, faça isso quando criar um conjunto de disponibilidade do Azure. Se você estiver usando o portal Azure para criar o conjunto de disponibilidade, tome estas etapas:

   1. No portal Azure, selecione **Criar um recurso** para abrir o Azure Marketplace. Procure **Conjunto de disponibilidade**.
   1. Selecionar **conjunto de disponibilidade .**
   1. Selecione **Criar**.
   1. Em **Criar conjunto de disponibilidade,** forneça esses valores:
      - **Nome**: um nome para o conjunto de disponibilidade.
      - **Assinatura**: Sua assinatura do Azure.
      - **Grupo de recursos**: Se você quiser usar um grupo existente, clique em **Selecionar existente** e, em seguida, selecione o grupo na lista. Caso contrário, selecione **Criar novo** e digite um nome para o grupo.
      - **Local**: defina o local onde você planeja criar as máquinas virtuais.
      - **Domínios de falha**: Use o padrão **(3**).
      - **Atualizar domínios**: Use o padrão **(5**).
   1. Selecione **Criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Provisione duas máquinas virtuais do SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, confira [Provisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque as duas máquinas virtuais:

   - No mesmo grupo de recursos do Azure como seu conjunto de disponibilidade.
   - Na mesma rede que o controlador de domínio.
   - Em uma sub-rede que tenha espaço de endereço IP suficiente para máquinas virtuais e todas as FCIs que você pode eventualmente usar no cluster.
   - No conjunto de disponibilidade do Azure.

      >[!IMPORTANT]
      >Você não pode definir ou alterar o conjunto de disponibilidade depois de criar uma máquina virtual.

   Escolha uma imagem do Azure Marketplace. Você pode usar uma imagem do Azure Marketplace que inclui o Windows Server e o SQL Server, ou usar uma que inclua apenas o Windows Server. Para obter detalhes, consulte [Visão geral do SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

   As imagens oficiais do SQL Server na Galeria Azure incluem uma instância instalada do SQL Server, o software de instalação do SQL Server e a chave necessária.

   >[!IMPORTANT]
   > Após criar a máquina virtual, remova a instância do SQL Server autônomo pré-instalado. Você usará a mídia SQL Server pré-instalada para criar o SQL Server FCI depois de configurar o cluster de failover e o compartilhamento de arquivos premium como armazenamento.

   Alternativamente, você pode usar imagens do Azure Marketplace que contêm apenas o sistema operacional. Escolha uma imagem **do Datacenter do Windows Server 2016** e instale o SQL Server FCI depois de configurar o cluster de failover e o compartilhamento de arquivos premium como armazenamento. Esta imagem não contém mídia de instalação do SQL Server. Coloque a mídia de instalação do SQL Server em um local onde você pode executá-la para cada servidor.

1. Depois que o Azure criar suas máquinas virtuais, conecte-se a cada uma usando RDP.

   Quando você se conecta pela primeira vez a uma máquina virtual usando RDP, um prompt pergunta se você deseja permitir que o PC seja descoberto na rede. Selecione **Sim**.

1. Se você estiver usando uma das imagens de máquina virtual baseadas no SQL Server, remova a instância do SQL Server.

   1. Em **Programas e Recursos,** clique com o botão direito do **mouse no Microsoft SQL Server 201_ (64 bits)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todos os recursos em **Serviços de Mecanismo de Banco de Dados**. Não remova recursos **compartilhados**. Você verá algo como a seguinte captura de tela:

        ![Selecionar Recursos](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Selecione **Next**e selecione **Remover**.

1. <span id="ports"> </span> Abra as portas de firewall.  

   Em cada máquina virtual, abra essas portas no Firewall do Windows:

   | Finalidade | Porta TCP | Observações
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se você tiver usado uma imagem da galeria, essa porta será aberta automaticamente.
   | Investigação de integridade | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.
   | Compartilhamento de arquivo | 445 | Porta usada pelo serviço de compartilhamento de arquivos.

1. [Adicione as máquinas virtuais ao domínio pré-existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, você pode configurar o compartilhamento de arquivos premium.

## <a name="step-2-mount-the-premium-file-share"></a>Passo 2: Monte o compartilhamento de arquivos premium

1. Faça login no [portal Azure](https://portal.azure.com) e vá para sua conta de armazenamento.
1. Vá para **Arquivo Compartilha ndo** serviço **Arquivo** e selecione o compartilhamento de arquivos premium que deseja usar para o armazenamento SQL.
1. Selecione **Conectar** para trazer a seqüência de conexões para o compartilhamento de arquivos.
1. Selecione a letra de unidade que deseja usar na lista de paradas e copie os dois blocos de código para o Bloco de Notas.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Copie ambos os comandos PowerShell do portal de conexão de compartilhamento de arquivos":::

1. Use RDP para conectar-se ao VM do SQL Server com a conta que o seu SQL Server FCI usará para a conta de serviço.
1. Abra um console de comando PowerShell administrativo.
1. Execute os comandos que você salvou anteriormente quando você estava trabalhando no portal.
1. Vá para o compartilhamento usando o File Explorer ou a caixa de diálogo **Executar** (chave de logotipo do Windows + r). Use o `\\storageaccountname.file.core.windows.net\filesharename`caminho da rede . Por exemplo, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie pelo menos uma pasta no compartilhamento de arquivos recém-conectado para colocar seus arquivos SQL Data.
1. Repita essas etapas em cada VM do SQL Server que participará do cluster.

  > [!IMPORTANT]
  > - Considere usar um compartilhamento de arquivos separado para arquivos de backup para salvar o IOPS e a capacidade espacial deste compartilhamento para arquivos data e log. Você pode usar um compartilhamento de arquivos premium ou padrão para arquivos de backup.
  > - Se você estiver no Windows 2012 R2 ou mais antigo, siga essas mesmas etapas para montar o compartilhamento de arquivos que você vai usar como testemunha de compartilhamento de arquivos. 

## <a name="step-3-configure-the-failover-cluster"></a>Passo 3: Configure o cluster failover

O próximo passo é configurar o cluster failover. Nesta etapa, você completará as seguintes subetapas:

1. Adicione o recurso de clusterde failover do Servidor do Windows.
1. Valide o cluster.
1. Crie o cluster failover.
1. Crie a testemunha na nuvem (para o Windows Server 2016 e mais nova) ou a testemunha de compartilhamento de arquivos (para o Windows Server 2012 R2 e mais antigo).


### <a name="add-windows-server-failover-clustering"></a>Adicionar cluster de failover do Servidor do Windows

1. Conecte-se à primeira máquina virtual com RDP usando uma conta de domínio que seja um membro dos administradores locais e que tenha permissão para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. [Adicionar clusterde failover a cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar o Failover Clustering da ui, dê essas etapas em ambas as máquinas virtuais:
   1. No **Gerenciador de**servidores, **selecione Gerenciar**e, em seguida, **selecione Adicionar funções e recursos**.
   1. No **Assistente adicionar funções e recursos,** selecione **Next** até chegar a **Selecionar recursos**.
   1. Em **Selecionar recursos,** selecione **Clusterde failover**. Inclua todos os recursos e as ferramentas de gerenciamento. Selecione **Adicionar recursos**.
   1. Selecione **Next**e selecione **Concluir** para instalar os recursos.

   Para instalar o Failover Clustering usando o PowerShell, execute o seguinte script de uma sessão powershell do administrador em uma das máquinas virtuais:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na ui ou usando powerShell.

Para validar o cluster usando a ui, dê as seguintes etapas em uma das máquinas virtuais:

1. Em **Server Manager,** selecione **Ferramentas**e selecione **Failover Cluster Manager**.
1. Em **Failover Cluster Manager**, selecione **Action**e selecione **Validar configuração**.
1. Selecione **Avançar**.
1. Em **Selecionar servidores ou um cluster,** digite os nomes de ambas as máquinas virtuais.
1. Em **Opções de teste,** **selecione Executar apenas testes que selecione**. Selecione **Avançar**.
1. Em **Seleção de Teste,** selecione todos os testes, exceto **o Storage** and Storage Spaces **Direct,** conforme mostrado aqui:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Selecione testes de validação de cluster":::

1. Selecione **Avançar**.
1. Em **Confirmação,** selecione **Next**.

O **Assistente para Validar uma Configuração** executa os testes de validação.

Para validar o cluster usando o PowerShell, execute o seguinte script de uma sessão powershell do administrador em uma das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornarão os nós de cluster.
- Um nome para o cluster de failover
- Um endereço IP do cluster de failover. Você pode usar um endereço IP que não é usado na mesma rede virtual e sub-rede do Azure que os nós de cluster.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 através do Windows Server 2016

O script PowerShell a seguir cria um cluster de failover para o Windows Server 2012 através do Windows Server 2016. Atualize o script com os nomes dos nomes dos nados (os nomes das máquinas virtuais) e um endereço IP disponível na rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O script PowerShell a seguir cria um cluster failover para o Windows Server 2019. Para obter mais informações, consulte [Failover cluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nomes dos nados (os nomes das máquinas virtuais) e um endereço IP disponível na rede virtual Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Crie uma testemunha em nuvem (Win 2016 +)

Se você estiver no Windows Server 2016 e maior, você precisará criar uma Testemunha de Nuvem. Cloud Witness é um novo tipo de testemunha de quórum de cluster que está armazenado em uma bolha de armazenamento Azure. Isso remove a necessidade de uma VM separada que hospede um compartilhamento de testemunhas ou usando um compartilhamento de arquivos separado.

1. [Crie uma testemunha de nuvem para o cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Criar um contêiner de blob.

1. Salve as chaves de acesso e a URL do contêiner.

### <a name="configure-quorum"></a>Configurar quorum 

Para o Windows Server 2016 e maior, configure o cluster para usar a testemunha de nuvem que você acabou de criar. Siga todas as etapas [Configure a testemunha de quórum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

Para o Windows Server 2012 R2 ou mais antigo, siga os mesmos passos em [Configure a testemunha de quórum na interface do usuário,](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) mas na página **Select Quorum Witness,** selecione a opção Configurar uma testemunha **de compartilhamento de arquivos.** Especifique o compartilhamento de arquivos que você alocou para ser a testemunha de compartilhamento de arquivos, seja uma que você configurou em uma máquina virtual separada ou montada no Azure. 


## <a name="step-4-test-cluster-failover"></a>Passo 4: Failover de cluster de teste

Teste failover do seu cluster. No **Failover Cluster Manager,** clique com o botão direito do mouse no cluster e selecione **Mais ações** > **Mover nó de recurso de cluster** > de**núcleo**e selecione o outro nó do cluster. Mova o recurso do cluster central para cada nó do cluster e, em seguida, mova-o de volta para o nó primário. Se você conseguir mover com sucesso o cluster para cada nó, você está pronto para instalar o SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Failover do cluster de teste movendo o recurso central para os outros nós":::

## <a name="step-5-create-the-sql-server-fci"></a>Passo 5: Crie o FCI do Servidor SQL

Depois de configurar o cluster failover, você pode criar o FCI do SQL Server.

1. Conecte-se à primeira máquina virtual usando RDP.

1. No **Failover Cluster Manager**, certifique-se de que todos os recursos de cluster principais estão na primeira máquina virtual. Se você precisar, mova todos os recursos para esta máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. Selecione **instalação**.

1. Na **Central de Instalação do Servidor SQL,** selecione **Instalação**.

1. Selecione **Nova instalação de cluster de failover do SQL Server**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados FCI precisam estar no compartilhamento de arquivos premium. Digite o caminho completo da ação, nesta forma: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Um aviso aparecerá, dizendo que você especificou um servidor de arquivos como o diretório de dados. Esse aviso é esperado. Certifique-se de que a conta de usuário com a que você rdp'd na VM quando você persistiu o compartilhamento de arquivos é a mesma conta que o serviço SQL Server usa para evitar possíveis falhas.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Use o compartilhamento de arquivos como diretórios de dados SQL":::

1. Depois de concluir as etapas no assistente, a configuração instalará um FCI do Servidor SQL no primeiro nó.

1. Depois que a configuração instalar o FCI no primeiro nó, conecte-se ao segundo nó usando RDP.

1. Abra a **Central de Instalação do SQL Server**. Selecione **Instalação**.

1. Selecione **Adicionar nó a um cluster de failover do SQL Server**. Siga as instruções no assistente para instalar o SQL Server e adicione o servidor ao FCI.

   >[!NOTE]
   >Se você usou uma imagem da Galeria do Azure Marketplace com o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se você não usou uma dessas imagens, instale as ferramentas do SQL Server separadamente. Confira [Baixar o SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Passo 6: Crie o balanceador de carga Do Zure

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster de cada vez. Nessa solução, o balanceador de carga mantém o endereço IP para o FCI do SQL Server.

Para obter mais informações, consulte [Criar e configurar um balanceador de carga Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. No portal Azure, acesse o grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Procure no Azure Marketplace para **obter balanceador de carga**. Selecione **Balanceador de carga**.

1. Selecione **Criar**.

1. Configure o balanceador de carga usando os seguintes valores:

   - **Assinatura**: Sua assinatura do Azure.
   - **Grupo de recursos**: O grupo de recursos que contém suas máquinas virtuais.
   - **Nome**: um nome que identifica o balanceador de carga.
   - **Região**: A localização do Azure que contém suas máquinas virtuais.
   - **Tipo:** Público ou privado. Um balanceador de carga privado pode ser acessado a partir de dentro da rede virtual. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se seu aplicativo precisar de acesso ao SQL Server diretamente pela internet, use um balanceador de carga pública.
   - **SKU**: Padrão.
   - **Rede virtual**: A mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP:** Estática. 
   - **Endereço IP privado :** O endereço IP que você atribuiu ao recurso de rede de cluster sql server FCI.

   A imagem a seguir mostra a **UI do balanceador de carga** Create:

   ![Configure o balanceador de carga](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao grupo de recursos do Azure que contém as máquinas virtuais e localize o novo balanceador de carga. Você pode precisar atualizar a exibição no grupo de recursos. Escolha o balanceador de carga.

1. Selecione **pools backend**e selecione **Adicionar**.

1. Associe o pool de back-end com o conjunto de disponibilidade que contém as VMs.

1. Em **Configurações IP de rede target,** selecione **MÁQUINA VIRTUAL** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão FCI.

1. Selecione **OK** para criar o pool de backend.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar um teste de integridade do balanceador de carga

1. Na lâmina do balanceador de carga, selecione **sondas Health**.

1. Selecione **Adicionar**.

1. Na lâmina da sonda <span id="probe"> </span> De adicionar **saúde,** defina os seguintes parâmetros da sonda de saúde.

   - **Nome**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: A porta que você criou no firewall para o teste de saúde [nesta etapa](#ports). Neste artigo, o exemplo usa `59999`a porta TCP .
   - **Intervalo**: 5 segundos.
   - **Limite não íntegro**: duas falhas consecutivas.

1. Selecione **OK**.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. Na lâmina do balanceador de carga, selecione **Regras de balanceamento de carga**.

1. Selecione **Adicionar**.

1. Defina os parâmetros das regras de balanceamento de carga:

   - **Nome**: um nome para as regras de balanceamento de carga.
   - **Endereço IP frontend :** o endereço IP para o recurso de rede de cluster SQL Server FCI.
   - **Porta**: A porta SQL Server FCI TCP. A porta de instância padrão é 1433.
   - **Porta backend :** Usa a mesma porta que o valor **da porta** quando você habilita IP **flutuante (retorno direto do servidor)**.
   - **Pool backend**: O nome do pool de backend que você configurou anteriormente.
   - **Sonda de**saúde : A sonda de saúde que você configurou anteriormente.
   - **Persistência de sessão**: nenhuma.
   - **Tempo limite de ociosidade (minutos)**: 4.
   - **IP flutuante (retorno direto do servidor)**: Ativado.

1. Selecione **OK**.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Passo 7: Configure o cluster para a sonda

Defina o parâmetro de porta de investigação de cluster no PowerShell.

Para definir o parâmetro de porta do teste de cluster, atualize as variáveis no script a seguir com valores do seu ambiente. Remova os suportes`<` `>`angulares (e) do script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A lista a seguir descreve os valores que você precisa atualizar:

   - `<Cluster Network Name>`: O nome do cluster failover do Servidor Do Windows para a rede. Em **Failover Cluster Manager** > **Networks**, clique com o botão direito do mouse na rede e selecione **Propriedades**. O valor correto está em **Nome**, na guia **Geral**.

   - `<SQL Server FCI IP Address Resource Name>`: O nome do recurso de endereço IP do SQL Server FCI. Em **Failover Cluster Manager** > **Roles**, a função SQL Server FCI, em Nome **do servidor,** clique com o botão direito do mouse no recurso de endereço IP e selecione **Propriedades**. O valor correto está em **Nome**, na guia **Geral**.

   - `<ILBIP>`: o endereço IP do ILB. Esse endereço é configurado no Portal do Azure como o endereço front-end do ILB. Esse também é o endereço IP de FCI do SQL Server. Você pode localizá-lo no **Gerenciador de Cluster de Failover**, na mesma página de propriedades em que você localizou o `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: A porta de sonda configurada no teste de saúde do balanceador de carga. Qualquer porta TCP não usada é válida.

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro de cluster deve ser o endereço de difusão de TCP IP: `255.255.255.255`.

Depois de definir o teste de cluster, você pode ver todos os parâmetros de cluster no PowerShell. Execute este script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>Passo 8: Failover de teste fci

Teste o failover de FCI para validar a funcionalidade do cluster. Siga estas etapas:

1. Conecte-se a um dos nós de cluster FcI do SQL Server usando RDP.

1. Abrir **failover cluster manager**. Selecione **funções**. Observe qual nó possui a função FCI do SQL Server.

1. Clique com o botão direito do mouse na função FCI do SQL Server.

1. Selecione **Mover**e, em seguida, selecione **O Melhor Nó Possível**.

**Failover Cluster Manager** mostra a função e seus recursos ficam offline. Os recursos então se movem e voltam on-line no outro nó.

### <a name="test-connectivity"></a>Testar a conectividade

Para testar a conectividade, entre em outra máquina virtual na mesma rede virtual. Abra o **SQL Server Management Studio** e conecte-se ao nome de FCI do SQL Server.

>[!NOTE]
>Se você precisar, você pode [baixar SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

As máquinas virtuais do Azure suportam o Microsoft Distributed Transaction Coordinator (MSDTC) no Windows Server 2019 com armazenamento em CSV (Clustered Shared Volumes, volumes compartilhados) e um [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais Azure, o MSDTC não é suportado no Windows Server 2016 ou anteriormente porque:

- O recurso MSDTC em cluster não pode ser configurado para usar armazenamento compartilhado. No Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver disponível. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.

## <a name="see-also"></a>Confira também

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de cluster de falha do servidor SQL](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
