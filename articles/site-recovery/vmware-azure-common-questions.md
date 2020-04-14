---
title: Perguntas comuns sobre recuperação de desastres da VMware com a recuperação do site do Azure
description: Obtenha respostas para perguntas comuns sobre a recuperação de desastres de VMms VMware no local para o Azure usando o Azure Site Recovery.
ms.date: 11/14/2019
ms.topic: conceptual
ms.openlocfilehash: d551cef7037c0b6d7286cbb4b70d8f7a8f7f5cae
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259503"
---
# <a name="common-questions-about-vmware-to-azure-replication"></a>Perguntas comuns sobre a replicação do VMware para Azure

Este artigo responde a perguntas comuns que podem surgir quando você implantar a recuperação de desastres de máquinas virtuais VMware (VMs) no local para o Azure.

## <a name="general"></a>Geral

### <a name="what-do-i-need-for-vmware-vm-disaster-recovery"></a>O que preciso para a recuperação de desastres vMware VM?

[Conheça os componentes envolvidos](vmware-azure-architecture.md) na recuperação de desastres de VMware VMs.

### <a name="can-i-use-site-recovery-to-migrate-vmware-vms-to-azure"></a>Posso usar o Site Recovery para migrar VMware VMs para o Azure?

Sim. Além de usar o Site Recovery para configurar a recuperação completa de desastres para VMs VMware, você também pode usar o Site Recovery para migrar vMs VMware no local para o Azure. Neste cenário, você replica vms VMware no local para o Armazenamento Azure. Em seguida, você faz failover do local para o Azure. Após o failover, os aplicativos e as cargas de trabalho estarão disponíveis e em execução nas VMs do Azure. O processo é como configurar a recuperação completa de desastres, exceto que em uma migração você não pode falhar de volta do Azure.

### <a name="does-my-azure-account-need-permissions-to-create-vms"></a>Minha conta do Azure precisa de permissões para criar VMs?

Se você é um administrador da assinatura, tem as permissões de replicação necessárias. Se você não é um administrador, você precisa de permissões para tomar essas ações:

- Crie uma VM azure no grupo de recursos e na rede virtual que você especifica quando configura a recuperação do site.
- Escreva para a conta de armazenamento selecionada ou disco gerenciado com base em sua configuração.

[Saiba mais](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) sobre as permissões necessárias.

### <a name="what-applications-can-i-replicate"></a>Quais aplicativos posso replicar?

