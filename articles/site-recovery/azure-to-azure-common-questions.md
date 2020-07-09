---
title: Perguntas comuns sobre a recuperação de desastre de VM do Azure com o Azure Site Recovery
description: Este artigo responde a perguntas comuns sobre a recuperação de desastre de VM do Azure quando você usa o Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 22848d84896989b1872c55e687c4a5e73da31de8
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86134035"
---
# <a name="common-questions-azure-to-azure-disaster-recovery"></a>Perguntas comuns: Recuperação de desastre do Azure para o Azure

Este artigo responde a perguntas comuns sobre a recuperação de desastre de VMs do Azure para outra região do Azure quando você usa o [Azure Site Recovery](site-recovery-overview.md).

## <a name="general"></a>Geral

### <a name="how-is-site-recovery-priced"></a>Como é o Site Recovery é precificado?

Examine [Preço do Azure Site Recovery para VMs](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="how-does-the-free-tier-for-azure-site-recovery-work"></a>Como funciona a camada gratuita do Azure Site Recovery?

Cada instância protegida pelo Azure Site Recovery é gratuita durante os primeiros 31 dias de proteção. Após esse período, a proteção para cada instância terá os preços mostrados em [Preços do Azure Site Recovery para as Máquinas Virtuais do Azure](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/).

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?

Sim. Entretanto, embora o Azure Site Recovery seja gratuito durante os primeiros 31 dias de uma instância protegida, você poderá ser cobrado pelo Armazenamento do Azure, por transações de armazenamento e por transferência de dados. Uma Máquina Virtual recuperada também poderá gerar encargos de Computação do Azure. Veja os detalhes completos sobre preços em [Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery).

### <a name="what-are-the-best-practices-for-azure-virtual-machines-disaster-recovery"></a>Quais são as melhores práticas para a recuperação de desastre das Máquinas Virtuais do Azure?

1. [Entender a arquitetura de Azure para Azure](azure-to-azure-architecture.md)
1. [Examinar as configurações com suporte e sem suporte](azure-to-azure-support-matrix.md)
1. [Configurar a recuperação de desastre para VMs do Azure](azure-to-azure-how-to-enable-replication.md)
1. [Executar um failover de teste](azure-to-azure-tutorial-dr-drill.md)
1. [Fazer failover e failback para a região primária](azure-to-azure-tutorial-failover-failback.md)

### <a name="how-is-capacity-ensured-in-the-target-region"></a>Como a capacidade é garantida na região de destino?

A equipe do Site Recovery e equipe de gerenciamento de capacidade do Azure planejam a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery sejam implantadas na região de destino.

## <a name="replication"></a>Replicação

### <a name="can-i-replicate-vms-enabled-through-azure-disk-encryption"></a>Posso replicar VMs habilitadas por meio da criptografia de disco do Azure?

Sim. O Site Recovery é compatível com a recuperação de desastre de VMs que têm o Azure Disk Encryption habilitado. Quando você habilita a replicação, o Azure copia todas as chaves de criptografia de disco e os segredos necessários da região de origem para a região de destino no contexto do usuário. Se você não tiver as permissões corretas, o administrador da segurança poderá usar um script para copiar as chaves e os segredos.

- O Site Recovery é compatível com o Azure Disk Encryption para VMs do Azure que executam o Windows.
- O Site Recovery é compatível com a versão 0.1 do Azure Disk Encryption, que tem um esquema que requer o Azure AD (Azure Active Directory). O Site Recovery também é compatível com a versão 1.1, que não requer o Azure AD. [Saiba mais sobre o esquema de extensão do Azure Disk Encryption](../virtual-machines/extensions/azure-disk-enc-windows.md#extension-schema).
  - Para o Azure Disk Encryption versão 1.1, você precisa usar as VMs do Windows com discos gerenciados.
  - [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md) sobre como habilitar a replicação de VMs criptografadas.

### <a name="can-i-select-an-automation-account-from-a-different-resource-group"></a>Posso selecionar uma conta de automação de um grupo de recursos diferente?

Atualmente, não há suporte para isso por meio do portal, mas você pode escolher uma conta de automação de um grupo de recursos diferente por meio do PowerShell.

### <a name="after-specifying-an-automation-account-that-is-in-a-different-resource-group-than-the-vault-am-i-permitted-to-delete-the-runbook-if-there-is-no-other-vault-to-specify"></a>Depois de especificar uma conta de automação que esteja em um grupo de recursos diferente do cofre, tenho permissão para excluir o runbook se não houver outro cofre para especificar?

O runbook personalizado criado é uma ferramenta e é seguro excluir se o mesmo não for mais necessário.

### <a name="can-i-replicate-vms-to-another-subscription"></a>É possível replicar VMs para outra assinatura?

Sim, você pode replicar VMs do Azure para uma assinatura diferente no mesmo locatário do Azure AD.

Configure a recuperação de desastre [entre assinaturas](https://azure.microsoft.com/blog/cross-subscription-dr) selecionando outra assinatura no momento da replicação.

### <a name="can-i-replicate-zone-pinned-azure-vms-to-another-region"></a>Posso replicar VMs do Azure fixadas em zonas para outra região?

Sim, é possível [replicar VMs fixadas em zonas](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region) para outra região.

### <a name="can-i-exclude-disks"></a>É possível excluir discos?

Sim, é possível excluir discos no momento da proteção usando o PowerShell. Para saber mais, veja [como excluir discos da replicação](azure-to-azure-exclude-disks.md).

### <a name="can-i-add-new-disks-to-replicated-vms-and-enable-replication-for-them"></a>É possível adicionar novos discos a VMs replicadas e habilitar a replicação para eles?

Sim, é possível adicionar novos discos a VMs replicadas e habilitar a replicação para eles nas VMs do Azure com discos gerenciados. Quando você adiciona um novo disco a uma VM do Azure que está habilitada para replicação, a integridade da replicação para a VM mostra um aviso. Esse aviso informa que um ou mais discos na VM estão disponíveis para proteção. Você pode habilitar a replicação para os discos adicionados.

- Se você habilitar a proteção para os discos adicionados, o aviso desaparecerá após a replicação inicial.
- Se você não habilitar a replicação para o disco, poderá ignorar o aviso.
- Se você fizer failover de uma VM que tem um disco adicionado e replicação habilitada, haverá pontos de replicação. Os pontos de replicação mostrarão os discos que estão disponíveis para recuperação.

Por exemplo, digamos que uma VM tenha um só disco e você adicione um novo. Talvez um ponto de replicação tenha sido criado antes de você adicionar o disco. Esse ponto de replicação mostrará que tem "1 de 2 discos".

O Site Recovery não é compatível com a "remoção a quente" de um disco de uma VM replicada. Se você remover um disco de VM, será necessário desabilitar e reabilitar a replicação para a VM.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?

A replicação é contínua quando você está replicando VMs do Azure para outra região do Azure. Para saber mais, veja [Arquitetura de replicação do Azure para o Azure](./azure-to-azure-architecture.md#replication-process).

### <a name="can-i-replicate-virtual-machines-within-a-region-i-need-this-functionality-to-migrate-vms"></a>Posso replicar máquinas virtuais dentro de uma região? Preciso dessa funcionalidade para migrar VMs.

Você não pode usar uma solução de recuperação de disco de Azure para Azure para replicar VMs em uma região.

### <a name="can-i-replicate-vm-instances-to-any-azure-region"></a>É possível replicar instâncias de VM em qualquer região do Azure?

Com o Site Recovery, é possível replicar e recuperar VMs entre duas regiões dentro do mesmo cluster geográfico. Clusters geográficos são definidos pensando em latência de dados e soberania. Para obter mais informações, veja [Matriz de suporte regional](./azure-to-azure-support-matrix.md#region-support) do Site Recovery.

### <a name="does-site-recovery-require-internet-connectivity"></a>O Site Recovery requer conectividade com a Internet?

Não, o Site Recovery não requer conectividade com a Internet. Porém, ele requer acesso aos intervalos de IP e URLs do Site Recovery, conforme mencionado [Rede na recuperação de site de VM do Azure](./azure-to-azure-about-networking.md#outbound-connectivity-for-urls).

### <a name="can-i-replicate-an-application-that-has-a-separate-resource-group-for-separate-tiers"></a>É possível replicar o aplicativo que tem um grupo de recursos separado para camadas diferentes?

Sim, é possível replicar o aplicativo e manter a configuração de recuperação de desastre em um grupo de recursos separado também.

Por exemplo, se você tiver um aplicativo com aplicativo, banco de dados e Web de cada camada no grupo de recursos separado, será necessário clicar no [assistente de replicação](./azure-to-azure-how-to-enable-replication.md#enable-replication) três vezes para proteger todas as camadas. O Site Recovery replicará essas três camadas em três grupos de recursos diferentes.

### <a name="can-i-move-storage-accounts-across-resource-groups"></a>É possível mover contas de armazenamento entre grupos de recursos?

Não. Esse é um cenário sem suporte. No entanto, se você mover acidentalmente as contas de armazenamento para um grupo de recursos diferente e excluir o grupo de recursos original, poderá criar um grupo de recursos com o mesmo nome do antigo e, em seguida, mover a conta de armazenamento para o novo.

## <a name="replication-policy"></a>Política de replicação

### <a name="what-is-a-replication-policy"></a>O que é uma política de replicação?

Uma política de replicação define as configurações para o histórico de retenção dos pontos de recuperação. A política também define a frequência de instantâneos consistentes em termos de aplicativo. Por padrão, o Azure Site Recovery cria uma nova política de replicação com configurações padrão de:

- 24 horas para o histórico de retenção dos pontos de recuperação.
- 60 minutos para a frequência dos instantâneos consistentes em termos de aplicativo.

[Saiba mais sobre as configurações de replicação](./azure-to-azure-tutorial-enable-replication.md#configure-replication-settings).

### <a name="what-is-a-crash-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de falha?

Um ponto de recuperação consistente em termos de falhas tem os dados em disco como se você tivesse recebido o cabo de alimentação do servidor durante o instantâneo. Ele não inclui o que estava na memória quando o instantâneo foi tirado.

Atualmente, a maioria dos aplicativos pode recuperar-se bem de instantâneos consistente em termos de falha. Um ponto de recuperação consistente em termos de falha geralmente é suficiente para sistemas operacionais sem banco de dados e aplicativos como servidores, servidores DHCP, servidores de impressão.

### <a name="what-is-the-frequency-of-crash-consistent-recovery-point-generation"></a>Qual é a frequência de geração de ponto de recuperação consistente com a falha?

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos.

### <a name="what-is-an-application-consistent-recovery-point"></a>O que é ponto de recuperação consistente em termos de aplicativo?

Pontos de recuperação consistentes em termos de aplicativo são criados com base em instantâneos consistentes em termos de aplicativo. Pontos de recuperação consistentes em termos de aplicativo capturam os mesmos dados de instantâneos consistentes em termos de falhas, além de todos os dados na memória e todas as transações em andamento.

Devido a seu conteúdo adicional, instantâneos de aplicativo consistente são os mais envolvidos e levam mais tempo para executar. Recomendamos pontos de recuperação consistentes em termos de aplicativo para sistemas operacionais de banco de dados como o SQL Server.

### <a name="what-is-the-impact-of-application-consistent-recovery-points-on-application-performance"></a>Qual é o impacto de pontos de recuperação consistentes em termos de aplicativo no desempenho do aplicativo?

Os pontos de recuperação consistentes com o aplicativo capturam todos os dados na memória e em processamento. Como os pontos de recuperação capturam esses dados, eles exigem estruturas como o Serviço de Cópias de Sombra de Volume no Windows para desativar o aplicativo. Se o processo de captura for frequente, poderá afetar o desempenho quando a carga de trabalho já estiver ocupada. Não é recomendável usar a frequência baixa para pontos de recuperação consistentes com o aplicativo em cargas de trabalho que não são de banco de dados. Mesmo para a carga de trabalho do banco de dados, 1 hora é suficiente.

### <a name="what-is-the-minimum-frequency-of-application-consistent-recovery-point-generation"></a>Qual é a frequência mínima de geração de ponto de recuperação consistente com o aplicativo?

O Site Recovery pode criar um ponto de recuperação consistente com o aplicativo com frequência mínima de 1 hora.

### <a name="how-are-recovery-points-generated-and-saved"></a>Como os pontos de recuperação são gerados e salvos?

Para entender como Site Recovery gera pontos de recuperação, veja um exemplo de uma política de replicação. Essa política de replicação tem um ponto de recuperação com uma janela de retenção de 24 horas e um instantâneo de frequência consistente com o aplicativo de 1 hora.

O Site Recovery cria um ponto de recuperação consistente em termos de falha a cada 5 minutos. Você não pode alterar essa frequência. Na última hora, você pode escolher entre 12 pontos consistentes com falhas e 1 ponto consistente com o aplicativo. Conforme o tempo decorre, o Site Recovery remove todos os pontos de recuperação além da última hora e salva apenas um ponto de recuperação por hora.

A captura de tela a seguir ilustra o exemplo. Na captura de tela:

- Na última hora, há pontos de recuperação com uma frequência de 5 minutos.
- Depois da última hora, o Site Recovery mantém apenas um ponto de recuperação.

   ![Lista de pontos de recuperação gerados](./media/azure-to-azure-troubleshoot-errors/recoverypoints.png)

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?

O ponto de recuperação mais antigo que você pode usar é de 72 horas.

### <a name="i-have-a-replication-policy-of-24-hours-what-will-happen-if-a-problem-prevents-site-recovery-from-generating-recovery-points-for-more-than-24-hours-will-my-previous-recovery-points-be-lost"></a>Tenho uma política de replicação de 24 horas. O que acontecerá se um problema impedir o Site Recovery de gerar pontos de recuperação por mais de 24 horas? Meus pontos de recuperação anteriores serão perdidos?

Não, o Site Recovery manterá todos os pontos de recuperação anteriores. Dependendo da janela de retenção dos pontos de recuperação, o Site Recovery substituirá o ponto mais antigo somente se ele gerar novos pontos. Devido ao problema, o Site Recovery não pode gerar novos pontos de recuperação. Até que haja novos pontos de recuperação, todos os pontos antigos permanecerão depois que você chegar à janela de retenção.

### <a name="after-replication-is-enabled-on-a-vm-how-do-i-change-the-replication-policy"></a>Após a replicação ser habilitada em uma VM, como fazer para alterar a política de replicação?

Vá para **Cofre do Site Recovery** > **Infraestrutura do Site Recovery** > **Políticas de replicação**. Selecione a política que você deseja editar e salve as alterações. Qualquer alteração também será aplicada a todas as replicações existentes.

### <a name="are-all-the-recovery-points-a-complete-copy-of-the-vm-or-a-differential"></a>Todos os pontos de recuperação são uma cópia completa da VM ou diferencial?

O primeiro ponto de recuperação gerado tem a cópia completa. Os pontos de recuperação sucessivos têm alterações delta.

### <a name="does-increasing-the-retention-period-of-recovery-points-increase-the-storage-cost"></a>Aumentar o período de retenção de pontos de recuperação aumenta o custo de armazenamento?

Sim, se você aumentar o período de retenção de 24 horas para 72 horas, o Site Recovery salvará os pontos de recuperação por mais 48 horas. Adicionar tempo gerará em encargos de armazenamento. Por exemplo, um ponto de recuperação pode ter alterações delta de 10 GB com um custo por GB de US$ 0,16 por mês. Os encargos adicionais seriam de US$ 1,60 × 48 por mês.

### <a name="can-i-enable-replication-with-app-consistency-in-linux-servers"></a>Posso habilitar a replicação com consistência de aplicativo em servidores Linux?

Sim. O Azure Site Recovery para o sistema operacional Linux dá suporte a scripts personalizados de aplicativo para consistência de aplicativo. O script personalizado com pré e pós-opções será usado pelo agente de mobilidade Azure Site Recovery durante a consistência do aplicativo. [Saiba mais](./site-recovery-faq.md#can-i-enable-replication-with-app-consistency-in-linux-servers)

## <a name="multi-vm-consistency"></a>Consistência de várias VMs

### <a name="what-is-multi-vm-consistency"></a>O que é consistência de várias VMs?

A consistência de várias VMs garante que o ponto de recuperação seja consistente em todas as máquinas virtuais replicadas.

O Site Recovery fornece uma opção de **consistência de várias VMs**, que cria um grupo de replicação de todos os computadores.

Quando você faz failover das máquinas virtuais, elas terão pontos de recuperação consistentes com o aplicativo e com falhas.

Veja o tutorial para [habilitar a consistência de várias VMs](./azure-to-azure-tutorial-enable-replication.md#enable-replication-for-a-vm).

### <a name="can-i-fail-over-a-single-virtual-machine-within-a-multi-vm-consistency-replication-group"></a>É possível fazer failover de uma máquina virtual dentro de um grupo de replicação de consistência de várias VMs?

Ao selecionar a opção **Consistência de várias VMs**, você está dizendo que o aplicativo tem uma dependência em todas as máquinas virtuais dentro de um grupo. O failover de uma máquina virtual não é permitido.

### <a name="how-many-virtual-machines-can-i-replicate-as-a-part-of-a-multi-vm-consistency-replication-group"></a>Quantas máquinas virtuais posso replicar como parte de um grupo de replicação de consistência de várias VMs?

Você pode replicar 16 máquinas virtuais ao mesmo tempo em um grupo de replicação.

### <a name="when-should-i-enable-multi-vm-consistency"></a>Quando habilitar a consistência de várias VMs?

Como isso faz uso intenso da CPU, habilitar a consistência de várias VMs pode afetar o desempenho da carga de trabalho. Ela deverá ser usada somente se os computadores estiverem executando a mesma carga de trabalho e você precisar de consistência entre vários computadores. Por exemplo, se você tiver duas instâncias do SQL Server e dois servidores Web em um aplicativo, deverá ter consistência de várias VMs somente para as instâncias do SQL Server.

### <a name="can-you-add-an-already-replicating-vm-to-a-replication-group"></a>É possível adicionar uma VM que já está replicando a um grupo de replicação?
Você pode adicionar uma VM a um novo grupo de replicação ao habilitar a replicação. Também pode adicionar uma VM a um grupo de replicação existente ao habilitar a replicação. No entanto, não pode adicionar uma VM que já está replicando a um grupo de replicação novo ou existente.
 
## <a name="failover"></a>Failover


### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como a capacidade é garantida na região de destino para as VMs do Azure?

A equipe do Site Recovery e equipe de gerenciamento de capacidade do Azure planejam a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery sejam implantadas na região de destino.

### <a name="is-failover-automatic"></a>O failover é automático?

O failover não é automático. Você inicia failovers com um só clique no portal ou usando o [PowerShell](azure-to-azure-powershell.md) para disparar um failover.

### <a name="can-i-keep-a-public-ip-address-after-a-failover"></a>É possível reter um endereço IP público após o failover?

Você não pode manter o endereço IP público do aplicativo de produção após um failover.

Ao usar uma carga de trabalho como parte do processo de failover, você precisa atribuir um recurso de IP público do Azure à carga de trabalho. O recurso de IP público do Azure deve estar disponível na região de destino. Você pode atribuir o recurso de IP público do Azure manualmente ou automatizá-lo com um plano de recuperação. Saiba como [configurar endereços IP públicos após o failover](concepts-public-ip-address-with-site-recovery.md#public-ip-address-assignment-using-recovery-plan).

### <a name="can-i-keep-a-private-ip-address-during-a-failover"></a>É possível reter um endereço IP privado durante o failover?

Sim, é possível reter o endereço IP privado. Por padrão, quando você habilita a recuperação de desastre para VMs do Azure, o Site Recovery cria recursos de destino com base nas configurações do recurso de origem. Nas Máquinas Virtuais do Azure configuradas com endereços IP estáticos, o Site Recovery tentará provisionar o mesmo endereço IP para a VM de destino se ele não estiver em uso.
Saiba mais sobre como [manter os endereços IP durante o failover](site-recovery-retain-ip-azure-vm-failover.md).

### <a name="after-a-failover-why-is-the-server-assigned-a-new-ip-address"></a>Após o failover, por que o servidor recebe um novo endereço IP aleatório?

O Site Recovery tenta fornecer o endereço IP no momento do failover. Se outra máquina virtual estiver usando esse endereço, o Site Recovery definirá o próximo endereço IP disponível como destino.

Saiba mais sobre como [configurar o mapeamento de rede e o endereçamento IP para redes virtuais](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).

### <a name="what-are-latest-lowest-rpo-recovery-points"></a>Quais são os pontos de recuperação **Mais recentes (menor RPO)** ?

A opção **Último (menor RPO)** processa primeiro todos os dados que foram enviados para o Site Recovery. Depois que o serviço processa os dados, ele cria um ponto de recuperação para cada VM antes de fazer failover para a VM. Essa opção fornece o RPO (objetivo de ponto de recuperação) mais baixo. A VM criada após o failover tem todos os dados replicados para o Site Recovery no momento do failover.

### <a name="do-latest-lowest-rpo-recovery-points-have-an-impact-on-failover-rto"></a>Os pontos de recuperação **Mais recentes (menor RPO)** afetam o RTO do failover?

Sim. O Site Recovery processa todos os dados pendentes antes de fazer failover, portanto, essa opção tem um RTO (objetivo de tempo de recuperação) mais alto em comparação a outras.

### <a name="what-does-the-latest-processed-option-in-recovery-points-mean"></a>O que significa a opção **Últimos processados** nos pontos de recuperação?

A opção **Últimos processados** faz failover de todas as VMs no plano para o ponto de recuperação mais recente que o Site Recovery processou. Para ver o último ponto de recuperação de uma VM específica, verifique os **Pontos de Recuperação Mais Recentes** nas configurações da VM. Essa opção fornece um RTO baixo, pois nenhum tempo é gasto no processamento de dados não processados.

### <a name="what-happens-if-my-primary-region-experiences-an-unexpected-outage"></a>O que acontecerá se minha região primária apresentar uma interrupção inesperada?

É possível disparar um failover após a interrupção. O Site Recovery não precisa de conectividade da região primária para fazer failover.

### <a name="what-is-an-rto-of-a-vm-failover"></a>O que é um RTO de um failover de VM?

O Site Recovery tem um [SLA de RTO de 2 horas](https://azure.microsoft.com/support/legal/sla/site-recovery/v1_2/). No entanto, na maioria das vezes, o Site Recovery faz failover de máquinas virtuais em minutos. É possível calcular o RTO acessando os trabalhos de failover, que mostram o tempo necessário para criar a VM. Para o RTO do plano de recuperação, veja a seção abaixo.

## <a name="recovery-plans"></a>Planos de recuperação

### <a name="what-is-a-recovery-plan"></a>O que é um plano de recuperação?

Um plano de recuperação no Site Recovery orquestra a recuperação de failover de VMs. Os planos de recuperação também ajudam a tornar a recuperação precisa, repetível e automatizada de forma consistente. Um plano de recuperação atende às seguintes necessidades:

- Definir um grupo de máquinas virtuais que fazem failover em conjunto
- Definir as dependências entre as máquinas virtuais para que o aplicativo seja executado com precisão
- Automatizar a recuperação juntamente com ações manuais personalizadas para realizar tarefas que não o failover de máquinas virtuais

Saiba mais sobre como [criar planos de recuperação](site-recovery-create-recovery-plans.md).

### <a name="how-is-sequencing-achieved-in-a-recovery-plan"></a>Como o sequenciamento é feito em um plano de recuperação?

Em um plano de recuperação, é possível criar vários grupos para realizar o sequenciamento. Cada grupo faz o failover ao mesmo tempo. As máquinas virtuais que fazem parte do mesmo grupo fazem failover juntas, seguidas por outro grupo. Para saber como modelar um aplicativo usando um plano de recuperação, veja [Sobre planos de recuperação](recovery-plan-overview.md#model-apps).

### <a name="how-can-i-find-the-rto-of-a-recovery-plan"></a>Como localizar o RTO de um plano de recuperação?

Para verificar o RTO de um plano de recuperação, faça um failover de teste para o plano de recuperação e vá para **trabalhos do Site Recovery**.
No exemplo a seguir, confira o trabalho **SAPTestRecoveryPlan**. O trabalho levou 8 minutos e 59 segundos para fazer failover de todas as máquinas virtuais e executar ações especificadas.

![Lista de trabalhos do Site Recovery](./media/azure-to-azure-troubleshoot-errors/recoveryplanrto.PNG)

### <a name="can-i-add-automation-runbooks-to-the-recovery-plan"></a>É possível adicionar runbooks de automação ao plano de recuperação?

Sim, é possível integrar runbooks de Automação do Azure ao plano de recuperação. Saiba mais sobre como [adicionar runbooks da Automação do Azure](site-recovery-runbook-automation.md).

## <a name="reprotection-and-failback"></a>Nova proteção e failback

### <a name="i-failed-over-from-the-primary-region-to-a-disaster-recovery-region-are-vms-in-a-dr-region-protected-automatically"></a>Fiz failover da região primária para uma região de recuperação de desastre. As VMs em uma região de DR são protegidas automaticamente?

Não. Ao fazer [failover](./azure-to-azure-tutorial-failover-failback.md) das VMs do Azure de uma região para outra, as VMs iniciam na região de DR em um estado desprotegido. Para fazer failback das VMs na região primária, é necessário [proteger novamente](./azure-to-azure-how-to-reprotect.md) as VMs na região secundária.

### <a name="at-the-time-of-reprotection-does-site-recovery-replicate-complete-data-from-the-secondary-region-to-the-primary-region"></a>No momento da nova proteção, o Site Recovery replica dados completos da região secundária para a região primária?

Depende da situação. Se a região de origem da VM existir, apenas as alterações entre o disco de origem e o disco de destino serão sincronizadas. O Site Recovery calcula os diferenciais comparando os discos e, em seguida, transfere os dados. Esse processo normalmente leva algumas horas. Para saber mais sobre o que acontece durante a nova proteção, veja [Nova proteção de VMs do Azure com failover para a região primária](./azure-to-azure-how-to-reprotect.md#what-happens-during-reprotection).

### <a name="how-much-time-does-it-take-to-fail-back"></a>Quanto tempo demora para fazer failback?

Após a nova proteção, o failback assume a mesma quantidade de tempo que leva para fazer failover da região primária para uma região secundária.

## <a name="capacity"></a><a name="capacity"></a>Capacidade

### <a name="how-is-capacity-ensured-in-the-target-region-for-azure-vms"></a>Como a capacidade é garantida na região de destino para as VMs do Azure?

A equipe do Site Recovery e equipe de gerenciamento de capacidade do Azure planejam a capacidade de infraestrutura suficiente. Quando você inicia um failover, as equipes também ajudam a garantir que as instâncias de VM protegidas pelo Site Recovery sejam implantadas na região de destino.

### <a name="does-site-recovery-work-with-reserved-instances"></a>O Site Recovery funciona com instâncias reservadas?

Sim, você pode comprar [VMs do Azure reservadas](https://azure.microsoft.com/pricing/reserved-vm-instances/) na região de recuperação de desastre e as operações de failover do Site Recovery as usarão. Nenhuma configuração adicional é necessária.

## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?

Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está sendo executado nas VMs. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

O Site Recovery tem os certificados ISO 27001:2013, 27018, HIPAA e DPA. O serviço está passando por avaliações SOC2 e FedRAMP JAB.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?

Sim, a criptografia em trânsito e a [em repouso](../storage/common/storage-service-encryption.md) são compatíveis com o Azure.

## <a name="next-steps"></a>Próximas etapas

- [Examine os requisitos de compatibilidade de Azure para Azure](azure-to-azure-support-matrix.md).
- [Configurar a replicação de Azure para Azure](azure-to-azure-tutorial-enable-replication.md).
- Se você tiver dúvidas após a leitura deste artigo, publique-as na [página de perguntas P e R da Microsoft dos Serviços de Recuperação do Azure](/answers/topics/azure-site-recovery.html).
