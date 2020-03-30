---
title: Configurar failover e failback para servidores físicos com recuperação de site
description: Saiba como executar failover de servidores físicos para o Azure e failback para o site local para recuperação de desastre com o Azure Site Recovery
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2019
ms.author: raynew
ms.openlocfilehash: ea5893f45962d67f4b6f3e9a261c65aa0ec926bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497863"
---
# <a name="fail-over-and-fail-back-physical-servers-replicated-to-azure"></a>Executar failover e failback de servidores físicos replicados para Azure

Este tutorial descreve como falhar sobre servidores físicos no local que estão se replicando para o Azure com [o Azure Site Recovery](site-recovery-overview.md). Depois de falhar, você falha de volta do Azure para o seu site no local quando ele estiver disponível.

## <a name="before-you-start"></a>Antes de começar

- [Saiba](failover-failback-overview.md) mais sobre o processo de failover na recuperação de desastres.
- Se você quer falhar sobre várias máquinas, [aprenda](recovery-plan-overview.md) a reunir máquinas em um plano de recuperação.
- Antes de fazer um failover completo, execute uma [broca de recuperação de desastres](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando como esperado.
- Siga [estas instruções](site-recovery-failover.md#prepare-to-connect-after-failover) para se preparar para conectar-se às VMs do Azure após o failover.



## <a name="run-a-failover"></a>Executar um failover

### <a name="verify-server-properties"></a>Verificar as propriedades do servidor

Confira as propriedades do servidor e verifique se ele está em conformidade com os [Requisitos do Azure](vmware-physical-azure-support-matrix.md#replicated-machines) quanto a VMs do Azure.

1. Em **Itens Protegidos**, clique em **Itens Replicados** e selecione o computador.
2. No painel **Item Replicado**, há um resumo das informações do computador, o status de integridade e os últimos pontos de recuperação disponíveis. Clique em **Propriedades** para exibir mais detalhes.
3. Em **Computação e Rede,** você pode modificar o nome Do Zure, o grupo de recursos, o tamanho do destino, [o conjunto de disponibilidade](../virtual-machines/windows/tutorial-availability-sets.md)e as configurações de disco gerenciadas
4. Você pode exibir ou modificar as configurações de rede, incluindo a rede/sub-rede na qual a VM do Azure estará localizada após o failover e o endereço IP que será atribuído a ela.
5. Em **Discos**, é possível visualizar as informações sobre o sistema operacional e os discos de dados do computador.

### <a name="fail-over-to-azure"></a>Fazer failover para o Azure

1. Em **Configurações** > **Itens replicados** clique na máquina > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Você pode usar uma das seguintes opções:
   - **Mais recente**: essa opção primeiro processa todos os dados enviados ao Site Recovery. Ela fornece o RPO (objetivo de ponto de recuperação) mais baixo porque a VM do Azure criada após o failover tem todos os dados que foram replicados para o Site Recovery quando o failover foi disparado.
   - **Últimos processados**: essa opção executa failover do computador para o último ponto de recuperação processado pelo Site Recovery. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
   - **Consistente com o aplicativo mais recente**: essa opção executa failover do computador para o ponto de recuperação consistente com o aplicativo mais recente pelo Site Recovery.
   - **Personalizado**: especifica um ponto de recuperação.

3. Selecione **Desligar o computador antes do início do failover** se quiser que o Site Recovery tente realizar um desligamento o computador de origem antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Jobs.**
4. Se você preparou a conexão com a VM do Azure, conecte-se para validá-la após o failover.
5. Depois de verificar, **Confirme** o failover. Essa ação exclui todos os pontos de recuperação disponíveis.

> [!WARNING]
> Não cancele um failover em andamento. Antes do início do failover, a replicação do computador é interrompido. Se você cancelar o failover, ele será interrompido, mas o computador não replicará novamente.
> Para servidores físicos, o processamento adicional de failover pode levar cerca de oito a dez minutos para concluir.

## <a name="automate-actions-during-failover"></a>Automatize ações durante o failover

Você pode querer automatizar ações durante o failover. Para fazer isso, você pode usar scripts ou runbooks de automação do Azure em planos de recuperação.

- [Aprenda](site-recovery-create-recovery-plans.md) a criar e personalizar planos de recuperação, incluindo a adição de scripts.
- [Aprenda abut](site-recovery-runbook-automation.md) adicionando runbooks da Azure Automation aos planos de recuperação.

## <a name="configure-settings-after-failover"></a>Configure configurações após failover

Após failover, você precisa [configurar as configurações do Azure](site-recovery-failover.md#prepare-in-azure-to-connect-after-failover) para se conectar às VMs Azure replicadas. Além disso, configure o endereçamento IP [interno e público.](site-recovery-failover.md#set-up-ip-addressing)

## <a name="prepare-for-reprotection-and-failback"></a>Prepare-se para reproteção e failback

Depois de falhar no Azure, você reprotege as VMs do Azure replicando-as no site local. Depois que eles estiverem se replicando, você pode recomplicá-los de volta ao local, executando um failover do Azure para o seu site local.

1. Servidores físicos replicados no Azure usando o Site Recovery só podem falhar como VMs VMware. Você precisa de uma infraestrutura de VMware local para executar o failback. Siga as etapas [deste artigo](vmware-azure-prepare-failback.md) para se preparar para reproteção e failback, incluindo a configuração de um servidor de processo no Azure e um servidor de destino mestre no local e a configuração de uma VPN site-to-site, ou peering privado ExpressRoute, para failback.
2. Certifique-se de que o servidor de configuração local está sendo executado e conectado ao Azure. Durante o failover para o Azure, o site no local pode não estar acessível e o servidor de configuração pode estar indisponível ou desligado. Durante o failback, a VM deve existir no banco de dados do servidor de configuração. Caso contrário, o failback será malsucedido.
3. Exclua quaisquer instantâneos no servidor de destino principal no local. A reproteção não funcionará se houver instantâneos.  Os instantâneos na VM são automaticamente mesclados durante um trabalho de reproteção.
4. Se você estiver reprotegendo as VMs reunidas em um grupo de replicação para obter consistência de várias VM, certifique-se de que todas elas tenham o mesmo sistema operacional (Windows ou Linux) e certifique-se de que o servidor de destino mestre que você implanta tenha o mesmo tipo de sistema operacional. Todas as VMs em um grupo de replicação devem usar o mesmo servidor de destino mestre.
5. Abra [as portas necessárias](vmware-azure-prepare-failback.md#ports-for-reprotectionfailback) para failback.
6. Certifique-se de que o servidor vCenter está conectado antes da refalha. Caso contrário, desconectando discos e anexá-los de volta para a máquina virtual falha.
7. Se um servidor vCenter gerenciar as VMs para as quais você falhará, certifique-se de que você tem as permissões necessárias. Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. No entanto, durante a reproteção, o failover falha porque os datastores não podem ser descobertos e não estão listados durante a reproteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma [conta/permissões apropriadas](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery)e, em seguida, tentar novamente o trabalho. 
8. Se você usou um modelo para criar suas máquinas virtuais, certifique-se de que cada VM tenha seu próprio UUID para os discos. Se o UIID VM no local se choca com o UUID do servidor de destino principal porque ambos foram criados a partir do mesmo modelo, a reproteção falhará. Implantar a partir de um modelo diferente.
9. Se você estiver falhando em um servidor vCenter alternativo, certifique-se de que o novo servidor de destino vCenter e o servidor de destino mestre sejam descobertos. Normalmente, se eles não são os datastores não são acessíveis ou não são visíveis no **Reprotect**.
10. Verifique os seguintes cenários em que você não pode falhar:
    - Se você estiver usando a edição gratuita ESXi 5.5 ou a edição livre do vSphere 6 Hypervisor. Atualize para uma versão diferente.
    - Se você tiver um servidor físico Windows Server 2008 R2 SP1.
    - VMs que [foram migradas](migrate-overview.md#what-do-we-mean-by-migration).
    - Uma VM que foi transferida para outro grupo de recursos.
    - Uma réplica da Azure VM que foi excluída.
    - Uma réplica da Azure VM que não está protegida (replicando-se no local).
10. [Revise os tipos de failback que](concepts-types-of-failback.md) você pode usar - recuperação de localização original e recuperação de localização alternativa.


## <a name="reprotect-azure-vms-to-an-alternate-location"></a>Reproteja as VMs do Azure para um local alternativo

Este procedimento pressupõe que a VM no local não está disponível.

1. No cofre > **Configurações** > **Itens replicados,** clique com o botão direito do mouse na máquina que foi falhada sobre > **Re-Protect**.
2. Em **Proteger novamente**, verifique se **Do Azure para local** está selecionado.
3. Especifique o servidor de destino mestre local e o servidor de processo.
4. Em **Armazenamento de dado**, selecione o armazenamento de dados de destino mestre para o qual deseja recuperar os discos locais.
       - Use esta opção se a VM local foi excluída ou não existir, e você precisa criar novos discos.
       - Essa configuração é ignorada se os discos já existirem, mas você precisa especificar um valor.
5. Selecione a unidade de retenção de destino mestre. A politica de failback é selecionada automaticamente.
6. Clique em **OK** para iniciar a nova proteção. Um trabalho começa a replicar a VM do Azure no local. Você pode acompanhar o andamento na guia **Trabalhos**.

> [!NOTE]
> Se você deseja recuperar a VM do Azure para uma VM local existente, monte o armazenamento de dados da máquina virtual local com acesso de leitura/gravação no host ESXi do servidor de destino mestre.


## <a name="fail-back-from-azure"></a>Failback do Azure

Execute o failover da seguinte maneira:

1. Na página **Itens duplicados**, clique como botão direito do mouse no computador > **Failover Não Planejado**.
2. Em **Confirmar Failover**, verifique se a direção do failover é do Azure.
3.Selecione o ponto de recuperação que deseja usar para o failover.
    - Recomendamos que você use o **último** ponto de recuperação. O ponto consistente do aplicativo está por trás do ponto mais recente no tempo, e causa alguma perda de dados.
    - **O mais recente** é um ponto de recuperação consistente com falhas.
    - Quando o failover é executado, o Site Recovery encerra as VMs do Azure e inicializa a VM local. Haverá em certo tempo de inatividade, portanto escolha um momento apropriado.
4. Com o botão direito do mouse no computador e, em seguida, clique em **Confirmar**. Isso dispara um trabalho que remove as VMs do Azure.
5. Verifique se as VMs do Azure foram desligadas conforme o esperado.


## <a name="reprotect-on-premises-machines-to-azure"></a>Proteger novamente as máquinas locais para o Azure

Os dados agora devem estar de volta no site local, mas eles não estão sendo replicados para o Azure. Você pode iniciar a replicação para o Azure novamente da seguinte maneira:

1. Em cofre > **Configurações** >**Itens replicados**, selecione as VMs com failback e clique em **Proteger novamente**.
2. Selecione o servidor de processo que é usado para enviar os dados replicados para o Azure e, em seguida, clique em **OK**.


## <a name="next-steps"></a>Próximas etapas

Após o término do trabalho de reproteção, a VM no local será replicada para o Azure. Conforme necessário, você pode [executar outro failover](site-recovery-failover.md) para Azure.
