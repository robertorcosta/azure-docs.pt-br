---
title: Configure a recuperação de desastres do SAP NetWeaver com a recuperação do site do Azure
description: Saiba como configurar a recuperação de desastres para o SAP NetWeaver com o Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 29acd1b00d23e4f1c2f241027dadbbb406e5e049
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190788"
---
# <a name="set-up-disaster-recovery-for-a-multi-tier-sap-netweaver-app-deployment"></a>Configurar a recuperação de desastre para uma implantação de aplicativo do SAP NetWeaver de várias camadas

A maioria das implantações do SAP de grande e médio porte usam alguma forma de solução de recuperação de desastre. A importância de se ter soluções de recuperação de desastre sólidas foi aumentando à medida que os processos de negócios mais vitais foram movidos para aplicativos como o SAP. O Azure Site Recovery foi testado e integrado a aplicativos SAP. A Recuperação do Site excede os recursos da maioria das soluções de recuperação de desastres no local e a um custo total de propriedade menor do que as soluções concorrentes.

Com o Site Recovery, você pode:
* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no local por meio da replicação de componentes no Azure.
* Habilite a proteção de aplicativos SAP NetWeaver e não NetWeaver de produção em execução no Azure por meio da replicação de componentes em outro datacenter do Azure.
* Simplifique a migração de nuvem usando o Site Recovery para migrar sua implantação do SAP para o Azure.
* Simplifique os upgrades, testes e prototipagem do projeto SAP criando um clone de produção demanda para testar aplicativos SAP.

Você pode proteger as implantações de aplicativos SAP NetWeaver usando [o Azure Site Recovery](site-recovery-overview.md). Este artigo abrange as práticas recomendadas para proteger uma implantação sap netweaver de três níveis no Azure quando você se replica em outro data center do Azure usando o Site Recovery. O artigo descreve cenários e configurações suportadas e como fazer failovers de teste (exercícios de recuperação de desastres) e failovers reais.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, certifique-se de que você sabe fazer as tarefas a seguir:

* [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md)
* [Criar uma rede de recuperação](site-recovery-azure-to-azure-networking-guidance.md)
* [Executar um failover de teste para o Azure](azure-to-azure-walkthrough-test-failover.md)
* [Executar um failover para o Azure](site-recovery-failover.md)
* [Replicar um controlador de domínio](site-recovery-active-directory.md)
* [Replicar uma instância do SQL Server](site-recovery-sql.md)

## <a name="supported-scenarios"></a>Cenários com suporte

