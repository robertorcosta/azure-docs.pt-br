---
title: Solucionar problemas de replicação para recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery | Microsoft Docs
description: Este artigo fornece informações sobre a solução de problemas comuns de replicação durante a recuperação de desastre de VMs VMware e servidores físicos para o Azure usando o Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/2/2019
ms.author: mayg
ms.openlocfilehash: f91ee5654b4add37d3cce4f875be1f9c2b398ab9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259486"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Solução de problemas de replicação para VMs VMware e servidores físicos

Este artigo descreve alguns problemas comuns e erros específicos que você pode encontrar quando você replica VMMs e servidores físicos no local para o Azure usando [o Site Recovery](site-recovery-overview.md).

## <a name="step-1-monitor-process-server-health"></a>Passo 1: Monitorar a saúde do servidor de processos

A Recuperação do Site usa o [servidor de processo](vmware-physical-azure-config-process-server-overview.md#process-server) para receber e otimizar dados replicados e enviá-los para o Azure.

Recomendamos que você monitore a saúde dos servidores de processo no portal, para garantir que eles estejam conectados e funcionando corretamente, e que a replicação esteja progredindo para as máquinas de origem associadas ao servidor de processo.

- [Saiba mais sobre](vmware-physical-azure-monitor-process-server.md) o monitoramento de servidores de processos.
- [Examinar práticas recomendadas](vmware-physical-azure-troubleshoot-process-server.md#best-practices-for-process-server-deployment)
- [Solucionar problemas](vmware-physical-azure-troubleshoot-process-server.md#check-process-server-health) de saúde do servidor do processo.

## <a name="step-2-troubleshoot-connectivity-and-replication-issues"></a>Passo 2: Solucionar problemas de conectividade e replicação

Falhas de replicação iniciais e contínuas geralmente são causadas por problemas de conectividade entre o servidor de origem e o servidor de processo ou entre o servidor de processo e o Azure.

Para resolver esses problemas, [solucionamos problemas de conectividade e replicação.](vmware-physical-azure-troubleshoot-process-server.md#check-connectivity-and-replication)




## <a name="step-3-troubleshoot-source-machines-that-arent-available-for-replication"></a>Passo 3: Solucionar problemas máquinas de origem que não estão disponíveis para replicação

Ao tentar selecionar o computador de origem para habilitar a replicação por meio do Azure Site Recovery, o computador pode não estar disponível para você por um dos seguintes motivos:

* **Duas máquinas virtuais com uuid da mesma instância**: Se duas máquinas virtuais sob o vCenter tiverem a mesma ocorrência UUID, a primeira máquina virtual descoberta pelo servidor de configuração é mostrada no portal Azure. Para resolver esse problema, assegure que não haja duas máquinas virtuais com o mesmo UUID de instância. Esse cenário é comumente visto em casos em que uma VM de backup se torna ativa e é registrada em nossos registros de descobertas. Consulte [o Azure Site Recovery VMware-to-Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) para resolver.
* **Credenciais incorretas do usuário do vCenter**: Certifique-se de que você adicionou as credenciais corretas do vCenter ao configurar o servidor de configuração usando o modelo OVF ou a configuração unificada. Para verificar as credenciais que você adicionou durante a instalação, consulte [Modificar credenciais para a descoberta automática](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **privilégios insuficientes**do vCenter : Se as permissões fornecidas para acessar o vCenter não tiverem as permissões necessárias, a falha em descobrir máquinas virtuais pode ocorrer. Verifique se as permissões descritas em [Preparar uma conta para a descoberta automática](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) foram adicionadas à conta de usuário do vCenter.
* Servidores de **gerenciamento de recuperação do Site do Azure**: Se a máquina virtual for usada como servidor de gerenciamento em uma ou mais das seguintes funções - Servidor de configuração /servidor de processo de escala/servidor de destino mestre, então você não poderá escolher a máquina virtual do portal. Os servidores de gerenciamento não podem ser replicados.
* **Já protegido/com falha através dos serviços de recuperação do site do Azure**: Se a máquina virtual já estiver protegida ou falhada através da Recuperação do Site, a máquina virtual não está disponível para selecionar para proteção no portal. Verifique se a máquina virtual que você está procurando no portal já não está protegida por algum outro usuário ou sob uma assinatura diferente.
* **vCenter não conectado**: Verifique se o vCenter está em estado conectado. Para verificar, acesse o cofre dos Serviços de Recuperação > Infraestrutura do Site Recovery > Servidores de Configuração > clique no respectivo servidor de configuração > uma folha será aberta à direita com os detalhes dos servidores associados. Verifique se o vCenter está conectado. Se estiver em um estado "Não Conectado", resolva o problema e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **ESXi desligado**: Se o host ESXi sob o qual a máquina virtual reside estiver em estado desligado, então a máquina virtual não será listada ou não será selecionável no portal Azure. Ligue o host ESXi e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server) no portal. Depois disso, a máquina virtual será listada no portal.
* **Reinicialização pendente**: Se houver uma reinicialização pendente na máquina virtual, então você não poderá selecionar a máquina no portal Azure. Conclua as atividades de reinicialização pendente e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.
* **IP não encontrado**: Se a máquina virtual não tiver um endereço IP válido associado a ele, então você não poderá selecionar a máquina no portal Azure. Atribua um endereço IP válido à máquina virtual e [atualize o servidor de configuração](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Depois disso, a máquina virtual será listada no portal.

### <a name="troubleshoot-protected-virtual-machines-greyed-out-in-the-portal"></a>Solução de problemas máquinas virtuais protegidas acinzentadas no portal

As máquinas virtuais que são replicadas no Site Recovery não estão disponíveis no portal do Azure se há entradas duplicadas no sistema. Para saber como excluir entradas obsoletas e resolver o problema, consulte [o Azure Site Recovery VMware-to-Azure: Como limpar entradas duplicadas ou obsoletas](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="no-crash-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Nenhum ponto de recuperação consistente de falha disponível para o VM nos últimos minutos 'XXX'

Alguns dos problemas mais comuns estão listados abaixo

### <a name="initial-replication-issues-error-78169"></a>Problemas iniciais de replicação [erro 78169]

Acima de um acima, garantindo que não há problemas relacionados à conectividade, largura de banda ou sincronização de tempo, certifique-se de que:

- Nenhum software antivírus está bloqueando a recuperação do site do Azure. Saiba [mais](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) sobre as exclusões de pastas necessárias para a recuperação do site do Azure.

### <a name="source-machines-with-high-churn-error-78188"></a>Máquinas de origem com alta rotatividade [erro 78188]

Possíveis causas:
- A taxa de alteração de dados (bytes de gravação/seg) nos discos listados da máquina virtual é mais do que os [limites suportados pelo Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) para o tipo de conta de armazenamento alvo de replicação.
- Há um aumento repentino na taxa de churn devido à alta quantidade de dados pendente para upload.

Como resolver o problema:
- Certifique-se de que o tipo de conta de armazenamento de destino (Padrão ou Premium) seja provisionado de acordo com a exigência de taxa de churn na origem.
- Se você já estiver replicando em um disco gerenciado Premium (tipo de disco asrseed), certifique-se de que o tamanho do disco suporta a taxa de churn observada conforme os limites de recuperação do site. Você pode aumentar o tamanho do disco assada, se necessário. Siga as etapas abaixo:
    - Navegue até a lâmina de discos da máquina replicada impactada e copie o nome do disco da réplica
    - Navegue até este disco gerenciado por réplica
    - Você pode ver um banner na lâmina Visão Geral dizendo que uma URL SAS foi gerada. Clique neste banner e cancele a exportação. Ignore este passo se você não ver o banner.
    - Assim que a URL sas for revogada, vá para a lâmina de configuração do disco gerenciado e aumente o tamanho para que o ASR suporte a taxa de churn observada no disco de origem
- Se o churn observado for temporário, aguarde algumas horas para que o upload de dados pendentes seja observado e crie pontos de recuperação.
- Se o disco contiver dados não críticos, como logs temporários, dados de teste etc., considere mover esses dados para outro lugar ou excluir completamente este disco da replicação
- Se o problema continuar a persistir, use o planejador de [implantação](site-recovery-deployment-planner.md#overview) de recuperação do site para ajudar a planejar a replicação.

### <a name="source-machines-with-no-heartbeat-error-78174"></a>Máquinas de origem sem batimentocardíaco [erro 78174]

Isso acontece quando o agente de mobilidade de recuperação do site do Azure na Máquina de Origem não está se comunicando com o Servidor de Configuração (CS).

Para resolver o problema, use as seguintes etapas para verificar a conectividade da rede da VM de origem para o Servidor Config:

1. Verifique se a máquina de origem está em execução.
2. Faça login na Máquina de Origem usando uma conta que tenha privilégios de administrador.
3. Verifique se os seguintes serviços estão em execução e se não reiniciar os serviços:
   - Svagents (agente do InMage Scout VX)
   - Serviço de Aplicativo InMage Scout
4. Na Máquina de Origem, examine os registros no local para obter detalhes de erro:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-with-no-heartbeat-error-806"></a>Servidor de processo sem batimentocardíaco [erro 806]
Caso não haja batimentos cardíacos do Servidor de Processo (PS), verifique se:
1. PS VM está funcionando
2. Verifique os seguintes registros no PS para obter detalhes de erro:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="master-target-server-with-no-heartbeat-error-78022"></a>Servidor de destino mestre sem batimentocardíaco [erro 78022]

Isso acontece quando o agente de mobilidade de recuperação do site do Azure no destino principal não está se comunicando com o Servidor de configuração.

Para resolver o problema, use as seguintes etapas para verificar o status do serviço:

1. Verifique se a VM de destino mestre está sendo executado.
2. Faça login na VM alvo mestre usando uma conta que tem privilégios de administrador.
    - Verifique se o serviço de svagents está em execução. Se estiver em execução, reinicie o serviço
    - Verifique os registros no local para obter detalhes de erro:

          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
3. Para registrar o destino mestre com o servidor de configuração, navegue até a pasta **%PROGRAMDATA%\ASR\Agent**e execute o seguinte no prompt de comando:
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="error-id-78144---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>Erro ID 78144 - Nenhum ponto de recuperação consistente com o aplicativo disponível para a VM nos últimos minutos 'XXX'

Foram feitos aprimoramentos nas versões [9.23](vmware-physical-mobility-service-overview.md#mobility-service-agent-version-923-and-higher) & [9.27](site-recovery-whats-new.md#update-rollup-39) do agente de mobilidade para lidar com os comportamentos de falha de instalação do VSS. Certifique-se de que você está nas versões mais recentes para obter a melhor orientação sobre a solução de problemas de falhas do VSS.

Alguns dos problemas mais comuns estão listados abaixo

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>Causa 1: Problema conhecido no servidor SQL 2008/2008 R2
**Como corrigir** : Há um problema conhecido com o servidor SQL 2008/2008 R2. Consulte este artigo KB [Azure Site Recovery Agent ou outrafalha de backup VSS não componente para um servidor que hospeda o SQL Server 2008 R2](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>Causa 2: Os trabalhos de recuperação do site do Azure falham em servidores que hospedam qualquer versão de instâncias do SQL Server com AUTO_CLOSE DBs
**Como corrigir** : Consulte artigo [kb](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>Causa 3: Problema conhecido no SQL Server 2016 e 2017
**Como corrigir** : Consulte artigo [kb](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)


### <a name="more-causes-due-to-vss-related-issues"></a>Mais causas devido a problemas relacionados com VSS:

Para solucionar ainda mais os problemas, verifique os arquivos na máquina de origem para obter o código exato de erro para falha:

    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

Como localizar os erros no arquivo?
Procure a string "vacpError" abrindo o arquivo vacp.log em um editor

    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

No exemplo acima **2147754994** é o código de erro que informa sobre a falha como mostrado abaixo

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>O escritor VSS não está instalado - Erro 2147221164

*Como corrigir*: Para gerar a tag de consistência do aplicativo, o Azure Site Recovery usa o Microsoft Volume Shadow Copy Service (VSS). Ele instala um provedor VSS para sua operação para tirar instantâneos de consistência do aplicativo. Este provedor VSS está instalado como um serviço. Caso o serviço do Provedor VSS não esteja instalado, a criação do instantâneo de consistência do aplicativo falha com o id de erro 0x80040154 "Classe não registrada". </br>
Consulte [artigo para solução de problemas de instalação de escritor VSS](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)

#### <a name="vss-writer-is-disabled---error-2147943458"></a>O escritor VSS está desativado - Erro 2147943458

**Como corrigir**: Para gerar a tag de consistência do aplicativo, o Azure Site Recovery usa o Microsoft Volume Shadow Copy Service (VSS). Ele instala um provedor VSS para sua operação para tirar instantâneos de consistência do aplicativo. Este provedor VSS está instalado como um serviço. Caso o serviço do Provedor VSS esteja desativado, a criação do snapshot de consistência do aplicativo falha com o id de erro "O serviço especificado está desativado e não pode ser iniciado (0x80070422)". </br>

- Se o VSS estiver desativado,
    - Verifique se o tipo de inicialização do serviço do provedor VSS está definido como **Automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - Erro 2147754756

**Como corrigir**: Para gerar a tag de consistência do aplicativo, o Azure Site Recovery usa o Microsoft Volume Shadow Copy Service (VSS).
Verifique se o serviço de provedor VSS de recuperação do site do Azure está instalado ou não. </br>

- Tente novamente a instalação do Provedor usando os seguintes comandos:
- Desinstale o provedor existente: C:\Arquivos do programa (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- Reinstalação: C:\Arquivos do programa (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd

Verifique se o tipo de inicialização do serviço do provedor VSS está definido como **Automático**.
    - Reinicie os seguintes serviços:
        - Serviço VSS
        - Provedor de VSS do Azure Site Recovery
        - Serviço VDS

## <a name="next-steps"></a>Próximas etapas

Se precisar de mais ajuda, poste sua pergunta no [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma comunidade ativa e um dos nossos engenheiros poderá ajudá-lo.
