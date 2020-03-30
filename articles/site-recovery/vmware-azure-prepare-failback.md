---
title: Prepare vms vmware para reproteção e failback com a recuperação do site do Azure
description: Prepare-se para fail back de VMs VMware após failover com a recuperação do site do Azure
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: 5a330f8cba31640d0116ca3d5ccab352ce5b3509
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257180"
---
# <a name="prepare-for-reprotection-and-failback-of-vmware-vms"></a>Prepare-se para reproteção e failback de VMs VMware

Após [o failover](site-recovery-failover.md) de VMMs vMware ou servidores físicos no local para o Azure, você reprotege as VMs do Azure criadas após failover, para que elas se reproduzam no site local. Com a replicação do Azure para o local no local, você pode falhar ao executar um failover do Azure para o local quando estiver pronto.

Antes de continuar, obtenha uma visão geral rápida com este vídeo sobre como falhar de volta do Azure para um site no local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

## <a name="reprotectionfailback-components"></a>Componentes de reproteção/failback

Você precisa de uma série de componentes e configurações no lugar antes de poder reproteger e falhar no Azure.

**Componente**| **Detalhes**
--- | ---
**Servidor de configuração local** | O servidor de configuração no local deve estar em execução e conectado ao Azure.<br/><br/> O VM que você está falhando de volta deve existir no banco de dados do servidor de configuração. Se o desastre afetar o servidor de configuração, restaure-o com o mesmo endereço IP para garantir que o failback funcione.<br/><br/>  Se os endereços IP de máquinas replicadas forem retidos no failover, a conectividade local-a-local (ou conectividade ExpressRoute) deve ser estabelecida entre as máquinas Azure VMs e a NIC failback do servidor de configuração. Para endereços IP retidos, o servidor de configuração precisa de dois NICs - um para conectividade de máquina de origem e outro para conectividade de failback do Azure. Isso evita a sobreposição de intervalos de endereços de sub-rede para a fonte e falha em VMs.
**Servidor de processos no Azure** | Você precisa de um servidor de processo no Azure antes de poder voltar ao seu site local.<br/><br/> O servidor de processo recebe dados da VM Azure protegida e os envia para o site local.<br/><br/> Você precisa de uma rede de baixa latência entre o servidor de processo e a VM protegida, por isso recomendamos que você implante o servidor de processo no Azure para maior desempenho de replicação.<br/><br/> Para a prova de conceito, você pode usar o servidor de processo no local e o ExpressRoute com peering privado.<br/><br/> O servidor de processo deve estar na rede Azure na qual a falha sobre vm está localizada. O servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local e o servidor de destino mestre.
**Servidor de destino mestre separado** | O servidor de destino mestre recebe dados de failback e, por padrão, um servidor de destino mestre do Windows é executado no servidor de configuração local.<br/><br/> Um servidor de destino mestre pode ter até 60 discos conectados a ele. As VMs que estão sendo falhadas de volta têm mais de um total coletivo de 60 discos, ou se você estiver falhando em grandes volumes de tráfego, crie um servidor de destino mestre separado para failback.<br/><br/> Se as máquinas estão reunidas em um grupo de replicação para consistência multi-VM, as VMs devem ser todas Windows, ou todas devem ser Linux. Por quê? Como todas as VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre, e o servidor de destino mestre deve ter o mesmo sistema operacional (com a mesma ou uma versão superior) do que as das máquinas replicadas.<br/><br/> O servidor de destino mestre não deve ter nenhum instantâneo em seus discos, caso contrário, a reproteção e o failback não funcionarão.<br/><br/> O destino mestre não pode ter um controlador SCSI Paravirtual. O controlador só pode ser um controlador LSI Logic. Sem um controlador de Lógica LSI, a nova proteção falha.
**Política de replicação de failback** | Para replicar de volta ao site local, você precisa de uma política de failback. Essa política é criada automaticamente quando você cria uma diretiva de replicação para o Azure.<br/><br/> A política é associada automaticamente ao servidor de configuração. Ele está configurado para um limite de RPO de 15 minutos, retenção de ponto de recuperação de 24 horas e frequência de instantâneo consistente com aplicativo é de 60 minutos. A política não pode ser editada. 
**Peering privado de VPN/ExpressRoute de site para site** | A reproteção e o failback precisam de uma conexão VPN site-to-site ou peering privado ExpressRoute para replicar dados. 


## <a name="ports-for-reprotectionfailback"></a>Portas para reproteção/failback

Várias portas devem ser abertas para reproteção/failback. O gráfico a seguir ilustra as portas e reprotege/fluxo de failback.

![Portas para failover e failback](./media/vmware-azure-reprotect/failover-failback.png)


## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor em processo no Azure

1. [Configure um servidor de processo](vmware-azure-set-up-process-server-azure.md) no Azure para failback.
2. Certifique-se de que as VMs do Azure podem chegar ao servidor de processo. 
3. Certifique-se de que a conexão VPN site-site ou a rede privada de peering ExpressRoute tenha largura de banda suficiente para enviar dados do servidor de processo para o site local.