Você pode usar o Azure Site Recovery para implementar uma solução de recuperação de desastre nos seguintes cenários:
* Você tem sistemas SAP em execução em um data center do Azure e está replicando-os em outro data center do Azure (recuperação de desastres Azure-to-Azure). 
   Para saber mais, confira [Arquitetura de replicação do Azure para o Azure](https://aka.ms/asr-a2a-architecture).
* Você tem sistemas SAP em execução em servidores VMware (ou físicos) no local. Você também está replicando os sistemas SAP para um local de recuperação de desastres em um data center do Azure (recuperação de desastres VMware-to-Azure). 
   Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do VMware para o Azure](https://aka.ms/asr-v2a-architecture).
* Você tem sistemas SAP em execução no local Hyper-V. Você também está replicando os sistemas SAP para um local de recuperação de desastres em um data center do Azure (recuperação de desastres Hyper-V-to-Azure).
   Este cenário requer alguns componentes adicionais. Para saber mais, confira [Arquitetura de replicação do Hyper-V para o Azure](https://aka.ms/asr-h2a-architecture).

Neste artigo, usamos um cenário de recuperação **de desastres Azure-to-Azure.** O cenário mostra os recursos de recuperação de desastres SAP da Recuperação do Site. Como a replicação do Site Recovery não é específica do aplicativo, o processo descrito deve também se aplicar a outros cenários.

### <a name="required-foundation-services"></a>Serviços básicos necessários
No cenário discutido neste artigo, os seguintes serviços de base são implantados:
* Gateway de VPN do Azure ou Azure ExpressRoute
* Pelo menos um controlador de domínio do Azure Active Directory e servidor DNS, em execução no Azure

Recomendamos que você estabeleça essa infraestrutura antes de implantar o Site Recovery.

## <a name="reference-sap-application-deployment"></a>Implantação de referência do aplicativo SAP

Esta arquitetura de referência está executando o SAP NetWeaver em um ambiente Windows no Azure com alta disponibilidade. Essa arquitetura é implantada com tamanhos específicos de máquina virtual (VM) que você pode alterar para atender às necessidades da sua organização.

![Diagrama de um padrão de implantação típico do SAP](./media/site-recovery-sap/sap-netweaver_latest.png)

## <a name="disaster-recovery-considerations"></a>Considerações de recuperação de desastres

Para a recuperação de desastres, você deve ser capaz de falhar em uma região secundária. Cada camada usa uma estratégia diferente para fornecer proteção contra recuperação de desastres.

#### <a name="vms-running-sap-web-dispatcher-pools"></a>VMs executando pools SAP Web Dispatcher

O componente Web Dispatcher funciona como um balanceador de carga para tráfego SAP entre os servidores de aplicativos SAP. Para obter alta disponibilidade para o componente Web Dispatcher, o Azure Load Balancer implementa a configuração paralela do Web Dispatcher. O Web Dispatcher usa uma configuração de round-robin para distribuição de tráfego HTTP(S) entre os Web Dispatchers disponíveis no pool de balanceadores.

#### <a name="vms-running-application-servers-pools"></a>VMs executando pools de servidores de aplicativos
A transação SMLG gerencia grupos de login para servidores de aplicativos ABAP. Ele usa a função de balanceamento de carga no servidor de mensagens dos Serviços Centrais para distribuir carga de trabalho entre os pools de servidores de aplicativos SAP para sapguis e tráfego RFC. Você pode replicar este gerenciamento usando a Recuperação do Site.

#### <a name="vms-running-sap-central-services-clusters"></a>VMs executando clusters SAP Central Services
Essa arquitetura de referência executa o Central Services em VMs na camada de aplicativo. O Central Services é um ponto de falha potencial quando em uma única VM. Implantação típica e alta disponibilidade não são requisitos.

Para implementar uma solução de alta disponibilidade, você pode usar um cluster de disco compartilhado ou um cluster de compartilhamento de arquivos. Para configurar VMs para um cluster de disco compartilhado, use Cluster de Failover do Windows Server. Recomendamos que use a testemunha da nuvem como testemunha de quórum.

 > [!NOTE]
 > Como a Recuperação do Site não replica a testemunha de nuvem, recomendamos que você implante a testemunha de nuvem na região de recuperação de desastres.

Para suportar o ambiente de cluster failover, o [SIOS DataKeeper Cluster Edition](https://azuremarketplace.microsoft.com/marketplace/apps/sios_datakeeper.sios-datakeeper-8) faz a função de volume compartilhado do cluster. Na função, o SIOS DataKeeper Cluster replica discos independentes de propriedade dos nós de cluster. Como o Azure não oferece suporte nativo a discos compartilhados, ele requer soluções fornecidas pelo SIOS.

Você também pode lidar com clustering implementando um cluster de compartilhamento de arquivos. Recentemente, a [SAP](https://blogs.sap.com/2018/03/19/migration-from-a-shared-disk-cluster-to-a-file-share-cluster) modificou o padrão de implantação do Central Services para acessar os diretórios globais /sapmnt por meio de um caminho UNC. Nós ainda recomendamos que você certifique-se de que a parte /sapmnt UNC está altamente disponível. Você pode verificar sua instância de Serviços Centrais. Use o Cluster failover do Windows Server com o SOFS (Scale Out File Server) e o recurso Storage Spaces Direct (S2D) no Windows Server 2016.

 > [!NOTE]
 > Atualmente, a Recuperação do Site suporta apenas a replicação de ponto consistente por falhas de máquinas virtuais que usam espaços de armazenamento direto e o nó passivo do SIOS Datakeeper.


## <a name="more-disaster-recovery-considerations"></a>Mais considerações sobre recuperação de desastres

Você pode usar a Recuperação do Site para orquestrar o failover da implantação completa do SAP em regiões do Azure.
A seguir estão as etapas para configurar a recuperação de desastres:

1. Replicar máquinas virtuais
1. Criar uma rede de recuperação
1. Replicar um controlador de domínio
1. Replicar a camada de banco de dados
1. Execute um teste de failover
1. Execute um failover

A seguir está a recomendação para recuperação de desastres de cada camada usada neste exemplo.

 **Camadas do SAP** | **Recomendação**
 --- | ---
**Pool do SAP Web Dispatcher** |  Reproduza usando a recuperação do site 
**Pool de servidores de aplicativo do SAP** |  Reproduza usando a recuperação do site 
**Cluster do SAP Central Services** |  Reproduza usando a recuperação do site 
**Máquinas virtuais do Active Directory** |  Use a replicação do diretório Ativo 
**Servidores de banco de dados SQL** |  Use o SQL Server sempre na replicação

## <a name="replicate-virtual-machines"></a>Replicar máquinas virtuais

Para iniciar a replicação de todas as máquinas virtuais do aplicativo SAP para o datacenter de recuperação de desastre do Azure, siga as orientações em [Replicar uma máquina virtual no Azure](azure-to-azure-walkthrough-enable-replication.md).

* Para obter orientação sobre a proteção do Active Directory e do DNS, saiba [como proteger o Active Directory e o DNS](site-recovery-active-directory.md).

* Para obter orientações sobre como proteger o nível de banco de dados em execução no SQL Server, saiba [como proteger o SQL Server](site-recovery-sql.md).

## <a name="networking-configuration"></a>Configuração de rede

Se você usar um endereço IP estático, poderá especificar o endereço IP que você deseja que a máquina virtual use. Para definir o endereço IP, vá para **Computação e Configurações de** > rede**Cartão de interface de rede**.

![Captura de tela que mostra como definir um endereço IP privado no painel de adaptador de rede do Site Recovery](./media/site-recovery-sap/sap-static-ip.png)


## <a name="create-a-recovery-plan"></a>Criar um plano de recuperação

Um plano de recuperação dá suporte à sequência de várias camadas em um aplicativo de várias camadas durante um failover. O sequenciamento ajuda a manter a consistência do aplicativo. Quando você criar um plano de recuperação para um aplicativo Web de várias camadas, conclua as etapas descritas em [Criar um plano de recuperação usando o Site Recovery](site-recovery-create-recovery-plans.md).

### <a name="add-virtual-machines-to-failover-groups"></a>Adicionar máquinas virtuais aos grupos de failover

1. Crie um plano de recuperação adicionando o servidor de aplicativos, o web dispatcher e os VMs de serviços SAP Central.
1. Selecione **Personalizar** para agrupar as VMs. Por padrão, todas as VMs fazem parte do Grupo 1.

### <a name="add-scripts-to-the-recovery-plan"></a>Adicionar scripts ao plano de recuperação
Para que seus aplicativos funcionem corretamente, talvez seja necessário fazer algumas operações nas máquinas virtuais do Azure. Faça essas operações após o failover ou durante um failover de teste. Você também pode automatizar algumas operações pós-failover. Por exemplo, atualize a entrada De DNS e altere vinculações e conexões adicionando scripts correspondentes ao plano de recuperação.

Você pode implantar os scripts de recuperação de site mais usados em sua conta de automação do Azure, selecionando **Implantar no Azure**. Quando você usar qualquer script publicado, siga a orientação no script.

[![Implantar no Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione um script de pré-ação ao Grupo 1 para falhar sobre o grupo de disponibilidade do SQL Server. Use o script ASR-SQL-FailoverAG publicado nos scripts de amostra. Siga a orientação no script e faça as alterações necessárias no script adequadamente.
1. Adicione um script pós-ação para anexar um balanceador de carga às máquinas virtuais com falha do nível Web (Grupo 1). Use o script ASR-AddSingleLoadBalancer publicado nos scripts de exemplo. Siga a orientação no script e faça as alterações necessárias no script conforme necessário.

![Plano de recuperação do SAP](./media/site-recovery-sap/sap_recovery_plan.png)


## <a name="run-a-test-failover"></a>Execute um teste de failover

1. No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que você criou para aplicativos SAP.
1. Selecione **Failover de Teste**.
1. Para iniciar o processo de failover de teste, selecione o ponto de recuperação e a rede virtual do Azure.
1. Quando o ambiente secundário está ativo, faça validações.
1. Quando as validações estiverem concluídas, limpe o ambiente de failover selecionando **failover de teste de limpeza**.

Para obter mais informações, consulte [Failover de teste para Azure no Site Recovery ](site-recovery-test-failover-to-azure.md).

## <a name="run-a-failover"></a>Executar um failover

1. No Portal do Azure, selecione seu cofre de Serviços de Recuperação.
1. Selecione o plano de recuperação que você criou para aplicativos SAP.
1. Selecione **Failover**.
1. Para iniciar o processo de failover, selecione o ponto de recuperação.

Para obter mais informações, consulte [Failover no Site Recovery](site-recovery-failover.md).

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a construção de uma solução de recuperação de desastres para implantações sap NetWeaver usando o Site Recovery. Consulte o white paper para download [SAP NetWeaver: Construindo uma solução de recuperação de desastres com recuperação de site](https://aka.ms/asr_sap). O white paper discute recomendações para várias arquiteturas SAP. Você pode ver aplicativos suportados e tipos de VM para SAP no Azure. Há também opções de plano para testar sua solução de recuperação de desastres.
* Saiba mais sobre [como replicar outras cargas de trabalho](site-recovery-workload.md) usando o Site Recovery.