Você pode replicar qualquer aplicativo ou carga de trabalho em execução em uma VMware VMm que atenda aos [requisitos de replicação](vmware-physical-azure-support-matrix.md#replicated-machines).

- O Site Recovery suporta a replicação com reconhecimento de aplicativos, para que os aplicativos possam ser reprovados e com falha de volta a um estado inteligente.
- A Recuperação do Site integra-se aos aplicativos da Microsoft, como SharePoint, Exchange, Dynamics, SQL Server e Active Directory. Também trabalha em estreita colaboração com os principais fornecedores, incluindo Oracle, SAP, IBM e Red Hat.

[Saiba mais](site-recovery-workload.md) sobre a proteção de carga de trabalho.

### <a name="can-i-use-a-guest-os-server-license-on-azure"></a>Posso usar uma licença de servidor de sO convidado no Azure?

Sim, os clientes do Microsoft Software Assurance podem usar o [Benefício Híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) para economizar nos custos de licenciamento para computadores do Windows Server que são migrados para o Azure ou usar o Azure para a recuperação de desastre.

## <a name="security"></a>Segurança

### <a name="what-access-to-vmware-servers-does-site-recovery-need"></a>Que acesso aos servidores VMware o Site Recovery precisa?

O Site Recovery precisa de acesso aos servidores VMware para:

- Configure um VMware VMM executando o servidor de configuração de recuperação do site.
- Descobrir automaticamente as VMs para replicação.

### <a name="what-access-to-vmware-vms-does-site-recovery-need"></a>Que acesso às VMs VMware precisa de recuperação de site?

- Para replicar, uma VMware VMm deve ter o serviço de mobilidade de recuperação do site instalado e executado. Você pode implantar a ferramenta manualmente ou pode especificar que a Recuperação do Site faça uma instalação push do serviço quando habilitar a replicação de uma VM.
- Durante a replicação, as VMs comunicam-se com o Site Recovery da seguinte maneira:
    - As VMs se comunicam com o servidor de configuração na porta HTTPS 443 para o gerenciamento de replicação.
    - As VMs enviam dados de replicação para o servidor de processo na porta HTTPS 9443. (Esta configuração pode ser modificada.)
    - Se você habilitar a consistência de várias VMs, as VMs irão comunicar-se pela porta 20004.

### <a name="is-replication-data-sent-to-site-recovery"></a>Os dados de replicação são enviados para o Site Recovery?

Não, a Recuperação do Site não intercepta dados replicados e não tem nenhuma informação sobre o que está sendo executado em suas VMs. Os dados de replicação são trocados entre hipervisores VMware e Armazenamento Azure. O Site Recovery não tem a capacidade de interceptar os dados. Somente os metadados necessários para administrar a replicação e o failover é que são enviados para o serviço de Recuperação de Site.

A Recuperação do Site é certificada para ISO 27001:2013 e 27018, HIPAA e DPA. Está no processo de avaliações soc2 e FedRAMP JAB.

## <a name="pricing"></a>Preços

### <a name="how-do-i-calculate-approximate-charges-for-vmware-disaster-recovery"></a>Como calcular taxas aproximadas para recuperação de desastres VMware?

Use a [calculadora de preços](https://aka.ms/asr_pricing_calculator) para estimar custos enquanto usa a Recuperação do Site.

Para uma estimativa detalhada dos custos, execute a ferramenta de planejador de implantação para [VMware](https://aka.ms/siterecovery_deployment_planner) e use o [relatório de estimativa de custos](https://aka.ms/asr_DP_costreport).

### <a name="is-there-any-difference-in-cost-between-replicating-to-storage-or-directly-to-managed-disks"></a>Há alguma diferença de custo entre a replicação ao armazenamento ou diretamente aos discos gerenciados?

Os discos gerenciados são carregados ligeiramente diferente das contas de armazenamento. [Saiba mais](https://azure.microsoft.com/pricing/details/managed-disks/) sobre preços de discos gerenciados.

### <a name="is-there-any-difference-in-cost-when-replicating-to-general-purpose-v2-storage-account"></a>Existe alguma diferença de custo ao replicar a conta de armazenamento V2 do General Purpose?

Você normalmente verá um aumento no custo de transações incorrido em contas de armazenamento GPv2, uma vez que a recuperação do site do Azure é pesada. [Leia mais](../storage/common/storage-account-upgrade.md#pricing-and-billing) para estimar a mudança.

## <a name="mobility-service"></a>Serviço de mobilidade

### <a name="where-can-i-find-the-mobility-service-installers"></a>Onde é possível localizar os instaladores do Serviço de Mobilidade?

Os instaladores estão na pasta %ProgramData%\ASR\home\svsystems\pushinstallsvc\repositório no servidor de configuração.

## <a name="how-do-i-install-the-mobility-service"></a>Como fazer para instalar o Serviço Mobility?

Em cada VM que você deseja replicar, instale o serviço por um dos vários métodos:

- [Instalação de push](vmware-physical-mobility-service-overview.md#push-installation)
- [Instalação manual](vmware-physical-mobility-service-overview.md#install-the-mobility-service-using-ui) da UI ou PowerShell
- Implantação usando uma ferramenta de implantação, como [o Gerenciador de Configuração](vmware-azure-mobility-install-configuration-mgr.md)

## <a name="managed-disks"></a>Discos gerenciados

### <a name="where-does-site-recovery-replicate-data-to"></a>Para onde o Site Recovery replica dados?

A Recuperação do Site replica VMMs vMware e servidores físicos no Local para discos gerenciados no Azure.

- O servidor do processo de recuperação do site grava registros de replicação em uma conta de armazenamento de cache na região de destino.
- Esses logs são usados para criar pontos de recuperação em discos gerenciados pelo Azure que têm prefixo de **disco assada**.
- Quando ocorre failover, o ponto de recuperação selecionado é usado para criar um novo disco gerenciado de destino. Este disco gerenciado é anexado à VM no Azure.
- As VMs que foram replicadas anteriormente em uma conta de armazenamento (antes de março de 2019) não são afetadas.

### <a name="can-i-replicate-new-machines-to-storage-accounts"></a>Posso replicar novas máquinas em contas de armazenamento?

Não. A partir de março de 2019, no portal Azure, você pode replicar apenas em discos gerenciados pelo Azure.

A replicação de novas VMs para uma conta de armazenamento está disponível apenas usando o PowerShell ou a API REST (versão 2018-01-10 ou 2016-08-10).

### <a name="what-are-the-benefits-of-replicating-to-managed-disks"></a>Quais são os benefícios de replicar em discos gerenciados?

[Saiba como](https://azure.microsoft.com/blog/simplify-disaster-recovery-with-managed-disks-for-vmware-and-physical-servers/) A recuperação do site simplifica a recuperação de desastres com discos gerenciados.

### <a name="can-i-change-the-managed-disk-type-after-a-machine-is-protected"></a>Posso alterar o tipo de disco gerenciado depois que uma máquina é protegida?

Sim, você pode facilmente [alterar o tipo de disco gerenciado](https://docs.microsoft.com/azure/virtual-machines/windows/convert-disk-storage) para replicação contínua. Antes de alterar o tipo, certifique-se de que nenhuma URL de assinatura de acesso compartilhado seja gerada no disco gerenciado:

1. Vá para o recurso **Disco Gerenciado** no portal DoZure e verifique se você tem um banner de URL de assinatura de acesso compartilhado na lâmina **Visão Geral.**
1. Se o banner estiver presente, selecione-o para cancelar a exportação em andamento.
1. Mude o tipo do disco nos próximos minutos. Se você alterar o tipo de disco gerenciado, aguarde que novos pontos de recuperação sejam gerados pelo Azure Site Recovery.
1. Use os novos pontos de recuperação para qualquer failover ou failover de teste no futuro.

### <a name="can-i-switch-replication-from-managed-disks-to-unmanaged-disks"></a>Posso mudar a replicação de discos gerenciados para discos não gerenciados?

Não. A mudança de gerenciado para não gerenciado não é suportada.

## <a name="replication"></a>Replicação

### <a name="what-are-the-replicated-vm-requirements"></a>Quais são os requisitos de VM replicadas?

[Saiba mais](vmware-physical-azure-support-matrix.md#replicated-machines) sobre os requisitos de suporte para VMware VMs e servidores físicos.

### <a name="how-often-can-i-replicate-to-azure"></a>Com que frequência é possível replicar para o Azure?

A replicação é contínua ao replicar VMs VMware para o Azure.

### <a name="can-i-extend-replication"></a>É possível estender replicação?

Esse tipo de replicação estendida ou encadeada não tem suporte. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959).

### <a name="can-i-do-an-offline-initial-replication"></a>É possível fazer uma replicação inicial offline?

A replicação offline não é suportada. Solicite esse recurso no [fórum de comentários](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="what-is-asrseeddisk"></a>O que é assada disk?

Para cada disco de origem, os dados são replicados em um disco gerenciado no Azure. Este disco tem o prefixo de **asrseeddisk**. Ele armazena a cópia do disco de origem e todos os instantâneos do ponto de recuperação.

### <a name="can-i-exclude-disks-from-replication"></a>Posso excluir discos da replicação?

Sim, você pode excluir discos.

### <a name="can-i-replicate-vms-that-have-dynamic-disks"></a>Posso replicar VMs que tenham discos dinâmicos?

Discos dinâmicos podem ser replicados. O disco do sistema operacional deve ser um disco básico.

### <a name="if-i-use-replication-groups-for-multi-vm-consistency-can-i-add-a-new-vm-to-an-existing-replication-group"></a>Se eu usar grupos de replicação para consistência de várias VMs, posso adicionar uma nova VM a um grupo de replicação existente?

Sim, você pode adicionar novas VMs a um grupo de replicação existente quando ativar a replicação para elas. No entanto:

- Você não pode adicionar uma VM a um grupo de replicação existente após o início da replicação.
- Você não pode criar um grupo de replicação para VMs existentes.

### <a name="can-i-modify-vms-that-are-replicating-by-adding-or-resizing-disks"></a>Posso modificar as VMs que estão replicando adicionando ou redimensionando discos?

Para a replicação do VMware para o Azure, você pode modificar o tamanho do disco das VMs de origem. Se você quiser adicionar novos discos, você deve adicionar o disco e reativar a proteção para a VM.

### <a name="can-i-migrate-on-premises-machines-to-a-new-vcenter-server-without-impacting-ongoing-replication"></a>Posso migrar as máquinas locais para um novo vCenter Server sem afetar a replicação contínua?

Não. Uma alteração do VMware Vcenter ou da migração afetará a replicação contínua. Configure a Recuperação do Site com o novo vCenter Server e habilite a replicação para máquinas novamente.

### <a name="can-i-replicate-to-a-cache-or-target-storage-account-that-has-a-virtual-network-with-azure-firewalls-configured-on-it"></a>Posso replicar para uma conta de armazenamento de cache ou destino que tenha uma rede virtual (com firewalls Azure) configurada nele?

Não, a Recuperação do Site não suporta a replicação do Azure Storage em redes virtuais.

## <a name="component-upgrade"></a>Atualização de componentes

### <a name="my-version-of-the-mobility-services-agent-or-configuration-server-is-old-and-my-upgrade-failed-what-do-i-do"></a>Minha versão do agente de serviços de mobilidade ou servidor de configuração é antiga, e minha atualização falhou. O que devo fazer?

A Recuperação do Site segue o modelo de suporte N-4. [Saiba mais](https://aka.ms/asr_support_statement) sobre como atualizar de versões muito antigas.

### <a name="where-can-i-find-the-release-notes-and-update-rollups-for-azure-site-recovery"></a>Onde posso encontrar as notas de versão e atualizar rollups para a recuperação do site do Azure?

[Conheça novas atualizações](site-recovery-whats-new.md)e [obtenha informações sobre rollup](service-updates-how-to.md).

### <a name="where-can-i-find-upgrade-information-for-disaster-recovery-to-azure"></a>Onde posso encontrar informações de upgrade para recuperação de desastres no Azure?

[Saiba mais sobre atualização](https://aka.ms/asr_vmware_upgrades).

## <a name="do-i-need-to-reboot-source-machines-for-each-upgrade"></a>Preciso reinicializar as máquinas de origem para cada atualização?

Uma reinicialização é recomendada, mas não obrigatória para cada atualização. [Saiba mais](https://aka.ms/asr_vmware_upgrades).

## <a name="configuration-server"></a>Servidor de configuração

### <a name="what-does-the-configuration-server-do"></a>O que o servidor de configuração faz?

O servidor de configuração executa os componentes locais do Site Recovery, incluindo:

- O próprio servidor de configuração. O servidor coordena as comunicações entre componentes locais e o Azure e gerencia a replicação de dados.
- O servidor de processo, que funciona como um gateway de replicação. Este servidor:
    1. Recebe dados de replicação.
    2. Otimiza os dados com armazenamento em cache, compactação e criptografia.
    3. Envia os dados para o Azure Storage.
  O servidor de processo também faz uma instalação push do Serviço de Mobilidade em VMs e realiza a descoberta automática de VMs VMware no local.
- O servidor de destino mestre, que lida com dados de replicação durante o failback do Azure.

[Saiba mais](vmware-azure-architecture.md) sobre os componentes e processos do servidor de configuração.

### <a name="where-do-i-set-up-the-configuration-server"></a>Onde o servidor de configuração deve ser configurado?

Você precisa de um VMM VM no local para o servidor de configuração. Para recuperação de desastres do servidor físico, instale o servidor de configuração em uma máquina física.

### <a name="what-do-i-need-for-the-configuration-server"></a>O que preciso para o servidor de configuração?

Analise os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites).

### <a name="can-i-manually-set-up-the-configuration-server-instead-of-using-a-template"></a>É possível configurar manualmente o servidor de configuração em vez de utilizar um modelo?

Recomendamos que você [crie o vm do servidor de configuração](vmware-azure-deploy-configuration-server.md) usando a versão mais recente do modelo OVF (Open Virtualization Format, formato de virtualização aberta). Se você não puder usar o modelo (por exemplo, se você não tiver acesso ao servidor VMware), [baixe](physical-azure-set-up-source.md) o arquivo de configuração do portal e configure o servidor de configuração.

### <a name="can-a-configuration-server-replicate-to-more-than-one-region"></a>Um servidor de configuração pode replicar para mais de uma região?

Não. Para replicar em mais de uma região, você precisa de um servidor de configuração em cada região.

### <a name="can-i-host-a-configuration-server-in-azure"></a>É possível hospedar um servidor de configuração no Azure?

Embora seja possível, o Azure VM executando o servidor de configuração precisaria se comunicar com sua infra-estrutura VMware e VMs no local. Essa comunicação adiciona latência e impacta a replicação contínua.

### <a name="how-do-i-update-the-configuration-server"></a>Como fazer para atualizar o servidor de configuração?

[Saiba](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) como atualizar o servidor de configuração.

- Você pode encontrar as últimas informações de atualização na página de atualizações do [Azure](https://azure.microsoft.com/updates/?product=site-recovery).
- Você pode baixar a versão mais recente do portal. Ou, você pode baixar a versão mais recente do servidor de configuração diretamente do [Microsoft Download Center](https://aka.ms/asrconfigurationserver).
- Se sua versão for mais de quatro versões mais antigas que a versão atual, consulte a [declaração de suporte](https://aka.ms/asr_support_statement) para orientação de atualização.

### <a name="should-i-back-up-the-configuration-server"></a>Devo fazer backup do servidor de configuração?

É recomendável fazer backups agendados regulares do servidor de configuração.

- Para um failback bem-sucedido, a reprodução com falha da VM deve existir no banco de dados do servidor de configuração.
- O servidor de configuração deve estar em execução e em um estado conectado.
- [Saiba mais](vmware-azure-manage-configuration-server.md) sobre tarefas comuns de gerenciamento de servidor de configuração.

### <a name="when-im-setting-up-the-configuration-server-can-i-download-and-install-mysql-manually"></a>Quando estou configurando o servidor de configuração, posso fazer o download e instalar o MySQL manualmente?

Sim. Faça o download o MySQL e colocá-lo na pasta C:\Temp\ASRSetup. Em seguida, instale-o manualmente. Quando você configura a VM do servidor de configuração e aceita os termos, o MySQL será listado como **Já instalado** em **Faça o download e instale**.

### <a name="can-i-avoid-downloading-mysql-but-let-site-recovery-install-it"></a>Posso evitar de fazer o download de MySQL mas permitir que o Site Recovery instale-o?

Sim. Faça o download do instalador do MySQL e coloque-o na pasta C:\Temp\ASRSetup. Ao configurar a VM do servidor de configuração, aceite os termos e selecione **Baixar e instalar**. O portal usará o instalador que você adicionou para instalar o MySQL.

### <a name="can-i-use-the-configuration-server-vm-for-anything-else"></a>Posso usar a VM do servidor de configuração para mais alguma coisa?

Não. Use a VM apenas para o servidor de configuração.

### <a name="can-i-clone-a-configuration-server-and-use-it-for-orchestration"></a>Posso clonar um servidor de configuração e usá-lo para orquestração?

Não. Configure um novo servidor de configuração para evitar problemas de registro.

### <a name="can-i-change-the-vault-in-which-the-configuration-server-is-registered"></a>Posso alterar o cofre no qual o servidor de configuração está registrado?

Não. Depois que um cofre é associado ao servidor de configuração, ele não pode ser alterado. [Aprenda](vmware-azure-manage-configuration-server.md#register-a-configuration-server-with-a-different-vault) a registrar um servidor de configuração com um cofre diferente.

### <a name="can-i-use-the-same-configuration-server-for-disaster-recovery-of-both-vmware-vms-and-physical-servers"></a>Posso usar o mesmo servidor de configuração para recuperação de desastres de VMware vms e servidores físicos?

Sim, mas note que a máquina física pode ser falhada apenas para uma VMware VMware.

### <a name="where-can-i-download-the-passphrase-for-the-configuration-server"></a>Onde posso fazer o download da frase secreta para o servidor de configuração?

[Aprenda](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase) a baixar a senha.

### <a name="where-can-i-download-vault-registration-keys"></a>Em que local posso baixar as chaves de registro do cofre?

No cofre Serviços de recuperação, selecione **Servidores de configuração** na **infra-estrutura** > de recuperação do site**gerenciar**. Em seguida, em **Servidores,** selecione **Baixar a chave de registro** para baixar o arquivo de credenciais do cofre.

### <a name="can-a-single-configuration-server-be-used-to-protect-multiple-vcenter-instances"></a>Um único servidor de configuração pode ser usado para proteger várias instâncias do vCenter?

Sim, um único servidor de configuração pode proteger VMs em vários vCenters.  Não há limite de quantas instâncias do vCenter podem ser adicionadas ao servidor de configuração, no entanto, os limites para quantas VMs um único servidor de configuração pode proteger se aplicam.

### <a name="can-a-single-configuration-server-protect-multiple-clusters-within-vcenter"></a>Um único servidor de configuração pode proteger vários clusters no vCenter?

Sim, o Azure Site Recovery pode proteger VMs em diferentes clusters.

## <a name="process-server"></a>Servidor de processo

### <a name="why-am-i-unable-to-select-the-process-server-when-i-enable-replication"></a>Por que não consigo selecionar o servidor de processo quando comodo a replicação?

Atualizações nas versões 9.24 e posteriores agora exibem a [saúde do servidor de processo quando você habilitar a replicação](vmware-azure-enable-replication.md#enable-replication). Esse recurso ajuda a evitar o estrangulamento do servidor de processos e a minimizar o uso de servidores de processo insalubres.

### <a name="how-do-i-update-the-process-server-to-version-924-or-later-for-accurate-health-information"></a>Como atualizo o servidor de processo para a versão 9.24 ou posterior para obter informações precisas sobre saúde?

A partir [da versão 9.24,](service-updates-how-to.md#links-to-currently-supported-update-rollups)mais alertas foram adicionados para indicar a saúde do servidor do processo. [Atualize seus componentes de recuperação de site para a versão 9.24 ou posterior](service-updates-how-to.md#links-to-currently-supported-update-rollups) para que todos os alertas sejam gerados.

### <a name="how-can-i-ensure-high-availability-of-the-process-server"></a>Como posso garantir alta disponibilidade do servidor de processo?

Ao configurar mais de um servidor de processo, o projeto fornece flexibilidade para mover máquinas protegidas de um servidor de processo insalubre para um servidor de processo de trabalho. A movimentação de uma máquina de um servidor de processo para outro deve ser iniciada explicitamente/manualmente através das etapas definidas aqui: movimentação de [VMs entre servidores de processo](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

## <a name="failover-and-failback"></a>Failover e failback

### <a name="can-i-use-the-on-premises-process-server-for-failback"></a>Posso usar o servidor de processo no local para failback?

Recomendamos fortemente a criação de um servidor de processo no Azure para fins de failback, para evitar latências de transferência de dados. Além disso, caso você separe a rede de VMs de origem com a rede azure voltada para o servidor de configuração, é essencial usar o servidor de processo criado no Azure para failback.

### <a name="can-i-keep-the-ip-address-on-failover"></a>Posso manter o endereço IP no failover?

Sim, você pode manter o endereço IP no failover. Certifique-se de especificar o endereço IP de destino nas configurações **Computação e Rede** para a VM antes do failover. Além disso, desligue as máquinas no momento do failover para evitar conflitos de endereço IP durante o failback.

### <a name="can-i-change-the-target-vm-size-or-vm-type-before-failover"></a>Posso alterar o tamanho da VM de destino ou o tipo de VM antes do failover?

Sim, você pode alterar o tipo ou tamanho do VM a qualquer momento antes do failover. No portal, use as configurações **de Computação e Rede** para a VM replicada.

### <a name="how-far-back-can-i-recover"></a>A que tempo é possível fazer failback de recuperação?

Para VMware para Azure, o ponto de recuperação mais antigo que você pode usar é 72 horas.

### <a name="how-do-i-access-azure-vms-after-failover"></a>Como fazer para acessar VMs do Azure após o failover?

Após o failover, você pode acessar as VMs do Azure através de uma conexão segura com a Internet, através de uma VPN site-to-site ou através do Azure ExpressRoute. Para se conectar, você deve preparar várias coisas. [Saiba mais](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

### <a name="is-failed-over-data-resilient"></a>Os dados com falha são resistentes?

O Azure foi desenvolvido para resiliência. A Recuperação do Site é projetada para failover em um data center secundário do Azure, conforme exigido pelo contrato de nível de serviço do Azure (SLA). Quando o failover ocorrer, certificamos que seus metadados e cofres permaneçam na mesma região geográfica que você escolheu para o seu cofre.

### <a name="is-failover-automatic"></a>O failover é automático?

[Failover](site-recovery-failover.md) não é automático. Você inicia um failover fazendo uma única seleção no portal ou pode usar o [PowerShell](/powershell/module/az.recoveryservices) para ativar um failover.

### <a name="can-i-fail-back-to-a-different-location"></a>É possível fazer failback para um local diferente?

Sim. Se você falhou no Azure, você pode falhar de volta a um local diferente se o original não estiver disponível. [Saiba mais](concepts-types-of-failback.md#alternate-location-recovery-alr).

### <a name="why-do-i-need-a-vpn-or-expressroute-with-private-peering-to-fail-back"></a>Por que eu preciso de uma VPN ou ExpressRoute com peering privado para falhar?

Quando você recua do Azure, os dados do Azure são copiados de volta para a VM no local e o acesso privado é necessário.


## <a name="automation-and-scripting"></a>Automação e scripts

### <a name="can-i-set-up-replication-with-scripting"></a>É possível configurar a replicação com scripts?

Sim. Você pode automatizar fluxos de trabalho de recuperação de site usando a API resto, powershell ou o SDK do Azure. [Saiba mais](vmware-azure-disaster-recovery-powershell.md).

## <a name="performance-and-capacity"></a>Desempenho e capacidade

### <a name="can-i-throttle-replication-bandwidth"></a>É possível limitar a largura de banda de replicação?

Sim. [Saiba mais](site-recovery-plan-capacity-vmware.md).

## <a name="next-steps"></a>Próximas etapas

- [Analisar](vmware-physical-azure-support-matrix.md) os requisitos de suporte.
- [Configurar](vmware-azure-tutorial.md) replicação de VMware para o Azure.
