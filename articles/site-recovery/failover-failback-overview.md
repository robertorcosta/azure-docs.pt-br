---
title: Sobre failover e failback na Recuperação do Site do Azure
description: Saiba mais sobre failover e failable no Azure Site Recovery.
ms.topic: conceptual
ms.date: 12/24/2019
ms.openlocfilehash: d9b54f3c452212e12419a5ffd67b116c8660308d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281802"
---
# <a name="about-on-premises-disaster-recovery-failoverfailback"></a>Sobre failover/failback de recuperação de desastres no local

Este artigo fornece uma visão geral do failover e failback durante a recuperação de desastres de máquinas locais para o Azure com [a Azure Site Recovery](site-recovery-overview.md).

## <a name="recovery-stages"></a>Estágios de recuperação

Failover e failback na Recuperação do Site tem quatro estágios:

- **Estágio 1: Falha no local**: Depois de configurar a replicação para o Azure para máquinas no local, quando o local do local cair, você falha com essas máquinas no Azure. Após o failover, as VMs do Azure são criadas com base nos dados replicados.
- **Estágio 2: Reproteja as VMs do Azure**: No Azure, você reprotege as VMs do Azure para que eles comecem a se replicar de volta ao local. A VM no local (se disponível) é desligada durante a reproteção, para ajudar a garantir a consistência dos dados.
- **Estágio 3: Fail over from Azure**: Quando seu site no local estiver funcionando normalmente novamente, você executará outro failover, desta vez para falhar de volta As VMs do Azure para o seu site no local. Você pode falhar de volta ao local original do qual você falhou, ou para um local alternativo.
- **Estágio 4: Reproteja as máquinas no local**: Depois de falhar, habilite novamente a replicação das máquinas no local para o Azure.

## <a name="failover"></a>Failover

Você executa um failover como parte de sua estratégia de continuidade de negócios e recuperação de desastres (BCDR).

- Como um primeiro passo em sua estratégia bcdr, você replica suas máquinas locais para o Azure de forma contínua. Os usuários acessam cargas de trabalho e aplicativos em execução nas máquinas de origem local.
- Se houver necessidade, por exemplo, se houver uma paralisação no local, você falhará com as máquinas de replicação no Azure. As VMs do Azure são criadas usando os dados replicados.
- Para a continuidade de negócios, os usuários podem continuar acessando aplicativos nas VMs do Azure.

Failover é uma atividade de duas fases:

- **Failover**: O failover que cria e traz uma VM Azure usando o ponto de recuperação selecionado.
- **Confirmar**: Após failover, verifique o VM no Azure:
    - Em seguida, você pode comprometer o failover no ponto de recuperação selecionado ou selecionar um ponto diferente para o compromisso.
    - Depois de cometer o failover, o ponto de recuperação não pode ser alterado.


## <a name="connect-to-azure-after-failover"></a>Conecte-se ao Azure após failover

Para se conectar às VMs do Azure criadas após failover usando RDP/SSH, existem uma série de requisitos.

