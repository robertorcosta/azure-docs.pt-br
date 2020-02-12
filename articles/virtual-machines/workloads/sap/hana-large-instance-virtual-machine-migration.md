---
title: Migrando SAP HANA no Azure (instâncias grandes) para máquinas virtuais do Azure | Microsoft Docs
description: Como migrar SAP HANA no Azure (instâncias grandes) para máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154914"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA na migração de instância grande do Azure para máquinas virtuais do Azure
Este artigo descreve possíveis cenários de implantação de instância grande do Azure e oferece abordagem de planejamento e migração com tempo de inatividade de transição minimizado

## <a name="overview"></a>Visão geral
Desde o anúncio das instâncias grandes do Azure para SAP HANA (HLI) em setembro de 2016, muitos clientes adotaram esse hardware como um serviço para sua plataforma de computação na memória.  Nos últimos anos, a extensão de escalabilidade de VM do Azure combinada com o suporte do modelo de implantação de expansão do HANA excedeu a maioria das demandas de capacidade de banco de dados ERP dos clientes empresariais.  Começamos a ver os clientes que expressam o desejo de migrar sua carga de trabalho SAP HANA de servidores físicos para VMs do Azure.
Este guia não é um documento de configuração passo a passo, mas descreve os modelos de implantação comuns e oferece planejamento, a migração aconselha a intenção de chamar as considerações necessárias para a preparação para minimizar o tempo de inatividade da transição.

