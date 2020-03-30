---
title: Perguntas gerais sobre o serviço de recuperação de sites do Azure
description: Este artigo discute questões gerais populares sobre a Recuperação do Site do Azure.
ms.topic: conceptual
ms.date: 1/24/2020
ms.author: raynew
ms.openlocfilehash: a9d0ae4a6e60a72bbb1148aca1a75c44506b2e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257674"
---
# <a name="general-questions-about-azure-site-recovery"></a>Perguntas gerais sobre a recuperação do site do Azure

Este artigo resume perguntas frequentes sobre a Recuperação do Site do Azure. Para cenários específicos revisão desses artigos

- [Perguntas sobre a recuperação de desastres da Azure VM para o Azure](azure-to-azure-common-questions.md)
- [Perguntas sobre recuperação de desastres vmware VM para o Azure](vmware-azure-common-questions.md)
- [Perguntas sobre recuperação de desastres hyper-V VM para o Azure](hyper-v-azure-common-questions.md)
 
## <a name="general"></a>Geral

### <a name="what-does-site-recovery-do"></a>O que faz o Site Recovery?
O Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando e automatizando a replicação de VMs do Azure entre regiões, máquinas virtuais locais e servidores físicos no Azure e máquinas locais em um datacenter secundário. [Saiba mais](site-recovery-overview.md).

### <a name="can-i-protect-a-virtual-machine-that-has-a-docker-disk"></a>Posso proteger uma máquina virtual que tem um disco Docker?

Não. Esse é um cenário sem suporte.

## <a name="service-providers"></a>Provedores de serviço

### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Sou um provedor de serviço. O Site Recovery funciona com modelos de infraestrutura compartilhados e dedicados?
Sim, o Site Recovery dá suporte aos modelos de infraestrutura dedicados e compartilhados.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Para o provedor de serviço, a identidade do meu locatário é compartilhada com o serviço da Recuperação de Site?
Não. A identidade do locatário permanece anônima. Seus locatários não precisam acessar o Portal da Recuperação de Site. Somente o administrador do provedor de serviços interage com o portal.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Os dados de aplicativo do locatário vão para o Azure?
Durante a replicação entre os sites de propriedade do provedor de serviços, os dados de aplicativo nunca vão para o Azure. Os dados são criptografados em trânsito e replicados diretamente entre os sites do provedor de serviço.

Se você estiver replicando no Azure, os dados de aplicativo são enviados para o armazenamento do Azure, mas não para o serviço de Recuperação de Site. Os dados são criptografados em trânsito e permanecem criptografados no Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Meus locatários receberão alguma fatura relativa a serviços do Azure?
Não. A relação de cobrança do Azure é direta com o provedor de serviços. Os provedores de serviços são responsáveis por gerar faturas específicas para seus locatários.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Ao replicar no Azure, precisamos executar máquinas virtuais no Azure o tempo todo?
Não, os dados são replicados para o armazenamento do Azure em sua assinatura. Quando você executa um failover de teste (análise de DR) ou um failover real, o Site Recovery cria automaticamente máquinas virtuais em sua assinatura.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Vocês garantem o isolamento no nível de locatário quando faço a replicação no Azure?
Sim.

