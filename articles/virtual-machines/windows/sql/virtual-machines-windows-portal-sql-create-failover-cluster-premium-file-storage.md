---
title: SQL Server FCI com compartilhamento de arquivos Premium – máquinas virtuais do Azure
description: Este artigo explica como criar uma instância de cluster de failover SQL Server usando um compartilhamento de arquivos Premium em máquinas virtuais do Azure.
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
ms.openlocfilehash: 39f04005776f3b451ad7c64c76f9aa5d8c4a7768
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72330083"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configurar SQL Server instância de cluster de failover com compartilhamento de arquivos Premium em máquinas virtuais do Azure

Este artigo explica como criar uma FCI (instância de cluster de failover) SQL Server em máquinas virtuais do Azure usando um [compartilhamento de arquivos Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md). 

Os compartilhamentos de arquivos Premium são compartilhamentos de arquivos consistentemente de baixa latência que têm suporte total para uso com a instância de cluster de failover do SQL Server 2012 e mais recente no Windows Server 2012 e mais recente. Os compartilhamentos de arquivos Premium proporcionam maior flexibilidade, permitindo que você redimensione e dimensione o compartilhamento de arquivos sem nenhum tempo de inatividade. 


## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e fazer antes de continuar.

Você deve ter uma compreensão operacional das seguintes tecnologias:

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de Cluster de Failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

Uma diferença importante é que, em um cluster de failover de VM IaaS do Azure, recomendamos uma única NIC por servidor (nó de cluster) e uma única sub-rede. A rede do Azure tem redundância física que torna as NICs e sub-redes adicionais desnecessárias em um cluster convidado da VM IaaS do Azure. Embora o relatório de validação de cluster emita um aviso de que os nós só podem ser acessados em uma única rede, esse aviso pode ser ignorado com segurança em clusters de failover de VM IaaS do Azure. 

Além disso, você deve ter uma compreensão geral das tecnologias a seguir:

- [Compartilhamento de arquivos premium do Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupos de recursos do Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> Neste momento, SQL Server instâncias de cluster de failover em máquinas virtuais do Azure só têm suporte com o modo de gerenciamento [leve](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) da [extensão do agente IaaS SQL Server](virtual-machines-windows-sql-server-agent-extension.md). Desinstale a extensão completa das VMs que participam do cluster de failover e registre-as com o provedor de recursos de VM do SQL no modo `lightweight`. A extensão completa oferece suporte a recursos como backup automatizado, aplicação de patch e gerenciamento avançado do Portal. Esses recursos não funcionarão para VMs do SQL depois que o agente for reinstalado no modo de gerenciamento leve.

### <a name="workload-consideration"></a>Consideração da carga de trabalho

Os compartilhamentos de arquivos Premium fornecem IOPS e, durante toda a capacidade, atenderá às necessidades de várias cargas de trabalho. No entanto, para cargas de trabalho com uso intensivo de e/s, considere [SQL Server FCI com espaços de armazenamento diretos](virtual-machines-windows-portal-sql-create-failover-cluster.md) com base em discos Premium gerenciados ou em discos ultra.  

Verifique a atividade de IOPS do seu ambiente atual e verifique se os arquivos Premium fornecerão o IOPS de que você precisa antes de iniciar uma implantação ou migração. Use os contadores de disco do monitor de desempenho do Windows e monitore o IOPS total (transferências de disco/s) e a taxa de transferência (bytes de disco/s) necessários para dados de SQL Server, log e arquivos de BD temporários. Muitas cargas de trabalho têm intermitência de e/s para que seja uma boa ideia verificar durante períodos de uso intenso e observar a IOPS máxima, bem como a média de IOPS. Os compartilhamentos de arquivos Premium fornecem IOPS com base no tamanho do compartilhamento. Os arquivos Premium também fornecem intermitências especiais, onde você pode aumentar sua e/s para triplo da quantidade de linha de base por até uma hora. 

Para obter mais informações sobre o desempenho de compartilhamento de arquivos premium, consulte [níveis de desempenho de compartilhamento de arquivos](https://docs.microsoft.com/en-us/azure/storage/files/storage-files-planning#file-share-performance-tiers). 

### <a name="licensing-and-pricing"></a>Licenciamento e preço

Em máquinas virtuais do Azure, você pode licenciar SQL Server usando suas imagens de VM de PAYG (BYOL) ou traga sua própria licença. O tipo de imagem que você escolheu afeta como você é cobrado.

Com licenciamento PAYG, uma instância de cluster de failover (FCI) do SQL Server em Máquinas Virtuais do Microsoft Azure, incorre em encargos de todos os nós de FCI, incluindo os nós passivos. Para mais informações, consulte [Preços de Máquinas Virtuais do SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Os clientes com Contrato Enterprise com Software Assurance têm o direito de usar um nó FCI passivo livre para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens de VM BYOL e, em seguida, use a mesma licença nos nós ativo e passivo do FCI. Para obter mais informações, consulte [Contrato Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar licenciamento PAYG e BYOL para SQL Server em Máquinas Virtuais do Microsoft Azure, consulte [Introdução às VMs do SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [Preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="limitations"></a>Limitações

- Não há suporte para FILESTREAM em um cluster de failover com um compartilhamento de arquivos premium. Para usar FILESTREAM, implante o cluster usando [espaços de armazenamento diretos](virtual-machines-windows-portal-sql-create-failover-cluster.md). 

## <a name="prerequisites"></a>Pré-requisitos 

Antes de seguir as instruções neste artigo, você já deve ter:

- Uma assinatura do Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta com permissão para criar objetos na máquina virtual do Azure.
- Uma rede virtual do Azure e uma sub-rede com espaço de endereço IP suficiente para os seguintes componentes:
   - As duas máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na Rede Azure, apontando para os controladores de domínio.
- Um [compartilhamento de arquivos Premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) com base na cota de armazenamento do seu banco de dados para seus arquivos. 
- Um compartilhamento de arquivos para backups que é diferente do compartilhamento de arquivos Premium usado para seus arquivos de dados. Esse compartilhamento de arquivos pode ser Standard ou Premium. 

Com esses pré-requisitos em vigor, é possível continuar com a criação do cluster de failover. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-virtual-machines"></a>Etapa 1: criar máquinas virtuais

1. Faça logon na [portal do Azure](https://portal.azure.com) com sua assinatura.

1. [Criar um conjunto de disponibilidade do Azure](../tutorial-availability-sets.md).

   O conjunto de disponibilidade agrupa máquinas virtuais em domínios de falha e domínios de atualização. O conjunto de disponibilidade garante que o aplicativo não seja afetado por pontos únicos de falha, como o comutador de rede ou a unidade de energia de um rack de servidores.

   Se não criou o grupo de recursos para as máquinas virtuais, faça-o quando você criar um conjunto de disponibilidade do Azure. Se estiver usando o portal do Azure para criar o conjunto de disponibilidade, siga estas etapas:

   - No portal do Azure, clique em **+** para abrir o Azure Marketplace. Procure **Conjunto de disponibilidade**.
   - Clique em **Conjunto de disponibilidade**.
   - Clique em **Criar**.
   - Na folha **Criar conjunto de disponibilidade**, defina os seguintes valores:
      - **Nome**: um nome para o conjunto de disponibilidade.
      - **Assinatura**: sua assinatura do Azure.
      - **Grupo de recursos**: se você quiser usar um grupo existente, clique em **Usar existente** e selecione o grupo na lista suspensa. Caso contrário, escolha **Criar Novo** e digite um nome para o grupo.
      - **Local**: defina o local onde você planeja criar as máquinas virtuais.
      - **Domínios de falha**: use o padrão (3).
      - **Domínios de atualização**: use o padrão (5).
   - Clique em **Criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Provisione duas máquinas virtuais do SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, confira [Provisionar uma máquina virtual do SQL Server no portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

   Coloque as duas máquinas virtuais:

   - No mesmo grupo de recursos do Azure em que o conjunto de disponibilidade está.
   - Na mesma rede que o controlador de domínio.
   - Em uma sub-rede com espaço suficiente de endereços IP para ambas as máquinas virtuais e todos ps FCIs que você pode vir a usar nesse cluster.
   - No conjunto de disponibilidade do Azure.   

      >[!IMPORTANT]
      >Você não pode definir nem alterar a disponibilidade definida depois que uma máquina virtual é criada.

   Escolha uma imagem do Azure Marketplace. Você pode usar uma imagem do Marketplace com isso que inclua apenas o Windows Server e o SQL Server ou o Windows Server. Para obter detalhes, confira [Visão geral do SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md)

   As imagens oficiais do SQL Server na Galeria do Azure incluem uma instância do SQL Server instalada, mais o software de instalação do SQL Server e a chave necessária.

   >[!IMPORTANT]
   > Após criar a máquina virtual, remova a instância do SQL Server autônomo pré-instalado. Você usará a mídia de SQL Server pré-instalada para criar o SQL Server FCI depois de configurar o cluster de failover e o compartilhamento de arquivos Premium como armazenamento. 

   Como alternativa, você pode usar imagens do Azure Marketplace apenas com o sistema operacional. Escolha uma imagem do **Windows Server 2016 datacenter** e instale o SQL Server FCI depois de configurar o cluster de failover e o compartilhamento de arquivos Premium como armazenamento. Esta imagem não contém a mídia de instalação do SQL Server. Coloque a mídia de instalação em um local onde você possa executar a instalação do SQL Server para cada servidor. 

1. Depois que o Azure criar máquinas virtuais, conecte-se a cada máquina virtual com o RDP.

   Quando você se conecta pela primeira vez a uma máquina virtual com o RDP, o computador perguntará se deseja você permitir que esse computador seja detectável na rede. Clique em **Sim**.

1. Se estiver usando uma das imagens de máquina virtual baseada no SQL Server, remova a instância do SQL Server.

   - Em **programas e recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 201_ (64 bits)** e clique em **Desinstalar/alterar**.
   - Clique em **Remover**.
   - Selecione a instância padrão.
   - Remova todos os recursos em **Serviços de Mecanismo de Banco de Dados**. Não remova **Recursos Compartilhados**. Confira a seguinte figura:

      ![Remover Recursos](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Clique em **Avançar** e em **Remover**.

1. <a name="ports"></a>Abra as portas do firewall.

   Em cada máquina virtual, abra as portas a seguir no Firewall do Windows.

   | Finalidade | Porta TCP | Notas
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se você tiver usado uma imagem da galeria, essa porta será aberta automaticamente.
   | Investigação de integridade | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.   
   | Compartilhamento de arquivos | 445 | Porta usada pelo serviço de compartilhamento de arquivos. 

1. [Adicione as máquinas virtuais ao domínio pré-existente](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

Depois que as máquinas virtuais são criadas e configuradas, você pode configurar o compartilhamento de arquivos premium.

## <a name="step-2-mount-premium-file-share"></a>Etapa 2: montar o compartilhamento de arquivos Premium

1. Entre no [portal do Azure](https://portal.azure.com) e vá para sua conta de armazenamento.
1. Vá para **compartilhamentos de arquivos** em **serviço de arquivo** e selecione o compartilhamento de arquivos premium que você deseja usar para o armazenamento do SQL. 
1. Selecione **conectar** para exibir a cadeia de conexão para o compartilhamento de arquivos. 
1. Selecione a letra da unidade que você deseja usar na lista suspensa e, em seguida, copie os dois blocos de código para um bloco de notas.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/premium-file-storage-commands.png" alt-text="Copie os dois comandos do PowerShell do portal de conexão de compartilhamento de arquivos":::

1. RDP na VM SQL Server usando a conta que seu SQL Server FCI usará para a conta de serviço. 
1. Inicie um console de comando do PowerShell administrativo. 
1. Execute os comandos do portal que você salvou anteriormente. 
1. Navegue até o compartilhamento com o explorador de arquivos ou a caixa de diálogo **executar** (chave do Windows + r) usando o caminho de rede `\\storageaccountname.file.core.windows.net\filesharename`. Exemplo: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie pelo menos uma pasta no compartilhamento de arquivos conectado recentemente para posicionar seus arquivos de dados SQL. 
1. Repita essas etapas em cada VM SQL Server que participará do cluster. 

  > [!IMPORTANT]
  > Considere usar um compartilhamento de arquivos separado para arquivos de backup para salvar a capacidade de IOPS e tamanho desse compartilhamento para arquivos de dados e de log. Você pode usar um compartilhamento de arquivos Premium ou Standard para arquivos de backup

## <a name="step-3-configure-failover-cluster-with-file-share"></a>Etapa 3: configurar o cluster de failover com o compartilhamento de arquivos 

A próxima etapa é configurar o cluster de failover. Nesta etapa, você executará as seguintes subetapas:

1. Adicionar o recurso Clustering de Failover do Windows
1. Validar o cluster
1. Criar o cluster de failover
1. Criar a testemunha de nuvem


### <a name="add-windows-failover-clustering-feature"></a>Adicionar o recurso Clustering de Failover do Windows

1. Para começar, conecte-se à primeira máquina virtual com o RDP usando uma conta de domínio que seja membro do grupo local Administradores e tenha permissões para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. [Adicionar o recurso de Cluster de Failover para cada máquina virtual](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar o recurso de Cluster de Failover por meio da interface do usuário, execute as etapas a seguir nas duas máquinas virtuais.
   - Em **Gerenciador de Servidor**, clique em **Gerenciar** e clique em **Adicionar Funções e Recursos**.
   - No **Assistente para Adicionar Funções e Recursos**, clique em **Avançar** até chegar a **Selecionar Recursos**.
   - Em **Selecionar Recursos**, clique em **Clustering de Failover**. Inclua todos os recursos e as ferramentas de gerenciamento. Clique em **Adicionar Recursos**.
   - Clique em **Avançar** e em **Concluir** para instalar os recursos.

   Para instalar o recurso de Cluster de Failover com o PowerShell, execute o script a seguir em uma sessão do PowerShell de administrador em uma das máquinas virtuais.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Validar o cluster

Este guia se refere a instruções em [validar cluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Valide o cluster na interface do usuário ou com o PowerShell.

Para validar o cluster com a interface do usuário, execute as etapas a seguir em uma das máquinas virtuais.

1. Em **Gerenciador do Servidor**, clique em **Ferramentas** e clique em **Gerenciador de Cluster de Failover**.
1. Em **Gerenciador de Cluster de Failover**, clique em **Ação** e clique em **Validar Configuração...** .
1. Clique em \\**Próximo**.
1. Em **Selecionar Servidores ou um Cluster**, digite o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, escolha **Executar apenas os testes selecionados**. Clique em \\**Próximo**.
1. Na **seleção de teste**, inclua todos os testes, exceto **armazenamento** e **espaços de armazenamento diretos**. Confira a seguinte figura:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/cluster-validation.png" alt-text="Testes de validação de cluster":::

1. Clique em \\**Próximo**.
1. Em **Confirmação**, clique em **Avançar**.

O **Assistente para Validar uma Configuração** executa os testes de validação.

Para validar o cluster com o PowerShell, execute o seguinte script em uma sessão do PowerShell de administrador em uma das máquinas virtuais.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de failover


Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornam os nós do cluster.
- Um nome para o cluster de failover
- Um endereço IP do cluster de failover. Você pode usar um endereço IP que não seja usado na mesma sub-rede e rede virtual do Azure que os nós de cluster.

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

O PowerShell a seguir cria um cluster de failover para o **Windows Server 2012-2016**. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP da VNET do Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O PowerShell a seguir cria um cluster de failover para o Windows Server 2019.  Para obter mais informações, consulte o [objeto cluster de failover de blog: cluster de rede](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP da VNET do Azure:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem

A Testemunha de Nuvem é um novo tipo de testemunha de quorum de cluster armazenado em um Azure Storage Blob. Isso elimina a necessidade de uma VM separada que hospede um compartilhamento de testemunha.

1. [Crie uma testemunha de nuvem para o cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Criar um contêiner de blob.

1. Salve as chaves de acesso e a URL do contêiner.

1. Configure a testemunha de quorum do cluster de failover. Consulte, [Configurar a testemunha de quorum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) na IU.


## <a name="step-4-test-cluster-failover"></a>Etapa 4: testar o failover de cluster

Failover de teste do cluster. Em Gerenciador de Cluster de Failover, clique com o botão direito do mouse no cluster > **mais ações** > **mover recurso de cluster de núcleo** > **selecione nó** e selecione o outro nó do cluster. Mova o recurso de cluster principal para cada nó do cluster e, em seguida, mova-o de volta para o nó primário. Se for possível mover o cluster para cada nó com êxito, você estará pronto para instalar o SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/test-cluster-failover.png" alt-text="Testar o failover de cluster movendo o recurso principal para os outros nós":::

## <a name="step-5-create-sql-server-fci"></a>Etapa 5: criar SQL Server FCI

Depois de configurar o cluster de failover, você pode criar o SQL Server FCI.

1. Conecte-se à primeira máquina virtual com o RDP.

1. No **Gerenciador de Cluster de Failover**, verifique se todos os recursos de cluster principais estão na primeira máquina virtual. Se necessário, mova todos os recursos para essa máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. Clique em **Instalação**.

1. Na **Central de Instalação do SQL Server**, clique em **Instalação**.

1. Clique em **Nova instalação de cluster de failover do SQL Server**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados do FCI precisam estar no compartilhamento de arquivos premium. Digite o caminho completo do compartilhamento, na forma de `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Um aviso será exibido, notificando que você especificou um servidor de arquivos como o diretório de dados. Isso é esperado. Verifique se a mesma conta com a qual você persistiu o compartilhamento de arquivos é a mesma conta que o serviço SQL Server usa para evitar possíveis falhas. 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/use-file-share-as-data-directories.png" alt-text="Usar o compartilhamento de arquivos como diretórios de dados SQL":::

1. Depois que você concluir o assistente, a configuração instalará um FCI do SQL Server no primeiro nó.

1. Após a configuração instalar com êxito o FCI no primeiro nó, conecte-se ao segundo nó com o RDP.

1. Abra a **Central de Instalação do SQL Server**. Clique em **Instalação**.

1. Clique em **Adicionar nó a um cluster de failover do SQL Server**. Siga as instruções no assistente para instalar o SQL server e adicionar esse servidor ao FCI.

   >[!NOTE]
   >Se você usou uma imagem da Galeria do Azure Marketplace com o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se não usou essa imagem, instale as ferramentas do SQL Server separadamente. Confira [Baixar o SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-azure-load-balancer"></a>Etapa 6: criar o Azure Load Balancer

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster de cada vez. Nessa solução, o balanceador de carga mantém o endereço IP para o FCI do SQL Server.

[Criar e configurar um balanceador de carga do Azure](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. No portal do Azure, vá para o Grupo de Recursos com as máquinas virtuais.

1. Clique em **+ Adicionar**. Procure o **balanceador de carga** no Marketplace. Clique em **Balanceador de Carga**.

1. Clique em **Criar**.

1. Configure o balanceador de carga com:

   - **Assinatura**: sua assinatura do Azure.
   - **Grupo de recursos**: use o mesmo grupo de recursos que as máquinas virtuais.
   - **Nome**: um nome que identifica o balanceador de carga.
   - **Região**: Use o mesmo local do Azure que suas máquinas virtuais.
   - **Tipo**: o balanceador de carga pode ser público ou privado. Um balanceador de carga privado pode ser acessado na mesma VNET. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se o aplicativo precisa ter acesso ao SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **SKU**: a SKU do balanceador de carga deve ser padrão. 
   - **Rede Virtual**: a mesma rede que as máquinas virtuais.
   - **Atribuição de endereço IP**: a atribuição de endereço IP deve ser estática. 
   - **Endereço IP privado**: o mesmo endereço IP que você atribuiu ao recurso de rede de cluster FCI do SQL Server.
   Confira a seguinte figura:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao Grupo de Recursos do Azure com as máquinas virtuais e localize o novo balanceador de carga. Talvez você precise atualizar o modo de exibição no Grupo de Recursos. Clique no balanceador de carga.

1. Clique em **Pools de back-end** e clique em **+ Adicionar** para adicionar um pool de back-end.

1. Associe o pool de back-end com o conjunto de disponibilidade que contém as VMs.

1. Em **Configurações de IP de rede de destino**, marque **MÁQUINA VIRTUAL** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão FCI. 

1. Clique em **OK** para criar o pool de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar um teste de integridade do balanceador de carga

1. Na folha de balanceador de carga, clique em **Investigações de integridade**.

1. Clique em **+ Adicionar**.

1. Na folha **Adicionar investigação de integridade**, <a name="probe"></a>Definir os parâmetros de investigação de integridade:

   - **Nome**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: defina para a porta que você criou no firewall para a investigação de integridade nesta [etapa](#ports). Neste artigo, o exemplo usa a porta TCP `59999`.
   - **Intervalo**: 5 segundos.
   - **Limite não íntegro**: duas falhas consecutivas.

1. Clique em OK.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. Na folha balanceador de carga, clique em **regras de balanceamento de carga**.

1. Clique em **+ Adicionar**.

1. Defina os parâmetros de regras de balanceamento de carga:

   - **Nome**: um nome para as regras de balanceamento de carga.
   - **Endereço IP de front-end**: use o endereço IP para o recurso de rede do cluster FCI do SQL Server.
   - **Porta**: definida para a porta TCP do SQL Server FCI. A porta de instância padrão é 1433.
   - **Porta de back-end**: esse valor usa a mesma porta que o valor de **Porta** quando você habilita **IP flutuante (retorno de servidor direto)** .
   - **Pool de back-end**: use o nome do pool de back-end que você configurou anteriormente.
   - **Investigação de integridade**: use a investigação de integridade que você configurou anteriormente.
   - **Persistência de sessão**: nenhuma.
   - **Tempo limite de ociosidade (minutos)** : 4.
   - **IP flutuante (retorno de servidor direto)** : habilitado

1. Clique em **OK**.

## <a name="step-7-configure-cluster-for-probe"></a>Etapa 7: configurar o cluster para investigação

Defina o parâmetro de porta de investigação de cluster no PowerShell.

Para definir o parâmetro de porta de investigação do cluster, atualize as variáveis no script a seguir usando valores do seu ambiente. Remova os colchetes angulares `<>` do script. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

No script anterior, defina os valores para o seu ambiente. A lista a seguir descreve os valores:

   - `<Cluster Network Name>`: Nome do cluster de failover do Windows Server para a rede. Em **Gerenciador de Cluster de Failover** > **Redes**, clique com o botão direito do mouse na rede e clique em **Propriedades**. O valor correto está em **Nome**, na guia **Geral**. 

   - `<SQL Server FCI IP Address Resource Name>`: nome de recurso de endereço IP de FCI do SQL Server. Em **Gerenciador de cluster de failover** **funções** > , na função SQL Server FCI, em **nome do servidor**, clique com o botão direito do mouse no recurso de endereço IP e clique em **Propriedades**. O valor correto está em **Nome**, na guia **Geral**. 

   - `<ILBIP>`: o endereço IP do ILB. Esse endereço é configurado no Portal do Azure como o endereço front-end do ILB. Esse também é o endereço IP de FCI do SQL Server. Você pode localizá-lo no **Gerenciador de Cluster de Failover**, na mesma página de propriedades em que você localizou o `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: é a porta de investigação configurada na investigação de integridade do balanceador de carga. Qualquer porta TCP não usada é válida. 

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro de cluster deve ser o endereço de difusão de TCP IP: `255.255.255.255`.

Depois de definir a investigação de cluster, você poderá ver todos os parâmetros de cluster no PowerShell. Execute o seguinte script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>Etapa 8: testar o failover do FCI

Teste o failover de FCI para validar a funcionalidade do cluster. Execute as seguintes etapas:

1. Conecte-se a um de nós do cluster FCI do SQL Server com o RDP.

1. Abra o **Gerenciador de Cluster de Failover**. Clique em **Funções**. Observe qual nó possui a função FCI do SQL Server.

1. Clique com o botão direito do mouse na função FCI do SQL Server.

1. Clique em **Mover** e em **Melhor Nó Possível**.

**Gerenciador de Cluster de Failover** mostra a função, e seus recursos ficam offline. Os recursos são movidos e ficam online no outro nó.

### <a name="test-connectivity"></a>Testar a conectividade

Para testar a conectividade, faça logon em outra máquina virtual na mesma rede virtual. Abra o **SQL Server Management Studio** e conecte-se ao nome de FCI do SQL Server.

>[!NOTE]
>Se necessário, você pode [baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

As Máquinas Virtuais do Azure dão suporte ao Coordenador de Transações Distribuídas da Microsoft (MSDTC) no Windows Server 2019 com o armazenamento nos volumes compartilhados clusterizados (CSV) e um [balanceador de carga padrão](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais do Azure, o MSDTC não é suportado no Windows Server 2016 e anteriores porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. Com o Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver lá. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.

## <a name="see-also"></a>Consulte também

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de Cluster de Failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).
