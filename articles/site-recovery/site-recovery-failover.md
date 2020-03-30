---
title: Execute um failover durante a recuperação de desastres com a recuperação do site do Azure
description: Como falhar sobre VMs/servidores físicos para o Azure com a Recuperação do Site do Azure.
ms.service: site-recovery
ms.topic: article
ms.date: 12/10/2019
ms.openlocfilehash: 99a197e8f5ebac8a3b0be1b567ee41b43a2c4476
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471261"
---
# <a name="run-a-failover-from-on-premises-to-azure"></a>Execute um failover do local para o Azure

Este artigo descreve como falhar sobre máquinas locais para o Azure no [Azure Site Recovery](site-recovery-overview.md)

## <a name="before-you-start"></a>Antes de começar

- [Saiba](failover-failback-overview.md) mais sobre o processo de failover na recuperação de desastres.
- Se você quer falhar sobre várias máquinas, [aprenda](recovery-plan-overview.md) a reunir máquinas em um plano de recuperação.
- Antes de fazer um failover completo, execute uma [broca de recuperação de desastres](site-recovery-test-failover-to-azure.md) para garantir que tudo esteja funcionando como esperado.

## <a name="prepare-to-connect-after-failover"></a>Prepare-se para se conectar após failover

Para ter certeza de que você pode se conectar às VMs do Azure que são criadas após o failover, aqui estão uma série de coisas que você precisa fazer no local antes do failover.


### <a name="prepare-on-premises-to-connect-after-failover"></a>Prepare-se no local para se conectar após failover

Se você quiser se conectar às VMs do Azure usando RDP/SSH após failover, há uma série de coisas que você precisa fazer no local antes do failover.

**Após failover** | **Local** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | Computador local antes do failover | Para acessar a VM do Azure pela Internet, habilite o RDP e fazer com que as regras de TCP e UDP sejam adicionadas para o **Público** e que o RDP foi permitido para todos os perfis no **Firewall do Windows** > **Aplicativos Permitidos**.<br/><br/> Para acessar o Azure VM por meio de uma conexão site-a-site, habilite o RDP na máquina e certifique-se de que o RDP seja permitido nos**aplicativos e recursos permitidos**pelo **Firewall** -> do Windows, para redes **de domínio e privado.**<br/><br/> <br/><br/> Remova quaisquer rotas estáticas persistentes e proxy WinHTTP. Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Verifique se não existem Windows atualizações pendentes na VM virtual quando disparar um failover. A atualização do Windows poderá iniciar quando o failover for feito, e você não poderá fazer logon na VM até que a atualização seja concluída.
**VM do Azure executando Linux** | Computador local antes do failover | Verifique se o serviço Secure Shell na VM está definido para iniciar automaticamente na inicialização do sistema.<br/><br/> Verifique se as regras de firewall permitem uma conexão SSH com ele.


## <a name="run-a-failover"></a>Executar um failover

Este procedimento descreve como executar um failover para um [plano de recuperação](site-recovery-create-recovery-plans.md). Se você quiser executar um failover para uma única VM, siga as instruções de um [VMware VM,](vmware-azure-tutorial-failover-failback.md)um [servidor físico](physical-to-azure-failover-failback.md)ou um [Hyper-V VM](hyper-v-azure-failover-failback-tutorial.md).


Execute o failover do plano de recuperação da seguinte forma:

1. No cofre de recuperação do site, selecione **Planos** > de recuperação*recoveryplan_name*.
2. Clique em **Failover**.

    ![Failover](./media/site-recovery-failover/Failover.png)

