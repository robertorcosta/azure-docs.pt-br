---
title: Perguntas comuns sobre a recuperação de desastres da Azure VM com a recuperação do site do Azure
description: Este artigo responde a perguntas comuns sobre a recuperação de desastres do Azure VM quando você usa o Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 7d3bcc32dc8f1412a5adbc175a5f8618628bce83
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547884"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Perguntas comuns: Recuperação de desastres azure-to-Azure

Este artigo responde a perguntas comuns sobre a recuperação de desastres de VMs do Azure para outra região do Azure para quando você usa [o Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?

Revisar [os preços de recuperação do site do Azure para VMs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona a camada gratuita do Azure Site Recovery?

Cada instância protegida pelo Azure Site Recovery é gratuita durante os primeiros 31 dias de proteção. Após esse período, a proteção para cada instância está nas taxas nos [preços de recuperação do Site do Azure para máquinas virtuais do Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?

Sim. Embora o Azure Site Recovery esteja gratuito durante os primeiros 31 dias de uma instância protegida, você pode incorrer em cobranças pelo Azure Storage, transações de armazenamento e transferências de dados. Uma Máquina Virtual recuperada também pode incorrer em cargas de computação do Azure. Obtenha detalhes completos sobre os preços no [Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Quais são as melhores práticas para a recuperação de desastres do Azure Virtual Machines?

1. [Entender a arquitetura de Azure para Azure](azure-to-azure-architecture.md)
1. [Examinar as configurações com suporte e sem suporte](azure-to-azure-support-matrix.md)
1. [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-how-to-enable-replication.md)
1. [Execute um failover de teste](azure-to-azure-tutorial-dr-drill.md)
1. [Fazer failover e failback para a região primária](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Como a capacidade é garantida na região alvo?

A equipe de recuperação do site e a equipe de gerenciamento de capacidade do Azure planejam capacidade suficiente de infra-estrutura. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery sejam implantadas na região de destino.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Posso replicar VMs habilitadas por meio da criptografia de disco do Azure?

Sim. A Recuperação do Site suporta a recuperação de desastres de VMs que têm a Criptografia de Disco Do Azure ativada. Quando você habilita a replicação, o Azure copia todas as chaves e segredos de criptografia de disco necessários da região de origem para a região de destino no contexto do usuário. Se você não tiver as permissões apropriadas, o administrador de segurança pode usar um script para copiar as chaves e segredos.

- A Recuperação do Site suporta a criptografia de disco Azure para VMs do Azure que estão executando o Windows.
- A Recuperação do Site suporta a versão 0.1 do Azure Disk Encryption, que tem um esquema que requer o Azure Active Directory (Azure AD). A Recuperação do Site também suporta a versão 1.1, que não requer o Azure AD. [Saiba mais sobre o esquema de extensão para criptografia de disco Do Zure](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Para o Azure Disk Encryption versão 1.1, você tem que usar as VMs do Windows com discos gerenciados.
  - [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre como ativar a replicação de VMs criptografadas.

### <a name="can-i-replicate-vms-to-another-subscription"></a>É possível replicar VMs para outra assinatura?

Sim, você pode replicar VMs do Azure para uma assinatura diferente no mesmo locatário do Azure AD.

Configure a recuperação de [desastres entre as assinaturas](https://azure.microsoft.com/blog/cross-subscription-dr) selecionando outra assinatura no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Posso replicar VMs do Azure fixadas em zonas para outra região?

Sim, é possível [replicar VMs fixadas em zonas](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>É possível excluir discos?

Sim, é possível excluir discos no momento da proteção usando o PowerShell. Para obter mais informações, veja [como excluir discos da replicação](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>Posso adicionar novos discos às VMs replicadas e habilitar a replicação para eles?

Sim, adicionar novos discos às VMs replicadas e habilitar a replicação para eles é suportado para VMs Azure com discos gerenciados. Quando você adiciona um novo disco a uma VM do Azure habilitada para replicação, a saúde de replicação da VM mostra um aviso. Esse aviso afirma que um ou mais discos na VM estão disponíveis para proteção. Você pode habilitar a replicação para adicionar discos.

- Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial.
- Se você não habilitar a replicação para o disco, você pode descartar o aviso.
- Se você falhar sobre uma VM que tenha um disco adicionado e a replicação ativada, haverá pontos de replicação. Os pontos de replicação mostrarão os discos disponíveis para recuperação.

Por exemplo, digamos que uma VM tem um único disco e você adiciona um novo. Pode haver um ponto de replicação que foi criado antes de você adicionar o disco. Este ponto de replicação mostrará que ele consiste em "1 de 2 discos".

A recuperação do site não suporta "hot remove" de um disco de uma VM replicada. Se você remover um disco VM, você precisa desativar e, em seguida, reativar a replicação para a VM.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?

A replicação é contínua quando você está replicando VMs do Azure para outra região do Azure. Para saber mais, veja [Arquitetura de replicação do Azure para o Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-architecture#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Posso replicar máquinas virtuais dentro de uma região? Preciso dessa funcionalidade para migrar VMs.

Você não pode usar uma solução de recuperação de disco Azure-to-Azure para replicar VMs dentro de uma região.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>Posso replicar instâncias de VM em qualquer região do Azure?

Usando a Recuperação do Site, você pode replicar e recuperar VMs entre quaisquer duas regiões dentro do mesmo cluster geográfico. Clusters geográficos são definidos pensando em latência de dados e soberania. Para obter mais informações, veja [Matriz de suporte regional](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support) do Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>O Site Recovery requer conectividade com a Internet?

Não, a recuperação do site não requer conectividade com a internet. Mas requer acesso a URLs de recuperação de site e faixas IP, como mencionado em [rede na recuperação de desastres do Azure VM](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>Posso replicar um aplicativo que tenha um grupo de recursos separado para níveis separados?

Sim, você pode replicar o aplicativo e manter a configuração de recuperação de desastres em um grupo de recursos separado também.

Por exemplo, se o aplicativo tiver o aplicativo, o banco de dados e a Web de cada nível em um grupo de recursos separado, então você terá que selecionar o [assistente de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication#enable-replication) três vezes para proteger todos os níveis. A Recuperação do Site irá replicar esses três níveis em três grupos de recursos diferentes.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma diretiva de replicação define as configurações para o histórico de retenção de pontos de recuperação. A política também define a frequência de instantâneos consistentes com aplicativos. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de:

- 24 horas para o histórico de retenção dos pontos de recuperação.
- 60 minutos para a frequência dos instantâneos consistentes em termos de aplicativo.

[Saiba mais sobre as configurações de replicação](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de falha?

Um ponto de recuperação consistente com falhas tem os dados no disco como se você puxasse o cabo de alimentação do servidor durante o snapshot. O ponto de recuperação consistente com a falha não inclui nada que estava na memória quando o instantâneo foi tirado.

Atualmente, a maioria dos aplicativos pode recuperar-se bem de instantâneos consistente em termos de falha. Um ponto de recuperação consistente em termos de falha geralmente é suficiente para sistemas operacionais sem banco de dados e aplicativos como servidores, servidores DHCP, servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência de geração de ponto de recuperação consistente com a falha?

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de aplicativo?

Pontos de recuperação consistentes em termos de aplicativo são criados com base em instantâneos consistentes em termos de aplicativo. Os pontos de recuperação consistentes com aplicativos capturam os mesmos dados que instantâneos consistentes com falhas, ao mesmo tempo em que capturam dados na memória e em todas as transações em processo.

Devido ao seu conteúdo extra, os instantâneos consistentes com aplicativos são os mais envolvidos e levam mais tempo. Recomendamos pontos de recuperação consistentes em termos de aplicativo para sistemas operacionais de banco de dados como o SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto de pontos de recuperação consistentes em termos de aplicativo no desempenho do aplicativo?

Os pontos de recuperação consistentes com aplicativos capturam todos os dados na memória e no processo. Como os pontos de recuperação capturam esses dados, eles requerem uma estrutura como o Volume Shadow Copy Service no Windows para quiescem o aplicativo. Se o processo de captura for frequente, pode afetar o desempenho quando a carga de trabalho já estiver ocupada. Não recomendamos que você use baixa freqüência para pontos de recuperação consistentes com aplicativos para cargas de trabalho não-banco de dados. Mesmo para a carga horária do banco de dados, 1 hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima de geração de ponto de recuperação consistente com o aplicativo?

A recuperação do site pode criar um ponto de recuperação consistente com uma frequência mínima de 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?

Para entender como o Site Recovery gera pontos de recuperação, vamos ver um exemplo de uma política de replicação. Esta política de replicação tem um ponto de recuperação com uma janela de retenção de 24 horas e um instantâneo de freqüência consistente com o aplicativo de 1 hora.

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos. Você não pode mudar essa freqüência. Para a última hora, você pode escolher entre 12 pontos consistentes com falhas e 1 ponto consistente com o aplicativo. À medida que o tempo avança, a Recuperação do Site poda todos os pontos de recuperação além da última hora e economiza apenas 1 ponto de recuperação por hora.

A captura de tela a seguir ilustra o exemplo. Na captura de tela:

- Na última hora, há pontos de recuperação com uma frequência de 5 minutos.
- Além da última hora, a Recuperação do Site mantém apenas 1 ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?

O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Tenho uma política de replicação de 24 horas. O que acontecerá se um problema impedir que a Recuperação do Site gere pontos de recuperação por mais de 24 horas? Meus pontos de recuperação anteriores serão perdidos?

Não, o Site Recovery manterá todos os pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, a Recuperação do Site substitui o ponto mais antigo apenas se ele gerar novos pontos. Por causa do problema, a Recuperação do Site não pode gerar novos pontos de recuperação. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão após você chegar à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Após a replicação ser habilitada em uma VM, como fazer para alterar a política de replicação?

Vá para as políticas de**replicação**da**infra-estrutura** > de recuperação do local do **cofre** > de recuperação do site do site . Selecione a diretiva que deseja editar e salve as alterações. Qualquer alteração também será aplicada a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa da VM ou diferencial?

O primeiro ponto de recuperação gerado tem a cópia completa. Os pontos de recuperação sucessivos têm alterações delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Aumentar o período de retenção de pontos de recuperação aumenta o custo de armazenamento?

Sim, se você aumentar o período de retenção de 24 horas para 72 horas, a Recuperação do Site salvará os pontos de recuperação por mais 48 horas. Adicionar tempo gerará em encargos de armazenamento. Por exemplo, um único ponto de recuperação pode ter alterações delta de 10 GB com um custo por GB de US $ 0,16 por mês. Taxas adicionais seriam de $1,60 × 48 por mês.

## <a name="multi-vm-consistency"></a>Consistência de várias VMs

### <a name="what-is-multi-vm-consistency"></a>O que é consistência multi-VM?

A consistência de vários VM garante que o ponto de recuperação seja consistente em todas as máquinas virtuais replicadas.

A Recuperação do Site fornece uma opção **de consistência Multi-VM,** que cria um grupo de replicação de todas as máquinas.

Quando você falha sobre as máquinas virtuais, elas terão pontos de recuperação consistentes com falhas compartilhadas e consistentes com aplicativos.

Passe pelo tutorial para ativar a [consistência multi-VM](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>Posso falhar sobre uma única máquina virtual dentro de um grupo de replicação de consistência multi-VM?

Quando você seleciona a opção **de consistência Multi-VM,** você está afirmando que o aplicativo tem uma dependência de todas as máquinas virtuais dentro de um grupo. O failover de uma única máquina virtual não é permitido.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quantas máquinas virtuais posso replicar como parte de um grupo de replicação de consistência multi-VM?

Você pode replicar 16 máquinas virtuais ao mesmo tempo em um grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando devo ativar a consistência multi-VM?

Como a consistência de vários VM é intensiva na CPU, a habilitação pode afetar o desempenho da carga de trabalho. Use a consistência multi-VM somente se as máquinas estiverem executando a mesma carga de trabalho e você precisar de consistência em várias máquinas. Por exemplo, se você tiver duas instâncias do SQL Server e dois servidores web em um aplicativo, você deve ter consistência de vários VM apenas para as instâncias do SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>Você pode adicionar uma VM já replicando a um grupo de replicação?

Você pode adicionar uma VM a um novo grupo de replicação enquanto habilita a replicação. Você também pode adicionar uma VM a um grupo de replicação existente enquanto habilita a replicação. No entanto, não é possível adicionar uma VM já replicando a um novo grupo de replicação ou grupo de replicação existente.

## <a name="failover"></a>Failover

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como a capacidade é garantida na região alvo das VMs do Azure?

A equipe de recuperação do site e a equipe de gerenciamento de capacidade do Azure planejam capacidade suficiente de infra-estrutura. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery sejam implantadas na região de destino.

### <a name="is-failover-automatic"></a>O failover é automático?

O failover não é automático. Você pode iniciar failovers com um único clique no portal, ou pode usar o [PowerShell](azure-to-azure-powershell.md) para ativar um failover.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>Posso manter um endereço IP público após um failover?

Você não pode manter o endereço IP público do aplicativo de produção após um failover.

Quando você traz uma carga de trabalho como parte do processo de failover, você precisa atribuir um recurso IP público do Azure à carga de trabalho. O recurso IP público do Azure deve estar disponível na região alvo. Você pode atribuir manualmente o recurso IP público do Azure ou automatizá-lo com um plano de recuperação. Saiba como [configurar endereços IP públicos após failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>Posso manter um endereço IP privado durante um failover?

Sim, você pode manter um endereço IP privado. Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, o Site Recovery cria recursos de destino com base nas configurações do recurso de origem. Para máquinas virtuais azure configuradas com endereços IP estáticos, a Recuperação do Site tenta provisionar o mesmo endereço IP para a VM de destino se não estiver em uso.
Saiba como [manter endereços IP durante o failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Após um failover, por que o servidor é atribuído um novo endereço IP?

O Site Recovery tenta fornecer o endereço IP no momento do failover. Se outra máquina virtual estiver usando esse endereço, o Site Recovery definirá o próximo endereço IP disponível como destino.

Saiba mais sobre [a configuração do mapeamento de rede e endereçamento ip para redes virtuais.](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms)

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Quais são os pontos de recuperação **Mais recentes (menor RPO)**?

A **opção Mais recente (rpo mais baixo)** primeiro processa todos os dados enviados para a Recuperação do Site. Após o serviço processa os dados, ele cria um ponto de recuperação para cada VM antes de falhar na VM. Esta opção fornece o menor rpo (recovery point objective, objetivo de ponto de recuperação). O VM criado após failover tem todos os dados replicados para recuperação do site a partir de quando o failover foi acionado.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Os pontos de recuperação **Mais recentes (menor RPO)** afetam o RTO do failover?

Sim. O Site Recovery processa todos os dados pendentes antes de falhar, de modo que esta opção tem um Objetivo de tempo de recuperação maior (RTO) em comparação com outras opções.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que significa a opção **Últimos processados** nos pontos de recuperação?

A **opção processada mais recente** falha sobre todas as VMs do plano até o último ponto de recuperação que a Recuperação do Site processou. Para ver o último ponto de recuperação de uma VM específica, verifique **os pontos de recuperação mais recentes** nas configurações da VM. Essa opção fornece um RTO baixo, pois nenhum tempo é gasto no processamento de dados não processados.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>O que acontece se minha região primária sofre uma paralisação inesperada?

É possível disparar um failover após a interrupção. A recuperação do site não precisa de conectividade da região primária para fazer o failover.

### <a name="what-is-an-rto-of-a-vm-failover"></a>O que é um RTO de um failover VM?

A Recuperação do Site tem um [SLA RTO de 2 horas](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). No entanto, na maioria das vezes, a recuperação do site falha sobre máquinas virtuais em poucos minutos. Você pode calcular o RTO indo para os trabalhos de failover, que mostram o tempo que levou para trazer a VM. Para o plano de recuperação RTO, consulte a próxima seção.

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?

Um plano de recuperação no Site Recovery orquestra a recuperação de failover de VMs. Os planos de recuperação também ajudam a tornar a recuperação precisa, repetível e automatizada de forma consistente. Um plano de recuperação atende às seguintes necessidades:

- Definir um grupo de máquinas virtuais que fazem failover em conjunto
- Definir as dependências entre as máquinas virtuais para que o aplicativo seja executado com precisão
- Automatizar a recuperação juntamente com ações manuais personalizadas para realizar tarefas que não o failover de máquinas virtuais

Saiba mais [sobre a criação de planos de recuperação](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como o sequenciamento é feito em um plano de recuperação?

Em um plano de recuperação, é possível criar vários grupos para realizar o sequenciamento. Cada grupo faz o failover ao mesmo tempo. As máquinas virtuais que fazem parte do mesmo grupo falham juntas, seguidas por outro grupo. Para saber como modelar um aplicativo usando um plano de recuperação, veja [Sobre planos de recuperação](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como localizar o RTO de um plano de recuperação?

Para verificar o RTO de um plano de recuperação, faça um failover de teste para o plano de recuperação e vá para **trabalhos do Site Recovery**.
No exemplo a seguir, consulte o trabalho **SAPTestRecoveryPlan**. O trabalho levou 8 minutos e 59 segundos para falhar em todas as máquinas virtuais e fazer ações especificadas.

![Lista de trabalhos do Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>É possível adicionar runbooks de automação ao plano de recuperação?

Sim, é possível integrar runbooks de Automação do Azure ao plano de recuperação. Saiba mais sobre [como adicionar runbooks da Azure Automation](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Nova proteção e failback

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Eu falhei da região primária para uma região de recuperação de desastres. As VMs em uma região de DR são protegidas automaticamente?

Não. Ao fazer [failover](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-failover-failback) das VMs do Azure de uma região para outra, as VMs iniciam na região de DR em um estado desprotegido. Para fazer failback das VMs na região primária, é necessário [proteger novamente](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect) as VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da nova proteção, o Site Recovery replica dados completos da região secundária para a região primária?

Depende da situação. Se a VM da região de origem existir, então apenas as alterações entre o disco de origem e o disco de destino serão sincronizadas. O Site Recovery calcula os diferenciais comparando os discos e, em seguida, transfere os dados. Esse processo normalmente leva algumas horas. Para obter mais informações sobre o que acontece durante a reproteção, consulte [Reprotect falhou sobre as instâncias de VM do Azure para a região principal](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-reprotect#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo demora para fazer failback?

Após a reproteção, o failback leva cerca da mesma quantidade de tempo que leva para falhar da região primária para uma região secundária.

## <a name="capacity"></a><a name="capacity"></a>Capacity

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como a capacidade é garantida na região alvo das VMs do Azure?

A equipe de recuperação do site e a equipe de gerenciamento de capacidade do Azure planejam capacidade suficiente de infra-estrutura. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery sejam implantadas na região de destino.

### <a name="does-site-recovery-work-with-reserved-instances"></a>A recuperação do site funciona com instâncias reservadas?

Sim, você pode comprar [VMs azure reservados](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastres, e as operações de failover do Site Recovery as usarão. Nenhuma configuração adicional é necessária.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?

Não, a Recuperação do Site não intercepta dados replicados, e não tem nenhuma informação sobre o que está sendo executado em suas VMs. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

A Recuperação do Site é certificada em ISO 27001:2013, 27018, HIPAA e DPA. O serviço está passando por avaliações de SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?

Sim, tanto a criptografia em trânsito quanto [a criptografia em repouso no Azure](https://docs.microsoft.com/azure/storage/storage-service-encryption) são suportadas.

## <a name="next-steps"></a>Próximas etapas

- [Revise os requisitos de suporte do Azure-to-Azure](azure-to-azure-support-matrix.md).
- [Configure a replicação Azure-to-Azure](azure-to-azure-tutorial-enable-replication.md).
- Se você tiver dúvidas após ler este artigo, publique-as no [fórum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).