## <a name="assumptions"></a>Suposições
Este artigo pressupõe o seguinte:
- O único interesse considerado é uma migração de serviço de computação de banco de dados do HANA homogêneo da cópia de instância grande (HLI) do Hana para a VM do Azure sem atualização significativa de software ou aplicação de patch. Essas pequenas atualizações incluem o uso de uma versão do sistema operacional mais recente ou da versão do HANA explicitamente declarada com suporte em notas SAP relevantes. 
- Todas as atividades de atualização/atualizações, se desejado, precisam ser executadas antes ou após a migração.  Por exemplo, SAP HANA MCOS convertendo para a implantação MDC. 
- A abordagem de migração que ofereceria o menor tempo de inatividade é SAP HANA replicação do sistema. Outros métodos de migração não fazem parte do escopo deste documento.
- Isso se aplica a SKUs Rev3 e Rev4 da HLI.
- A arquitetura de implantação do HANA permanece inalterada principalmente durante a migração.  Ou seja, um sistema com uma única instância de DR permanecerá da mesma maneira no destino.
- Os clientes revisaram e compreenderam o Contrato de Nível de Serviço (SLA) da arquitetura de destino (para ser). 
- Devido às diferenças em termos comerciais entre HLIs e VMs, os clientes devem monitorar o uso de suas VMs para o gerenciamento de custos.
- Os clientes entendem e reconhecem que o HLI é uma plataforma de computação dedicada. No entanto, as VMs são executadas na infraestrutura compartilhada, que é segura e isolada de outros locatários.
- Os clientes validaram que as VMs de destino dão suporte à sua arquitetura pretendida. Para ver todos os SKUs de VM com suporte para a implantação do SAP HANA, verifique o [SAP Hana diretório de hardware](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Os clientes validaram o plano de design e migração.
- Planeje o nó de recuperação de desastre junto com o site primário.  Os clientes não poderão usar o nó de DR do HLI para o site primário em execução nas VMs após a migração.
- Os clientes copiaram os arquivos de backup necessários para as VMs de destino, com base na capacidade de recuperação e nos requisitos de conformidade dos negócios. Isso é para cobrir a necessidade de recuperação pontual durante o período de transição.
- Para o HSR HA, os clientes precisam configurar e configurar o dispositivo STONITH por SAP HANA guias de alta disponibilidade para [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) e [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Ele não é pré-configurado como o caso de HLI.
- Essa abordagem de migração não abrange as SKUs de HLI com a configuração Optane.

## <a name="deployment-scenarios"></a>Cenários de implantação
Os modelos comuns de implantação com os clientes do HLI são resumidos na tabela a seguir.  A migração para VMs do Azure para todos os cenários de HLI é possível.  Alguns cenários podem exigir uma pequena modificação arquitetônica para obter as vantagens das ofertas de serviço atuais do Azure.

| ID do cenário | Cenário de HLI | Migrar para a VM idêntica? | Comentário |
| --- | --- | --- | --- |
| 1 | [Nó único com um SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Sim | - |
| 2 | [Nó único com MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Sim | - |
| 3 | [Nó único com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual do Azure, altere a solução de DR atual para HSR ou backup/restauração |
| 4 | [Nó único com DR (multipropósito) usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual do Azure, altere a solução de DR atual para HSR ou backup/restauração |
| 5 | [HSR com STONITH para alta disponibilidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Sim | Nenhum SBD pré-configurado para VMs de destino.  Selecione e implante uma solução STONITH.  Opções possíveis: agente de isolamento do Azure (com suporte para [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA com HSR, DR com replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR com HSR ou backup/restauração |
| 7 | [Failover automático do host (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Sim | Usar o seja para armazenamento compartilhado com VMs do Azure |
| 8 | [Escalar horizontalmente com em espera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Sim | BW/4HANA com M128s, M416s, VMs M416ms usando seja para armazenamento somente |
| 9 | [Expansão sem espera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Sim | BW/4HANA com M128s, M416s, VMs M416ms (com ou sem usar seja para armazenamento) |
| 10 | [Escalar horizontalmente com recuperação de desastre usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR com HSR ou backup/restauração |
| 11 | [Nó único com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Sim | - |
| 12 | [HSR de nó único para DR (com otimização de custo)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Sim | - |
| 13 | [HA e DR com HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Sim | - |
| 14 | [HA e DR com HSR (com otimização de custo)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Sim | - |
| 15 | [Escale horizontalmente com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Sim | BW/4HANA com M128s. M416s, VMs M416ms (com ou sem o uso de seja para armazenamento) |


## <a name="source-hli-planning"></a>Planejamento de origem (HLI)
Ao integrar um servidor de HLI, o gerenciamento de serviços da Microsoft e os clientes passaram pelo planejamento das configurações específicas de computação, rede, armazenamento e sistema operacional para executar o banco de dados de SAP HANA.  O planejamento semelhante precisa ocorrer para a migração para a VM do Azure.

### <a name="sap-hana-housekeeping"></a>SAP HANA a manutenção 
Antes de migrar o banco de dados do HANA, é uma boa prática organizar o conteúdo do banco de dados de forma que os logs indesejados, desatualizados ou obsoletos não sejam migrados para o novo banco de dado.  A manutenção geralmente envolve a exclusão ou o arquivamento de dados antigos, expirados ou inativos.  Portanto, essas ações de ' higiene de dados ' devem ser testadas em sistemas de não produção para validar sua validade de corte de dados antes do uso de produção.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Permitir conectividade de rede para novas VMs e, ou rede virtual 
Na implantação de HLI de um cliente, a conectividade de rede do Azure vnets foi estabelecida com base nas informações descritas no artigo [SAP Hana arquitetura de rede (instâncias grandes)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture). Além disso, o roteamento de tráfego de rede é feito da maneira descrita na seção "roteamento no Azure".
1. Com a finalidade de configurar uma nova VM como o destino HSR para a migração, se a nova VM do Azure for colocada na rede virtual existente com intervalos de endereços IP, que já têm permissão para se conectar ao nó HLI, nenhuma outra atualização de conectividade de HLI é necessária
2. Se a nova VM do Azure for colocada em uma nova rede virtual (pode estar em outra região) e emparelhada com a rede virtual existente, a chave de serviço do ExpressRoute e a ID de recurso do provisionamento de HLI original poderão ser usadas para permitir o acesso a esse novo intervalo de IP de rede virtual.  Coordene com o gerenciamento de serviços da Microsoft para habilitar a conectividade de rede virtual para HLI.  Observação: para minimizar a latência de rede entre as camadas do aplicativo e do banco de dados, as camadas do aplicativo e do banco de dados devem estar na mesma rede virtual.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>Conjunto de disponibilidade de camada de aplicativo, Zonas de Disponibilidade e grupo de posicionamento de proximidade existentes
O modelo de implantação atual é feito para atender a certos objetivos de nível de serviço.  Nessa mudança, verifique se a infraestrutura de destino atenderá ou excederá as metas definidas.  
Mais provável do que não, clientes servidores de aplicativos SAP são colocados em um conjunto de disponibilidade.  Se o nível de serviço de implantação atual for satisfatório e 
- Se a substituição do servidor de SAP HANA for feita por meio da resolução de nomes por meio da troca de IPs para o nome de host do BD, nada mais precisará ser feito.  
- Se você não estiver usando o PPG, certifique-se de posicionar todos os servidores de aplicativo e DB na mesma zona para minimizar a latência de rede.
- Se você estiver usando PPG, consulte a seção deste documento planejamento de destino, conjunto de disponibilidade, Zonas de Disponibilidade e grupo de posicionamento de proximidade (PPG)

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo de descontinuação da replicação de armazenamento (se usado)
Se a replicação de armazenamento estiver sendo usada como solução de recuperação de desastre, a replicação precisará ser encerrada (não agendada) depois que o aplicativo SAP tiver sido desligado e o último SAP HANA catálogo, arquivo de log, os backups de dados tiverem sido replicados nos volumes de armazenamento remoto.  Essa ação é uma tática de precaução que temos um banco de dados atual na DR HLI, caso um desastre ocorra durante a transição de VM física para o Azure.

### <a name="data-backups-preservation-consideration"></a>Consideração de preservação de backups de dados
Após a transferência para SAP HANA na VM do Azure, todos os backups de log ou dados baseados em instantâneos na HLI não são facilmente acessíveis ou restauráveis para uma VM, se necessário. Para o período de transição antecipado, antes que o backup baseado no Azure crie histórico suficiente para atender aos requisitos de recuperação pontual, recomendamos pegar backups de nível de arquivo, além de instantâneos em HLI, dias/semanas antes da transferência.  Faça com que esses backups sejam copiados para uma conta de armazenamento do Azure acessível pela nova VM SAP HANA. Além de fazer backup do conteúdo de HLI, também é uma prática prudente ter os backups completos da estrutura SAP prontamente acessíveis, caso uma reversão seja necessária.

### <a name="adjusting-system-monitoring"></a>Ajustando o monitoramento do sistema 
Os clientes usam muitas ferramentas diferentes para monitorar e enviar notificações de alerta para sistemas em sua estrutura SAP.  Esse item é apenas uma chamada simples para a ação apropriada ao adicionar as novas VMs que incorporam qualquer ajuste para monitoramento e atualizar os destinatários de notificação de alerta, se necessário.

### <a name="microsoft-operations-team-involvement"></a>Envolvimento da equipe de operações da Microsoft 
Abra um tíquete das bases de portal do Azure na instância de HLI existente.  Depois que o tíquete de suporte for criado, um engenheiro de suporte entrará em contato com você por email.  

### <a name="engage-microsoft-account-team"></a>Participe da equipe de contas da Microsoft
Planeje a migração perto do horário de renovação de aniversário do contrato de HLI para minimizar o excesso de despesas no recurso de computação. Para encerrar a folha HLI, é necessário coordenar o encerramento do contrato e o desligamento real da unidade.

## <a name="destination-planning"></a>Planejamento de destino
Uma nova infra-estrutura para assumir o lugar de um existente merece pensar em garantir que a nova adição caiba no grande esquema de coisas.  Abaixo estão alguns pontos principais para contemplation.

### <a name="resource-availability-in-the-target-region"></a>Disponibilidade de recursos na região de destino 
A região de implantação atual do servidor de aplicativos SAP normalmente está em proximidade com o HLIs associado.  No entanto, os HLIs são oferecidos em menos locais do que as regiões do Azure disponíveis.  Na migração do HLI físico para a VM do Azure, ele apresenta a oportunidade de "ajustar" a distância de proximidade de todos os serviços relacionados para otimização de desempenho.  Ao fazer isso, uma consideração importante seria garantir que a região escolhida tenha os recursos de seu interesse.  Por exemplo, a disponibilidade de determinada família de VM ou a oferta de zonas do Azure para consideração de alta disponibilidade.

### <a name="virtual-network"></a>Rede virtual 
O arquiteto de infraestrutura será confrontado com uma opção de executar o novo banco de dados HANA dentro da rede virtual do aplicativo SAP existente ou para criar um novo.  O principal fator de decisão é o layout de rede atual para a estrutura SAP.  Caso uma alteração na infraestrutura de implantação seja considerada, por exemplo, indo de uma zona para a implantação de duas zonas e tirando proveito do PPG. Esse aprimoramento de disponibilidade impõe mudanças de arquitetura. Para obter mais informações, consulte o artigo [PPG do Azure para latência de rede ideal com o aplicativo SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Segurança
Se o novo SAP HANA inicial de VM em uma vnet/sub-rede nova ou existente, ele representa um novo serviço comercialmente crítico que requer proteção.  O controle de acesso adequado em conformidade com a política de segurança de informações da empresa para essa nova classe de serviço deve ser avaliado e implantado. 

### <a name="vm-sizing-recommendation"></a>Recomendação de dimensionamento de VM
Essa migração também é uma oportunidade de dimensionar o seu mecanismo de computação do HANA.  É possível aproveitar as [exibições do sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) Hana em conjunto com o Hana Studio para entender o consumo de recursos do sistema, que permite o dimensionamento correto para impulsionar a eficiência dos gastos.

### <a name="storage"></a>Armazenamento 
O desempenho do armazenamento é um dos fatores que afetam a experiência do usuário do aplicativo SAP.  Base em uma determinada SKU de VM, há uma recomendação de layout de armazenamento mínima publicada [SAP Hana configurações de armazenamento de máquina virtual do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). É recomendável revisar essas especificações mínimas e comparadas com as estatísticas de sistema de HLI existentes para garantir a capacidade e o desempenho de e/s adequada para a nova VM do HANA.

Se você configurar o PPG para a nova VM do HANA e seus servidores associados, envie um tíquete de suporte para inspecionar e garantir a colocação do armazenamento e da VM do HANA.  
Como sua solução de backup pode precisar ser alterada, o custo de armazenamento também deve ser revisitado para evitar surpresas de gastos operacionais. 

### <a name="storage-replication-for-disaster-recovery"></a>Replicação de armazenamento para recuperação de desastre
No HLI, a replicação de armazenamento foi oferecida como a opção padrão para a replicação de recuperação de desastre para o banco de dados HANA. Esse recurso não é a opção padrão com a VM do Azure. Considere o HSR, o backup/restauração ou outras soluções com suporte que atendem às suas necessidades de negócios.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Conjuntos de disponibilidade, Zonas de Disponibilidade e grupos de posicionamento de proximidade 
Considerando a redução da distância entre a camada do aplicativo e a SAP HANA para manter a latência de rede no mínimo, a nova VM de banco de dados e os servidores de aplicativos SAP atuais devem ser colocados em um grupo de posicionamento de proximidade (PPG). Consulte [grupo de posicionamento de proximidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para saber, como o conjunto de disponibilidade do Azure e zonas de disponibilidade funcionam com PPG para implantações do SAP.
Se os membros do sistema HANA de destino forem implantados em mais de uma zona do Azure, os clientes deverão ter uma exibição clara do perfil de latência das zonas escolhidas. O posicionamento dos componentes do sistema SAP é ideal em relação à distância proximal entre o aplicativo SAP e o banco de dados.  A ferramenta de [teste de latência de zona de disponibilidade](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) de domínio público ajuda a facilitar a medição.  


### <a name="backup-strategy"></a>Estratégia de backup
Muitos clientes já estão usando soluções de backup de terceiros para SAP HANA em HLI.  Nesse caso, apenas uma VM protegida adicional e os bancos de dados HANA precisam ser configurados.  Os trabalhos de backup de HLI contínuos para o momento não serão agendados se o computador estiver sendo encerrado após a migração.
O backup do Azure para SAP HANA na VM já está disponível para o público em geral.  Consulte estes links para obter informações detalhadas sobre: [backup](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database), [restauração](https://docs.microsoft.com/azure/backup/sap-hana-db-restore), [gerenciar](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) SAP Hana backup em VMs do Azure.

### <a name="dr-strategy"></a>Estratégia de DR
Se os objetivos de nível de serviço acomodam um tempo de recuperação mais longo de um backup simples para o armazenamento de BLOBs e a restauração in-loco ou para uma nova VM, é a estratégia de DR mais simples e menos dispendiosa.  
Como a plataforma de instância grande em que o HANA DR normalmente é feito com o HSR; Na VM do Azure, o HSR também é a solução de recuperação de desastre mais natural e nativa SAP HANA.  Independentemente de a implantação de origem ser uma instância única, clusterizada com ou sem failover automático ou escalabilidade horizontal de vários nós, uma réplica HSR de destino da infraestrutura de origem é necessária na região de recuperação de desastres. Essa réplica de DR de destino HSR será estabelecida depois que a migração de HLI principal para VM for concluída.  A instância de DR HANA será registrada no SAP HANA primário na instância de VM como um site de replicação secundário.  

### <a name="sap-application-server-connectivity-destination-change"></a>Alteração de destino de conectividade do servidor de aplicativos SAP
A abordagem de migração de HSR resulta em um novo host de BD do HANA e, portanto, um novo nome de host do BD para a camada de aplicativo, os perfis do SAP precisam ser modificados para refletir o novo nome do host.  Se a opção for feita pela resolução de nomes preservando o nome do host, nenhuma alteração de perfil será necessária.

### <a name="operating-system"></a>Sistema operacional
As imagens do sistema operacional para HLI e VM, embora estejam no mesmo nível de versão, o SLES 11 SP4, por exemplo, não são idênticos. Os clientes devem validar os pacotes necessários, hot fixes, patches, kernel e correções de segurança no HLI para que eles possam ser instalados no sistema operacional da VM de destino.  Além disso, é comum que os clientes configurem uma versão mais recente do sistema operacional na VM de destino para SAP HSR.  Com suporte por [Observação SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nova solicitação de licença SAP
Uma chamada simples para solicitar uma nova licença SAP para o novo sistema HANA agora que ele foi migrado para VMs.

### <a name="service-level-agreement-sla-differences"></a>Diferenças de SLA (contrato de nível de serviço)
Os autores gostam de destacar a diferença do SLA de disponibilidade entre o HLI e a VM do Azure.  Por exemplo, pares clusterizados HLIs HA oferecem 99,99% de disponibilidade. Para obter o mesmo SLA, é necessário implantar VMs em zonas de disponibilidade. Este [artigo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descreve a disponibilidade com arquiteturas de implantação associadas para que os clientes possam planejar essa infraestrutura de destino adequadamente.


## <a name="migration-strategy"></a>Estratégia de migração
Neste documento, abordamos apenas a abordagem de replicação do sistema HANA para a migração do HLI para a VM do Azure.  Depende da solução de armazenamento de destino implantada, o processo difere ligeiramente. As etapas de alto nível são descritas abaixo.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM com Premium/ultra-discos para dados
Para VMs que são implantadas com Premium ou ultra discos, a configuração de replicação do sistema SAP HANA padrão se aplica e pode ser usada para configurar o HSR.  O [artigo de ajuda da SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) referenciado fornece uma visão geral das etapas envolvidas na configuração da replicação do sistema entre dois sistemas, levando em um sistema secundário, fazendo failback para um sistema primário e desabilitando a replicação do sistema.  Para fins de migração, precisaremos apenas da configuração, assumindo e desabilitando a replicação do sistema nas etapas de origem de HLI.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM com seja para volumes de dados e de log
Em um alto nível, os instantâneos de armazenamento de HLI mais recentes dos volumes de dados e de log completos precisam ser copiados para o armazenamento do Azure, onde podem ser acessados e recuperáveis pela VM HANA de destino.  O processo de cópia pode ser feito com qualquer ferramenta de cópia nativa do Linux.  

>[!Important]
> A cópia e a transferência de dados podem levar horas dependendo do tamanho do banco e da largura de banda da rede do HANA.  A maior parte do processo de cópia deve ser feita antes do tempo de inatividade principal do HANA DB.

### <a name="mcos-to-mdc-conversion"></a>Conversão de MCOS para MDC
O modelo de implantação vários componentes em um sistema (MCOS) foi escolhido por alguns dos nossos clientes do HLI.  A motivação era burlar a limitação do instantâneo de armazenamento de vários contêineres de bancos de dados (MDC) das versões anteriores do SAP HANA.  No modelo MCOS, várias instâncias SAP HANA independentes são empilhadas em uma folha HLI.  O uso de HSR para a migração funcionaria bem, resultando em várias VMs do HANA com um único banco de um locatário.  Esse estado final torna um cenário mais ocupado do que um cliente pode ter trabalhado.  Com a implantação padrão do SAP HANA 2,0 sendo MDC, uma alternativa viável é executar a [movimentação do locatário do Hana](https://launchpad.support.sap.com/#/notes/2472478) após a migração do HSR.  Esse processo "consolida" esses bancos de dados do HANA independentes em colocatários em um único contêiner do HANA.  

### <a name="application-layer-consideration"></a>Consideração da camada de aplicativo
O servidor de BD é exibido como o centro de um sistema SAP.  Todos os servidores de aplicativos devem estar localizados perto do SAP HANA DB.  Em alguns casos, quando o novo uso de PPG é desejado, a realocação de servidores de aplicativos existentes para o PPG em que a VM do HANA pode ser necessária.  A criação de novos servidores de aplicativos pode ser considerada mais fácil se você já tiver modelos de implantação úteis.  
Se os servidores de aplicativos existentes e a nova VM HANA estiverem localizados de maneira ideal, nenhum novo servidor de aplicativos precisará ser criado, a menos que seja desejado capacidade adicional.  
Se uma nova infraestrutura for criada para melhorar a disponibilidade do serviço, os servidores de aplicativos existentes poderão se tornar desnecessários e devem ser desligados e excluídos.
Se o nome de host da VM de destino for alterado e for diferente do nome do host do HLI, os perfis do servidor de aplicativos SAP precisarão ser ajustados para apontarem para o novo host.  Se apenas o endereço IP do BD HANA tiver sido alterado, uma atualização de registro DNS será necessária para conduzir as conexões de entrada para a nova VM HANA.

### <a name="acceptance-test"></a>Teste de aceitação
Embora a migração de HLI para VM não faça nenhuma alteração de material no conteúdo do banco de dados em comparação com uma migração heterogênea, ainda é recomendável validar as funcionalidades principais e o aspecto de desempenho da nova instalação.  

### <a name="cutover-plan"></a>Plano de transição
Embora essa migração seja direta, ela envolve o encerramento de um BD existente.  O planejamento cuidadoso para preservar o sistema de origem com seu conteúdo associado e suas imagens de backup são críticos no caso de fallback ser necessário.  Um bom planejamento oferece uma reversão de Speedier.   


## <a name="post-migration"></a>Após a migração
O trabalho de migração não é feito até que tenhamos dissociado com segurança qualquer serviço dependente de HLI ou conectividade para garantir que a integridade dos dados seja preservada.  Além disso, desligue os serviços desnecessários.  Esta seção chama alguns itens de primeira vista.

### <a name="decommissioning-the-hli"></a>Encerrando o HLI
Após uma migração bem-sucedida do BD HANA para a VM do Azure, nenhuma transação de negócios de produção deve ser conduzida no banco de BD de HLI.  No entanto, manter o HLI ativo e em execução por um período igual à sua janela de retenção de dados de backup local é uma prática segura garantindo a recuperação de dados Speedier, se necessário.  Somente depois a lâmina HLI deve ser encerrada.  Além do empreendimento técnico, os clientes em seu melhor interesse devem concluir de forma contratual os compromissos de HLI com a Microsoft.  Os clientes devem entrar em contato com seus representantes da Microsoft para trabalhar com o processo de descomissionamento de HLI.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Remover qualquer proxy (ex: iptables, BIGIP) configurado para HLI 
Se um serviço de proxy como o de um IPTables for usado para rotear o tráfego local para e do HLI, esse serviço não será mais necessário após a migração bem-sucedida para a VM.  No entanto, esse serviço de conectividade deve ser retido, desde que a folha HLI ainda esteja em pé, conforme discutido no tópico descomissionamento.  Esse serviço pode ser desligado depois que a folha HLI é completamente descomissionada. 

### <a name="remove-global-reach-for-hli"></a>Remover Alcance Global para HLI 
Alcance Global normalmente é usado para conectar o gateway do ExpressRoute do cliente com o gateway de ExpressRoute do HLI, permitindo que o tráfego local do cliente alcance o locatário de HLI diretamente sem a necessidade de um serviço de proxy.  Como o caso com o serviço de proxy IPTables, o GlobalReach deve ser mantido até que a folha HLI seja completamente descomissionada.

### <a name="operating-system-subscription--movereuse"></a>Assinatura do sistema operacional – mover/reutilizar
À medida que os servidores de VM estão acima e as folhas de HLI são encerradas, as assinaturas de sistema operacional podem ser substituídas ou reutilizadas para evitar o pagamento duplo de licenças de sistema operacional.



## <a name="next-steps"></a>Próximas etapas
Consulte estes artigos:
- [SAP Hana configurações de infraestrutura e operações no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Cargas de trabalho do SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
