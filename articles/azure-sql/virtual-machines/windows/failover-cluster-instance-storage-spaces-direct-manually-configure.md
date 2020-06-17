---
title: FCI do SQL Server - máquinas virtuais do Azure | Microsoft Docs
description: Este artigo explica como criar uma instância de cluster de failover do SQL Server em máquinas virtuais do Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 75c25454451b733870f8a674b292cd131454f4d2
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/27/2020
ms.locfileid: "84032307"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Configurar uma instância de cluster de failover do SQL Server em máquinas virtuais do Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Este artigo explica como criar uma FCI (instância de cluster de failover) do SQL Server em máquinas virtuais do Azure no modelo do Azure Resource Manager. Esta solução usa [Espaços de Armazenamento Diretos do Windows Server 2016 Datacenter Edition](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) como uma rede SAN virtual baseada em software que sincroniza o armazenamento (discos de dados) entre os nós (VMs do Azure) em um cluster do Windows. Os Espaços de Armazenamento Diretos eram novos no Windows Server 2016.

O diagrama a seguir mostra a solução completa em máquinas virtuais do Azure:

![A solução completa](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

Este diagrama mostra:

- Duas máquinas virtuais do Azure em um cluster de failover do Windows Server. Quando uma máquina virtual está em um cluster de failover, ela também é chamada de *nó de cluster* ou *nó*.
- Cada máquina virtual tem dois ou mais discos de dados.
- Os Espaços de Armazenamento Diretos sincronizam os dados no disco de dados e apresentam o armazenamento sincronizado como um pool de armazenamento.
- O pool de armazenamento apresenta um CSV (Volume Compartilhado Clusterizado) para o cluster de failover.
- A função de cluster FCI do SQL Server usa o CSV para as unidades de dados.
- Um balanceador de carga do Azure para manter o endereço IP para o FCI do SQL Server.
- Um conjunto de disponibilidade do Azure mantém todos os recursos.

>[!NOTE]
>Todos os recursos do Azure do diagrama estão no mesmo grupo de recursos.

Para obter detalhes sobre os Espaços de Armazenamento Diretos, confira [Espaços de Armazenamento Diretos do Windows Server 2016 Datacenter Edition](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Os Espaços de Armazenamento Diretos são compatíveis com dois tipos de arquiteturas: convergida e hiperconvergida. A arquitetura neste documento é hiperconvergida. Uma infraestrutura hiperconvergida coloca o armazenamento nos mesmos servidores que hospedam o aplicativo em cluster. Nessa arquitetura, o armazenamento está em cada nó de FCI do SQL Server.

## <a name="licensing-and-pricing"></a>Licenciamento e preços

Nas máquinas virtuais do Azure você pode licenciar o SQL Server usando imagens de VM PAYG (pagas conforme o uso) ou BYOL (traga sua própria licença). O tipo de imagem escolhido afeta a maneira como você é cobrado.

Com o licenciamento pago conforme o uso, uma FCI (instância de cluster de failover) do SQL Server em máquinas virtuais do Azure gera cobranças por todos os nós da FCI, incluindo os nós passivos. Para mais informações, consulte [Preços de Máquinas Virtuais do SQL Server Enterprise](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Se você tiver um Contrato Enterprise com o Software Assurance, poderá usar um nó passivo gratuito da FCI para cada nó ativo. Para aproveitar esse benefício no Azure, use imagens de VM BYOL e a mesma licença nos nós ativo e passivo da FCI. Para obter mais informações, consulte [Contrato Enterprise](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Para comparar os licenciamentos pagos conforme o uso e BYOL para SQL Server em máquinas virtuais do Azure, confira [Introdução às VMs do SQL](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-vms).

Para obter informações completas sobre o licenciamento do SQL Server, consulte [Preços](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exemplo de modelo do Azure

Você pode criar essa solução inteira no Azure por meio de um modelo. Um exemplo de um modelo está disponível no GitHub em [Modelos de início rápido do Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Este exemplo não foi criado nem testado para qualquer carga de trabalho específica. Você pode executar o modelo para criar uma FCI do SQL Server com armazenamento de Espaços de Armazenamento Diretos conectados ao seu domínio. Você pode avaliar o modelo e modificá-lo de acordo com a necessidade.

## <a name="before-you-begin"></a>Antes de começar

Há algumas coisas que você precisa saber e implementar antes de começar.

### <a name="what-to-know"></a>O que é preciso saber
Você deve ter uma compreensão operacional das seguintes tecnologias:

- [Tecnologias de cluster do Windows](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Instâncias de Cluster de Failover do SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

Uma coisa a ser observada é que em um cluster de failover convidado de uma VM de IaaS do Azure, recomendamos um adaptador de rede por servidor (nó de cluster) e uma sub-rede. A Rede do Azure tem redundância física, o que torna desnecessários os adaptadores de rede e as sub-redes adicionais em um cluster convidado de uma VM de IaaS do Azure. O relatório de validação de cluster avisará que os nós podem ser acessados apenas em uma rede. Você pode ignorar esse aviso em clusters de failover convidados de VM de IaaS do Azure.

Você também deve ter uma compreensão geral das seguintes tecnologias:

- [Solução hiperconvergida que usa Espaços de Armazenamento Diretos no Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Grupos de recursos do Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> No momento, só há suporte para as instâncias de cluster de failover do SQL Server em máquinas virtuais do Azure no [modo de gerenciamento leve](sql-vm-resource-provider-register.md#management-modes) da [Extensão SQL Server IaaS Agent](sql-server-iaas-agent-extension-automate-management.md). Para alterar do modo de extensão completo para leve, exclua o recurso **Máquina Virtual do SQL** para as VMs correspondentes e registre-as no provedor de recursos de VM do SQL no modo leve. Ao excluir o recurso **Máquina Virtual do SQL** usando o portal do Azure, **desmarque a caixa de seleção ao lado da Máquina Virtual correta**. A extensão completa dá suporte a recursos como backup automatizado, aplicação de patch e gerenciamento avançado do portal. Esses recursos não funcionarão em VMs do SQL depois que o agente for reinstalado no modo de gerenciamento leve.

### <a name="what-to-have"></a>O que é preciso ter

Para realizar as etapas deste artigo, você já deve ter:

- Uma assinatura do Microsoft Azure.
- Um domínio do Windows em máquinas virtuais do Azure.
- Uma conta que tenha permissões para criar objetos em máquinas virtuais do Azure e no Active Directory.
- Uma rede virtual do Azure e uma sub-rede com um espaço suficiente de endereços IP para estes componentes:
   - As duas máquinas virtuais.
   - O endereço IP do cluster de failover.
   - Um endereço IP para cada FCI.
- DNS configurado na rede do Azure, apontando para os controladores de domínio.

Com esses pré-requisitos em vigor, comece a criar seu cluster de failover. A primeira etapa é criar as máquinas virtuais.

## <a name="step-1-create-the-virtual-machines"></a>Etapa 1: Criar as máquinas virtuais

1. Entre no [portal do Azure](https://portal.azure.com) com sua assinatura.

1. [Criar um conjunto de disponibilidade do Azure](../../../virtual-machines/linux/tutorial-availability-sets.md).

   O conjunto de disponibilidade agrupa máquinas virtuais em domínios de falha e domínios de atualização. Isso garante que o aplicativo não seja afetado por pontos únicos de falha, como o comutador de rede ou a unidade de energia de um rack de servidores.

   Se você não criou o grupo de recursos para as máquinas virtuais, faça isso ao criar um conjunto de disponibilidade do Azure. Se estiver usando o portal do Azure para criar o conjunto de disponibilidade, siga estas etapas:

   1. Na portal do Azure, selecione **Criar um recurso** para abrir o Azure Marketplace. Procure **Conjunto de disponibilidade**.
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
   - Em uma sub-rede que tenha espaço de endereços IP suficiente para ambas as máquinas virtuais e todas as FCIs que você possa eventualmente usar nesse cluster.
   - No conjunto de disponibilidade do Azure.

      >[!IMPORTANT]
      >Você não pode definir nem alterar o conjunto de disponibilidade depois de criar uma máquina virtual.

   Escolha uma imagem no Azure Marketplace. Você pode usar uma imagem do Azure Marketplace que inclua o Windows Server e o SQL Server ou usar uma que inclua apenas o Windows Server. Para obter detalhes, confira [Visão geral do SQL Server em máquinas virtuais do Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).

   As imagens oficiais do SQL Server na Galeria do Azure incluem uma instância do SQL Server instalada, o software de instalação do SQL Server e a chave necessária.

   Escolha a imagem correta com base na maneira como deseja pagar a licença do SQL Server:

   - **Licenciamento de pagamento conforme o uso**. O custo por segundo dessas imagens inclui o licenciamento do SQL Server:
      - **SQL Server 2016 Enterprise no Windows Server 2016 Datacenter**
      - **SQL Server 2016 Standard no Windows Server 2016 Datacenter**
      - **SQL Server 2016 Developer no Windows Server 2016 Datacenter**

   - **BYOL (Traga sua própria licença)**

      - **(BYOL) SQL Server 2016 Enterprise no Windows Server 2016 Datacenter**
      - **(BYOL) SQL Server 2016 Standard no Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >Após criar a máquina virtual, remova a instância do SQL Server autônomo pré-instalado. Você usará a mídia pré-instalada do SQL Server para criar a FCI do SQL Server depois de configurar o cluster de failover e os Espaços de Armazenamento Diretos.

   Como alternativa, você pode usar imagens do Azure Marketplace que contenham apenas o sistema operacional. Escolha uma imagem do **Windows Server 2016 Datacenter** e instale a FCI do SQL Server depois de configurar o cluster de failover e os Espaços de Armazenamento Diretos. Esta imagem não contém a mídia de instalação do SQL Server. Coloque a mídia de instalação do SQL Server em uma localização em que possa executá-la para cada servidor.

1. Depois que o Azure criar suas máquinas virtuais, conecte-se a cada uma usando RDP.

   Quando você se conectar pela primeira vez a uma máquina virtual usando o RDP, um aviso perguntará se deseja permitir que o computador seja detectável na rede. Selecione **Sim** na barra superior.

1. Se estiver usando uma das imagens de máquina virtual baseada no SQL Server, remova a instância do SQL Server.

   1. Em **Programas e Recursos**, clique com o botão direito do mouse em **Microsoft SQL Server 2016 (64 bits)** e selecione **Desinstalar/Alterar**.
   1. Selecione **Remover**.
   1. Selecione a instância padrão.
   1. Remova todos os recursos em **Serviços de Mecanismo de Banco de Dados**. Não remova os **Recursos Compartilhados**. Você verá algo como a seguinte captura de tela:

      ![Selecionar Recursos](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/03-remove-features.png)

   1. Selecione **Avançar** e **Remover**.

1. <a name="ports"></a>Abra as portas do firewall.

   Em cada máquina virtual, abra estas portas no Firewall do Windows:

   | Finalidade | Porta TCP | Observações
   | ------ | ------ | ------
   | SQL Server | 1433 | Porta normal para instâncias padrão do SQL Server. Se você tiver usado uma imagem da galeria, essa porta será aberta automaticamente.
   | Investigação de integridade | 59999 | Qualquer porta TCP aberta. Em uma etapa posterior, configure a [investigação de integridade](#probe) do balanceador de carga e o cluster para usar essa porta.  

1. Adicione armazenamento à máquina virtual. Para obter informações detalhadas, confira [adicionar armazenamento](../../../virtual-machines/linux/disks-types.md).

   As duas máquinas virtuais precisam de pelo menos dois discos de dados.

   Anexe discos brutos, não discos com formatação NTFS.
      >[!NOTE]
      >Se você anexar discos com formatação NTFS, poderá habilitar os Espaços de Armazenamento Diretos apenas sem uma verificação de qualificação do disco.  

   Anexe no mínimo dois SSDs premium a cada VM. É recomendável ter pelo menos discos P30 (1 TB).

   Defina o cache de host como **Somente leitura**.

   A capacidade de armazenamento que você usa em ambientes de produção depende de sua carga de trabalho. Os valores descritos neste artigo são para teste e demonstração.

1. [Adicione as máquinas virtuais ao domínio pré-existente](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

Depois de criar e configurar as máquinas virtuais, configure o cluster de failover.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Etapa 2: Configurar o cluster de failover do Windows Server com os Espaços de Armazenamento Diretos

A próxima etapa é configurar o cluster de failover com os Espaços de Armazenamento Diretos. Nesta etapa, você realizará estas subetapas:

1. Adicionar o recurso de clustering de failover do Windows Server.
1. Validar o cluster.
1. Criar o cluster de failover.
1. Criar a testemunha em nuvem.
1. Adicionar armazenamento.

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

Para referências adicionais sobre as próximas etapas, veja as instruções da etapa 3 de [Solução hiperconvergida usando Espaços de Armazenamento Diretos no Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validar o cluster

Valide o cluster na interface do usuário ou usando o PowerShell.

Para validar o cluster usando a interface do usuário, execute as seguintes etapas em uma das máquinas virtuais:

1. Em **Gerenciador do Servidor**, selecione **Ferramentas** e **Gerenciador de Cluster de Failover**.
1. Em **Gerenciador de Cluster de Failover**, selecione **Ação** e **Validar Configuração**.
1. Selecione **Avançar**.
1. Em **Selecionar Servidores ou um Cluster**, insira o nome de ambas as máquinas virtuais.
1. Em **Opções de teste**, selecione **Executar apenas os testes selecionados**. Selecione **Avançar**.
1. Em **Seleção de Teste**, selecione todos os testes, exceto de **Armazenamento**, conforme mostrado aqui:

   ![Selecionar testes de validação de cluster](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Selecione **Avançar**.
1. Em **Confirmação**, selecione **Avançar**.

O Assistente para Validar uma Configuração executa os testes de validação.

Para validar o cluster usando o PowerShell, execute o seguinte script em uma sessão de administrador do PowerShell em uma das máquinas virtuais:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

Depois de validar o cluster, crie o cluster de failover.

### <a name="create-the-failover-cluster"></a>Criar o cluster de failover

Para criar o cluster de failover, você precisa de:
- Os nomes das máquinas virtuais que se tornarão os nós de cluster.
- Um nome para o cluster de failover
- Um endereço IP do cluster de failover. Você pode usar um endereço IP que não seja usado na mesma sub-rede e rede virtual do Azure que os nós de cluster.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 ao Windows Server 2016

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2008 por meio do Windows Server 2016. Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

O script do PowerShell a seguir cria um cluster de failover para o Windows Server 2019. Para obter mais informações, confira: [Cluster de failover: objeto de rede de cluster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Atualize o script com os nomes dos nós (os nomes das máquinas virtuais) e um endereço IP disponível da rede virtual do Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Criar uma testemunha de nuvem

A Testemunha em Nuvem é um novo tipo de testemunha de quorum de cluster armazenada em um Azure Storage Blob. Isso elimina a necessidade de uma VM separada que hospeda um compartilhamento de testemunha.

1. [Crie uma testemunha de nuvem para o cluster de failover](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Criar um contêiner de blob.

1. Salve as chaves de acesso e a URL do contêiner.

1. Configure a testemunha de quorum do cluster de failover. Confira [Configurar a testemunha de quorum na interface do usuário](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Adicionar armazenamento

Os discos para os Espaços de Armazenamento Diretos precisam estar vazios. Eles não podem conter partições nem outros dados. Para limpar os discos, siga [as etapas neste guia](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Habilitar Espaços de Armazenamento Diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   O seguinte script do PowerShell habilita os Espaços de Armazenamento Diretos:  

   ```powershell
   Enable-ClusterS2D
   ```

   No **Gerenciador de Cluster de Failover**, agora você pode ver o pool de armazenamento.

1. [Criar um volume](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Os Espaços de Armazenamento Diretos criam automaticamente um pool de armazenamento quando você os habilita. Agora você está pronto para criar um volume. O cmdlet do PowerShell `New-Volume` automatiza o processo de criação de volume. Esse processo inclui a formatação, a adição do volume ao cluster e a criação de um Volume Compartilhado Clusterizado (CSV). Este exemplo cria um CSV de 800 GB (gigabytes):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Após a conclusão do comando, um volume de 800 GB é montado como um recurso de cluster. O volume está em `C:\ClusterStorage\Volume1\`.

   Esta captura de tela mostra um Volume Compartilhado Clusterizado com Espaços de Armazenamento Diretos:

   ![Volume Compartilhado Clusterizado](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Etapa 3: Testar o failover do cluster de failover

No **Gerenciador de Cluster de Failover**, verifique se você pode mover o recurso de armazenamento para outro nó de cluster. Se conseguir se conectar ao cluster de failover usando o **Gerenciador de Cluster de Failover** e mover o armazenamento de um nó para o outro, você estará pronto para configurar a FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Etapa 4: Criar a FCI do SQL Server

Depois de configurar o cluster de failover e todos os componentes do cluster, incluindo o armazenamento, é possível criar a FCI do SQL Server.

1. Conecte-se à primeira máquina virtual usando o RDP.

1. No **Gerenciador de Cluster de Failover**, verifique se todos os Recursos Principais de Cluster estão na primeira máquina virtual. Se necessário, mova todos os recursos para aquela máquina virtual.

1. Localize a mídia de instalação. Se a máquina virtual usa uma das imagens do Azure Marketplace, a mídia está localizada em `C:\SQLServer_<version number>_Full`. Selecione **instalação**.

1. Na **Central de Instalação do SQL Server**, selecione **Instalação**.

1. Selecione **Nova instalação de cluster de failover do SQL Server**. Siga as instruções no Assistente para instalar o SQL Server FCI.

   Os diretórios de dados de FCI precisam estar no armazenamento de cluster. Com os Espaços de Armazenamento Diretos, o armazenamento não é um disco compartilhado, mas um ponto de montagem para um volume em cada servidor. Os Espaços de Armazenamento Diretos sincronizam o volume entre ambos os nós. O volume é apresentado ao cluster como um Volume Compartilhado Clusterizado. Use o ponto de montagem CSV para os diretórios de dados.

   ![Diretórios de dados](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Ao concluir as instruções do assistente, a configuração instalará uma FCI do SQL Server no primeiro nó.

1. Depois que a Instalação instalar a FCI com êxito no primeiro nó, conecte-se ao segundo nó usando o RDP.

1. Abra a **Central de Instalação do SQL Server**. Selecione **Instalação**.

1. Selecione **Adicionar um nó a um cluster de failover do SQL Server**. Siga as instruções no assistente para instalar o SQL Server e adicionar o servidor à FCI.

   >[!NOTE]
   >Se você usou uma imagem da galeria do Azure Marketplace que contém o SQL Server, as ferramentas do SQL Server foram incluídas com a imagem. Se você não usou uma dessas imagens, instale as ferramentas do SQL Server separadamente. Confira [Baixar o SSMS (SQL Server Management Studio)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Etapa 5: Criar o Azure Load Balancer

Em máquinas virtuais do Azure, os clusters usam um balanceador de carga para manter um endereço IP que precisa estar em um nó de cluster de cada vez. Nessa solução, o balanceador de carga mantém o endereço IP para o FCI do SQL Server.

Para obter mais informações, confira [Criar e configurar um Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Criar o balanceador de carga no portal do Azure

Para criar o balanceador de carga:

1. No portal do Azure, acesse o grupo de recursos que contém as máquinas virtuais.

1. Selecione **Adicionar**. Pesquise **Load Balancer** no Azure Marketplace. Selecione **Load Balancer**.

1. Selecione **Criar**.

1. Configure o balanceador de carga com:

   - **Assinatura**: Sua assinatura do Azure.
   - **Grupo de recursos**: o grupo de recursos que contém as máquinas virtuais.
   - **Name**: um nome que identifica o balanceador de carga.
   - **Região**: a localização do Azure que contém as máquinas virtuais.
   - **Digitar**: público ou privado. Um balanceador de carga privado pode ser acessado na rede virtual. A maioria dos aplicativos do Azure pode usar um balanceador de carga privado. Se o aplicativo precisar de acesso ao SQL Server diretamente pela Internet, use um balanceador de carga público.
   - **SKU**: Standard.
   - **Rede virtual**: a mesma rede que a das máquinas virtuais.
   - **Atribuição de endereço IP**: estático. 
   - **Endereço IP privado**: o endereço IP atribuído ao recurso de rede de cluster da FCI do SQL Server.

 A seguinte captura de tela mostra a interface do usuário **Criar balanceador de carga**:

   ![Configurar o balanceador de carga](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Configurar o pool de back-end do balanceador de carga

1. Retorne ao grupo de recursos do Azure que contém as máquinas virtuais e localize o novo balanceador de carga. Talvez você precise atualizar a exibição no grupo de recursos. Escolha o balanceador de carga.

1. Selecione **Pools de back-end** e **Adicionar**.

1. Associe o pool de back-end com o conjunto de disponibilidade que contém as VMs.

1. Em **Configurações de IP de rede de destino**, selecione **MÁQUINA VIRTUAL** e escolha as máquinas virtuais que participarão como nós de cluster. Certifique-se de incluir todas as máquinas virtuais que hospedarão FCI.

1. Selecione **OK** para criar o pool de back-end.

### <a name="configure-a-load-balancer-health-probe"></a>Configurar um teste de integridade do balanceador de carga

1. Na folha do balanceador de carga, selecione **Investigações de integridade**.

1. Selecione **Adicionar**.

1. Na folha **Adicionar investigação de integridade**, <a name="probe"></a>defina os parâmetros da investigação de integridade.

   - **Name**: um nome para a investigação de integridade.
   - **Protocolo**: TCP.
   - **Porta**: Defina para a porta que você criou no firewall para a investigação de integridade [nesta etapa](#ports). Neste artigo, o exemplo usa a porta TCP `59999`.
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
   - **IP flutuante (retorno de servidor direto)** : Habilitado.

1. Selecione **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Etapa 6: Configurar o cluster para a investigação

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

## <a name="step-7-test-fci-failover"></a>Etapa 7: Testar o failover da FCI

Teste o failover de FCI para validar a funcionalidade do cluster. Siga estas etapas:

1. Conecte-se a um dos nós de cluster da FCI do SQL Server usando o RDP.

1. Abra o **Gerenciador de Cluster de Failover**. Selecione **funções**. Observe qual nó possui a função FCI do SQL Server.

1. Clique com o botão direito do mouse na função FCI do SQL Server.

1. Selecione **Mover** e **O Melhor Nó Possível**.

O **Gerenciador de Cluster de Failover** mostra a função e seus recursos ficam offline. Os recursos são movidos e ficam online no outro nó.

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

[Configurar Espaços de Armazenamento Diretos com a Área de Trabalho Remota (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Solução hiperconvergida com Espaços de Armazenamento Diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Visão geral de Espaços de Armazenamento Diretos](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[Compatibilidade do SQL Server com Espaços de Armazenamento Diretos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