**Failover** | **Local** | **Ações**
--- | --- | ---
**Azure VM (Windows)** | Na máquina no local antes do failover | **Acesso pela internet**: Habilitar RDP. Certifique-se de que as regras TCP e UDP sejam adicionadas para **público**, e que o RDP é permitido para todos os perfis em**Aplicativos Permitidos pelo** **Firewall** > do Windows .<br/><br/> **Acesso através de VPN site-to-site**: Habilite RDP na máquina. Verifique se o RDP é permitido nos**aplicativos e recursos permitidos pelo**Firewall -> do **Windows,** para redes **de domínio e privado.**<br/><br/>  Verifique se a política de SAN do sistema operacional está definida como **OnlineAll**. [Saiba mais](https://support.microsoft.com/kb/3031135).<br/><br/> Verifique se não existem Windows atualizações pendentes na VM virtual quando disparar um failover. O Windows Update pode começar quando você falhar, e você não poderá fazer logon na VM até que as atualizações sejam feitas.
**VM do Azure executando o Windows** | Na VM Azure após failover |  [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> As regras do grupo de segurança da rede sobre a falha sobre a VM (e a sub-rede Azure à qual está conectada) devem permitir conexões recebidas à porta RDP.<br/><br/> Verifique o **Diagnóstico de inicialização** para examinar uma captura de tela da VM. Se você não puder se conectar, verifique se a VM está sendo executado e revise [as dicas de solução de problemas](https://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).
**VM do Azure executando Linux** | Na máquina no local antes do failover | Verifique se o serviço Secure Shell na VM está definido para iniciar automaticamente na inicialização do sistema.<br/><br/> Verifique se as regras de firewall permitem uma conexão SSH com ele.
**VM do Azure executando Linux** | Na VM Azure após failover | As regras de grupo de segurança de rede na VM com failover (e a sub-rede do Azure à qual ela está conectada) precisam permitir conexões de entrada para a porta SSH.<br/><br/> [Adicione um endereço IP público](https://aka.ms/addpublicip) para a VM.<br/><br/> Verifique os **Diagnósticos de inicialização** para obter uma captura de tela da VM.<br/><br/>

## <a name="types-of-failover"></a>Tipos de failover

A Recuperação do Site oferece diferentes opções de failover.

**Failover** | **Detalhes** | **Recuperação** | **Fluxo de trabalho**
--- | --- | --- | ---
**Failover de teste** | Usado para executar uma broca que valida sua estratégia BCDR, sem qualquer perda de dados ou tempo de inatividade.| Cria uma cópia da VM no Azure, sem impacto na replicação contínua ou no ambiente de produção. | 1. Execute um failover de teste em uma única VM ou em várias VMs em um plano de recuperação.<br/><br/> 2. Selecione um ponto de recuperação a ser usado para o failover do teste.<br/><br/> 3. Selecione uma rede Azure na qual a VM Do Zure será localizada quando for criada após failover. A rede é usada apenas para o failover de teste.<br/><br/> 4. Verifique se a broca funcionou como esperado. A Recuperação do Site limpa automaticamente as VMs criadas no Azure durante a broca.
**Failover planejado-Hyper-V**  | Normalmente usado para tempo de inatividade planejado.<br/><br/> As VMs de origem estão desligadas. Os dados mais recentes são sincronizados antes de começar o failover. | Perda zero de dados para o fluxo de trabalho planejado. | 1. Planeje uma janela de manutenção de tempo de inatividade e notifique os usuários.<br/><br/> 2. Desafaça aplicativos voltados para o usuário.<br/><br/> 3. Inicie um failover planejado com o último ponto de recuperação. O failover não é executado se a máquina não for desligada ou se os erros forem encontrados.<br/><br/> 4. Após o failover, verifique se a réplica do Azure VM está ativa no Azure.<br/><br/> 5. Comprometa o failover para terminar. A ação de confirmação exclui todos os pontos de recuperação.
**Failover-Hyper-V** | Normalmente executado se houver uma paralisação não planejada, ou o site principal não estiver disponível.<br/><br/> Desligá-lo opcionalmente e sincronizar as alterações finais antes de começar o failover.  | Perda mínima de dados para aplicativos. | 1. Inicie seu plano BCDR. <br/><br/> 2. Inicie um failover. Especifique se a Recuperação do Site deve desligar a VM e sincronizar/replicar as últimas alterações antes de acionar o failover.<br/><br/> 3. Você pode falhar em várias opções de ponto de recuperação, resumidas na tabela abaixo.<br/><br/> Se você não habilitar a opção de desligar a VM ou se a Recuperação do Site não puder desligá-la, o último ponto de recuperação será usado.<br/>O failover funciona mesmo que a máquina não possa ser desligada.<br/><br/> 4. Após o failover, verifique se a réplica do Azure VM está ativa no Azure.<br/> Se necessário, você pode selecionar um ponto de recuperação diferente da janela de retenção de 24 horas.<br/><br/> 5. Comprometa o failover para terminar. A ação commit exclui todos os pontos de recuperação disponíveis.
**Failover-VMware** | Normalmente executado se houver uma paralisação não planejada, ou o site principal não estiver disponível.<br/><br/> Opcionalmente, especifique que a Recuperação do Site deve tentar ativar um desligamento da VM e sincronizar e replicar as alterações finais antes de iniciar o failover.  | Perda mínima de dados para aplicativos. | 1. Inicie seu plano BCDR. <br/><br/> 2. Inicie um failover da Recuperação do Site. Especifique se a Recuperação do Site deve tentar ativar o desligamento da VM e sincronizar antes de executar o failover.<br/> O failover funciona mesmo que as máquinas não possam ser desligadas.<br/><br/> 3. Após o failover, verifique se a réplica do Azure VM está ativa no Azure. <br/>Se necessário, você pode selecionar um ponto de recuperação diferente da janela de retenção de 72 horas.<br/><br/> 5. Comprometa o failover para terminar. A ação de confirmação exclui todos os pontos de recuperação.<br/> Para VMs do Windows, o Site Recovery desativa as ferramentas VMware durante o failover.

## <a name="failover-processing"></a>Processamento de failover

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. Você pode observar tempos de failover de teste mais longos para:

* VMs VMware executando uma versão do Serviço de Mobilidade anterior à 9.8.
* Servidores físicos.
* VMs VMware Linux.
* VMs Hyper-V protegidas como servidores físicos.
* VMs VMware que não têm o serviço DHCP habilitado.
* VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

## <a name="recovery-point-options"></a>Opções de ponto de recuperação

Durante o failover, você pode selecionar uma série de opções de ponto de recuperação.

**Opção** | **Detalhes**
--- | ---
**Mais recente (rpo mais baixo)** | Esta opção fornece o menor rpo (recovery point objective, objetivo de ponto de recuperação). Primeiro processa todos os dados que foram enviados para o serviço de recuperação de site, para criar um ponto de recuperação para cada VM, antes de falhar com ele. Este ponto de recuperação tem todos os dados replicados para recuperação do site quando o failover foi acionado.
**Últimas processadas**  | Esta opção falha sobre VMs para o último ponto de recuperação processado pela Recuperação do Site. Para ver o último ponto de recuperação de uma VM específica, verifique **os pontos de recuperação mais recentes** nas configurações da VM. Essa opção fornece um RTO (Objetivo do Tempo de Recuperação) baixo porque não há tempo gasto para processar dados não processados.
**Mais recente aplicativo consistente** |  Essa opção falha em VMs para o ponto de recuperação mais recente consistente com o aplicativo processado pelo Site Recovery, se os pontos de recuperação consistentes com o aplicativo estiverem ativados. Verifique o último ponto de recuperação nas configurações da VM.
**O mais recente multi-VM processado** | Essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VM habilitada. As VMs com a configuração habilitaram o failover para o último ponto de recuperação consistente com várias VMs em comum. Quaisquer outras VMs no plano falham no último ponto de recuperação processado.
**Mais recente multi-VM-consistente com aplicativos** |  Essa opção está disponível para planos de recuperação com uma ou mais VMs com consistência de várias VM habilitada. As VMs que fazem parte de um failover do grupo de replicação para o ponto de recuperação comum mais recente de várias VMs consistente com aplicativo. Outras VMs fazem failover para seus últimos pontos de recuperação consistentes com aplicativo.
**Personalizado** | Use esta opção para falhar sobre uma VM específica para um determinado ponto de recuperação no tempo. Esta opção não está disponível para planos de recuperação.

> [!NOTE]
> Os pontos de recuperação não podem ser migrados para outro cofre dos Serviços de Recuperação.

## <a name="reprotectionfailback"></a>Reproteção/failback

Após o failover para o Azure, as VMs Azure replicadas estão em um estado desprotegido.

- Como um primeiro passo para não voltar ao seu site local, você precisa iniciar as VMs do Azure replicando-se no local. O processo de reproteção depende do tipo de máquinas que você falhou.
- Depois que as máquinas estiverem se replicando do Azure para o local, você pode executar um failover do Azure para o local.
- Depois que as máquinas estiverem sendo executados no local novamente, você pode habilitar a replicação para que elas se repliquem ao Azure para recuperação de desastres.

O failback funciona da seguinte forma:

- Para falhar, um VM precisa de pelo menos um ponto de recuperação para falhar. Em um plano de recuperação, todas as VMs do plano precisam de pelo menos um ponto de recuperação.
- Recomendamos que você use o ponto de recuperação **mais recente** para falhar (este é um ponto consistente com a falha).
    - Há uma opção de ponto de recuperação consistente com o aplicativo. Neste caso, uma única VM se recupera para seu mais recente ponto de recuperação consistente com o aplicativo disponível. Para um plano de recuperação com um grupo de replicação, cada grupo de replicação se recupera ao seu ponto de recuperação disponível comum.
    - Pontos de recuperação consistentes com aplicativos podem estar atrasados no tempo, e pode haver perda de dados.
- Durante o failover do Azure para o site local, o Site Recovery encerra as VMs do Azure. Quando você comete o failover, a Recuperação do Site remove as VMs azure com falha no Azure.


## <a name="vmwarephysical-reprotectionfailback"></a>VMware/reproteção física/failback

Para reproteger e reprovar máquinas VMware e servidores físicos do Azure para os locais, você precisa de uma infra-estrutura de failback e há uma série de requisitos.

- **Servidor em processo temporário no Azure**: para fazer failback do Azure, você configura uma VM do Azure para atuar como um servidor em processo, a fim de manipular a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
- **Conexão VPN**: para fazer failback, você precisa de uma conexão VPN (ou ExpressRoute) da rede do Azure para o site local.
- **Servidor de destino mestre separado**: por padrão, o servidor de destino mestre que foi instalado com o servidor de configuração na VM do VMware local manipula o failback. Se for necessário fazer failback de grandes volumes de tráfego, configure um servidor de destino mestre local separado para essa finalidade.
- **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. Essa política é criada automaticamente quando você cria uma política de replicação de local para o Azure.
    - Esta diretiva está automaticamente associada ao servidor de configuração.
    - Você não pode editar esta política.
    - Valores da política: limite de RPO - 15 minutos; Retenção de ponto de recuperação - 24 Horas; Frequência de instantâneo consistente com aplicativo - 60 minutos.

Saiba mais sobre vMware/reproteção física e failback:
- [Revise](vmware-azure-reprotect.md#before-you-begin) os requisitos adicionais para reproteção e failback.
- [Implante](vmware-azure-prepare-failback.md#deploy-a-process-server-in-azure) um servidor de processo no Azure.
- [Implante](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) um servidor de destino mestre separado.

Quando você reprotege as VMs do Azure para o local, você pode especificar que deseja falhar de volta ao local original ou a um local alternativo.

- **Recuperação de localização original**: Isso falha de volta do Azure para a mesma máquina de origem no local, se ela existir. Nesse cenário, apenas as mudanças são replicadas de volta ao local.
- **Recuperação de localização alternativa**: Se a máquina no local não existir, você pode falhar de volta do Azure para um local alternativo. Quando você reprotege a VM do Azure para o local, a máquina no local é criada. A replicação completa dos dados ocorre do Azure para o local. - [- Revisar](concepts-types-of-failback.md) os requisitos e limitações para failback de localização.



## <a name="hyper-v-reprotectionfailback"></a>Reproteção/failback do Hiper-V

Para reproteger e reprovar Hiper-VMs do Azure para os locais:

- Você só pode falhar em Hiper-V VMs replicando usando uma conta de armazenamento. Não é suportado failback de VMs Hyper-V que se replicam usando discos gerenciados.
- Os hosts Hyper-V (ou Centro de Sistema VMM, se usados) devem ser conectados ao Azure.
- Você executa um failback planejado do Azure para o local.
- Nenhum componente específico precisa ser configurado para o failback Hyper-V VM.
- Durante o failover planejado, você pode selecionar opções para sincronizar dados antes da reprodução:
    - **Sincronizar dados antes do failover**: Esta opção minimiza o tempo de inatividade das máquinas virtuais à medida que sincroniza as máquinas sem desligá-las.
        - Fase 1: Tira um instantâneo da VM do Azure e copia-a para o host Hyper-V no local. O computador continua em execução no Azure.
        - Fase 2: Desliga a VM do Azure para que não ocorram novas alterações por lá. O conjunto final de alterações delta é transferido para o servidor local e a VM no local é iniciada.
    - **Sincronizar dados apenas durante o failover**: Esta opção é mais rápida porque esperamos que a maior parte do disco tenha sido alterada e, portanto, não realize cálculos de soma de cheques. Ela executa um download do disco. Recomendamos que você use esta opção se a VM estiver sendo usada no Azure por um tempo (um mês ou mais), ou se a VM no local tiver sido excluída.

[Saiba mais](hyper-v-azure-failback.md) sobre reproteção e failback do Hyper-V.

Quando você reprotege as VMs do Azure para o local, você pode especificar que deseja falhar de volta ao local original ou a um local alternativo.

- **Recuperação de localização original**: Isso falha de volta do Azure para a mesma máquina de origem no local, se ela existir. Neste cenário, você seleciona uma das opções de sincronização descritas no procedimento anterior.
- **Recuperação de localização alternativa**: Se a máquina no local não existir, você pode falhar de volta do Azure para um local alternativo. Quando você reprotege a VM do Azure para o local, a máquina no local é criada. Com esta opção, recomendamos que você selecione a opção de sincronizar dados antes do failover
- [Revise](hyper-v-azure-failback.md) os requisitos e limitações para failback de localização.


Depois de falhar no site local, você habilita **o Reverse Replicate** para começar a replicar a VM para o Azure, completando o ciclo.




## <a name="next-steps"></a>Próximas etapas
- Falha em relação a [VMS específicas da VMware](vmware-azure-tutorial-failover-failback.md)
- Falha sobre [Hiper-V VS específicos](hyper-v-azure-failover-failback-tutorial.md).
- [Crie](site-recovery-create-recovery-plans.md) um plano de recuperação.
- Falha sobre [VMs em um plano de recuperação](site-recovery-failover.md).
- [Prepare-se para](vmware-azure-failback.md) VMware reproteção e failback.
- Fail back [Hyper-V VMs](hyper-v-azure-failback.md).

