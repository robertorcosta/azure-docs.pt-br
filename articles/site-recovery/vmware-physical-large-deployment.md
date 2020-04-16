---
title: Dimensione a recuperação de VMware/desastrefísico com a recuperação do site do Azure
description: Saiba como configurar a recuperação de desastres no Azure para um grande número de VMMs VMware ou servidores físicos no local com o Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: a3a2317554f02dc1f1198d8019bbfdb50e3cc71c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409764"
---
# <a name="set-up-disaster-recovery-at-scale-for-vmware-vmsphysical-servers"></a>Configure a recuperação de desastres em escala para VMware VMs/servidores físicos

Este artigo descreve como configurar a recuperação de desastres para o Azure para grandes números (> 1000) de VMMs vMware ou servidores físicos no local em seu ambiente de produção, usando o serviço [Azure Site Recovery.](site-recovery-overview.md)


## <a name="define-your-bcdr-strategy"></a>Defina sua estratégia de BCDR

Como parte de sua estratégia de continuidade de negócios e recuperação de desastres (BCDR), você define os RPOs (Recovery Point Objectives, objetivos de ponto de recuperação) e os RTOs (Recovery Time Objectives, objetivos de tempo de recuperação) para seus aplicativos de negócios e cargas de trabalho. O RTO mede a duração do tempo e do nível de serviço no qual um aplicativo ou processo de negócios deve ser restaurado e disponível, a fim de evitar problemas de continuidade.
- A Recuperação do Site fornece replicação contínua para VMware VMs e servidores físicos, e um [SLA](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/) para RTO.
- À medida que você planeja a recuperação de desastres em larga escala para VMware VMs e descobrir os recursos do Azure que você precisa, você pode especificar um valor de RTO que será usado para cálculos de capacidade.


## <a name="best-practices"></a>Práticas recomendadas

Algumas práticas recomendadas gerais para recuperação de desastres em larga escala. Essas melhores práticas são discutidas com mais detalhes nas próximas seções do documento.

- **Identifique os requisitos de destino**: Estime a capacidade e as necessidades de recursos no Azure antes de configurar a recuperação de desastres.
- **Plano para componentes de recuperação de site**: Descubra quais componentes de recuperação de site (servidor de configuração, servidores de processo) você precisa para atender à sua capacidade estimada.
- **Configure um ou mais servidores de processo de saída de escala**: Não use o servidor de processo que está sendo executado por padrão no servidor de configuração. 
- **Execute as últimas atualizações**: A equipe de recuperação do site lança novas versões dos componentes de recuperação do site regularmente, e você deve ter certeza de que está executando as versões mais recentes. Para ajudar com isso, acompanhe [as novidades das](site-recovery-whats-new.md) atualizações e [habilite e instale atualizações](service-updates-how-to.md) à medida que elas forem liberadas.
- **Monitore de forma proativa**: À medida que você começa a recuperação de desastres em funcionamento, você deve monitorar proativamente o status e a saúde de máquinas replicadas e recursos de infra-estrutura.
- **Exercícios de recuperação de desastres**: Você deve executar exercícios de recuperação de desastres regularmente. Isso não impacta no seu ambiente de produção, mas ajuda a garantir que o failover para o Azure funcione como esperado quando necessário.



## <a name="gather-capacity-planning-information"></a>Coletar informações de planejamento de capacidade

Reúna informações sobre seu ambiente local, para ajudar a avaliar e estimar as necessidades de capacidade do seu alvo (Azure).
- Para vMware, execute o Planejador de Implantação para VMware VMs para fazer isso.
- Para servidores físicos, reúna as informações manualmente.

### <a name="run-the-deployment-planner-for-vmware-vms"></a>Execute o planejador de implantação para VMS VMware

O Planejador de Implantação ajuda você a coletar informações sobre o ambiente local do VMware.

