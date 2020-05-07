---
title: Perguntas comuns sobre a recuperação de desastres de VM do Azure com o Azure Site Recovery
description: Este artigo responde a perguntas comuns sobre a recuperação de desastres de VM do Azure quando você usa Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: b6f665c5b0f2fbd291d20ef21d0a447d20f7c2da
ms.sourcegitcommit: 291b2972c7f28667dc58f66bbe9d9f7d11434ec1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82738041"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Perguntas comuns: recuperação de desastre do Azure para o Azure

Este artigo responde a perguntas comuns sobre a recuperação de desastre de VMs do Azure para outra região do Azure para quando você usa o [Azure site Recovery](site-recovery-overview.md).

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?

Examine os [preços de Azure site Recovery para as VMs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona a camada gratuita do Azure Site Recovery?

Cada instância protegida pelo Azure Site Recovery é gratuita durante os primeiros 31 dias de proteção. Após esse período, a proteção para cada instância tem as tarifas em [preço Azure site Recovery para máquinas virtuais do Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?

Sim. Embora Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você pode incorrer em encargos pelo armazenamento do Azure, transações de armazenamento e transferências de dados. Uma máquina virtual recuperada também pode incorrer em encargos de computação do Azure. Obtenha detalhes completos sobre preços em [preços Azure site Recovery](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Quais são as práticas recomendadas para a recuperação de desastre das máquinas virtuais do Azure?

1. [Entender a arquitetura de Azure para Azure](azure-to-azure-architecture.md)
1. [Examinar as configurações com suporte e sem suporte](azure-to-azure-support-matrix.md)
1. [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-how-to-enable-replication.md)
1. [Execute um teste de failover](azure-to-azure-tutorial-dr-drill.md)
1. [Fazer failover e failback para a região primária](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Como a capacidade é garantida na região de destino?

A equipe de Site Recovery e equipe de gerenciamento de capacidade do Azure planeja a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery serão implantadas na região de destino.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Posso replicar VMs habilitadas por meio da criptografia de disco do Azure?

Sim. Site Recovery dá suporte à recuperação de desastre de VMs que têm o Azure Disk Encryption habilitado. Quando você habilita a replicação, o Azure copia Todas as chaves de criptografia de disco necessárias e os segredos da região de origem para a região de destino no contexto do usuário. Se você não tiver as permissões apropriadas, o administrador de segurança poderá usar um script para copiar as chaves e os segredos.

- Site Recovery dá suporte a Azure Disk Encryption para VMs do Azure que estão executando o Windows.
- O Site Recovery dá suporte à versão 0,1 do Azure Disk Encryption, que tem um esquema que requer Azure Active Directory (Azure AD). O Site Recovery também dá suporte à versão 1,1, que não requer o Azure AD. [Saiba mais sobre o esquema de extensão para a criptografia de disco do Azure](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Para Azure Disk Encryption versão 1,1, você precisa usar as VMs do Windows com discos gerenciados.
  - [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre como habilitar a replicação para VMs criptografadas.

### <a name="can-i-replicate-vms-to-another-subscription"></a>É possível replicar VMs para outra assinatura?

Sim, você pode replicar VMs do Azure para uma assinatura diferente no mesmo locatário do Azure AD.

Configure a recuperação de desastres [entre assinaturas](https://azure.microsoft.com/blog/cross-subscription-dr) selecionando outra assinatura no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Posso replicar VMs do Azure fixadas em zonas para outra região?

Sim, é possível [replicar VMs fixadas em zonas](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>É possível excluir discos?

Sim, é possível excluir discos no momento da proteção usando o PowerShell. Para obter mais informações, consulte [como excluir discos da replicação](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Posso adicionar novos discos a VMs replicadas e habilitar a replicação para eles?

Sim, adicionar novos discos a VMs replicadas e habilitar a replicação para eles têm suporte para VMs do Azure com discos gerenciados. Quando você adiciona um novo disco a uma VM do Azure que está habilitada para replicação, a integridade da replicação para a VM mostra um aviso. Esse aviso informa que um ou mais discos na VM estão disponíveis para proteção. Você pode habilitar a replicação para discos adicionados.

- Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial.
- Se você não habilitar a replicação para o disco, poderá ignorar o aviso.
- Se você fizer failover de uma VM que tem um disco e replicação adicionais habilitados, haverá pontos de replicação. Os pontos de replicação mostrarão os discos que estão disponíveis para recuperação.

Por exemplo, digamos que uma VM tenha um único disco e você adicione um novo. Pode haver um ponto de replicação que foi criado antes de você adicionar o disco. Esse ponto de replicação mostrará que ele consiste em "1 de 2 discos".

Site Recovery não dá suporte a "remoção a quente" de um disco de uma VM replicada. Se você remover um disco de VM, será necessário desabilitar e reabilitar a replicação para a VM.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?

A replicação é contínua quando você está replicando VMs do Azure para outra região do Azure. Para saber mais, veja [Arquitetura de replicação do Azure para o Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Posso replicar máquinas virtuais dentro de uma região? Preciso dessa funcionalidade para migrar as VMs.

Você não pode usar uma solução de recuperação de disco do Azure para o Azure para replicar VMs em uma região.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Posso replicar instâncias de VM para qualquer região do Azure?

Usando Site Recovery, você pode replicar e recuperar VMs entre duas regiões no mesmo cluster geográfico. Clusters geográficos são definidos pensando em latência de dados e soberania. Para obter mais informações, veja [Matriz de suporte regional](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) do Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>O Site Recovery requer conectividade com a Internet?

Não, Site Recovery não requer conectividade com a Internet. Mas ele requer acesso a Site Recovery URLs e intervalos de IP, conforme mencionado em [rede na recuperação de desastre de VM do Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Posso replicar um aplicativo que tem um grupo de recursos separado para camadas separadas?

Sim, você pode replicar o aplicativo e manter a configuração de recuperação de desastre em um grupo de recursos separado também.

Por exemplo, se seu aplicativo tiver o aplicativo, o banco de dados e a Web de cada camada em um grupo de recursos separado, você precisará selecionar o [Assistente de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) três vezes para proteger todas as camadas. Site Recovery replicará essas três camadas em três grupos de recursos diferentes.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define as configurações para o histórico de retenção dos pontos de recuperação. A política também define a frequência de instantâneos consistentes com o aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de:

- 24 horas para o histórico de retenção dos pontos de recuperação.
- 60 minutos para a frequência dos instantâneos consistentes em termos de aplicativo.

[Saiba mais sobre as configurações de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de falha?

Um ponto de recuperação consistente com falhas tem os dados em disco como se você tiver recebido o cabo de alimentação do servidor durante o instantâneo. O ponto de recuperação consistente com falhas não inclui nada que estava na memória quando o instantâneo foi tirado.

Atualmente, a maioria dos aplicativos pode recuperar-se bem de instantâneos consistente em termos de falha. Um ponto de recuperação consistente em termos de falha geralmente é suficiente para sistemas operacionais sem banco de dados e aplicativos como servidores, servidores DHCP, servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência de geração de ponto de recuperação consistente com a falha?

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de aplicativo?

Pontos de recuperação consistentes em termos de aplicativo são criados com base em instantâneos consistentes em termos de aplicativo. Os pontos de recuperação consistentes com o aplicativo capturam os mesmos dados que os instantâneos consistentes com falhas, enquanto também capturam dados na memória e todas as transações em andamento.

Devido a seu conteúdo extra, os instantâneos consistentes com o aplicativo são os mais envolvidos e são mais bem demorados. Recomendamos pontos de recuperação consistentes em termos de aplicativo para sistemas operacionais de banco de dados como o SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto de pontos de recuperação consistentes em termos de aplicativo no desempenho do aplicativo?

Os pontos de recuperação consistentes com o aplicativo capturam todos os dados na memória e em processo. Como os pontos de recuperação capturam esses dados, eles exigem estrutura como Serviço de Cópias de Sombra de Volume no Windows para desativar o aplicativo. Se o processo de captura for frequente, ele poderá afetar o desempenho quando a carga de trabalho já estiver ocupada. Não recomendamos que você use a frequência baixa para pontos de recuperação consistentes com o aplicativo para cargas de trabalho que não são de banco de dados. Mesmo para a carga de trabalho do banco de dados, 1 hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima de geração de ponto de recuperação consistente com o aplicativo?

Site Recovery pode criar um ponto de recuperação consistente com o aplicativo com uma frequência mínima de 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?

Para entender como Site Recovery gera pontos de recuperação, vejamos um exemplo de uma política de replicação. Essa política de replicação tem um ponto de recuperação com uma janela de retenção de 24 horas e um instantâneo de frequência consistente com o aplicativo de 1 hora.

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos. Você não pode alterar essa frequência. Na última hora, você pode escolher entre 12 pontos consistentes com falhas e 1 ponto consistente com o aplicativo. À medida que o tempo progride, Site Recovery remove todos os pontos de recuperação além da última hora e salva apenas 1 ponto de recuperação por hora.

A captura de tela a seguir ilustra o exemplo. Na captura de tela:

- Na última hora, há pontos de recuperação com uma frequência de 5 minutos.
- Além da última hora, Site Recovery mantém apenas um ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?

O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Tenho uma política de replicação de 24 horas. O que acontecerá se um problema impedir a Site Recovery de gerar pontos de recuperação por mais de 24 horas? Meus pontos de recuperação anteriores serão perdidos?

Não, o Site Recovery manterá todos os pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, Site Recovery substituirá o ponto mais antigo somente se ele gerar novos pontos. Devido ao problema, Site Recovery não pode gerar novos pontos de recuperação. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão depois que você chegar à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Após a replicação ser habilitada em uma VM, como fazer para alterar a política de replicação?

Vá para **site Recovery cofre** > **site Recovery** > **políticas de replicação**de infraestrutura. Selecione a política que você deseja editar e salve as alterações. Qualquer alteração também será aplicada a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa da VM ou diferencial?

O primeiro ponto de recuperação gerado tem a cópia completa. Os pontos de recuperação sucessivos têm alterações delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Aumentar o período de retenção de pontos de recuperação aumenta o custo de armazenamento?

Sim, se você aumentar o período de retenção de 24 horas para 72 horas, Site Recovery salvará os pontos de recuperação por um adicional de 48 horas. Adicionar tempo gerará em encargos de armazenamento. Por exemplo, um único ponto de recuperação pode ter alterações delta de 10 GB com um custo por GB de $0.16 por mês. Os encargos adicionais seriam $1.60 × 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência de várias VMs

### <a name="what-is-multi-vm-consistency"></a>O que é consistência de várias VMs?

A consistência de várias VMs garante que o ponto de recuperação seja consistente em todas as máquinas virtuais replicadas.

Site Recovery fornece uma opção de **consistência de várias VMs** , que cria um grupo de replicação de todos os computadores.

Quando você faz failover das máquinas virtuais, elas terão pontos de recuperação consistentes com o aplicativo e com falhas.

Siga o tutorial para [habilitar a consistência de várias VMs](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Posso fazer failover de uma única máquina virtual em um grupo de replicação de consistência de várias VMs?

Ao selecionar a opção de **consistência de várias VMs** , você está informando que o aplicativo tem uma dependência em todas as máquinas virtuais dentro de um grupo. O failover de máquina virtual única não é permitido.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quantas máquinas virtuais posso replicar como parte de um grupo de replicação de consistência de várias VMs?

Você pode replicar 16 máquinas virtuais ao mesmo tempo em um grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando devo habilitar a consistência de várias VMs?

Como a consistência de várias VMs tem uso intensivo de CPU, a habilitação da TI pode afetar o desempenho da carga de trabalho. Use a consistência de várias VMs somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência em vários computadores. Por exemplo, se você tiver duas instâncias de SQL Server e dois servidores Web em um aplicativo, deverá ter consistência de várias VMs somente para as instâncias de SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Você pode adicionar uma VM que já está replicando a um grupo de replicação?
Você pode adicionar uma VM a um novo grupo de replicação ao habilitar a replicação. Você também pode adicionar uma VM a um grupo de replicação existente ao habilitar a replicação. No entanto, você não pode adicionar uma VM que já está replicando a um novo grupo de replicação ou grupo de replicação existente.
 
## <a name="failover"></a>Failover


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como a capacidade é garantida na região de destino para VMs do Azure?

A equipe de Site Recovery e equipe de gerenciamento de capacidade do Azure planeja a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery serão implantadas na região de destino.

### <a name="is-failover-automatic"></a>O failover é automático?

O failover não é automático. Você pode iniciar failovers com um único clique no portal ou pode usar o [PowerShell](azure-to-azure-powershell.md) para disparar um failover.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Posso manter um endereço IP público após um failover?

Você não pode manter o endereço IP público do aplicativo de produção após um failover.

Ao abrir uma carga de trabalho como parte do processo de failover, você precisa atribuir um recurso de IP público do Azure à carga de trabalho. O recurso de IP público do Azure deve estar disponível na região de destino. Você pode atribuir o recurso de IP público do Azure manualmente ou pode automatizá-lo com um plano de recuperação. Saiba como [configurar endereços IP públicos após o failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Posso manter um endereço IP privado durante um failover?

Sim, você pode manter um endereço IP privado. Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, o Site Recovery cria recursos de destino com base nas configurações do recurso de origem. Para máquinas virtuais do Azure configuradas com endereços IP estáticos, Site Recovery tenta provisionar o mesmo endereço IP para a VM de destino se ela não estiver em uso.
Saiba mais sobre como [manter os endereços IP durante o failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Após um failover, por que o servidor atribuiu um novo endereço IP?

O Site Recovery tenta fornecer o endereço IP no momento do failover. Se outra máquina virtual estiver usando esse endereço, o Site Recovery definirá o próximo endereço IP disponível como destino.

Saiba mais sobre como [Configurar o mapeamento de rede e o endereçamento IP para redes virtuais](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Quais são os pontos de recuperação **Mais recentes (menor RPO)**?

A opção **mais recente (menor RPO)** primeiro processa todos os dados que foram enviados para a site Recovery. Depois que o serviço processa os dados, ele cria um ponto de recuperação para cada VM antes de fazer failover para a VM. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo. A VM criada após o failover tem todos os dados replicados para Site Recovery de quando o failover foi disparado.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Os pontos de recuperação **Mais recentes (menor RPO)** afetam o RTO do failover?

Sim. O Site Recovery processa todos os dados pendentes antes do failover; portanto, essa opção tem um RTO (objetivo de tempo de recuperação) maior em comparação com outras opções.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que significa a opção **Últimos processados** nos pontos de recuperação?

A **última opção processada** faz failover em todas as VMs no plano para o último ponto de recuperação que site Recovery processado. Para ver o ponto de recuperação mais recente de uma VM específica, verifique os **pontos de recuperação mais recentes** nas configurações da VM. Essa opção fornece um RTO baixo, pois nenhum tempo é gasto no processamento de dados não processados.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>O que acontece se minha região primária apresentar uma interrupção inesperada?

É possível disparar um failover após a interrupção. Site Recovery não precisa de conectividade da região primária para fazer o failover.

### <a name="what-is-an-rto-of-a-vm-failover"></a>O que é um RTO de um failover de VM?

Site Recovery tem um [SLA de RTO de 2 horas](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). No entanto, na maioria das vezes, Site Recovery realiza failover de máquinas virtuais em minutos. Você pode calcular o RTO indo para os trabalhos de failover, que mostram o tempo necessário para abrir a VM. Para o RTO do plano de recuperação, consulte a próxima seção.

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?

Um plano de recuperação no Site Recovery orquestra a recuperação de failover de VMs. Os planos de recuperação também ajudam a tornar a recuperação precisa, repetível e automatizada de forma consistente. Um plano de recuperação atende às seguintes necessidades:

- Definir um grupo de máquinas virtuais que fazem failover em conjunto
- Definir as dependências entre as máquinas virtuais para que o aplicativo seja executado com precisão
- Automatizar a recuperação juntamente com ações manuais personalizadas para realizar tarefas que não o failover de máquinas virtuais

Saiba mais [sobre como criar planos de recuperação](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como o sequenciamento é feito em um plano de recuperação?

Em um plano de recuperação, é possível criar vários grupos para realizar o sequenciamento. Cada grupo faz o failover ao mesmo tempo. As máquinas virtuais que fazem parte do mesmo grupo fazem failover juntas, seguidas por outro grupo. Para saber como modelar um aplicativo usando um plano de recuperação, veja [Sobre planos de recuperação](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como localizar o RTO de um plano de recuperação?

Para verificar o RTO de um plano de recuperação, faça um failover de teste para o plano de recuperação e vá para **trabalhos do Site Recovery**.
No exemplo a seguir, consulte o trabalho **SAPTestRecoveryPlan**. O trabalho levou 8 minutos e 59 segundos para fazer failover de todas as máquinas virtuais e executar ações especificadas.

![Lista de trabalhos do Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>É possível adicionar runbooks de automação ao plano de recuperação?

Sim, é possível integrar runbooks de Automação do Azure ao plano de recuperação. Saiba mais sobre como [Adicionar runbooks de automação do Azure](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Nova proteção e failback

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Fiz failover da região primária para uma região de recuperação de desastre. As VMs em uma região de DR são protegidas automaticamente?

Não. Ao fazer [failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) das VMs do Azure de uma região para outra, as VMs iniciam na região de DR em um estado desprotegido. Para fazer failback das VMs na região primária, é necessário [proteger novamente](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) as VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da nova proteção, o Site Recovery replica dados completos da região secundária para a região primária?

Depende da situação. Se a VM da região de origem existir, somente as alterações entre o disco de origem e o disco de destino serão sincronizadas. O Site Recovery calcula os diferenciais comparando os discos e, em seguida, transfere os dados. Esse processo normalmente leva algumas horas. Para obter mais informações sobre o que acontece durante a nova proteção, consulte [proteger novamente as instâncias de VM do Azure com failover para a região primária](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo demora para fazer failback?

Após a nova proteção, o failback assume a mesma quantidade de tempo que leva para fazer failover da região primária para uma região secundária.

## <a name="capacity"></a><a name="capacity"></a>Capacity

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como a capacidade é garantida na região de destino para VMs do Azure?

A equipe de Site Recovery e equipe de gerenciamento de capacidade do Azure planeja a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery serão implantadas na região de destino.

### <a name="does-site-recovery-work-with-reserved-instances"></a>Site Recovery funciona com instâncias reservadas?

Sim, você pode comprar [VMs do Azure reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastre e site Recovery operações de failover as usarão. Nenhuma configuração adicional é necessária.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?

Não, Site Recovery não intercepta dados replicados e não tem nenhuma informação sobre o que está sendo executado em suas VMs. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

O Site Recovery é o ISO 27001:2013, 27018, HIPAA e DPA certificado. O serviço está passando por SOC2 e avaliação de FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?

Sim, há suporte para a criptografia em trânsito e [a criptografia em repouso no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) .

## <a name="next-steps"></a>Próximas etapas

- [Examine os requisitos de suporte do Azure para o Azure](azure-to-azure-support-matrix.md).
- [Configure a replicação do Azure para o Azure](azure-to-azure-tutorial-enable-replication.md).
- Se você tiver dúvidas depois de ler este artigo, poste-os no [Fórum dos serviços de recuperação do Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