3. Na **direção Failover** > **Failover,** deixe o padrão se estiver replicando no Azure.
4. Em **Failover,** selecione um **ponto de recuperação** para o qual falhar.

    - **Mais recente**: Use o último ponto. Isso processa todos os dados enviados ao serviço de recuperação de sites e cria um ponto de recuperação para cada máquina. Esta opção fornece o RPO mais baixo (Recovery Point Objective) porque a VM criada após failover tem todos os dados que foram replicados para recuperação do site quando o failover foi acionado.
   - **Mais recente processado**: Use esta opção para falhar sobre VMs para o último ponto de recuperação já processado pela Recuperação do Site. Você pode ver o último ponto de recuperação processado nos **últimos pontos de recuperação**da VM . Essa opção fornece um RTO baixo, pois não é gasto tempo para processar os dados não processados
   - **Mais recente consistente com**o aplicativo : Use esta opção para falhar VMs até o ponto de recuperação consistente do aplicativo mais recente que foi processado pela Recuperação do Site.
   - **O mais recente multi-VM processado**: Com esta opção, as VMs que fazem parte de um failover do grupo de replicação para o mais recente ponto de recuperação consistente de vários VM comuns. Outras máquinas virtuais falham em seu último ponto de recuperação processado. Esta opção é apenas para planos de recuperação que tenham pelo menos uma VM com consistência multi-VM ativada.
   - **Mais recente multi-VM-consistente**com aplicativos : Com esta opção, as VMs que fazem parte de um grupo de replicação falham no mais recente ponto de recuperação comum de vários VM. Outras máquinas virtuais fazem failover para seus últimos pontos de recuperação consistentes com aplicativo. Somente para planos de recuperação que tenham pelo menos uma VM com consistência multi-VM habilitada.
   - **Personalizado**: Não disponível para planos de recuperação. Esta opção é apenas para failover de VMs individuais.

5. Selecione **Máquina de desligar antes de iniciar o failover** se quiser que a Recuperação do Site desligue as VMs de origem antes de iniciar o failover. O failover continuará mesmo o desligamento falhar.  

    > [!NOTE]
    > Se você falhar em VMs Hyper-V, o desligamento tentará sincronizar e replicar os dados no local que ainda não foram enviados ao serviço, antes de acionar o failover. 

6. Siga o progresso do failover na página **Jobs.** Mesmo que ocorram erros, o plano de recuperação é executado até que esteja concluído.
7. Após o failover, entre no VM para validá-lo. 
8. Se você quiser mudar para diferentes pontos de recuperação para usar para o failover, use **Alterar ponto de recuperação**.
9. Quando estiver pronto, pode cometer o failover. A **ação Comprometer** exclui todos os pontos de recuperação disponíveis com o serviço. A opção **de ponto de recuperação Alterar** não estará mais disponível.

## <a name="run-a-planned-failover-hyper-v"></a>Execute um failover planejado (Hyper-V)

Você pode executar um failover planejado para VMs Hyper-V.

- Um failover planejado é uma opção de failover de perda zero de dados.
- Quando um failover planejado é acionado, primeiro as máquinas virtuais de origem são desligadas, depois os dados mais recentes são sincronizados e, em seguida, um failover é acionado.
- Você executa um failover planejado usando a opção **failover planejada.** Ele funciona de forma semelhante a um failover regular.
 
## <a name="track-failovers"></a>Falhas de pista

Há uma série de empregos associados ao failover.

![Failover](./media/site-recovery-failover/FailoverJob.png)

- **Verificação de pré-requisitos**: Garante que todas as condições necessárias para o failover sejam atendidas.
- **Failover**: Processa os dados para que uma VM Azure possa ser criada a partir dele. Se você tiver escolhido **o último** ponto de recuperação, um ponto de recuperação é criado a partir dos dados enviados para o serviço.
- **Início**: Cria uma VM Azure usando os dados processados na etapa anterior.

> [!WARNING]
> **Não cancele um failover em andamento**: Antes do failover ser iniciado, a replicação é interrompida para a VM. Se você cancelar um trabalho em andamento, o failover será cancelado, mas a VM não começará a se replicar. A replicação não pode ser reiniciada.


### <a name="extra-failover-time"></a>Tempo extra de failover

Em alguns casos, o failover vm requer passo intermediário que geralmente leva em torno de oito a 10 minutos para ser concluído. Estas são as máquinas que são afetadas por essa etapa/tempo adicional:

* Máquinas virtuais VMware executando uma versão de serviço mobility com mais de 9.8.
* Servidores físicos e VMs hiper-v protegidos como servidores físicos.
* VMs VMware Linux.
* VMware VMs em que esses drivers não estão presentes como drivers de inicialização:
    * storvsc
    * vmbus
    * storflt
    * intelide
    * atapi
* VMware VMs que não possuem DHCP ativado, independentemente de estarem usando DHCP ou endereços IP estáticos.


## <a name="automate-actions-during-failover"></a>Automatize ações durante o failover

Você pode querer automatizar ações durante o failover. Para fazer isso, você pode usar scripts ou runbooks de automação do Azure em planos de recuperação.

- [Aprenda](site-recovery-create-recovery-plans.md) a criar e personalizar planos de recuperação, incluindo a adição de scripts.
- [Saiba](site-recovery-runbook-automation.md) como adicionar os runbooks da Azure Automation aos planos de recuperação.


## <a name="configure-settings-after-failover"></a>Configure configurações após failover

### <a name="retain-drive-letters-after-failover"></a>Reter letras de unidade após failover

A Recuperação do Site lida com a retenção de letras de unidade. Se você estiver excluindo discos durante a replicação de VM, [revise um exemplo](exclude-disks-replication.md#example-1-exclude-the-sql-server-tempdb-disk) de como isso funciona.

### <a name="prepare-in-azure-to-connect-after-failover"></a>Prepare-se no Azure para se conectar após failover

Se você quiser se conectar às VMs do Azure criadas após failover usando RDP ou SSH, siga os requisitos resumidos na tabela.

**Failover** | **Local** | **Ações**
--- | --- | ---
**VM do Azure executando o Windows** | VM do Azure após o failover |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras de grupo de segurança de rede na VM com failover e a sub-rede do Azure à qual ela está conectada precisam permitir conexões de entrada para a porta RDP.<br/><br/> Verifique o **Diagnóstico de inicialização** para examinar uma captura de tela da VM.<br/><br/> Se você não puder se conectar, verifique se a VM está sendo executado e revise essas [dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM do Azure executando Linux** | VM do Azure após o failover | As regras de grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) precisam permitir conexões de entrada para a porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique os **Diagnósticos de inicialização** para obter uma captura de tela da VM.<br/><br/>

Siga as etapas descritas [aqui](site-recovery-failover-to-azure-troubleshoot.md) para solucionar quaisquer problemas de conectividade após o failover.

## <a name="set-up-ip-addressing"></a>Configurar endereçamento IP

- **Endereços IP internos**: Para definir o endereço IP interno de uma VM do Azure após o failover, você tem algumas opções:
    - Mantenha o mesmo endereço IP: Você pode usar o mesmo endereço IP na VM do Azure como aquele alocado na máquina local.
    - Use diferente sion de IP: Você pode usar um endereço IP diferente para a VM do Azure.
    - [Saiba mais](concepts-on-premises-to-azure-networking.md#assign-an-internal-address) sobre a configuração de endereços IP internos.
- **Endereços IP externos**: Você pode reter endereços IP públicos no failover. As VMs do Azure criadas como parte do processo de failover devem ser atribuídas a um endereço IP público do Azure disponível na região do Azure. Você pode atribuir um endereço IP público manualmente ou automatizando o processo com um plano de recuperação. [Saiba mais](concepts-public-ip-address-with-site-recovery.md).


## <a name="next-steps"></a>Próximas etapas

Depois de ter falhado, você precisa reproteger para começar a replicar as VMs do Azure de volta ao local. Depois que a replicação estiver em funcionamento, você pode falhar no local quando estiver pronto.

- [Saiba mais](failover-failback-overview.md#reprotectionfailback) sobre reproteção e failback.
- [Prepare-se](vmware-azure-reprotect.md) para reproteção e failback da VMware.
- [Falhar de volta](hyper-v-azure-failback.md) Hiper-VMs.
- [Saiba mais sobre](physical-to-azure-failover-failback.md) o processo de failover e failback para servidores físicos.

