---
title: Configurar a recuperação de desastre do SAP NetWeaver com o Azure Site Recovery
description: Este artigo descreve como configurar a recuperação de desastre para implantações de aplicativos SAP NetWeaver usando o Azure Site Recovery.
author: carmonmills
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: carmonm
ms.openlocfilehash: 3ae9a92a27da1b736bf9db6dff88660f7d40143b
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934454"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurar a recuperação de desastre para uma implantação de aplicativo do SAP NetWeaver de várias camadas

A maioria das implantações do SAP de grande e médio porte usam alguma forma de solução de recuperação de desastre. A importância de se ter soluções de recuperação de desastre sólidas foi aumentando à medida que os processos de negócios mais vitais foram movidos para aplicativos como o SAP. O Azure Site Recovery foi testado e integrado a aplicativos SAP. O Site Recovery excede a capacidade da maioria das soluções de recuperação de desastre locais e em um menor TCO (custo total de propriedade) que as soluções da concorrência.

Com o Site Recovery, você pode:
* **Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no local** por meio da replicação de componentes no Azure.
* **Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no Azure** por meio da replicação de componentes em outro datacenter do Azure.
* **Simplifique a migração na nuvem** usando o Site Recovery para migrar sua implantação do SAP para o Azure.
* **Simplifique as atualizações, teste e criação de protótipos de projeto SAP** criando um clone de produção sob demanda para testar aplicativos SAP.

Este artigo descreve como proteger as implantações de aplicativos SAP NetWeaver usando o [Azure Site Recovery](site-recovery-overview.md). O artigo aborda as melhores práticas para proteger uma implantação do SAP NetWeaver de três camadas no Azure por meio da replicação para outro datacenter do Azure usando o Site Recovery. Ele descreve os cenário e as configurações com suporte, e como executar failovers de teste (simulações de recuperação de desastre) e failovers reais.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar, certifique-se de que você sabe fazer as tarefas a seguir:

