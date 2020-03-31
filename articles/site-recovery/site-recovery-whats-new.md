---
title: O que há de novo na recuperação do site do Azure
description: Fornece um resumo dos novos recursos e das últimas atualizações no serviço de recuperação do site do Azure.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: abb1592bcacf025e9a052d7a9222f6fb3d2b72d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257427"
---
# <a name="whats-new-in-site-recovery"></a>Novidades do Site Recovery

O serviço [Azure Site Recovery](site-recovery-overview.md) é atualizado e aprimorado continuamente. Para ajudar você a se manter atualizado, este artigo fornece informações sobre as últimas versões, novos recursos e novo conteúdo. Esta página é atualizada regularmente.

Você pode seguir e assinar as notificações de atualização do Site Recovery no canal de atualizações do [Azure.](https://azure.microsoft.com/updates/?product=site-recovery)

## <a name="supported-updates"></a>Atualizações suportadas

Para componentes de recuperação de site, suportamos versões N-4, onde N é a versão mais recente lançada. Esses métodos estão resumidos na tabela a seguir.

**Atualização** |  **Instalação unificada** | **Ovas do servidor de configuração** | **Agente de serviçode mobilidade** | **Provedor de Recuperação de Site** | **Agente dos Serviços de Recuperação**
--- | --- | --- | --- | --- | ---
[Rollup 45](https://support.microsoft.com/help/4550047/) | 9.32.5487.1 | 5.1.5400.0 | 9.32.5487.1 | 5.1.5400.0 | 2.0.9165.0
[Rollup 43](https://support.microsoft.com/help/4537047/) | 9.31.5449.1 | 5.1.5300.0 | 9.31.5449.1 | 5.1.5300.0 | 2.0.9165.0
[Rollup 42](https://support.microsoft.com/help/4531426/) | 9.30.5407.1 | 5.1.5200.0 | 9.30.5407.1 | 5.1.5200.0 | 2.0.9165.0
[Rollup 41](https://support.microsoft.com/help/4528026/) | 9.29.5367.1 | 5.1.5000.0 | 9.29.5367.1 | 5.1.5000.0 | 2.0.9165.0
[Rollup 40](https://support.microsoft.com/help/4521530/) | 9.28.5345.1 | 5.1.4800.0 | 9.28.5345.1 | 5.1.4800.0 | 2.0.9165.0

[Saiba mais](service-updates-how-to.md) sobre a instalação e suporte de atualização.

> [!NOTE]
> A atualização do rollup 44 não é mostrada na tabela porque não incluía atualizações para os provedores e agentes de recuperação de site.

## <a name="updates-march-2020"></a>Atualizações (março de 2020)

### <a name="update-rollup-45"></a>Atualização rollup 45

[O rollup de atualização 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) fornece as seguintes atualizações:

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site conforme detalhado no rollup.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

## <a name="updates-january-2020"></a>Atualizações (janeiro de 2020)

### <a name="update-rollup-44"></a>Atualização rollup 44

[A atualização rollup 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Não houve atualizações para os provedores e agentes de recuperação de sites.
**Correções/melhorias de problemas** | Uma série de correções e melhorias conforme detalhado no rollup.

### <a name="azure-vmware-disaster-recovery"></a>Recuperação de desastres do Azure VMware

As máquinas virtuais do Azure agora suportam VMs habilitadas para criptografia em repouso com chaves gerenciadas pelo cliente. [Saiba mais](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Atualização rollup 43

[A atualização do rollup 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


## <a name="updates-november-2019"></a>Atualizações (novembro de 2019)

### <a name="update-rollup-42"></a>Atualização rollup 42

[A atualização do rollup 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Novos recursos para recuperação de desastres do Azure VM estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**UEFI** | A Recuperação do Site agora suporta a recuperação de desastres para VMs do Azure com arquitetura de inicialização baseada em UEFI.
**Linux** | A Recuperação do Site agora suporta As VMs do Azure executando o Linux com o Azure Disk Encryption (ADE).
**Geração 2** | Todas as VMs Azure de geração 2 agora são suportadas para recuperação de desastres.
**Regiões** | Agora você pode habilitar a recuperação de desastres para VMs azure no geo norueguês.

### <a name="vmware-to-azure-disaster-recovery"></a>Recuperação de desastre do VMware para o Azure

Novos recursos para recuperação de desastres VMware para Azure são resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**UEFI** | A Recuperação do Site agora suporta a recuperação de desastres para VMware VMs com arquitetura de inicialização baseada em UEFI.<br/><br/> Os sistemas operacionais suportados incluem o Windows Server 2019, o Windows Server 2016, o Windows Server 2012 R2, o Windows Server 2012, o SLES 12 SP4, o RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Atualização para atualização da pilha de manutenção/SHA-2

Para recuperação de desastres de VMs do Azure para uma região secundária, ou VMMs vMware ou servidores físicos no local para o Azure, observe o seguinte:

- A partir da versão 9.30.5407.1 da extensão de serviço de mobilidade (para VMs Azure) e agente de serviço de mobilidade (para vmware/máquinas físicas), alguns sistemas operacionais da máquina devem estar executando a atualização da pilha de manutenção e SHA-2. Os detalhes são mostrados na tabela abaixo.
- Instale a atualização e o SHA-2 de acordo com o KB vinculado. O SHA-1 não é suportado a partir de setembro de 2019 e, se a assinatura de código SHA-2 não estiver habilitada, a extensão do agente não instalará/atualizará como esperado.
- Saiba mais sobre [a atualização e os requisitos do SHA-2.](https://aka.ms/SHA-2KB)

**Sistema Operacional** | **VM do Azure** | **VMware VM/máquina física**
--- | --- | ---
**Windows 2008 R2 SP1** | [Atualização de pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização de pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 SP2** | [Atualização de pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização de pilha de manutenção](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 SP1** | [Atualização de pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Atualização de pilha de manutenção](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Atualizações (outubro de 2019)

### <a name="update-rollup-41"></a>Atualização rollup 41

[A atualização do rollup 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)



### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Novos recursos para recuperação de desastres do Azure VM estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Configurações de failover de teste** | Ao configurar um failover de teste, agora você pode configurar as configurações para o VM e a rede de failover de teste, incluindo endereço IP, NSG, equilíbrio de carga interna e o endereço IP público para cada NIC da máquina. Essas configurações são opcionais e não alteram o comportamento atual. Se você não configurar essas configurações, você pode escolher um Azure VNet no momento do failover do teste. [Saiba mais](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Planos de recuperação** | Os planos de recuperação estão agora limitados a 100 VMs, para garantir a confiabilidade do failover.

### <a name="vmware-to-azure-disaster-recovery"></a>Recuperação de desastre do VMware para o Azure

Novos recursos para recuperação de desastres VMware para Azure são resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Planos de recuperação** | Os planos de recuperação estão agora limitados a 100 VMs, para garantir a confiabilidade do failover.


## <a name="updates-september-2019"></a>Atualizações (setembro de 2019)

### <a name="update-rollup-40"></a>Atualização rollup 40

[A atualização rollup 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)




### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Novos recursos para recuperação de desastres do Azure VM estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Limpeza após failback** | Depois de falhar no Azure secundário e, em seguida, falhar de volta para a região primária, o Site Recovery limpa automaticamente as máquinas na região secundária. Não há necessidade de excluir manualmente VMs e NICs.
**Failover de teste retém endereço IP** | Agora você pode reter o endereço IP da VM de origem durante uma broca de recuperação de desastres e escolher um endereço IP estático para um failover de teste.

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
Novos alertas de servidor de processo | Adicionamos novos alertas de servidor de processo. [Saiba mais](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Recuperação de desastres hiper-V

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
Conta de armazenamento | O Site Recovery agora suporta o uso de contas de armazenamento com firewall ativado para recuperação de desastres Do Hyper-V para o Azure.  Você pode selecionar contas de armazenamento habilitadas para firewall como uma conta de destino ou para armazenamento em cache. Se você usar a conta habilitada para firewall, certifique-se de habilitar a opção de permitir serviços confiáveis da Microsoft.<br/><br/> Este é suportado para Hiper-V VMs com ou sem Centro de Sistema VMM.


## <a name="updates-august-2019"></a>Atualizações (agosto de 2019)

### <a name="update-rollup-39"></a>Atualização rollup 39

[A atualização rollup 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Novos recursos para recuperação de desastres do Azure VM estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Criptografia sem Ad do Azure** | A criptografia sem um aplicativo Azure AD agora é suportada para a replicação do Azure VM para discos gerenciados executando o Windows.
**Recursos de rede para failover** | Ao falhar em outra região, agora você pode anexar configurações de recursos de rede (NSGs, balanceamento de carga, endereço IP público) a uma VM.

## <a name="updates-july-2019"></a>Atualizações (julho de 2019)

### <a name="update-rollup-38"></a>Atualização rollup 38

[A atualização rollup 38](https://support.microsoft.com/help/4513507/) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="general"></a>Geral

A Recuperação do Site agora suporta o uso de contas de armazenamento v2 de uso geral para armazenamento em cache ou armazenamento de destino. Anteriormente, apenas v1 era suportado.

### <a name="vmware-to-azure-disaster-recovery"></a>Recuperação de desastre do VMware para o Azure

Agora você pode replicar discos de até 8 TB ao se replicar em uma VM Azure com discos gerenciados.


## <a name="updates-june-2019"></a>Atualizações (junho de 2019)

### <a name="update-rollup-37"></a>Atualização rollup 37

[A atualização rollup 37](https://support.microsoft.com/help/4508614/) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Atualizações para agentes e provedores de recuperação de site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Partições GPT** | A partir do Update Rollup 37 em diante (versão do serviço de mobilidade 9.25.5241.1), até cinco partições GPT são suportadas no UEFI. Antes desta atualização, quatro foram suportados.



## <a name="updates-may-2019"></a>Atualizações (maio de 2019)

### <a name="update-rollup-36"></a>Atualização rollup 36

[A atualização rollup 36](https://support.microsoft.com/help/4503156) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Replicar discos adicionados** | Habilite a replicação de discos de dados adicionados a uma VM Azure já habilitada para recuperação de desastres. [Saiba mais](azure-to-azure-enable-replication-added-disk.md).
**Atualizações automáticas** | Ao configurar atualizações automáticas para a extensão do serviço de mobilidade que é executada em VMs do Azure habilitadas para recuperação de desastres, agora você pode selecionar uma conta de automação existente para usar, em vez de usar a conta padrão criada pelo Site Recovery. [Saiba mais](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Monitoramento de servidores de processos** | Para recuperação de desastres de VMMs vMware e servidores físicos no local, monitore e soluciona problemas de servidor de processo com relatórios e alertas aprimorados de saúde do servidor. [Saiba mais](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Atualizações (março de 2019)

### <a name="update-rollup-35"></a>Atualização rollup 35

[A atualização rollup 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup)
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup)

### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Discos gerenciados** | A replicação de VMMs VMware e servidores físicos no local agora é diretamente para discos gerenciados no Azure. Os dados no local são enviados para uma conta de armazenamento de cache no Azure e os pontos de recuperação são criados em discos gerenciados no local de destino. Isso garante que você não precise gerenciar várias contas de armazenamento de destino.
**Servidor de configuração** | A Recuperação do Site agora suporta servidores de configuração com vários NICs. Adicione adaptadores adicionais ao VM do servidor de configuração antes de registrar o servidor de configuração no cofre. Se você adicionar depois, você precisa recadastrar o servidor no cofre.


## <a name="updates-february-2019"></a>Atualizações (fevereiro de 2019)

### <a name="update-rollup-34"></a>Atualização rollup 34

[A atualização rollup 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


### <a name="update-rollup-33"></a>Atualização rollup 33

[A atualização rollup 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Mapeamento de rede** | Para a recuperação de desastres do Azure VM, agora você pode usar qualquer rede de destino disponível quando habilitar a replicação.
**SSD Standard** | Agora você pode configurar a recuperação de desastres para VMs do Azure usando [discos SSD padrão](https://docs.microsoft.com/azure/virtual-machines/windows/disks-standard-ssd).
**Espaços de armazenamento Diretos** | Você pode configurar a recuperação de desastres para aplicativos em execução em aplicativos Azure VM usando [storage Spaces Direct](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para alta disponibilidade.  O uso do Storage Spaces Direct (S2D) juntamente com a Recuperação do Site fornece proteção abrangente das cargas de trabalho do Azure VM. O S2D permite hospedar um cluster de hóspedes no Azure. Isso é especialmente útil quando uma VM hospeda um aplicativo crítico, como a camada AsCS SAP, o SQL Server ou o servidor de arquivos de saída de escala.


### <a name="vmwarephysical-server-disaster-recovery"></a>Recuperação de desastres do VMware/servidor físico

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Sistema de arquivos Linux BRTFS** | A Recuperação do Site agora suporta a replicação de VMs VMware com o sistema de arquivos BRTFS. A replicação não é suportada se:<br/><br/>- O subvolume do sistema de arquivos BTRFS é alterado após a habilitação da replicação.<br/><br/>- O sistema de arquivos está espalhado por vários discos.<br/><br/>- O sistema de arquivos BTRFS suporta RAID.
**Windows Server 2019** | Suporte adicionado para máquinas que executam o Windows Server 2019.


## <a name="updates-january-2019"></a>Atualizações (janeiro de 2019)


### <a name="accelerated-networking-azure-vms"></a>Rede acelerada (VMs Azure)

A rede acelerada permite a virtualização de I/O raiz única (SR-IOV) em uma VM, melhorando o desempenho da rede. Quando você habilita a replicação em uma VM do Azure, o Site Recovery detecta se a rede acelerada está habilitada. Nesse caso, após o failover, o Site Recovery configura automaticamente a rede acelerada na VM do Azure de réplica de destino para o [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell#enable-accelerated-networking-on-existing-vms) e o [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli#enable-accelerated-networking-on-existing-vms).

[Saiba mais](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Atualização rollup 32

[A atualização rollup 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | O suporte foi adicionado para redhat workstation 6/7, e novas versões do kernel para Ubuntu, Debian e SUSE.
**Espaços de armazenamento Diretos** | A Recuperação do Site suporta VMs Azure usando o Storage Spaces Direct (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>VMware VMs/servidores físicos recuperação de desastres

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | O suporte foi adicionado para Redhat Enterprise Linux 7.6, RedHat Workstation 6/7, Oracle Linux 6.10 e Oracle Linux 7.6, e novas versões do kernel para Ubuntu, Debian e SUSE.


### <a name="update-rollup-31"></a>Atualização rollup 31

[A atualização rollup 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="vmware-vmsphysical-servers-replication"></a>Replicação de VMs/servidores físicos vMware

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** |  O suporte foi adicionado para o Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 com o Kernel Compatível red hat, e para a versão UEK (Unbreakable Enterprise Kernel 5).
**LVM** | Suporte adicionado para volumes LVM e LVM2.<br/><br/> O diretório /boot em uma partição de disco e em volumes LVM agora é suportado.
**Diretórios** | O suporte foi adicionado para esses diretórios configurados como partições separadas ou sistemas de arquivos que não estão no mesmo disco do sistema:<br/><br/> /(root), /boot, /usr, /usr/local, /var, /etc.
**Windows Server 2008** | Suporte adicionado para discos dinâmicos.
**Failover** | Tempo de failover melhorado para VMware VMs onde storvsc e vsbus não são drivers de inicialização.
**Suporte ao UEFI** | As VMs do Azure não suportam o TIPO de inicialização UEFI. Agora você pode migrar servidores físicos no local com UEFI para Azure com Recuperação de Site. A Recuperação do Site migra o servidor convertendo o tipo de inicialização em BIOS antes da migração. A Recuperação do Site suportava anteriormente esta conversão apenas para VMs. O suporte está disponível para servidores físicos que executam o Windows Server 2012 ou posterior.

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | O suporte foi adicionado para o Oracle Linux 6.8, Oracle Linux 6.9 e Oracle Linux 7.0 com o Kernel Compatível red hat, e para a versão UEK (Unbreakable Enterprise Kernel 5).
**Sistema de arquivos Linux BRTFS** | Suportado para VMs Azure.
**VMs azure em zonas de disponibilidade** | Você pode habilitar a replicação para outra região para VMs Azure implantados em zonas de disponibilidade. Agora você pode habilitar a replicação em uma VM do Azure e definir o destino de failover como uma única instância de VM, uma VM em um conjunto de disponibilidade ou uma VM em uma zona de disponibilidade. A configuração não afeta a replicação. [Leia](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) o comunicado.
**Armazenamento habilitado para firewall (portal/PowerShell)** | Suporte adicionado para [contas de armazenamento habilitadas para firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Você pode replicar VMs do Azure com discos não gerenciados em contas de armazenamento habilitadas para firewall em outra região do Azure para recuperação de desastres.<br/><br/> Você pode usar contas de armazenamento habilitadas para firewall como contas de armazenamento de destino para discos não gerenciados.<br/><br/> Suportado no portal e usando o PowerShell.

## <a name="updates-december-2018"></a>Atualizações (dezembro de 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Atualizações automáticas para o serviço de mobilidade (Azure VMs)

O Site Recovery adicionou uma opção para atualizações automáticas à extensão do Serviço de Mobilidade. A extensão do Serviço de Mobilidade é instalada em cada VM do Azure replicada pelo Site Recovery. Ao habilitar a replicação, selecione se deseja permitir que o Site Recovery gerencie as atualizações para a extensão.

As atualizações não exigem uma reinicialização de VM e não afetam a replicação. [Saiba mais](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Calculadora de Preços para recuperação de desastre da VM do Azure

A recuperação de desastres das VMs do Azure incorre nos custos de licenciamento da VM e nos custos de rede e armazenamento. O Azure fornece uma [Calculadora de Preços](https://aka.ms/a2a-cost-estimator) para ajudar você a entender esses custos. O Site Recovery agora fornece uma [estimativa de preço de exemplo](https://aka.ms/a2a-cost-estimator) que precifica uma implantação de exemplo com base em um aplicativo de três camadas usando seis VMs com 12 discos HDD Standard e 6 discos SSD Premium.

- A amostra presume uma mudança de dados de 10 GB por dia para o padrão e 20 GB para premium.
- Para sua implantação específica, você pode alterar as variáveis para estimar os custos.
- Você pode especificar o número de VMs, o número e o tipo de discos gerenciados e a taxa total esperada de alteração de dados entre as VMs.
- Além disso, você pode aplicar um fator de compactação para estimar os custos de largura de banda.

[Leia](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) o comunicado.


## <a name="updates-october-2018"></a>Atualizações (outubro de 2018)

### <a name="update-rollup-30"></a>Atualização rollup 30

[A atualização rollup 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM
Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte de regiões** | Suporte de recuperação de site adicionado para Austrália Central 1 e Austrália Central 2.
**Suporte para criptografia de disco** | Suporte adicionado para recuperação de desastres de VMs do Azure criptografados com a Ade (ADE) de disk encryption (ADE) com o aplicativo Azure AD. [Saiba mais](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Exclusão de disco** | Os discos não inicializados agora são automaticamente excluídos durante a replicação do Azure VM.
**Armazenamento habilitado para firewall (PowerShell)** | Suporte adicionado para [contas de armazenamento habilitadas para firewall](https://docs.microsoft.com/azure/storage/common/storage-network-security).<br/><br/> Você pode replicar VMs do Azure com discos não gerenciados em contas de armazenamento habilitadas para firewall em outra região do Azure para recuperação de desastres.<br/><br/> Você pode usar contas de armazenamento habilitadas para firewall como contas de armazenamento de destino para discos não gerenciados.<br/><br/> Suportado usando apenas PowerShell.


### <a name="update-rollup-29"></a>Atualização rollup 29

[A atualização rollup 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).


## <a name="updates-august-2018"></a>Atualizações (agosto de 2018)

### <a name="update-rollup-28"></a>Pacote cumulativo de atualizações 28

[A atualização rollup 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM
Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Adicionado suportado para RedHat Enterprise Linux 6.10; CentOS 6.10.<br/><br/>
**Suporte em nuvem** | Recuperação de desastres suportada para VMs Azure na nuvem da Alemanha.
**Recuperação de desastres por assinatura cruzada** | Suporte para replicar VMs do Azure em uma região para outra região em uma assinatura diferente, dentro do mesmo inquilino do Azure Active Directory. [Saiba mais](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação de desastres do VMware VMware/servidor físico
Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Suporte adicionado para RedHat Enterprise Linux 6.10, CentOS 6.10.<br/><br/> As VMs baseadas em Linux que usam o estilo de partição GUID partition table (GPT) no modo de compatibilidade do BIOS legado agora são suportadas. Reveja o [FaQ Azure VM](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks) para obter mais informações.
**Recuperação de desastres para VMs após migração** | O suporte para habilitar a recuperação de desastres para uma região secundária para uma VMware VMware no local migrou para o Azure, sem a necessidade de desinstalar o serviço de Mobilidade na VM antes de ativar a replicação.
**Windows Server 2008** | Suporte para máquinas migratórias que executam o Windows Server 2008 R2/2008 de 64 bits e 32 bits.<br/><br/> Somente migração (replicação e failover). Failback não é suportado.

## <a name="updates-july-2018"></a>Atualizações (julho de 2018)

### <a name="update-rollup-27-july-2018"></a>Atualização rollup 27 (julho 2018)

[A atualização rollup 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) fornece as seguintes atualizações.

**Atualização** | **Detalhes**
--- | ---
**Provedores e agentes** | Uma atualização para os agentes e provedores de recuperação do site (conforme detalhado no rollup).
**Correções/melhorias de problemas** | Uma série de correções e melhorias (conforme detalhado no rollup).

### <a name="azure-vm-disaster-recovery"></a>Recuperação de desastres do Azure VM

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Recuperação de desastres do VMware VMware/servidor físico

Os recursos adicionados este mês estão resumidos na tabela.

**Recurso** | **Detalhes**
--- | ---
**Suporte para Linux** | Suporte adicionado para Red Hat Enterprise Linux 7.5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Próximas etapas

Mantenha-se atualizado com nossas atualizações na página [Atualizações do Azure](https://azure.microsoft.com/updates/?product=site-recovery).