## <a name="deploy-a-separate-master-target-server"></a>Implantar um servidor de destino mestre separado

1. Observe os [requisitos e limitações](#reprotectionfailback-components)do servidor de destino mestre .
2. Crie um servidor de destino mestre [do Windows](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers) ou [Linux,](vmware-azure-install-linux-master-target.md) para corresponder ao sistema operacional das VMs que deseja reproteger e falhar.
3. Certifique-se de que não use o Storage vMotion para o servidor de destino mestre, ou o failback pode falhar. A máquina VM não pode ser iniciada porque os discos não estão disponíveis para ela.
    - Para evitar isso, exclua o servidor de destino mestre da sua lista vMotion.
    - Se um alvo-mestre sofrer uma tarefa de armazenamento vMotion após a reproteção, os discos VM protegidos conectados ao servidor de destino mestre migrarão para o alvo da tarefa vMotion. Se você tentar falhar depois disso, o descolamento do disco falha porque os discos não foram encontrados. Então é difícil encontrar os discos em suas contas de armazenamento. Se isso ocorrer, encontre-os manualmente e anexe-os à VM. Depois disso, a VM no local pode ser inicializada.

4. Adicione uma unidade de retenção ao servidor de destino mestre do Windows existente. Adicione um novo disco e formate a unidade. A unidade de retenção é usada para parar os pontos no tempo quando a VM se replica de volta ao local. Observe esses critérios. Se eles não forem atendidos, a unidade não está listada para o servidor de destino mestre:
    - O volume não é usado para nenhum outro propósito, como um alvo de replicação, e não está no modo de bloqueio.
    - O volume não é um volume de cache. O volume de instalação personalizado para o destino mestre e servidor de processo não está qualificado para um volume de retenção. Quando o servidor de processo e o destino mestre são instalados em um volume, o volume é um cache de destino mestre.
    - Não é o tipo de sistema de arquivos do volume FAT ou FAT32.
    - A capacidade do volume é diferente de zero.
    - O volume de retenção padrão para o Windows é o volume R.
    - O volume de retenção padrão para o Linux é /mnt/retention.

5. Adicione uma unidade se estiver usando um servidor de processo existente. A nova unidade deve atender aos requisitos na última etapa. Se a unidade de retenção não estiver presente, ele não aparecer na lista suspensa de seleção no portal. Depois de adicionar uma unidade ao destino mestre local, levará até 15 minutos para que a unidade apareça na seleção no portal. Você pode atualizar o servidor de configuração se a unidade não aparecer após 15 minutos.
6. Instale as ferramentas do VMware ou o open-vm-tools no servidor de destino principal. Sem as ferramentas, os datastores no host ESXi do destino mestre não podem ser detectados.
7. Defina o disco. EnableUUID=true setting in the configuration parameters of the master target VM in VMware. Se essa linha não existir, adicione-o. Essa configuração é necessária para fornecer um UUID consistente para o VMDK para que ele monta corretamente.
8. Verifique os requisitos de acesso ao vCenter Server:
    - Se a VM à qual você está falhando de volta estiver em um host ESXi gerenciado pelo VMware vCenter Server, o servidor de destino mestre precisa acessar o arquivo VM Virtual Machine Disk (VMDK) no local, a fim de gravar os dados replicados nos discos da máquina virtual. Certifique-se de que o datastore VM no local esteja montado no host de destino principal com acesso à leitura/gravação.
    - Se a VM não estiver em um host ESXi gerenciado por um VMware vCenter Server, a Recuperação do Site criará uma nova VM durante a reproteção. Esta VM é criada no host ESXi no qual você cria a VM do servidor de destino mestre. Escolha cuidadosamente o host ESXi para criar a VM no host que você deseja. O disco rígido da VM precisa estar em um armazenamento de dados acessível para o host no qual o servidor de destino mestre está em execução.
    - Outra opção, se a VM local já existir para failback, ela deverá ser excluída antes da execução de um failback. Failback então cria uma nova VM no mesmo host do host ESXi de destino mestre. Quando você falha em um local alternativo, os dados são recuperados para o mesmo datastore e o mesmo host ESXi que o usado pelo servidor-alvo principal no local.
9. Para máquinas físicas que falham em voltar às VMs VMware, você deve concluir a descoberta do host no qual o servidor de destino mestre está sendo executado, antes de poder reproteger a máquina.
10. Verifique se o host ESXi no qual o vm de destino principal tem pelo menos um datastore de datastore de arquivo de máquina virtual (VMFS) anexado a ele. Se nenhum armazenamento de dados VMFS estiver conectado, a entrada do datastore nas configurações de reproteção está vazia e você não pode prosseguir.


## <a name="next-steps"></a>Próximas etapas

[Reproteja](vmware-azure-reprotect.md) uma VM.
