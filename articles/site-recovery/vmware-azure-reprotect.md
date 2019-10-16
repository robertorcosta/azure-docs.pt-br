---
title: Proteger novamente as VMs do Azure para um site local durante a recuperação de desastre de VMs e servidores físicos do VMware | Microsoft Docs
description: Após o failover para o Azure durante a recuperação de desastre de VMs e servidores físicos do VMware, saiba como fazer o failback do Azure para o site local.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mayg
ms.openlocfilehash: 2f6f865f019b8b2a403865db4e59a7e86f59e509
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331053"
---
# <a name="reprotect-and-fail-back-machines-to-an-on-premises-site-after-failover-to-azure"></a>Proteja novamente e execute o failback de computadores em um site local após o failover no Azure

Depois de [failover](site-recovery-failover.md) de máquinas virtuais do VMware no local ou servidores físicos para o Azure, a primeira etapa com falha para seu site local é Proteja as VMs do Azure que foram criados durante o failover. Este artigo descreve como fazer isso. 

Para obter uma visão geral, assista a este vídeo sobre como fazer failover do Azure para um site local.<br /><br />
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="before-you-begin"></a>Antes de começar

Se você usou um modelo para criar suas máquinas virtuais, verifique se cada máquina virtual tem seus próprios UUIDs para os discos. Se o UUID da máquina virtual local conflita com o UUID do destino mestre porque ambos foram criadas a partir do mesmo modelo, que passou por failover falhará. Implante outro destino mestre que não foi criado a partir do mesmo modelo. Observe as seguintes informações:
- Se você estiver tentando realizar failback para um vCenter alternativo, verifique se seu novo vCenter e o servidor de destino mestre estão descobertos. Um sintoma típico é que os armazenamentos de dados não estão acessíveis ou não estão visíveis no **proteja** caixa de diálogo.
- Para replicar de volta para local, você precisa de uma política de failback. Essa política é criada automaticamente quando você criar uma política de frente. Observe as seguintes informações:
    - Essa política é automaticamente associada com o servidor de configuração durante a criação.
    - Essa política não é editável.
    - Os valores de conjunto da política são (limite de RPO = 15 minutos, retenção de ponto de recuperação = 24 horas, frequência de instantâneo de consistência do aplicativo = 60 minutos).
- Durante a reprotecção e o failback, o servidor de configuração local deve estar em execução e conectado.
- Se um vCenter Server gerenciar as máquinas virtuais para as quais fará failback, certifique-se de que você possui as [permissões necessárias](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) para descoberta de VMs nos servidores vCenter.
- Exclua instantâneos no servidor de destino mestre antes da nova proteção. Se as capturas instantâneas estiverem presentes no destino principal local ou na máquina virtual, a reprotecção falhará. Os instantâneos na máquina virtual são mesclados automaticamente durante um trabalho de nova proteção.
- Todas as máquinas virtuais de um grupo de replicação deve ser do mesmo tipo de sistema operacional (todos os Windows ou Linux todos). Um grupo de replicação com sistemas operacionais mistos atualmente não há suporte para a nova proteção e o failback para local. Isso ocorre porque o destino principal deve ser do mesmo sistema operacional da máquina virtual. Todas as máquinas virtuais de um grupo de replicação deve ter o mesmo destino mestre. 
- Um servidor de configuração é necessário localmente ao fazer um failback. Durante o failback, a máquina virtual deve existir no banco de dados do servidor de configuração. Caso contrário, o failback será malsucedido. Certifique-se de que você faça backups agendados regularmente de seu servidor de configuração. Em caso de desastre, restaure o servidor com o mesmo endereço IP para que o failback funcione. 
- A nova proteção e o failback exigem uma VPN de site a site (S2S) ou um emparelhamento privado do ExpressRoute para replicar dados. Forneça a rede de modo que as máquinas virtuais que passaram por failover no Azure possam alcançar (executar ping) o servidor de configuração local. Você precisa implantar um servidor de processo na rede do Azure das máquinas virtuais com failover. Esse servidor de processo também deve ser capaz de se comunicar com o servidor de configuração local e com o servidor de destino mestre.
- Caso os endereços IP dos itens replicados tenham sido retidos no failover, a conectividade S2S ou do ExpressRoute deve ser estabelecida entre as máquinas virtuais do Azure e a NIC de failback do servidor de configuração. Observe que a retenção de endereço IP requer que o servidor de configuração tenha duas NICs-uma para conectividade de computadores de origem e outra para a conectividade de failback do Azure. Isso é para evitar a sobreposição de intervalos de endereços de sub-rede da origem e as máquinas virtuais com failover.
- Certifique-se de que você abrir as seguintes portas para failover e failback:

    ![Portas para failover e failback](./media/vmware-azure-reprotect/failover-failback.png)

