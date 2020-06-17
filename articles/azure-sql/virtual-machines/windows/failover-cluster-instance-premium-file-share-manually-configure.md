---
title: FCI do SQL Server com compartilhamento de arquivo premium – Máquinas Virtuais do Azure
description: Este artigo explica como criar uma instância de cluster de failover do SQL Server usando um compartilhamento de arquivo premium em máquinas virtuais do Azure.
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
ms.openlocfilehash: 60526dbeb3e221e6a2e4c6b900ff3a109d4cdf8f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032417"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Configurar uma instância de cluster de failover do SQL Server com um compartilhamento de arquivo premium em máquinas virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma FCI (instância de cluster de failover) do SQL Server em máquinas virtuais do Azure usando um [compartilhamento de arquivo premium](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Os compartilhamentos de arquivo premium são compartilhamentos de arquivo consistentemente de baixa latência com suporte de SSD que têm suporte total para uso com Instâncias de Cluster de Failover para o SQL Server 2012 ou posterior no Windows Server 2012 ou posterior. Os compartilhamentos de arquivo premium proporcionam maior flexibilidade, permitindo que você redimensione e escale um compartilhamento de arquivo sem nenhum tempo de inatividade.


## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e implementar antes de começar.

Você deve ter uma compreensão operacional das seguintes tecnologias:

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de Cluster de Failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ser observada é que em um cluster de failover da VM IaaS do Azure, recomendamos uma só NIC por servidor (nó de cluster) e uma só sub-rede. A Rede do Azure tem redundância física, o que tornam desnecessárias as NICs e as sub-redes adicionais em um cluster convidado de uma VM IaaS do Azure. O relatório de validação de cluster avisará que os nós podem ser acessados apenas em uma rede. Ignore esse aviso em clusters de failover da VM IaaS do Azure.

Você também deve ter uma compreensão geral das seguintes tecnologias:

- [Compartilhamento de arquivo premium do Azure](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Grupos de recursos do Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> No momento, só há suporte para as instâncias de cluster de failover do SQL Server em máquinas virtuais do Azure no [modo de gerenciamento leve](sql-vm-resource-provider-register.md#management-modes) da [Extensão SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Para alterar do modo de extensão completo para leve, exclua o recurso **Máquina Virtual do SQL** para as VMs correspondentes e registre-as no provedor de recursos de VM do SQL no modo leve. Ao excluir o recurso **Máquina Virtual do SQL** usando o portal do Azure, **desmarque a caixa de seleção ao lado da Máquina Virtual correta**. A extensão completa dá suporte a recursos como backup automatizado, aplicação de patch e gerenciamento avançado do portal. Esses recursos não funcionarão em VMs do SQL depois que o agente for reinstalado no modo de gerenciamento leve.

Os compartilhamentos de arquivo premium fornecem capacidades de taxa de transferência e IOPS e que atenderão às necessidades de muitas cargas de trabalho. Para cargas de trabalho com uso intensivo de E/S, considere o uso das [Instâncias de Cluster de Failover do SQL Server com os Espaços de Armazenamento Diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md), com base em discos premium gerenciados ou discos ultra.  

Verifique a atividade de IOPS do seu ambiente e confirme se os compartilhamentos de arquivo premium fornecerão o IOPS de que você precisa antes de iniciar uma implantação ou uma migração. Use os contadores de disco do Monitor de Desempenho do Windows para monitorar o IOPS total (Transferências de Disco/segundo) e a taxa de transferência (Bytes de Disco/segundo) necessários para os arquivos de Dados, Log e BD Temporário do SQL Server.

Muitas cargas de trabalho têm E/S intermitente, sendo, portanto, uma boa ideia verificar isso durante períodos de uso intensivo e observar o IOPS máximo e médio. Os compartilhamentos de arquivo premium fornecem IOPS com base no tamanho do compartilhamento. Os compartilhamentos de arquivo premium também fornecem intermitências gratuitas que permitem triplicar a quantidade de linha de base da intermitência da E/S para até uma hora.

Para obter mais informações sobre o desempenho de compartilhamento de arquivo premium, confira [Níveis de desempenho do compartilhamento de arquivo](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licenciamento e preços

Nas máquinas virtuais do Azure, você pode licenciar o SQL Server usando imagens de VM PAYG (pagamento conforme o uso) ou BYOL (traga sua própria licença). O tipo de imagem escolhido afeta a maneira como você é cobrado.

Com o licenciamento pago conforme o uso, uma FCI (instância de cluster de failover) do SQL Server em máquinas virtuais do Azure gera cobranças por todos os nós da FCI, incluindo os nós passivos. Para mais informações, consulte [Preços de Máquinas Virtuais do SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se você tiver um Contrato Enterprise com o Software Assurance, poderá usar um nó passivo gratuito da FCI para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens de VM BYOL e a mesma licença nos nós ativo e passivo da FCI. Para obter mais informações, consulte [Contrato Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar os licenciamentos pagos conforme o uso e BYOL para SQL Server em máquinas virtuais do Azure, confira [Introdução às VMs do SQL](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [Preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>Fluxo de arquivos

Não há suporte para o fluxo de arquivos em um cluster de failover com um compartilhamento de arquivo premium. Para usar o fluxo de arquivos, implante o cluster usando os [Espaços de Armazenamento Diretos](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

## <a name="prerequisites"></a>Pré-requisitos

Para realizar as etapas deste artigo, você já deve ter:

- Uma assinatura do Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta de usuário de domínio que tenha permissões para criar objetos em máquinas virtuais do Azure e no Active Directory.
- Uma conta de usuário de domínio para executar o serviço SQL Server e com a qual você possa fazer logon na máquina virtual ao montar o compartilhamento de arquivo.  
- Uma rede virtual do Azure e uma sub-rede com um espaço suficiente de endereços IP para estes componentes:
   - Duas máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede do Azure, apontando para os controladores de domínio.
- Um [compartilhamento de arquivo premium](../../../storage/files/storage-how-to-create-premium-fileshare.md) a ser usado como a unidade clusterizada, com base na cota de armazenamento do seu banco de dados para os arquivos de dados.
- Se você estiver usando o Windows Server 2012 R2 e mais antigo, precisará de outro compartilhamento de arquivo para usar como a testemunha de compartilhamento de arquivo, pois há suporte para testemunhas de nuvem no Windows 2016 e mais recente. Use outro compartilhamento de arquivo do Azure ou um compartilhamento de arquivo em uma máquina virtual separada. Se você pretende usar outro compartilhamento de arquivo do Azure, monte-o com o mesmo processo do compartilhamento de arquivo premium usado para a unidade clusterizada. 

Com esses pré-requisitos em vigor, comece a criar seu cluster de failover. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-the-virtual-machines"></a>Etapa 1: Criar as máquinas virtuais

1. Entre no [portal do Azure](https://portal.azure.com) com sua assinatura.

1. [Criar um conjunto de disponibilidade do Azure](../../../virtual-machines/linux/tutorial-availability-sets.md).

   O conjunto de disponibilidade agrupa máquinas virtuais em domínios de falha e domínios de atualização. Ele verifica se o aplicativo não é afetado por pontos únicos de falha, como o comutador de rede ou a unidade de energia de um rack de servidores.

   Se você não criou o grupo de recursos para as máquinas virtuais, faça isso ao criar um conjunto de disponibilidade do Azure. Se estiver usando o portal do Azure para criar o conjunto de disponibilidade, siga estas etapas:

   1. No portal do Azure, selecione **Criar um recurso** para abrir o Azure Marketplace. Procure **Conjunto de disponibilidade**.
   1. Selecione **Conjunto de Disponibilidade**.
   1. Selecione **Criar**.
   1. Em **Criar conjunto de disponibilidade**, forneça estes valores:
      - **Name**: Um nome para o conjunto de disponibilidade.
      - **Assinatura**: Sua assinatura do Azure.
      - **Grupo de recursos**: caso deseje usar um grupo existente, clique em **Selecionar existente** e escolha o grupo na lista. Caso contrário, selecione **Criar** e insira um nome para o grupo.
      - **Localização**: Defina a localização em que você planeja criar as máquinas virtuais.
      - **Domínios de falha**: use o padrão (**3**).
      - **Domínios de atualização**: use o padrão (**5**).
   1. Selecione **Criar** para criar o conjunto de disponibilidade.

1. Crie as máquinas virtuais no conjunto de disponibilidade.

   Provisione duas máquinas virtuais do SQL Server no conjunto de disponibilidade do Azure. Para obter instruções, confira [Provisionar uma máquina virtual do SQL Server no portal do Azure](create-sql-vm-portal.md).

   Coloque as duas máquinas virtuais:

   - No mesmo grupo de recursos do Azure que o conjunto de disponibilidade.
   - Na mesma rede que o controlador de domínio.
   - Em uma sub-rede com um espaço suficiente de endereços IP para ambas as máquinas virtuais e todas as FCIs que você pode vir a usar nesse cluster.
   - No conjunto de disponibilidade do Azure.

      >[!IMPORTANT]
      >Você não pode definir nem alterar o conjunto de disponibilidade depois de criar uma máquina virtual.

   Escolha uma imagem no Azure Marketplace. Você pode usar uma imagem do Azure Marketplace que inclua o Windows Server e o SQL Server ou usar uma que inclua apenas o Windows Server. Para obter detalhes, confira [Visão geral do SQL Server em máquinas virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

   As imagens oficiais do SQL Server na Galeria do Azure incluem uma instância do SQL Server instalada, o software de instalação do SQL Server e a chave necessária.

   >[!IMPORTANT]
   > Após criar a máquina virtual, remova a instância do SQL Server autônomo pré-instalado. Você usará a mídia pré-instalada do SQL Server para criar a FCI do SQL Server depois de configurar o cluster de failover e o compartilhamento de arquivo premium como o armazenamento.

   Como alternativa, você pode usar imagens do Azure Marketplace que contenham apenas o sistema operacional. Escolha uma imagem do **Windows Server 2016 Datacenter** e instale a FCI do SQL Server depois de configurar o cluster de failover e o compartilhamento de arquivo premium como o armazenamento. Esta imagem não contém a mídia de instalação do SQL Server. Coloque a mídia de instalação do SQL Server em uma localização em que possa executá-la para cada servidor.

1. Depois que o Azure criar suas máquinas virtuais, conecte-se a cada uma usando RDP.

   Quando você se conectar pela primeira vez a uma máquina virtual usando o RDP, um aviso perguntará se deseja permitir que o computador seja detectável na rede. Selecione **Sim** na barra superior.

1. Se estiver usando uma das imagens de máquina virtual baseada no SQL Server, remova a instância do SQL Server.

   1. Em **Programas e Recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 201_ (64 bits)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todos os recursos em **Serviços de Mecanismo de Banco de Dados**. Não remova os **Recursos Compartilhados**. Você verá algo como a seguinte captura de tela:

        ![Selecionar Recursos](./media/failover-cluster-instance-premium-file-share-manually-configure/03-remove-features.png)

   1. Selecione **Avançar** e **Remover**.

1. <span id="ports"> </span> Abra as portas do firewall.  

   Em cada máquina virtual, abra estas portas no Firewall do Windows:

   | Finalidade | Porta TCP | Observações
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se você tiver usado uma imagem da galeria, essa porta será aberta automaticamente.
   | Investigação de integridade | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.
   | Compartilhamento de arquivo | 445 | Porta usada pelo serviço de compartilhamento de arquivo.

1. [Adicione as máquinas virtuais ao domínio pré-existente](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, configure o compartilhamento de arquivo premium.

## <a name="step-2-mount-the-premium-file-share"></a>Etapa 2: Montar o compartilhamento de arquivo premium

1. Entre no [portal do Azure](https://portal.azure.com) e acesse sua conta de armazenamento.
1. Acesse **Compartilhamentos de Arquivo** em **Serviço de arquivo** e selecione o compartilhamento de arquivo premium que deseja usar para o armazenamento do SQL.
1. Selecione **Conectar** para abrir a cadeia de conexão do compartilhamento de arquivo.
1. Selecione a letra da unidade que deseja usar na lista suspensa e copie os dois blocos de código para o Bloco de notas.


   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/premium-file-storage-commands.png" alt-text="Copiar os dois comandos do PowerShell do portal de conexão do compartilhamento de arquivo":::

1. Use o RDP para se conectar à VM do SQL Server com a conta que a FCI do SQL Server usará para a conta de serviço.
1. Abra um console de comando do PowerShell administrativo.
1. Execute os comandos que você salvou anteriormente enquanto trabalhava no portal.
1. Acesse o compartilhamento usando o Explorador de Arquivos ou a caixa de diálogo **Executar** (tecla do logotipo do Windows + r). Use o caminho de rede `\\storageaccountname.file.core.windows.net\filesharename`. Por exemplo, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Crie, pelo menos, uma pasta no compartilhamento de arquivo recém-conectado na qual os arquivos de Dados do SQL serão colocados.
1. Repita essas etapas em cada VM do SQL Server que participará do cluster.

  > [!IMPORTANT]
  > - Considere o uso de um compartilhamento de arquivo separado para arquivos de backup a fim de economizar a capacidade de IOPS e espaço desse compartilhamento para arquivos de Dados e de Log. Use um compartilhamento de arquivo premium ou Standard para arquivos de backup.
  > - Se estiver usando o Windows 2012 R2 e mais antigo, siga estas mesmas etapas para montar o compartilhamento de arquivo que pretende usar como a testemunha do compartilhamento de arquivo. 

## <a name="step-3-configure-the-failover-cluster"></a>Etapa 3: Configurar o cluster de failover

A próxima etapa será configurar o cluster de failover. Nesta etapa, você concluirá as seguintes subetapas:

1. Adicionar o recurso de clustering de failover do Windows Server.
1. Validar o cluster.
1. Criar o cluster de failover.
1. Criar a testemunha de nuvem (para o Windows Server 2016 e mais recente) ou a testemunha do compartilhamento de arquivo (para o Windows Server 2012 R2 e mais antigo).


### <a name="add-windows-server-failover-clustering"></a>Adicionar clustering de failover do Windows Server

1. Conecte-se à primeira máquina virtual com o RDP usando uma conta de domínio que seja membro dos administradores locais e tenha permissões para criar objetos no Active Directory. Use essa conta para o restante da configuração.

1. [Adicionar clustering de failover a cada máquina virtual](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

   Para instalar o clustering de failover por meio da interface do usuário, execute estas etapas em ambas as máquinas virtuais:
   1. No **Gerenciador do Servidor**, selecione **Gerenciar** e **Adicionar Funções e Recursos**.
   1. No **Assistente para Adicionar Funções e Recursos**, selecione **Avançar** até chegar a **Selecionar Recursos**.
   1. Em **Selecionar Recursos**, escolha **Clustering de Failover**. Inclua todos os recursos e as ferramentas de gerenciamento. Selecione **Adicionar Recursos**.
   1. Selecione **Avançar** e **Concluir** para instalar os recursos.

   Para instalar o clustering de failover usando o PowerShell, execute o seguinte script em uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na interface do usuário ou usando o PowerShell.

Para validar o cluster usando a interface do usuário, execute as seguintes etapas em uma das máquinas virtuais:

1. Em **Gerenciador do Servidor**, selecione **Ferramentas** e **Gerenciador de Cluster de Failover**.
1. Em **Gerenciador de Cluster de Failover**, selecione **Ação** e **Validar Configuração**.
1. Selecione **Avançar**.
1. Em **Selecionar Servidores ou um Cluster**, insira o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, selecione **Executar apenas os testes selecionados**. Selecione **Avançar**.
1. Em **Seleção de Teste**, selecione todos os testes, exceto **Armazenamento** e **Espaços de Armazenamento Diretos**, conforme mostrado aqui:

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/cluster-validation.png" alt-text="Selecionar testes de validação de cluster":::

1. Selecione **Avançar**.
1. Em **Confirmação**, selecione **Avançar**.

O **Assistente para Validar uma Configuração** executa os testes de validação.

Para validar o cluster usando o PowerShell, execute o seguinte script em uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornarão os nós de cluster.
- Um nome para o cluster de failover
- Um endereço IP do cluster de failover. Você pode usar um endereço IP que não seja usado na mesma sub-rede e rede virtual do Azure que os nós de cluster.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 ao Windows Server 2016

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2012 por meio do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2019. Para obter mais informações, confira: [Cluster de failover: objeto de rede de cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Criar uma testemunha de nuvem (Win 2016 e posterior)

Se estiver usando o Windows Server 2016 e superior, precisará criar uma testemunha de nuvem. A Testemunha em Nuvem é um novo tipo de testemunha de quorum de cluster armazenada em um Azure Storage Blob. Isso elimina a necessidade de uma VM separada que hospeda um compartilhamento de testemunha ou o uso de um compartilhamento de arquivo separado.

1. [Crie uma testemunha de nuvem para o cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Criar um contêiner de blob.

1. Salve as chaves de acesso e a URL do contêiner.

### <a name="configure-quorum"></a>Configurar o quorum 

Para o Windows Server 2016 e superior, configure o cluster para usar a testemunha de nuvem recém-criada. Siga todas as etapas em [Configurar a testemunha de quorum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

Para o Windows Server 2012 R2 e mais antigo, siga as mesmas etapas em [Configurar a testemunha de quorum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) mas, na página **Selecionar Testemunha de Quorum**, escolha a opção **Configurar uma testemunha de compartilhamento de arquivo**. Especifique o compartilhamento de arquivo alocado para ser a testemunha de compartilhamento de arquivo, seja ele configurado em uma máquina virtual separada ou montado no Azure. 


## <a name="step-4-test-cluster-failover"></a>Etapa 4: Testar o failover de cluster

Teste o failover do cluster. Em **Gerenciador de Cluster de Failover**, clique com o botão direito do mouse no cluster e selecione **Mais Ações** > **Mover Recurso Principal de Cluster** > **Selecionar nó** e escolha o outro nó do cluster. Mova o recurso principal de cluster para cada nó do cluster e mova-o novamente para o nó primário. Se você puder mover o cluster para cada nó com êxito, estará pronto para instalar o SQL Server.  

:::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/test-cluster-failover.png" alt-text="Testar o failover de cluster movendo o recurso principal para os outros nós":::

## <a name="step-5-create-the-sql-server-fci"></a>Etapa 5: Criar a FCI do SQL Server

Depois de configurar o cluster de failover, crie a FCI do SQL Server.

1. Conecte-se à primeira máquina virtual usando o RDP.

1. No **Gerenciador de Cluster de Failover**, verifique se todos os Recursos Principais de Cluster estão na primeira máquina virtual. Se necessário, mova todos os recursos para essa máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. Selecione **instalação**.

1. Na **Central de Instalação do SQL Server**, selecione **Instalação**.

1. Selecione **Nova instalação de cluster de failover do SQL Server**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados da FCI precisam estar no compartilhamento de arquivo premium. Insira o caminho completo do compartilhamento neste formato: `\\storageaccountname.file.core.windows.net\filesharename\foldername`. Um aviso será exibido, informando que você especificou um servidor de arquivos como o diretório de dados. Esse aviso é esperado. Verifique se a conta de usuário com a qual você entrou no RDP com a VM quando persistiu o compartilhamento de arquivo é a mesma conta usada pelo serviço SQL Server para evitar possíveis falhas.

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/use-file-share-as-data-directories.png" alt-text="Usar o compartilhamento de arquivo como diretórios de dados do SQL":::

1. Depois que você concluir as etapas no assistente, a Instalação instalará uma FCI do SQL Server no primeiro nó.

1. Depois que a Instalação instalar a FCI com êxito no primeiro nó, conecte-se ao segundo nó usando o RDP.

1. Abra a **Central de Instalação do SQL Server**. Selecione **Instalação**.

1. Selecione **Adicionar um nó a um cluster de failover do SQL Server**. Siga as instruções no assistente para instalar o SQL Server e adicionar o servidor à FCI.

   >[!NOTE]
   >Se você usou uma imagem da Galeria do Azure Marketplace com o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se você não usou uma dessas imagens, instale as ferramentas do SQL Server separadamente. Confira [Baixar o SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Etapa 6: Criar o Azure Load Balancer

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster de cada vez. Nessa solução, o balanceador de carga mantém o endereço IP para o FCI do SQL Server.

Para obter mais informações, confira [Criar e configurar um Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. No portal do Azure, acesse o grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Pesquise **Load Balancer** no Azure Marketplace. Selecione **Load Balancer**.

1. Selecione **Criar**.

1. Configure o balanceador de carga usando os seguintes valores:

   - **Assinatura**: Sua assinatura do Azure.
   - **Grupo de recursos**: o grupo de recursos que contém as máquinas virtuais.
   - **Name**: um nome que identifica o balanceador de carga.
   - **Região**: a localização do Azure que contém as máquinas virtuais.
   - **Digitar**: público ou privado. Um balanceador de carga privado pode ser acessado na rede virtual. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se o aplicativo precisar de acesso ao SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **SKU**: Standard.
   - **Rede virtual**: a mesma rede que a das máquinas virtuais.
   - **Atribuição de endereço IP**: estático. 
   - **Endereço IP privado**: o endereço IP atribuído ao recurso de rede de cluster da FCI do SQL Server.

   A seguinte imagem mostra a interface do usuário de **Criar balanceador de carga**:

   ![Configurar o balanceador de carga](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao grupo de recursos do Azure que contém as máquinas virtuais e localize o novo balanceador de carga. Talvez você precise atualizar a exibição no grupo de recursos. Escolha o balanceador de carga.

1. Selecione **Pools de back-end** e **Adicionar**.

1. Associe o pool de back-end com o conjunto de disponibilidade que contém as VMs.

1. Em **Configurações de IP de rede de destino**, selecione **MÁQUINA VIRTUAL** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão FCI.

1. Selecione **OK** para criar o pool de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar um teste de integridade do balanceador de carga

1. Na folha do balanceador de carga, selecione **Investigações de integridade**.

1. Selecione **Adicionar**.

1. Na folha **Adicionar investigação de integridade**, <span id="probe"> </span> defina os parâmetros de investigação de integridade a seguir.

   - **Name**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: a porta que você criou no firewall para a investigação de integridade [nesta etapa](#ports). Neste artigo, o exemplo usa a porta TCP `59999`.
   - **Intervalo**: 5 segundos.
   - **Limite não íntegro**: duas falhas consecutivas.

1. Selecione **OK**.

### <a name="set-load-balancing-rules"></a>Definir regras de balanceamento de carga

1. Na folha do balanceador de carga, selecione **Regras de balanceamento de carga**.

1. Selecione **Adicionar**.

1. Defina os parâmetros da regra de balanceamento de carga:

   - **Name**: um nome para a regras de balanceamento de carga.
   - **Endereço IP de front-end**: o endereço IP do recurso de rede de cluster da FCI do SQL Server.
   - **Porta**: a porta TCP da FCI do SQL Server. A porta de instância padrão é 1433.
   - **Porta de back-end**: usa a mesma porta que o valor da **Porta** quando você habilita **IP Flutuante (retorno de servidor direto)** .
   - **Pool de back-end**: o nome do pool de back-end configurado anteriormente.
   - **Investigação de integridade**: a investigação de integridade configurada anteriormente.
   - **Persistência de sessão**: Nenhum.
   - **Tempo limite de ociosidade (minutos)** : 4.
   - **IP flutuante (retorno de servidor direto)** : habilitado.

1. Selecione **OK**.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Etapa 7: Configurar o cluster para a investigação

Defina o parâmetro de porta de investigação de cluster no PowerShell.

Para definir o parâmetro de porta de investigação do cluster, atualize as variáveis no script a seguir com valores do seu ambiente. Remova os colchetes angulares (`<` e `>`) do script.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

A seguinte lista descreve os valores que você precisará atualizar:

   - `<Cluster Network Name>`: o nome do cluster de failover do Windows Server para a rede. Em **Gerenciador de Cluster de Failover** > **Redes**, clique com o botão direito do mouse na rede e selecione **Propriedades**. O valor correto está em **Nome**, na guia **Geral**.

   - `<SQL Server FCI IP Address Resource Name>`: o nome do recurso de endereço IP da FCI do SQL Server. Em **Gerenciador de Cluster de Failover** > **Funções**, na função FCI do SQL Server, em **Nome do Servidor**, clique com o botão direito do mouse no recurso de endereço IP e selecione **Propriedades**. O valor correto está em **Nome**, na guia **Geral**.

   - `<ILBIP>`: o endereço IP do ILB. Esse endereço é configurado no Portal do Azure como o endereço front-end do ILB. Esse também é o endereço IP de FCI do SQL Server. Você pode localizá-lo no **Gerenciador de Cluster de Failover**, na mesma página de propriedades em que você localizou o `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: a porta de investigação configurada na investigação de integridade do balanceador de carga. Qualquer porta TCP não usada é válida.

>[!IMPORTANT]
>A máscara de sub-rede para o parâmetro de cluster deve ser o endereço de difusão de TCP IP: `255.255.255.255`.

Depois de configurar a investigação de cluster, você poderá ver todos os parâmetros do cluster no PowerShell. Execute este script:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>Etapa 8: Testar o failover da FCI

Teste o failover de FCI para validar a funcionalidade do cluster. Siga estas etapas:

1. Conecte-se a um dos nós de cluster da FCI do SQL Server usando o RDP.

1. Abra o **Gerenciador de Cluster de Failover**. Selecione **funções**. Observe qual nó possui a função FCI do SQL Server.

1. Clique com o botão direito do mouse na função FCI do SQL Server.

1. Selecione **Mover** e **O Melhor Nó Possível**.

O **Gerenciador de Cluster de Failover** mostra a função e seus recursos ficam offline. Os recursos então são movidos e ficam online novamente no outro nó.

### <a name="test-connectivity"></a>Testar a conectividade

Para testar a conectividade, entre em outra máquina virtual na mesma rede virtual. Abra o **SQL Server Management Studio** e conecte-se ao nome de FCI do SQL Server.

>[!NOTE]
>Se necessário, [baixe o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Limitações

As máquinas virtuais do Azure são compatíveis com o MSDTC (Coordenador de Transações Distribuídas da Microsoft) no Windows Server 2019 com armazenamento em CSVs (Volumes Compartilhados Clusterizados) e um [Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md).

Em máquinas virtuais do Azure, o MSDTC não compatível com o Windows Server 2016 ou versões anteriores porque:

- O recurso MSDTC clusterizado não pode ser configurado para usar o armazenamento compartilhado. No Windows Server 2016, se você criar um recurso MSDTC, ele não mostrará nenhum armazenamento compartilhado disponível para uso, mesmo se o armazenamento estiver disponível. Esse problema foi corrigido no Windows Server 2019.
- O balanceador de carga básico não lida com portas RPC.

## <a name="see-also"></a>Confira também

- [Tecnologias de cluster do Windows](/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de Cluster de Failover do SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