### <a name="what-platforms-do-you-currently-support"></a>A quais plataformas vocês oferecem suporte atualmente?
Damos suporte ao Azure Pack, ao Sistema de Plataforma de Nuvem e a implantações baseadas no System Center (2012 e posterior). [Saiba mais](https://technet.microsoft.com/library/dn850370.aspx) sobre a integração do Azure Pack e da Recuperação de Site.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Você também dá suporte implantações únicas de Azure Pack e servidor VMM?
Sim, você pode replicar máquinas virtuais Hyper-V para o Azure ou entre os sites de provedor de serviços.  Observe que, se você replicar entre sites do provedor de serviços, a integração de runbook do Azure não estará disponível.

## <a name="pricing"></a>Preços

### <a name="where-can-i-find-pricing-information"></a>Onde posso encontrar informações sobre preços?
Revisar detalhes [dos preços de recuperação do site.](https://azure.microsoft.com/pricing/details/site-recovery/)


### <a name="how-can-i-calculate-approximate-charges-during-the-use-of-site-recovery"></a>Como posso calcular preços aproximados durante o uso do Site Recovery?

Você pode usar a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar custos enquanto usa o Site Recovery.

Para uma estimativa detalhada dos custos, execute a ferramenta de planejador de implantação para [VMware](https://aka.ms/siterecovery_deployment_planner) ou [Hyper-V](https://aka.ms/asr-deployment-planner)e use o [relatório de estimativa de custos](https://aka.ms/asr_DP_costreport).


### <a name="managed-disks-are-now-used-to-replicate-vmware-vms-and-physical-servers-do-i-incur-additional-charges-for-the-cache-storage-account-with-managed-disks"></a>Os discos gerenciados agora são usados para replicar VMware VMware e servidores físicos. Incorrer em cobranças adicionais para a conta de armazenamento de cache com discos gerenciados?

Não, não há taxas adicionais para cache. Quando você replica para a conta de armazenamento padrão, esse armazenamento de cache faz parte da mesma conta de armazenamento alvo.

### <a name="i-have-been-an-azure-site-recovery-user-for-over-a-month-do-i-still-get-the-first-31-days-free-for-every-protected-instance"></a>Sou usuário do Azure Site Recovery há mais de um mês. Eu ainda terei os primeiros 31 dias gratuitos para todas as instâncias protegidas?

Sim. Nenhuma instância protegida gera cobranças do Azure Site Recovery durante os primeiros 31 dias. Por exemplo, se você esteve protegendo 10 instâncias nos últimos 6 meses e conectou uma 11ª instância ao Azure Site Recovery, não há cobranças pela 11ª instância nos primeiros 31 dias. As primeiras 10 instâncias continuam a incorrer em cobranças de recuperação do site do Azure desde que foram protegidas por mais de 31 dias.

### <a name="during-the-first-31-days-will-i-incur-any-other-azure-charges"></a>Durante os primeiros 31 dias, serei cobrado por outras tarifas do Azure?

Sim, mesmo que o Site Recovery esteja gratuito durante os primeiros 31 dias de uma instância protegida, você pode incorrer em cobranças pelo Azure Storage, transações de armazenamento e transferência de dados. Uma máquina virtual recuperada também poderá gerar encargos de computação do Azure.


### <a name="is-there-a-cost-associated-to-perform-disaster-recovery-drillstest-failover"></a>Existe um custo associado para realizar exercícios de recuperação de desastres/failover de teste?

Não há nenhum custo separado para análise de recuperação de desastres. Haverá taxas de cálculo após a criação da VM após o failover do teste.



## <a name="security"></a>Segurança

### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Os dados de replicação são enviados para o serviço de Recuperação de Site?
Não, o Site Recovery não intercepta dados replicados e não tem informações sobre o que está em execução nas máquinas virtuais ou nos servidores físicos.
Os dados de replicação são trocados entre hosts locais do Hyper-V, hipervisores VMware ou servidores físicos e o armazenamento do Azure ou seu site secundário. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.  

O Site Recovery é certificado pela ISO 27001:2013, 27018, HIPAA, DPA e está em processo de conclusão de avaliação dos padrões SOC2 e FedRAMP JAB.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Para fins de conformidade, até os nossos metadados locais têm que permanecer na mesma região geográfica. O Site Recovery pode nos ajudar?
Sim. Quando você cria um cofre do Site Recovery em uma região de sua escolha, garantimos que todos os metadados de que precisamos para habilitar e administrar a replicação e o failover permaneçam dentro dos limites geográficos da região.

### <a name="does-site-recovery-encrypt-replication"></a>O Site Recovery criptografa a replicação?
Para máquinas virtuais e servidores físicos que estão sendo replicados entre sites locais, há suporte para a criptografia em trânsito. Para máquinas virtuais e servidores físicos que estão sendo replicados no Azure, há suporte tanto para a criptografia em trânsito quanto para a [criptografia em repouso (no Azure)](https://docs.microsoft.com/azure/storage/storage-service-encryption).

### <a name="how-can-i-enforce-tls-12-on-all-on-premises-azure-site-recovery-components"></a>Como posso impor o TLS 1.2 em todos os componentes de recuperação do Site Azure no local?
Os agentes de mobilidade instalados nos itens replicados comunicam-se ao Process Server apenas no TLS 1.2. No entanto, a comunicação do Configuration Server para o Azure e do Process Server para o Azure pode estar no TLS 1.1 ou 1.0. Siga a [orientação](https://support.microsoft.com/en-us/help/3140245/update-to-enable-tls-1-1-and-tls-1-2-as-default-secure-protocols-in-wi) para impor o TLS 1.2 em todos os servidores de configuração e servidores de processo configurados por você.


## <a name="disaster-recovery"></a>Recuperação de desastre

### <a name="what-can-site-recovery-protect"></a>O que o Site Recovery pode proteger?
* **VMs do Azure**: o Site Recovery pode replicar qualquer carga de trabalho em execução em uma VM do Azure com suporte
* **Máquinas virtuais do Hyper-V**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM Hyper-V.
* **Servidores físicos**: o Site Recovery pode proteger servidores físicos com o Windows ou o Linux em execução.
* **Máquinas virtuais VMware**: o Site Recovery pode proteger qualquer carga de trabalho em execução em uma VM VMware.

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quais cargas de trabalho posso proteger com o Site Recovery?
Você pode usar a Recuperação de Site para proteger a maioria das cargas de trabalho em execução em uma VM ou em um servidor físico com suporte. O Site Recovery também dá suporte para a replicação com reconhecimento de aplicativo, para que os aplicativos possam ser recuperados para um estado inteligente. Ele se integra aos aplicativos da Microsoft, incluindo o SharePoint, Exchange, Dynamics, SQL Server e Active Directory, e trabalha em conjunto com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat. [Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.

### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Posso gerenciar a recuperação de desastre nas minhas filiais com o Site Recovery?
Sim. Ao usar o Site Recovery para administrar a replicação e o failover em suas filiais, você poderá visualizar e administrar unificadamente todas as suas cargas de trabalho corporativas em um local centralizado. Você pode executar failovers e administrar a recuperação de desastre de todas as ramificações do escritório principal facilmente, sem precisar fazer visitas locais.


### <a name="is-disaster-recovery-supported-for-azure-vms"></a>A recuperação de desastres é suportada para VMs Azure?

Sim, a Recuperação do Site suporta desastres para As VMs do Azure entre as regiões do Azure. [Revise perguntas comuns](azure-to-azure-common-questions.md) sobre a recuperação de desastres do Azure VM.

### <a name="is-disaster-recovery-supported-for-vmware-vms"></a>A recuperação de desastres é suportada para VMs VMware?

Sim, a Recuperação do Site suporta a recuperação de desastres de VMms VMware no local. [Revise perguntas comuns](vmware-azure-common-questions.md) para recuperação de desastres de VMware VMs.

### <a name="is-disaster-recovery-supported-for-hyper-v-vms"></a>A recuperação de desastres é suportada para Hiper-VVs?
Sim, a Recuperação do Site suporta a recuperação de desastres de VMs hiper-V no local. [Revise perguntas comuns](hyper-v-azure-common-questions.md) para recuperação de desastres de VMs Hyper-V.

## <a name="is-disaster-recovery-supported-for-physical-servers"></a>A recuperação de desastres é suportada para servidores físicos?
Sim, o Site Recovery suporta a recuperação de desastres de servidores físicos locais executando o Windows e o Linux para o Azure ou para um site secundário. Conheça os requisitos para recuperação de desastres no [Azure](vmware-physical-azure-support-matrix.md#replicated-machines)e em[um site secundário.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
Observe que os servidores físicos serão executados como VMs no Azure após failover. O failback do Azure para um servidor físico no local não é suportado no momento. Você só pode falhar de volta a uma máquina virtual VMware.





## <a name="replication"></a>Replicação

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>É possível replicar em uma VPN site a site para o Azure?
O Azure Site Recovery replica dados em uma conta de armazenamento Do Zure ou discos gerenciados, em um ponto final público. A replicação não ocorre através de uma VPN site a site. 

### <a name="why-cant-i-replicate-over-vpn"></a>Por que não é possível replicar em VPN?

Quando você replica o Azure, o tráfego de replicação chega aos pontos finais públicos de um Armazenamento Azure. Assim, você só pode replicar pela internet pública ou via ExpressRoute (peering microsoft ou um peering público existente).

### <a name="can-i-use-riverbed-steelheads-for-replication"></a>Posso usar Riverbed SteelHeads para replicação?

Nosso parceiro, Riverbed, fornece orientações detalhadas sobre como trabalhar com o Azure Site Recovery. Revise o [guia de soluções](https://community.riverbed.com/s/article/DOC-4627).

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>É possível usar o ExpressRoute para replicar máquinas virtuais no Azure?
Sim, o [ExpressRoute pode ser usado](concepts-expressroute-with-site-recovery.md) para replicar máquinas virtuais locais no Azure.

- O Azure Site Recovery replica dados em um Armazenamento Azure em um ponto final público. Você precisa configurar o [peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering) ou usar um [peering público](../expressroute/about-public-peering.md) existente (preterido por novos circuitos) para usar o ExpressRoute para replicação de recuperação de site.
- Emparelhamento da Microsoft é o domínio de roteamento recomendado para replicação.
- Não há suporte para a replicação sobre emparelhamento privado.
- Se você estiver protegendo máquinas VMware ou máquinas físicas, certifique-se de que os [requisitos de rede](vmware-azure-configuration-server-requirements.md#network-requirements) para servidor de configuração também sejam atendidos. A conectividade com URLs específicos é necessária pelo Configuration Server para orquestração da replicação de recuperação de site. O ExpressRoute não pode ser usado para essa conectividade.
- Após o failover das máquinas virtuais para uma rede virtual do Azure, é possível acessá-las usando a configuração de [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#privatepeering) com a rede virtual do Azure.


### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-or-managed-disk-do-i-need"></a>Se eu replicar para o Azure, que tipo de conta de armazenamento ou disco gerenciado eu preciso?

Você precisa de um armazenamento LRS ou GRS. É recomendável usar GRS para que os dados sejam resilientes caso ocorra uma interrupção regional, ou se a região principal não puder ser recuperada. A rede deve estar na mesma região do que o cofre dos Serviços de Recuperação. O Armazenamento Premium tem suporte para VM do VMware, VM do Hyper-V e replicação de servidores físicos, quando você implantar o Site Recovery no Portal do Azure. Os discos gerenciados só suportam LRS.

### <a name="how-often-can-i-replicate-data"></a>Com que frequência posso replicar dados?
* **Hiper-V:** Os Hiper-VMs podem ser replicados a cada 30 segundos (exceto para armazenamento premium), cinco minutos ou 15 minutos.
* **VMs azure, VMs VMware, servidores físicos:** Uma freqüência de replicação não é relevante aqui. A replicação é contínua.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Posso estender a replicação do site de recuperação existente para um site terciário?
Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Posso fazer uma replicação offline na primeira vez em que replicar no Azure?
Não há suporte para isso. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Posso excluir discos específicos da replicação?
Haverá suporte para isso quando você estiver replicando VMs VMware e VMs do Hyper-V no Azure usando o Portal do Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Posso replicar máquinas virtuais com discos dinâmicos?
Os discos dinâmicos são suportados ao replicar máquinas virtuais Hyper-V e ao replicar VMs e máquinas físicas do VMware ao Azure. O disco do sistema operacional deve ser um disco básico.


### <a name="can-i-throttle-bandwidth-allotted-for-replication-traffic"></a>Posso acelerar a largura de banda alocada para o tráfego de replicação?
Sim. Você pode ler mais sobre o estrangulamento da largura de banda nestes artigos:

* [Planejamento de capacidade para a replicação de VMs VMware e servidores físicos](site-recovery-plan-capacity-vmware.md)
* [Planejamento de capacidade para a replicação de VMs Hyper-V para o Azure](site-recovery-capacity-planning-for-hyper-v-replication.md)



## <a name="failover"></a>Failover
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-vms-after-failover"></a>Se eu estou falhando com o Azure, como acessaras VMs do Azure após failover?

Você pode acessar as VMs do Azure em uma conexão segura da Internet, em uma VPN site a site ou na Azure ExpressRoute. Você precisa preparar uma série de coisas para se conectar. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).


### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Se eu fizer failover no Azure, como o Azure poderá garantir a resiliência dos meus dados?
O Azure foi desenvolvido para resiliência. A Recuperação do Site já foi projetada para failover em um data center secundário do Azure, de acordo com o SLA do Azure. Se isso acontecer, faremos com que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o cofre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Ao replicar entre dois datacenters, o que acontece se meu datacenter primário sofrer uma interrupção inesperada?
Você pode disparar um failover não planejado do site secundário. O Site Recovery não precisa de conectividade do local primário para fazer o failover.

### <a name="is-failover-automatic"></a>O failover é automático?
O failover não é automático. Você inicia failovers com um único clique no portal ou pode usar o [Site Recovery PowerShell](/powershell/module/az.recoveryservices) para ativar um failover. O failback é uma ação simples no portal do Site Recovery.

Para automatizar, você pode usar o Orchestrator ou o Operations Manager local para detectar a falha da máquina virtual e disparar o failover usando o SDK.

* [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
* [Saiba mais](site-recovery-failover.md) sobre failover.
* [Saiba mais](site-recovery-failback-azure-to-vmware.md) como realizar failback das VMs VMware e servidores físicos

### <a name="if-my-on-premises-host-is-not-responding-or-crashed-can-i-fail-back-to-a-different-host"></a>Se meu hospedeiro não está respondendo ou caiu, posso falhar de volta para um hospedeiro diferente?
Sim, você pode usar a recuperação em uma localização alternativa para fazer failback para um host diferente do Azure.

* [Para máquinas virtuais VMware](concepts-types-of-failback.md#alternate-location-recovery-alr)
* [Para máquinas virtuais Hyper-V](hyper-v-azure-failback.md#fail-back-to-an-alternate-location)

## <a name="automation"></a>Automação

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Posso automatizar os cenários do Site Recovery com um SDK?
Sim. Você pode automatizar fluxos de trabalho do Site Recovery usando a API Rest, o PowerShell ou o SDK do Azure. Cenários com suporte no momento para implantar a Recuperação de Site usando o PowerShell:

* [Replicar VMs Hyper-V em nuvens VMM para o Azure PowerShell do Resource Manager](hyper-v-vmm-powershell-resource-manager.md)
* [Replicar VMs Hyper-V sem VMM para o Azure PowerShell do Resource Manager](hyper-v-azure-powershell-resource-manager.md)
* [Replicar o VMware no Azure com o Gerenciador de Recursos do PowerShell](vmware-azure-disaster-recovery-powershell.md)

## <a name="componentprovider-upgrade"></a>Atualização de componentes/provedores

### <a name="where-can-i-find-the-release-notesupdate-rollups-of-site-recovery-upgrades"></a>Onde posso encontrar as notas de versão/upgrades de atualização de recuperação de site

[Conheça](site-recovery-whats-new.md) novas atualizações e [obtenha informações sobre rollup](service-updates-how-to.md).

## <a name="next-steps"></a>Próximas etapas
* Leia a [visão geral do Site Recovery](site-recovery-overview.md)