- Leia todo o [pré-requisitos para portas e lista de permissão de URL](vmware-azure-deploy-configuration-server.md#prerequisites).

## <a name="deploy-a-process-server-in-azure"></a>Implantar um servidor em processo no Azure

Você precisa de um servidor de processo no Azure antes de realizar o failback para o site local:

- O servidor de processo recebe dados das máquinas virtuais protegidas no Azure e, em seguida, envia dados para o site local.
- Uma rede de baixa latência é necessária entre o servidor de processo e a máquina virtual protegida. Portanto, é recomendável que você implante um servidor de processo no Azure. O desempenho da replicação será maior se o servidor de processo estiver mais próximo da máquina virtual de replicação (a máquina no Azure que passou pelo failover). 
- Para uma prova de conceito, você pode usar o servidor de processos local e o ExpressRoute com emparelhamento privado.

Para implantar um servidor de processo no Azure:

1. Se você precisar implantar um servidor de processo no Azure, consulte [configurar um servidor de processo no Azure para failback](vmware-azure-set-up-process-server-azure.md).
2. Máquinas virtuais do Azure envie dados de replicação para o servidor de processo. Configure redes, de modo que as VMs do Azure possam alcançar o servidor de processo.
3. Lembre-se de que a replicação do Azure para local pode ocorrer apenas por meio de VPN S2S, ou através do emparelhamento privado da rede do ExpressRoute. Certifique-se de que há largura de banda suficiente disponível através desse canal de rede.

## <a name="deploy-a-separate-master-target-server"></a>Implantar um servidor de destino mestre separado

O servidor de destino mestre recebe dados de failback. Por padrão, o servidor de destino mestre é executado no servidor de configuração local. No entanto, dependendo do volume do tráfego de failback, talvez seja necessário criar um servidor de destino mestre separado para o failback. Veja como criar um:

* [Crie um servidor de destino mestre Linux](vmware-azure-install-linux-master-target.md) para failback de VMs do Linux. Isso é necessário. Não há suporte para o servidor de destino mestre no LVM.
* Opcionalmente, crie um servidor de destino mestre separado para failback de VM do Windows. Para fazer isso, execute a instalação do Unified novamente e selecione para criar um servidor de destino mestre. [Saiba mais](site-recovery-plan-capacity-vmware.md#deploy-additional-master-target-servers). 

Depois de criar um servidor de destino mestre, faça o seguinte:

- Se a máquina virtual está presente no local no servidor do vCenter, o servidor de destino mestre precisa de acesso ao arquivo de disco de máquina Virtual (VMDK) da máquina virtual local. É necessário acesso para gravar os dados replicados em discos da máquina virtual. Verifique se o armazenamento de dados da máquina virtual local está montado no host de destino mestre com acesso de leitura/gravação.
- Se a máquina virtual não está presente no local no servidor do vCenter, o serviço de recuperação de Site precisa criar uma nova máquina virtual durante a nova proteção. Essa máquina virtual é criada no host ESX em que você criou o destino mestre. Escolha o host ESX com cuidado para que a máquina virtual de failback seja criada no host desejado.
- Você não pode usar vMotion de armazenamento para o servidor de destino mestre. Usar vMotion de armazenamento para o servidor de destino mestre pode causar o failback falha. A máquina virtual não pode iniciar porque os discos não estão disponíveis para ele. Para evitar esse problema, exclua os servidores de destino mestre de sua lista de vMotion.
- Se um destino mestre passar por uma tarefa de vMotion de armazenamento depois que passou por failover, os discos de máquina virtual protegida que estão anexados ao destino mestre migrar para o destino da tarefa vMotion. Se você tentar fazer failback após isso, a ação de desanexar o disco resultará em falha porque os discos não foram encontrados. Assim, ficará difícil localizar os discos em suas contas de armazenamento. Você precisará localizá-los manualmente e anexá-los à máquina virtual. Depois disso, a máquina virtual local poderá ser reinicializada.
- Adicione uma unidade de retenção ao servidor de destino mestre existente do Windows. Adicione um novo disco e formate a unidade. A unidade de retenção é usada para interromper os pontos no tempo em que a máquina virtual replica para o site local. A seguir estão alguns critérios de uma unidade de retenção. Se esses critérios não forem atendidos, a unidade não estiver listada para o servidor de destino mestre:
    - O volume não é usado para nenhum outro propósito, como um destino de replicação.
    - O volume não está no modo de bloqueio.
    - O volume não é um volume de cache. A instalação de destino mestre não existe nesse volume. O volume de instalação personalizado para o destino mestre e servidor de processo não está qualificado para um volume de retenção. Quando o servidor de processo e o destino mestre são instalados em um volume, o volume é um cache de destino mestre.
    - Não é o tipo de sistema de arquivos do volume FAT ou FAT32.
    - A capacidade do volume é diferente de zero.
    - O volume de retenção padrão para o Windows é o volume R.
    - O volume de retenção padrão para o Linux é /mnt/retention.
- Se você estiver usando uma máquina de servidor de configuração do servidor de processo existente ou um computador de servidor de destino de servidor/mestre de escala ou processo, você deve adicionar uma nova unidade. A nova unidade deve atender os requisitos anteriores. Se a unidade de retenção não estiver presente, ele não aparecer na lista suspensa de seleção no portal. Depois de adicionar uma unidade ao destino mestre local, levará até 15 minutos para que a unidade apareça na seleção no portal. Você também pode atualizar o servidor de configuração se a unidade não aparecer após 15 minutos.
- Instale as ferramentas do VMware ou o open-vm-tools no servidor de destino principal. Sem as ferramentas, os datastores no host ESXi do destino mestre não podem ser detectados.
- Defina a configuração `disk.EnableUUID=true` nos parâmetros de configuração da máquina virtual de destino mestre no VMware. Se essa linha não existir, adicione-o. Essa configuração é necessária para fornecer um UUID consistente para o VMDK para que ele monta corretamente.
- O host do ESX na qual o destino mestre é criado deve ter pelo menos uma máquina virtual arquivo system (VMFS) repositório de dados anexado a ele. Se não há armazenamentos de dados VMFS são anexados, a **Datastore** entrada na página nova proteção está vazia e não pode continuar.
- O servidor de destino mestre não pode ter instantâneos dos discos. Se houver instantâneos, a nova proteção e o failback falharão.
- O destino mestre não pode ter um controlador SCSI Paravirtual. O controlador só pode ser um controlador LSI Logic. Sem um controlador de Lógica LSI, a nova proteção falha.
- Para qualquer instância de destino mestre pode ter no máximo 60 discos conectados a ele. Se o número de máquinas virtuais sendo reprotegida ao destino mestre local tem mais de um número total de 60 discos, reprotects ao destino mestre começam a falhar. Certifique-se de que você tenha slots de disco suficientes no destino mestre ou implante servidores de destino mestre adicionais.
    

## <a name="enable-reprotection"></a>Habilitar nova proteção

Depois que uma máquina virtual é reinicializada no Azure, leva algum tempo para que o agente seja registrado no servidor de configuração (até 15 minutos). Durante esse tempo, não será capaz de proteger novamente e uma mensagem de erro indica que o agente não está instalado. Se isso acontecer, aguarde alguns minutos e tente proteger novamente.


1. Selecione **cofre** > **replicadas itens**. A máquina virtual que falhou e, em seguida, selecione **proteger novamente**. Ou, os botões de comando, selecione a máquina e, em seguida, selecione **proteger novamente**.
2. Verifique o **do Azure para local** direção de proteção está selecionada.
3. Em **Servidor de Destino Mestre** e **Servidor de Processo**, selecione o servidor de destino mestre local e o servidor de processo.  
4. Para **Repositório de Dados**, selecione o repositório de dados no qual você deseja recuperar os discos localmente. Essa opção é usada quando a máquina virtual no local é excluída e você precisa criar novos discos. Essa opção será ignorada se os discos já existem. Você ainda precisa especificar um valor.
5. Selecione a unidade de retenção.
6. A politica de failback é selecionada automaticamente.
7. Selecione **Okey** para iniciar a nova proteção. Um trabalho começará a replicar a máquina virtual do Azure para o site local. Você pode acompanhar o progresso na guia **trabalhos** . Quando a nova proteção for realizada com sucesso, a máquina virtual entrará em um estado protegido.

Observe as seguintes informações:
- Se você quiser recuperar para um local alternativo (quando a máquina virtual local for excluída), selecione a unidade de retenção e o repositório de dados configurados para o servidor de destino mestre. Quando você realiza o failback para o site local, as máquinas virtuais do VMware no plano de proteção de failback usam o mesmo repositório de dados que o servidor de destino mestre. Então, uma nova máquina virtual é criada no vCenter.
- Se você quiser recuperar a máquina virtual do Azure para uma máquina virtual local existente, monte os repositórios de dados da máquina virtual local com acesso de leitura/gravação no host de ESXi do servidor de destino mestre.

    ![Proteja a caixa de diálogo](./media/vmware-azure-reprotect/reprotectinputs.png)

- Também é possível proteger novamente no nível de um plano de recuperação. Um grupo de replicação pode ser protegido novamente somente por meio de um plano de recuperação. Quando você proteger novamente usando um plano de recuperação, você deve fornecer os valores para todos os computadores protegidos.
- Use o mesmo servidor de destino mestre para proteger novamente um grupo de replicação. Se você usar um servidor de destino mestre diferente para proteger novamente um grupo de replicação, o servidor não pode fornecer um ponto comum no tempo.
- A máquina virtual local é desativada durante a nova proteção. Isso ajuda a garantir a consistência de dados durante a replicação. Não ligar a máquina virtual após a conclusão da nova proteção.



## <a name="common-issues"></a>Problemas comuns

- Se você executar a descoberta de vCenter de Usuário somente leitura e proteger as máquinas virtuais, a proteção terá êxito e o failover funcionará. Durante a nova proteção, o failover falhar porque os armazenamentos de dados não podem ser descobertos. Um sintoma é que os repositórios de dados não estão listados durante a nova proteção. Para resolver esse problema, você pode atualizar as credenciais do vCenter com uma conta apropriada que tenha permissões e, em seguida, repita o trabalho. 
- Ao executar failback de uma máquina virtual Linux e executá-la localmente, você verá que o pacote do Gerenciador de Rede foi desinstalado do computador. Essa desinstalação ocorre porque o pacote do Gerenciador de rede é removido quando a máquina virtual será recuperada no Azure.
- Quando uma máquina virtual Linux é configurada com um endereço IP estático e o failover é feito para o Azure, o endereço IP é obtido do DHCP. Quando você executa o failover localmente, a máquina virtual continua a usar o DHCP para obter o endereço IP. Entrar manualmente para a máquina e, em seguida, defina o endereço IP para um endereço estático, se necessário. Uma máquina virtual do Windows podem obter seu endereço IP novamente.
- Se você usar a edição gratuita ESXi 5.5 ou a edição gratuita do vSphere 6 hipervisor, o failover for bem-sucedido, mas failback não for bem-sucedida. Para permitir o failback, atualize para a licença de avaliação dos programas.
- Se você não conseguir acessar o servidor de configuração do servidor de processo, use o Telnet para verificar a conectividade com o servidor de configuração na porta 443. Você também pode tentar executar ping no servidor de configuração do servidor de processo. Um servidor de processo também deve ter uma pulsação quando ele está conectado ao servidor de configuração.
- Um servidor do Windows Server 2008 R2 SP1 que é protegido como um servidor de local físico não pode ser com falha do Azure para um site local.
- Não é possível fazer failback nas seguintes circunstâncias:
    - Você migrou computadores para o Azure. [Saiba mais](migrate-overview.md#what-do-we-mean-by-migration).
    - Uma VM foi movida para outro grupo de recursos.
    - A VM do Azure foi excluída.
    - A proteção da VM foi desabilitada.
    - A VM foi criada manualmente no Azure. A máquina deve ter protegido inicialmente no local e failover para o Azure antes que passou por failover.
    - Você pode não apenas para um host de ESXi. Não é possível failback de VMs VMware ou servidores físicos para hosts do Hyper-V, computadores físicos ou estações de trabalho VMware.


## <a name="next-steps"></a>Próximos passos

Depois que a máquina virtual entrou em um estado protegido, você poderá [iniciar um failback](vmware-azure-failback.md). O failback desliga a máquina virtual no Azure e inicia a máquina virtual no local. Espere algum tempo de inatividade do aplicativo. Escolha um horário para realizar failback quando o aplicativo puder tolerar o tempo de inatividade.