* [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Criar uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
* [Executar um failover de teste para o Azure](azure-to-azure-walkthrough-test-failover.md)
* [Executar um failover para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicar o SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários com suporte
Você pode usar o Azure Site Recovery para implementar uma solução de recuperação de desastre nos seguintes cenários:
* Sistemas SAP em execução em um datacenter do Azure que replica para outro datacenter do Azure (recuperação de desastre do Azure para o Azure). Para saber mais, confira [Arquitetura de replicação do Azure para o Azure](https://aka.ms/asr-a2a-architecture).
* Sistemas SAP em execução em servidores VMware (ou físicos) locais que replicam para um local de recuperação de desastre em um datacenter do Azure (recuperação de desastre do VMware para o Azure). Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do VMware para o Azure](https://aka.ms/asr-v2a-architecture).
* Sistemas SAP em execução no Hyper-V localmente que são replicados em um local de recuperação de desastre em um datacenter do Azure (recuperação de desastre do Hyper-V para Azure). Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do Hyper-V para o Azure](https://aka.ms/asr-h2a-architecture).

Neste artigo, usamos um cenário de recuperação de desastre do **Azure para o Azure** a fim de demonstrar os recursos de recuperação de desastre de SAP do Site Recovery. Como a replicação do Site Recovery não é específica do aplicativo, o processo descrito deve também se aplicar a outros cenários.

### <a name="required-foundation-services"></a>Serviços básicos necessários
No cenário discutido neste artigo, os seguintes serviços de base são implantados:
* Gateway de VPN do Azure ou Azure ExpressRoute
* Pelo menos um controlador de domínio do Active Directory e um servidor DNS em execução no Azure

Recomendamos que você estabeleça essa infraestrutura antes de implantar o Site Recovery.

## <a name="reference-sap-application-deployment"></a>Implantação de referência do aplicativo SAP

Essa arquitetura de referência mostra um SAP NetWeaver em execução em um ambiente Windows no Azure com alta disponibilidade.  Essa arquitetura é implantada com tamanhos específicos de VM (máquina virtual) que podem ser alterados para acomodar as necessidades da sua organização.

![Diagrama de um padrão de implantação típico do SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerações de recuperação de desastres

Para DR (recuperação de desastres), você deve ser capaz de fazer failover para uma região secundária. Cada camada usa uma estratégia diferente para fornecer proteção de recuperação de desastres.

#### <a name="vms-running-sap-web-dispatcher-pool"></a>Pool do SAP Web Dispatcher em execução nas VMs 
O componente Web Dispatcher é usado como um balanceador de carga para tráfego da SAP entre os servidores de aplicativos SAP. Para obter alta disponibilidade para o componente Web Dispatcher, o Azure Load Balancer será usado para implementar a configuração paralela do Web Dispatcher em uma configuração de round robin para distribuição de tráfego de HTTP(S) entre os Web Dispatchers disponíveis no pool dos balanceadores. Isso será replicado usando o ASR (Azure Site Recovery) e scripts de automação serão usados para configurar o balanceador de carga na região de recuperação de desastre. 

#### <a name="vms-running-application-servers-pool"></a>VMs que executam o pool de servidores de aplicativos
Para gerenciar grupos de logon para servidores de aplicativos ABAP, é usada a transação SMLG. Ela usa a função de balanceamento de carga no servidor de mensagens do Central Services para distribuir a carga de trabalho entre o pool de servidores de aplicativos SAP para o tráfego de SAPGUIs e RFC. Isso será replicado usando o Azure Site Recovery 

#### <a name="vms-running-sap-central-services-cluster"></a>VMs que executam o cluster do SAP Central Services
Essa arquitetura de referência executa o Central Services em VMs na camada de aplicativo. O Central Services é um SPOF (ponto único de falha) potencial quando implantado em uma única VM — uma implantação típica quando a alta disponibilidade não é um requisito.<br>

Para implementar uma solução de alta disponibilidade, pode-se usar um cluster de disco compartilhado ou um cluster de compartilhamento de arquivos. Para configurar VMs para um cluster de disco compartilhado, use o Cluster de Failover do Windows Server. A Testemunha de Nuvem é recomendada como uma testemunha de quorum. 
 > [!NOTE]
 > O Azure Site Recovery não replica a testemunha de nuvem, portanto, é recomendável implantá-la na região de recuperação de desastre.

Para dar suporte ao ambiente de cluster de failover, a [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) executa a função de volume de cluster compartilhado replicando discos independentes pertencentes a nós de cluster. O Azure não oferece suporte a discos compartilhados e, portanto, requer soluções fornecidas pelo SIOS. 

Outra maneira de lidar com o clustering é implementando um cluster de compartilhamento de arquivos. Recentemente, a [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) modificou o padrão de implantação do Central Services para acessar os diretórios globais /sapmnt por meio de um caminho UNC. No entanto, ainda é recomendável garantir que o compartilhamento UNC /sapmnt seja altamente disponível. Isso pode ser feito na instância do Central Services usando um Cluster de Failover do Windows Server com SOFS (Servidor de Arquivos de Escalabilidade Horizontal) e o recurso S2D (Espaços de Armazenamento Diretos) no Windows Server 2016. 
 > [!NOTE]
 > Atualmente Azure Site Recovery suporte apenas à replicação de ponto consistente de falha de máquinas virtuais usando espaços de armazenamento diretos e o nó passivo do SIOS datakeeper


## <a name="disaster-recovery-considerations"></a>Considerações de recuperação de desastres

Você pode usar o Azure Site Recovery para orquestrar o failover da implantação completa do SAP em regiões do Azure.
Abaixo estão as etapas para configurar a recuperação de desastre 

1. Replicar máquinas virtuais 
2. Criar uma rede de recuperação
3.  Replicar um controlador de domínio
4.  Replicar a camada de banco de dados 
5.  Execute um teste de failover 
6.  Execute um failover 

Abaixo está a recomendação para recuperação de desastre de cada camada usada neste exemplo. 

 **Camadas do SAP** | **Recomendação**
 --- | ---
**Pool do SAP Web Dispatcher** |  Replicar usando o Site Recovery 
**Pool de servidores de aplicativo do SAP** |  Replicar usando o Site Recovery 
**Cluster do SAP Central Services** |  Replicar usando o Site Recovery 
**Máquinas virtuais do Active Directory** |  Replicação do Active Directory 
**Servidores do Banco de Dados SQL** |  SQL sempre em replicação

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para iniciar a replicação de todas as máquinas virtuais do aplicativo SAP para o datacenter de recuperação de desastre do Azure, siga as orientações em [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md).


* Para obter diretrizes sobre como proteger o Active Directory e DNS, consulte o documento [Proteger o Active Directory e DNS](site-recovery-active-directory.md).

* Para obter diretrizes sobre como proteger a camada de banco de dados em execução no SQL Server, consulte o documento [Proteger o SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configuração de rede

Se você usar um endereço IP estático, poderá especificar o endereço IP que você deseja que a máquina virtual use. Para configurar o endereço IP, vá para **Configurações de computação e rede** > **Placa de adaptador de rede**.

![Captura de tela que mostra como definir um endereço IP privado no painel de adaptador de rede do Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="creating-a-recovery-plan"></a>Criar um plano de recuperação
Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="adding-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

1.  Crie um plano de recuperação adicionando o servidor de aplicativos, o dispatcher da Web e as VMs do SAP Central Services.
2.  Clique em 'Personalizar' para agrupar as VMs. Por padrão, todas as VMs são parte do 'Grupo 1'.



### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure após o failover ou durante o teste de failover para que seus aplicativos funcionem corretamente. É possível automatizar algumas operações pós-failover. Por exemplo, você pode atualizar a entrada DNS e alterar associações e conexões adicionando scripts correspondentes ao plano de recuperação.


Você pode implantar os scripts do Azure Site Recovery mais comumente usados em sua conta de automação clicando no botão 'Implantar no Azure' abaixo. Quando você estiver usando qualquer script publicado, verifique se seguiu as diretrizes no script.

[![Implantar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de ação prévia para o 'Grupo 1' para fazer failover do grupo de Disponibilidade do SQL. Use o script 'ASR-SQL-FailoverAG' publicado nos scripts de exemplo. Verifique se você seguiu as diretrizes no script e faça adequadamente as alterações necessárias no script.
2. Adicione um script de ação posterior para anexar um balanceador de carga a máquinas virtuais da camada da Web (Grupo 1) nas quais o failover foi realizado. Use o script 'ASR-AddSingleLoadBalancer' publicado nos scripts de exemplo. Verifique se você seguiu as diretrizes no script e faça adequadamente as alterações necessárias no script.

![Plano de recuperação do SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Execute um teste de failover

1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Failover de Teste**.
4.  Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
5.  Quando o ambiente secundário está ativo, faça validações.
6.  Quando as validações forem concluídas, para limpar o ambiente de failover, selecione **Limpar failover de teste**.

Para obter mais informações, consulte [Failover de teste para Azure no Site Recovery ](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1.  No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
2.  Selecione o plano de recuperação que você criou para aplicativos SAP.
3.  Selecione **Failover**.
4.  Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Próximos passos
* Para saber mais sobre como criar uma solução de recuperação de desastre para implantações do SAP NetWeaver usando o Site Recovery, confira o white paper para download [SAP NetWeaver: criando uma solução de recuperação de desastre com o Azure Site Recovery](https://aka.ms/asr_sap). O white paper aborda as recomendações para várias arquiteturas do SAP, lista de aplicativos com suporte e tipos de VM para SAP no Azure e descreve opções de planos de teste para a solução de recuperação de desastre.
* Saiba mais sobre [como replicar outras cargas de trabalho](site-recovery-workload.md) usando o Site Recovery.