- Execute o Planejador de Implantação durante um período que representa um churn típico para suas VMs. Isso gerará estimativas e recomendações mais precisas.
- Recomendamos que você execute o Planejador de implantação na máquina do servidor de configuração, uma vez que o Planejador calcula a produtividade do servidor em que está sendo executado. [Saiba mais](site-recovery-vmware-deployment-planner-run.md#get-throughput) sobre como medir a capacidade de medição.
- Se você ainda não tiver um servidor de configuração configurado:
    - [Obtenha uma visão geral](vmware-physical-azure-config-process-server-overview.md) dos componentes de recuperação do site.
    - [Configure um servidor de configuração,](vmware-azure-deploy-configuration-server.md)a fim de executar o Planejador de implantação nele.

Em seguida, execute o Planejador da seguinte forma:

1. [Saiba mais sobre](site-recovery-deployment-planner.md) o Planejador de Implantação. Você pode baixar a versão mais recente do portal, ou [baixá-la diretamente](https://aka.ms/asr-deployment-planner).
2. Revise os [pré-requisitos](site-recovery-deployment-planner.md#prerequisites) e [as últimas atualizações](site-recovery-deployment-planner-history.md) para o Planejador de Implantação e [baixe e extraia](site-recovery-deployment-planner.md#download-and-extract-the-deployment-planner-tool) a ferramenta.
3. [Execute o Planejador de implantação](site-recovery-vmware-deployment-planner-run.md) no servidor de configuração.
4. [Gerar um relatório](site-recovery-vmware-deployment-planner-run.md#generate-report) para resumir estimativas e recomendações.
5. Analisar as recomendações do relatório e as [estimativas](site-recovery-vmware-deployment-planner-analyze-report.md) [de custos.](site-recovery-vmware-deployment-planner-cost-estimation.md)

>[!NOTE]
> Por padrão, a ferramenta é configurada para perfile gera relatório para até 1000 VMs. Você pode alterar esse limite aumentando o valor-chave MaxVMsSupported no arquivo ASRDeploymentPlanner.exe.config.

## <a name="plan-target-azure-requirements-and-capacity"></a>Requisitos e capacidade do destino do plano (Azure)

Usando suas estimativas e recomendações coletadas, você pode planejar recursos e capacidade de destino. Se você executou o Planejador de Implantação para VMs VMware, você pode usar uma série de recomendações do [relatório](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) para ajudá-lo.

- **VMs compatíveis**: Use este número para identificar o número de VMs que estão prontos para recuperação de desastres no Azure. As recomendações sobre largura de banda de rede e núcleos Azure são baseadas neste número.
- **Largura de banda de rede necessária**: Observe a largura de banda necessária para a replicação delta de VMs compatíveis. 
    - Quando você executa o Planejador, você especifica o RPO desejado em minutos. As recomendações mostram a largura de banda necessária para atender a esse RPO 100% e 90% do tempo. 
    - As recomendações de largura de banda da rede levam em conta a largura de banda necessária para o número total de servidores de configuração e servidores de processo recomendados no Planejador.
- **Núcleos Azure obrigatórios**: Observe o número de núcleos que você precisa na região azure alvo, com base no número de VMs compatíveis. Se você não tiver núcleos suficientes, no failover A Recuperação do Site não será capaz de criar as VMs Azure necessárias.
- **Tamanho do lote de VM recomendado**: O tamanho do lote recomendado é baseado na capacidade de terminar a replicação inicial do lote dentro de 72 horas por padrão, enquanto atende a um RPO de 100%. O valor da hora pode ser modificado.

Você pode usar essas recomendações para planejar os recursos do Azure, largura de banda da rede e loteamento de VM.

## <a name="plan-azure-subscriptions-and-quotas"></a>Planejar assinaturas e cotas do Azure

Queremos ter certeza de que as cotas disponíveis na assinatura de destino são suficientes para lidar com o failover.

**Tarefa** | **Detalhes** | **Ação**
--- | --- | ---
**Verificar núcleos** | Se os núcleos da cota disponível não forem iguais ou excederem a contagem total de metas no momento do failover, os failovers falharão. | Para VMs VMware, verifique se você tem núcleos suficientes na assinatura de destino para atender à recomendação principal do Deployment Planner.<br/><br/> Para servidores físicos, verifique se os núcleos Do Azure atendem às suas estimativas manuais.<br/><br/> Para verificar cotas, no portal azure > **Assinatura,** clique **em Uso + cotas**.<br/><br/> [Saiba mais](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) sobre o aumento de cotas.
**Verifique os limites de failover** | O número de failovers não deve exceder os limites de failover do Site Recovery. |  Se os failovers excederem os limites, você poderá adicionar assinaturas e falhar em várias assinaturas ou aumentar a cota para uma assinatura. 


### <a name="failover-limits"></a>Limites de failover

Os limites indicam o número de failovers suportados pelo Site Recovery dentro de uma hora, assumindo três discos por máquina.

O que significa obedecer? Para iniciar uma VM Azure, o Azure exige que alguns drivers estejam em estado inicial de inicialização, e serviços como o DHCP sejam definidos para iniciar automaticamente.
- As máquinas que cumprirem já terão essas configurações no lugar.
- Para máquinas que executam o Windows, você pode verificar proativamente a conformidade e torná-las compatíveis, se necessário. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010).
- As máquinas Linux só são trazidas para a conformidade no momento do failover.

**A máquina está em conformidade com o Azure?** | **Limites de VM do Azure (failover de disco gerenciado)**
--- | --- 
Sim | 2000
Não | 1000

- Os limites supõem que outros empregos mínimos estão em andamento na região-alvo para a assinatura.
- Algumas regiões do Azure são menores, e podem ter limites ligeiramente mais baixos.

## <a name="plan-infrastructure-and-vm-connectivity"></a>Planeje infraestrutura e conectividade VM

Após o failover para o Azure, você precisa que suas cargas de trabalho operem como fizeram no local e para permitir que os usuários acessem cargas de trabalho em execução nas VMs do Azure.

- [Saiba mais](site-recovery-active-directory.md#test-failover-considerations) sobre a falha em sua infra-estrutura de diretório ativo ou DNS no local para o Azure.
- [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) sobre a preparação para se conectar às VMs do Azure após o failover.



## <a name="plan-for-source-capacity-and-requirements"></a>Plano para capacidade de origem e requisitos

É importante que você tenha servidores de configuração suficientes e servidores de processo de escala para atender aos requisitos de capacidade. À medida que você inicia sua implantação em larga escala, comece com um único servidor de configuração e um único servidor de processo de saída de escala. À medida que você atinge os limites prescritos, adicione servidores adicionais.

>[!NOTE]
> Para VMware VMs, o Planejador de Implantação faz algumas recomendações sobre a configuração e os servidores de processo que você precisa. Recomendamos que você use as tabelas incluídas nos seguintes procedimentos, em vez de seguir a recomendação do Planejador de Implantação. 


## <a name="set-up-a-configuration-server"></a>Configurar um servidor de configuração
 
A capacidade do servidor de configuração é afetada pelo número de máquinas replicadas, e não pela taxa de rotatividade de dados. Para descobrir se você precisa de servidores de configuração adicionais, use esses limites de VM definidos.

**Cpu** | **Memória** | **Disco de cache** | **Limite da máquina replicada**
 --- | --- | --- | ---
8 vCPUs<br> 2 soquetes * 4 núcleos @ 2.5 Ghz | 16 GB | 600 GB | Até 550 máquinas<br> Presume-se que cada máquina tem três discos de 100 GB cada.

- Esses limites são baseados em um servidor de configuração configurado usando um modelo OVF.
- Os limites supõem que você não está usando o servidor de processo que está sendo executado por padrão no servidor de configuração.

Se você precisar adicionar um novo servidor de configuração, siga estas instruções:

- [Configure um servidor de configuração](vmware-azure-deploy-configuration-server.md) para recuperação de desastres VMware VM, usando um modelo OVF.
- [Configure um servidor de configuração](physical-azure-set-up-source.md) manualmente para servidores físicos ou para implantações de VMware que não possam usar um modelo OVF.

Ao configurar um servidor de configuração, observe que:

- Quando você configura um servidor de configuração, é importante considerar a assinatura e o cofre no qual ele reside, já que estes não devem ser alterados após a configuração. Se você precisar alterar o cofre, você tem que desassociar o servidor de configuração do cofre e registrá-lo. Isso interrompe a replicação de VMs no cofre.
- Se você quiser configurar um servidor de configuração com vários adaptadores de rede, você deve fazer isso durante a configuração. Você não pode fazer isso depois de registrar o servidor de configuração no cofre.

## <a name="set-up-a-process-server"></a>Configurar um servidor de processo

A capacidade do servidor de processo é afetada pelas taxas de rotatividade de dados, e não pelo número de máquinas habilitadas para replicação.

- Para grandes implantações, você deve sempre ter pelo menos um servidor de processo de escala.
- Para descobrir se você precisa de servidores adicionais, use a tabela a seguir.
- Recomendamos que você adicione um servidor com a especificação mais alta. 


**Cpu** | **Memória** | **Disco de cache** | **Taxa de churn**
 --- | --- | --- | --- 
12 vCPUs<br> 2 soquetes * 6 núcleos @ 2.5 Ghz | 24 GB | 1 GB | Até 2 TB por dia

Configure o servidor de processo da seguinte forma:

1. Analise os [pré-requisitos](vmware-azure-set-up-process-server-scale.md#prerequisites).
2. Instale o servidor no [portal](vmware-azure-set-up-process-server-scale.md#install-from-the-ui)ou na [linha de comando.](vmware-azure-set-up-process-server-scale.md#install-from-the-command-line)
3. Configure máquinas replicadas para usar o novo servidor. Se você já tem máquinas replicando:
    - Você pode [mover](vmware-azure-manage-process-server.md#switch-an-entire-workload-to-another-process-server) toda uma carga de trabalho do servidor de processo para o novo servidor de processo.
    - Alternativamente, você pode [mover](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load) VMs específicos para o novo servidor de processo.



## <a name="enable-large-scale-replication"></a>Habilite a replicação em larga escala

Depois de planejar a capacidade e implantar os componentes e infra-estrutura necessários, habilite a replicação para um grande número de VMs.

1. Classifique máquinas em lotes. Você habilita a replicação para VMs dentro de um lote e, em seguida, passa para o próximo lote.

    - Para VMware VMs, você pode usar o [tamanho de lote vm recomendado](site-recovery-vmware-deployment-planner-analyze-report.md#recommended-vm-batch-size-for-initial-replication) no relatório Deployment Planner.
    - Para máquinas físicas, recomendamos identificar lotes baseados em máquinas que tenham um tamanho e quantidade de dados semelhantes, e no throughput de rede disponível. O objetivo é emparcelar máquinas que provavelmente terminem sua replicação inicial em cerca da mesma quantidade de tempo.
    
2. Se o churn de disco para uma máquina for alto ou exceder os limites no Deployment thePlanner, você poderá mover arquivos não críticos que você não precisa replicar (como dumps de log ou arquivos temporários) da máquina. Para VMware VMs, você pode mover esses arquivos para um disco separado e, em seguida, [excluir esse disco](vmware-azure-exclude-disk.md) da replicação.
3. Antes de habilitar a replicação, verifique se as máquinas atendem aos [requisitos de replicação](vmware-physical-azure-support-matrix.md#replicated-machines).
4. Configure uma política de replicação para [VMs](vmware-azure-set-up-replication.md#create-a-policy) ou [servidores físicos vMware](physical-azure-disaster-recovery.md#create-a-replication-policy).
5. Habilite a replicação para [VMs vmware](vmware-azure-enable-replication.md) ou [servidores físicos](physical-azure-disaster-recovery.md#enable-replication). Isso inicia a replicação inicial das máquinas selecionadas.

## <a name="monitor-your-deployment"></a>Monitorar a implantação

Depois de iniciar a replicação do primeiro lote de VMs, comece a monitorar sua implantação da seguinte forma:  

1. Designe um administrador de recuperação de desastres para monitorar o estado de saúde das máquinas replicadas.
2. [Monitore eventos](site-recovery-monitor-and-troubleshoot.md) para itens replicados e a infra-estrutura.
3. [Monitore a saúde](vmware-physical-azure-monitor-process-server.md) de seus servidores de processo de escala.
4. Cadastre-se para receber [notificações de e-mail](https://docs.microsoft.com/azure/site-recovery/site-recovery-monitor-and-troubleshoot#subscribe-to-email-notifications) para eventos, para facilitar o monitoramento.
5. Realize [exercícios regulares de recuperação de desastres,](site-recovery-test-failover-to-azure.md)para garantir que tudo esteja funcionando como esperado.


## <a name="plan-for-large-scale-failovers"></a>Plano para failovers em larga escala

Em caso de desastre, você pode precisar falhar sobre um grande número de máquinas/cargas de trabalho para o Azure. Prepare-se para este tipo de evento da seguinte forma.

Você pode se preparar com antecedência para failover da seguinte forma:

- [Prepare sua infra-estrutura e VMs](#plan-infrastructure-and-vm-connectivity) para que suas cargas de trabalho estejam disponíveis após o failover e para que os usuários possam acessar as VMs do Azure.
- Observe os [limites de failover](#failover-limits) anteriormente neste documento. Certifique-se de que seus failovers se enquadrarão nestes limites.
- Execute [exercícios regulares de recuperação de desastres.](site-recovery-test-failover-to-azure.md) Brocas ajudam a:
    - Encontre lacunas em sua implantação antes do failover.
    - Estime o RTO de ponta a ponta para seus aplicativos.
    - Estime o RPO de ponta a ponta para suas cargas de trabalho.
    - Identificar conflitos de intervalo de endereçoip.
    - À medida que executa brocas, recomendamos que você não use redes de produção para brocas, evite usar os mesmos nomes de sub-rede em redes de produção e teste e limpe failovers de teste após cada broca.

Para executar um failover em larga escala, recomendamos o seguinte:

1. Crie planos de recuperação para failover de carga de trabalho.
    - Cada plano de recuperação pode desencadear failover de até 100 máquinas.
    - [Saiba mais](recovery-plan-overview.md) sobre planos de recuperação.
2. Adicione scripts de runbook do Azure Automation aos planos de recuperação, para automatizar quaisquer tarefas manuais no Azure. Tarefas típicas incluem configurar balanceadores de carga, atualizar DNS etc. [Saiba mais](site-recovery-runbook-automation.md)
2. Antes do failover, prepare as máquinas do Windows para que elas estejam em conformidade com o ambiente Azure. [Os limites de failover](#plan-azure-subscriptions-and-quotas) são mais altos para máquinas que cumprem. [Saiba mais](site-recovery-failover-to-azure-troubleshoot.md#failover-failed-with-error-id-170010) sobre os runbooks.
4.  Acione failover com o [start-AzRecoveryServicesAsrPlannedFailoverJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/start-azrecoveryservicesasrplannedfailoverjob?view=azps-2.0.0&viewFallbackFrom=azps-1.1.0) PowerShell cmdlet, juntamente com um plano de recuperação.



## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Monitorar Site Recovery](site-recovery-monitor-and-troubleshoot.md)
