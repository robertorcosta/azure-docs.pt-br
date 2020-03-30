---
title: Configure a recuperação de desastres para o SQL Server com a recuperação do site do Azure
description: Este artigo descreve como configurar a recuperação de desastres para o SQL Server usando o SQL Server e o Azure Site Recovery.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: sutalasi
ms.openlocfilehash: 429f46156da728bbc24108090eac8c04f68da71c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74084749"
---
# <a name="set-up-disaster-recovery-for-sql-server"></a>Configurar a recuperação de desastres para o SQL Server

Este artigo descreve como ajudar a proteger o back-end do SQL Server de um aplicativo. Você faz isso usando uma combinação de tecnologias de continuidade de negócios do SQL Server e recuperação de desastres (BCDR) e [recuperação de site do Azure](site-recovery-overview.md).

Antes de começar, certifique-se de entender os recursos de recuperação de desastres do SQL Server. Esses recursos incluem:

* Clustering de failover
* Grupos de disponibilidade AlwaysOn
* Espelhamento de banco de dados
* Envio de logs
* Replicação geográfica ativa
* Grupos de failover automático

## <a name="combining-bcdr-technologies-with-site-recovery"></a>Combinando tecnologias BCDR com recuperação de site

A escolha de uma tecnologia BCDR para recuperar as instâncias do SQL Server deve ser baseada nas necessidades de RTO (Recovery Time Objective, objetivo de tempo de recuperação) e rpo (recovery point objective, objetivo de ponto de recuperação) conforme descrito na tabela a seguir. Combine a Recuperação do Site com a operação failover da sua tecnologia escolhida para orquestrar a recuperação de toda a sua aplicação.

Tipo de implantação | Tecnologia BCDR | RTO esperado para sql server | RPO esperado para sql server |
--- | --- | --- | ---
SQL Server em uma infra-estrutura Azure como uma máquina virtual (IaaS) de serviço (IaaS) ou no local.| [Grupo de disponibilidade Sempre Ativo](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017) | O tempo que levou para fazer a réplica secundária como primária. | Como a replicação para a réplica secundária é assíncrona, há alguma perda de dados.
SQL Server em um VM Azure IaaS ou no local.| [Clustering de failover (FCI AlwaysOn)](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server?view=sql-server-2017) | O tempo que levou para falhar entre os nós. | Como o Always On FCI usa armazenamento compartilhado, a mesma exibição da instância de armazenamento está disponível no failover.
SQL Server em um VM Azure IaaS ou no local.| [Espelhamento de banco de dados (modo de alto desempenho)](https://docs.microsoft.com/sql/database-engine/database-mirroring/database-mirroring-sql-server?view=sql-server-2017) | O tempo que é preciso para forçar o serviço, que usa o servidor espelho como um servidor de espera quente. | A replicação é assíncrona. O banco de dados espelho pode ficar um pouco defasado em relação ao banco de dados principal. O lag é tipicamente pequeno. Mas pode se tornar grande se o sistema do servidor principal ou espelho estiver uma carga pesada.<br/><br/>O envio de log pode ser um suplemento para o espelhamento de banco de dados. É uma alternativa favorável ao espelhamento assíncrono do banco de dados.
SQL como plataforma como serviço (PaaS) no Azure.<br/><br/>Esse tipo de implantação inclui piscinas elásticas e servidores do Banco de Dados SQL do Azure. | Replicação geográfica ativa | 30 segundos após o failover ser acionado.<br/><br/>Quando o failover é ativado para um dos bancos de dados secundários, todos os outros secundários são automaticamente ligados ao novo primário. | RPO de cinco segundos.<br/><br/>A geo-replicação ativa usa a tecnologia Always On do SQL Server. Ele replica assíncronamente transações cometidas no banco de dados principal para um banco de dados secundário usando isolamento de instantâneos.<br/><br/>Os dados secundários garantem nunca ter transações parciais.
SQL como PaaS configurado com georeplicação ativa no Azure.<br/><br/>Esse tipo de implantação inclui uma instância gerenciada pelo Banco de Dados SQL, pools elásticos e servidores sql de banco de dados. | Grupos de failover automático | RTO de uma hora. | RPO de cinco segundos.<br/><br/>Grupos de failover automático fornecem a semântica do grupo em cima da geo-replicação ativa. Mas o mesmo mecanismo de replicação assíncrona é usado.
SQL Server em um VM Azure IaaS ou no local.| Replicação com recuperação do site do Azure | RTO é tipicamente menos de 15 minutos. Para saber mais, leia o [SLA RTO fornecido pela Recuperação do Site](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). | Uma hora para consistência de aplicação e cinco minutos para consistência de acidente. Se você está procurando por RPO mais baixo, use outras tecnologias BCDR.

> [!NOTE]
> Algumas considerações importantes quando você está ajudando a proteger as cargas de trabalho SQL com a recuperação do site:
> * Recuperação de site é agnóstico de aplicação. A recuperação do site pode ajudar a proteger qualquer versão do SQL Server que seja implantada em um sistema operacional suportado. Para saber mais, consulte a [matriz de suporte para recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.
> * Você pode optar por usar a Recuperação do Site para qualquer implantação no Azure, Hyper-V, VMware ou infra-estrutura física. Por favor, siga as orientações no final deste artigo sobre [como ajudar a proteger um cluster do SQL Server](#how-to-help-protect-a-sql-server-cluster) com a Recuperação do Site.
> * Certifique-se de que a taxa de alteração de dados observada na máquina está dentro [dos limites de recuperação do site](vmware-physical-azure-support-matrix.md#churn-limits). A taxa de variação é medida em bytes de gravação por segundo. Para máquinas que executam o Windows, você pode visualizar essa taxa de alteração selecionando a guia **Desempenho** no Gerenciador de tarefas. Observe a velocidade de gravação de cada disco.
> * A Recuperação do Site suporta a replicação de instâncias de cluster failover no Storage Spaces Direct. Para saber mais, veja [como ativar a replicação direta do Storage Spaces](azure-to-azure-how-to-enable-replication-s2d-vms.md).

## <a name="disaster-recovery-of-an-application"></a>Recuperação de desastres de um aplicativo

A Recuperação do Site orquestra o failover do teste e o failover de todo o seu aplicativo com a ajuda de planos de recuperação.

Existem alguns pré-requisitos para garantir que seu plano de recuperação seja totalmente personalizado de acordo com sua necessidade. Qualquer implantação do SQL Server normalmente precisa de uma implantação do Active Directory. Ele também precisa de conectividade para o seu nível de aplicação.

### <a name="step-1-set-up-active-directory"></a>Passo 1: Configurar o diretório ativo

Configure o Active Directory no local de recuperação secundário para que o SQL Server seja executado corretamente.

* **Pequena empresa**: Você tem um pequeno número de aplicativos e um único controlador de domínio para o site local. Se você quiser falhar em todo o site, use a replicação do Site Recovery. Este serviço replica o controlador de domínio para o datacenter secundário ou para o Azure.
* **Empresa de médio a grande porte**: Talvez seja necessário configurar controladores de domínio adicionais.
  - Se você tiver um grande número de aplicativos, tiver uma floresta de Diretório Ativo e quiser falhar por aplicativo ou carga de trabalho, configure outro controlador de domínio no datacenter secundário ou no Azure.
  -  Se você estiver usando grupos de disponibilidade Always On para recuperar em um site remoto, configure outro controlador de domínio no site secundário ou no Azure. Este controlador de domínio é usado para a instância do SQL Server recuperado.

As instruções deste artigo supõem que um controlador de domínio está disponível no local secundário. Para saber mais, consulte os procedimentos para [ajudar a proteger o Active Directory com a Recuperação do Site](site-recovery-active-directory.md).

### <a name="step-2-ensure-connectivity-with-other-tiers"></a>Passo 2: Garantir conectividade com outros níveis

Depois que o nível de banco de dados estiver sendo executado na região azure de destino, certifique-se de que você tenha conectividade com os níveis de aplicativo e web. Tome as medidas necessárias com antecedência para validar a conectividade com failover de teste.

Para entender como você pode projetar aplicativos para considerações de conectividade, veja estes exemplos:

* [Projete um aplicativo para recuperação de desastres em nuvem](../sql-database/sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* [Estratégias de recuperação de desastres de piscina selada](../sql-database/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)

### <a name="step-3-interoperate-with-always-on-active-geo-replication-and-auto-failover-groups"></a>Passo 3: Interoperar com grupos always on, geo-replicação ativa e auto-failover

As tecnologias BCDR Always On, active geo-replication e auto-failover groups têm réplicas secundárias do SQL Server em execução na região azure alvo. O primeiro passo para o failover do aplicativo é especificar essa réplica como primária. Esta etapa pressupõe que você já tem um controlador de domínio no secundário. O passo pode não ser necessário se você optar por fazer um failover automático. Falha nos níveis da Web e do aplicativo somente depois que o failover do banco de dados for concluído.

> [!NOTE]
> Se você ajudou a proteger as máquinas SQL com a Recuperação do Site, você só precisa criar um grupo de recuperação dessas máquinas e adicionar seu failover no plano de recuperação.

[Crie um plano de recuperação](site-recovery-create-recovery-plans.md) com máquinas virtuais de nível web e aplicativos. As etapas a seguir mostram como adicionar failover do nível de banco de dados:

1. Importe os scripts para falhar sobre o SQL Availability Group em uma [máquina virtual resource manager](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAG.ps1) e uma máquina virtual [clássica](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/asr-automation-recovery/scripts/ASR-SQL-FailoverAGClassic.ps1). Importe os scripts para sua conta do Azure Automation.

    [![Imagem de um logotipo "Deploy to Azure"](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

1. Adicione o script ASR-SQL-FailoverAG como uma pré-ação do primeiro grupo do plano de recuperação.

1. Siga as instruções disponíveis no script para criar uma variável de automação. Esta variável fornece o nome dos grupos de disponibilidade.

### <a name="step-4-conduct-a-test-failover"></a>Passo 4: Realizar um failover de teste

Algumas tecnologias BCDR, como o SQL Always On, não suportam nativamente failover de teste. Recomendamos a seguinte abordagem *somente ao usar tais tecnologias.*

1. Configure [o Backup do Azure](../backup/backup-azure-arm-vms.md) na VM que hospeda a réplica do grupo de disponibilidade no Azure.

1. Antes de acionar o failover de teste do plano de recuperação, recupere a VM do backup feito na etapa anterior.

    ![Captura de tela mostrando janela para restaurar uma configuração do Azure Backup](./media/site-recovery-sql/restore-from-backup.png)

1. [Forçar um quórum](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/force-a-wsfc-cluster-to-start-without-a-quorum#PowerShellProcedure) na VM que foi restaurado a partir de backup.

1. Atualize o endereço IP do ouvinte para ser um endereço disponível na rede de failover de teste.

    ![Captura de tela da caixa de diálogo de propriedades de janela de regras e endereço IP](./media/site-recovery-sql/update-listener-ip.png)

1. Faça o ouvinte ficar online.

    ![Captura de tela da janela rotulada Content_AG mostrando nomes e status do servidor](./media/site-recovery-sql/bring-listener-online.png)

1. Certifique-se de que o balanceador de carga na rede de failover tenha um endereço IP, do pool de endereços IP front-end correspondente a cada ouvinte do grupo de disponibilidade e com o VM do Servidor SQL no pool de back-end.

     ![Captura de tela da janela intitulada "SQL-AlwaysOn-LB - Frontend IP Pool](./media/site-recovery-sql/create-load-balancer1.png)

    ![Captura de tela da janela intitulada "SQL-AlwaysOn-LB - Backend IP Pool](./media/site-recovery-sql/create-load-balancer2.png)

1. Em grupos de recuperação posteriores, adicione failover do seu nível de aplicativo seguido do seu nível web para este plano de recuperação.

1. Faça um failover de teste do plano de recuperação para testar failover de ponta a ponta do seu aplicativo.

## <a name="steps-to-do-a-failover"></a>Etapas para fazer um failover

Depois de adicionar o script na Etapa 3 e validá-lo na Etapa 4, você pode fazer um failover do plano de recuperação criado na Etapa 3.

As etapas de failover para os níveis de aplicação e web devem ser as mesmas em planos de failover e failover.

## <a name="how-to-help-protect-a-sql-server-cluster"></a>Como ajudar a proteger um cluster do SQL Server

Para um cluster executando a edição Padrão do SQL Server ou o SQL Server 2008 R2, recomendamos que você use a replicação do Site Recovery para ajudar a proteger o SQL Server.

### <a name="azure-to-azure-and-on-premises-to-azure"></a>Azure para Azure e On-premises para Azure

A Recuperação do Site não fornece suporte a clusters de hóspedes ao se replicar em uma região do Azure. A edição Padrão do SQL Server também não fornece uma solução de recuperação de desastres de baixo custo. Neste cenário, recomendamos que você proteja o cluster sql server para uma instância autônoma do SQL Server no local principal e recupere-o no secundário.

1. Configure uma instância adicional do SQL Server independente na região principal do Azure ou no local.

1. Configure a instância para servir como um espelho para os bancos de dados que você deseja ajudar a proteger. Configure o espelhamento no modo de alta segurança.

1. Configure a recuperação do site no site principal para [VMs Azure,](azure-to-azure-tutorial-enable-replication.md) [Hyper-V](site-recovery-hyper-v-site-to-azure.md)ou [VMware e servidores físicos](site-recovery-vmware-to-azure-classic.md).

1. Use a replicação de recuperação de site para replicar a nova instância do SQL Server no site secundário. Como é uma cópia de espelho de alta segurança, ela será sincronizada com o cluster principal, mas replicada usando a replicação do Site Recovery.

   ![Imagem de um cluster padrão que mostra o relacionamento e o fluxo entre um site principal, A Recuperação do Site e o Azure](./media/site-recovery-sql/standalone-cluster-local.png)

### <a name="failback-considerations"></a>Considerações sobre failback

Para clusters Padrão do SQL Server, o failback após um failover não planejado requer um backup e restauração do SQL Server. Esta operação é feita desde a instância do espelho até o cluster original com o restabelecimento do espelho.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="how-does-sql-server-get-licensed-when-used-with-site-recovery"></a>Como o SQL Server é licenciado quando usado com recuperação de site?

A replicação de recuperação de site para SQL Server é coberta pelo benefício de recuperação de desastres da Garantia de Software. Essa cobertura se aplica a todos os cenários de recuperação de sites: no local para recuperação de desastres do Azure e recuperação de desastres transados do Azure IaaS. Consulte [os preços de recuperação do site do Azure](https://azure.microsoft.com/pricing/details/site-recovery/) para obter mais informações.

### <a name="will-site-recovery-support-my-sql-server-version"></a>A recuperação do site suportará minha versão do SQL Server?

Recuperação de site é agnóstico de aplicação. A recuperação do site pode ajudar a proteger qualquer versão do SQL Server que seja implantada em um sistema operacional suportado. Para mais, consulte a [matriz de suporte para recuperação](vmware-physical-azure-support-matrix.md#replicated-machines) de máquinas replicadas.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [a arquitetura de recuperação de sites](site-recovery-components.md).
* Para o SQL Server no Azure, saiba mais sobre [soluções de alta disponibilidade](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#azure-only-high-availability-solutions) para recuperação em uma região Secundária do Azure.
* Para o Banco de Dados SQL, saiba mais sobre a continuidade de [negócios](../sql-database/sql-database-business-continuity.md) e as opções [de alta disponibilidade](../sql-database/sql-database-high-availability.md) para recuperação em uma região Secundária do Azure.
* Para máquinas SQL Server no local, saiba mais sobre as [opções](../virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md#hybrid-it-disaster-recovery-solutions) de alta disponibilidade para recuperação em Máquinas Virtuais Do Azure.
