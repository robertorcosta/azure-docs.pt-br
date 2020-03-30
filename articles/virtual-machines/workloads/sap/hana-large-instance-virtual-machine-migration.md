---
title: Migração do SAP HANA no Azure (Instâncias Grandes) para máquinas virtuais do Azure| Microsoft Docs
description: Como migrar o SAP HANA no Azure (Instâncias Grandes) para máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617035"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA na migração de grande instância do Azure para máquinas virtuais do Azure
Este artigo descreve possíveis cenários de implantação do Azure Large Instance e oferece planejamento e abordagem de migração com tempo de inatividade de transição minimizado

## <a name="overview"></a>Visão geral
Desde o anúncio do Azure Large Instances for SAP HANA (HLI) em setembro de 2016, muitos clientes adotaram esse hardware como uma oferta de serviço para sua plataforma de computação na memória.  Nos últimos anos, a extensão de tamanho de VM do Azure, juntamente com o suporte à implantação de escala hana, excedeu a demanda de capacidade de banco de dados ERP da maioria dos clientes corporativos.  Começamos a ver clientes expressando o interesse de migrar sua carga de trabalho SAP HANA de servidores físicos para VMs Azure.
Este guia não é um documento de configuração passo-a-passo. Descreve os modelos comuns de implantação e oferece conselhos de planejamento e migração.  A intenção é chamar as considerações necessárias para a preparação para minimizar o tempo de inatividade da transição.

## <a name="assumptions"></a>Suposições
Este artigo pressupõe o seguinte:
- O único interesse considerado é uma migração homogênea do serviço de computação de banco de dados HANA de Hana Large Instance (HLI) para Azure VM sem atualização ou patching significativos de software. Essas pequenas atualizações incluem o uso de uma versão mais recente do Sistema Operacional ou versão HANA explicitamente declarada como suportada por notas SAP relevantes. 
- Todas as atividades de atualizações/upgrades precisam ser feitas antes ou depois da migração.  Por exemplo, SAP HANA MCOS convertendo-se para implantação de MDC. 
- A abordagem de migração que ofereceria menos tempo de inatividade é a replicação do sistema SAP HANA. Outros métodos de migração não fazem parte do escopo deste documento.
- Esta orientação é aplicável tanto para As SKUs Rev3 e Rev4 da HLI.
- A arquitetura de implantação hana permanece principalmente inalterada durante a migração.  Ou seja, um sistema com DR de instância única permanecerá da mesma forma no destino.
- Os clientes analisaram e entenderam o Contrato de Nível de Serviço (SLA) da arquitetura target (a ser). 
- Os termos comerciais entre HLIs e VMs são diferentes. Os clientes devem monitorar o uso de suas VMs para gerenciamento de custos.
- Os clientes entendem que o HLI é uma plataforma de computação dedicada, enquanto as VMs são executadas em infra-estrutura compartilhada, mas isolada.
- Os clientes validaram que as VMs de destino suportam sua arquitetura pretendida. Para ver todas as SKUs VM suportadas certificadas para implantação do SAP HANA, consulte o [diretório de hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Os clientes validaram o projeto e o plano de migração.
- Planeje a VM de recuperação de desastres junto com o local principal.  Os clientes não podem usar o HLI como o nó DR para o site principal em execução em VMs após a migração.
- Os clientes copiaram os arquivos de backup necessários para segmentar as VMs, com base nos requisitos de recuperação e conformidade dos negócios. Com backups acessíveis à VM, permite a recuperação point-in-time durante o período de transição.
- Para hsr HA, os clientes precisam configurar e configurar o dispositivo STONITH por guias SAP HANA HA para [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) e [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker).  Não está pré-configurado como o caso HLI.
- Essa abordagem de migração não cobre as SKUs HLI com configuração Optane.

## <a name="deployment-scenarios"></a>Cenários de implantação
Modelos de implantação comuns com clientes HLI são resumidos na tabela a seguir.  A migração para VMs do Azure para todos os cenários de HLI é possível.  Para se beneficiar dos serviços complementares do Azure disponíveis, pequenas alterações arquitetônicas podem ser necessárias.

| ID de cenário | Cenário HLI | Migrar para a VM verbatim? | Comentário |
| --- | --- | --- | --- |
| 1 | [Único nó com um SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | Sim | - |
| 2 | [Nó único com MCOS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | Sim | - |
| 3 | [Nó único com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual Do Azure, altere a solução DR atual para hsr ou backup/restauração |
| 4 | [Nó único com DR (multiuso) usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | Não | A replicação de armazenamento não está disponível com a plataforma virtual Do Azure, altere a solução DR atual para hsr ou backup/restauração |
| 5 | [HSR com STONITH para alta disponibilidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | Sim | Sem SBD pré-configurado para VMs de destino.  Selecione e implante uma solução STONITH.  Opções possíveis: Azure Esgrima Agent (suportado para [rhel](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker), [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)), SBD |
| 6 | [HA com HSR, DR com replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR por HSR ou backup/restauração |
| 7 | [Failover Automático do Host (1 + 1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | Sim | Use ANF para armazenamento compartilhado com VMs do Azure |
| 8 | [Expansão com espera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | Sim | BW/4HANA com M128s, M416s, VMs M416ms usando ANF apenas para armazenamento |
| 9 | [Expansão sem espera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | Sim | Bw/4HANA com M128s, M416s, VMs M416ms (com ou sem o uso de ANF para armazenamento) |
| 10 | [Scale-out com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | Não | Substitua a replicação de armazenamento para necessidades de DR por HSR ou backup/restauração |
| 11 | [Nó único com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | Sim | - |
| 12 | [HSR de nó único para DR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | Sim | - |
| 13 | [HA e DR com HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | Sim | - |
| 14 | [HA e DR com HSR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Sim | - |
| 15 | [Scale-out com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | Sim | BW/4HANA com M128s. M416s, M416ms VMs (com ou sem usar ANF para armazenamento) |


## <a name="source-hli-planning"></a>Planejamento de fonte (HLI)
Ao embarcar em um servidor HLI, tanto o Microsoft Service Management quanto os clientes passaram pelo planejamento das configurações específicas de computação, rede, armazenamento e sO para executar o banco de dados SAP HANA.  Um planejamento semelhante precisa ser realizado para a migração para a VM Do Zure.

### <a name="sap-hana-housekeeping"></a>Limpeza SAP HANA 
É uma boa prática operacional arrumar o conteúdo do banco de dados para que dados indesejados, desatualizados ou registros obsoletos não sejam migrados para o novo banco de dados.  A limpeza geralmente envolve a exclusão ou o arquivamento de dados antigos, expirados ou inativos.  Essas ações de "higiene dos dados" devem ser testadas em sistemas não produtivos para validar a validade do corte de dados antes do uso da produção.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Permitir conectividade de rede para novas VMs e, ou rede virtual 
Na implantação do HLI de um cliente, a rede foi configurada com base nas informações descritas na arquitetura de [rede SAP HANA (Large Instances).](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture) Além disso, o roteamento de tráfego de rede é feito da maneira descrita na seção 'Roteamento no Azure'.
- Ao configurar uma nova VM como alvo de migração, se ela for colocada na rede virtual existente com faixas de endereçoIP já permitidas para se conectar ao HLI, nenhuma atualização adicional de conectividade será necessária.
- Se o novo Azure VM for colocado em uma nova Rede Virtual Microsoft Azure, pode estar em outra região e espionar com a rede virtual existente, a chave de serviço ExpressRoute e o ID de recursos do provisionamento HLI original são utilizáveis para permitir o acesso a essa nova rede virtual intervalo IP de rede.  Coordene com o Microsoft Service Management para permitir a conectividade de rede virtual para HLI.  Nota: Para minimizar a latência da rede entre as camadas de aplicativo e banco de dados, as camadas de aplicativo e banco de dados devem estar na mesma rede virtual.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Conjunto de disponibilidade de camada de aplicativo existente, zonas de disponibilidade e ppg (Proximity Placement Group, configuração de proximidade)
O modelo de implantação atual é feito para satisfazer certos objetivos de nível de serviço.  Neste movimento, certifique-se de que a infra-estrutura-alvo cumprirá ou excederá as metas estabelecidas.  
Mais provável do que não, os servidores de aplicativos SAP dos clientes são colocados em um conjunto de disponibilidade.  Se o nível de serviço de implantação atual for satisfatório e 
- Se a VM de destino assumir o nome de host do nome lógico HLI, atualizar a resolução do endereço dns (serviço de nome de domínio) apontando para o IP da VM funcionaria sem atualizar nenhum perfil SAP
- Se você não estiver usando PPG, certifique-se de colocar todos os servidores de aplicativo e DB na mesma região para minimizar a latência da rede.
- Se você estiver usando o PPG, consulte a seção deste documento: 'Planejamento de destino, conjunto de disponibilidade, zonas de disponibilidade e grupo de colocação de proximidade (PPG)'.

### <a name="storage-replication-discontinuance-process-if-used"></a>Processo de interrupção da replicação de armazenamento (se usado)
Se a replicação de armazenamento for usada como solução DR, ela deve ser encerrada (não programada) após o aplicativo SAP ter sido desligado.  Além disso, o último catálogo SAP HANA, arquivo de registro e backups de dados foram replicados nos volumes remotos de armazenamento DR HLI.  Fazê-lo como precaução no caso de um desastre acontecer durante a transição do servidor físico para o Azure VM.

### <a name="data-backups-preservation-consideration"></a>Consideração sobre preservação de backups de dados
Após o corte para SAP HANA no Azure VM, todos os dados baseados em instantâneos ou backups de log no HLI não são facilmente acessíveis ou restauráveis para uma VM, se necessário. No período inicial de transição, antes que o backup baseado no Azure crie histórico suficiente para satisfazer os requisitos de recuperação point-in-time, recomendamos fazer backups no nível de arquivo, além de instantâneos no HLI, dias ou semanas antes do corte.  Tenha esses backups copiados para uma conta do Azure Storage acessível pelo novo VM SAP HANA.
Além de fazer backup do conteúdo HLI, é prudente ter backups completos do cenário SAP facilmente acessíveis no caso de uma reversão ser necessária.

### <a name="adjusting-system-monitoring"></a>Ajuste do monitoramento do sistema 
Os clientes usam muitas ferramentas diferentes para monitorar e enviar notificações de alerta para sistemas dentro de seu cenário SAP.  Este item é apenas uma chamada para ações apropriadas para incorporar alterações para monitoramento e atualizar os destinatários de notificação de alerta, se necessário.

### <a name="microsoft-operations-team-involvement"></a>Envolvimento da equipe de operações da Microsoft 
Abra um bilhete no portal Azure com base na instância existente do HLI.  Depois que o bilhete de suporte for criado, um engenheiro de suporte entrará em contato por e-mail.  

### <a name="engage-microsoft-account-team"></a>Engajar a equipe de conta da Microsoft
Planeje a migração perto do tempo de renovação de aniversário do contrato hli para minimizar os gastos desnecessários com recursos computacionais. Para desativar a lâmina HLI, é necessário coordenar a rescisão do contrato e o desligamento real da unidade.

## <a name="destination-planning"></a>Planejamento de destinos
Levantar uma nova infra-estrutura para tomar o lugar de um já existente merece algum pensamento para garantir que a nova adição se encaixe no grande esquema das coisas.  Abaixo estão alguns pontos-chave para contemplação.

### <a name="resource-availability-in-the-target-region"></a>Disponibilidade de recursos na região-alvo 
A região de implantação dos servidores de aplicativos SAP atual normalmente está próxima dos HLIs associados.  No entanto, os HLIs são oferecidos em menos locais do que as regiões disponíveis do Azure.  Ao migrar o HLI físico para o Azure VM, também é um bom momento para 'ajustar' a distância de proximidade de todos os serviços relacionados para otimização de desempenho.  Ao fazê-lo, uma das principais considerações é garantir que a região escolhida tenha todos os recursos necessários.  Por exemplo, a disponibilidade de determinada família De VM ou a oferta de Zonas Azure para configuração de alta disponibilidade.

### <a name="virtual-network"></a>Rede virtual 
Os clientes precisam escolher se devem executar o novo banco de dados HANA em uma rede virtual existente ou criar um novo.  O principal fator decisivo é o layout de rede atual para o cenário SAP.  Também quando a infra-estrutura passa de uma zona para a implantação de duas zonas e usa PPG, ela impõe mudanças arquitetônicas. Para obter mais informações, consulte o artigo [Azure PPG para obter uma latência de rede ideal com o aplicativo SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).   

### <a name="security"></a>Segurança
Se o novo SAP HANA VM aterrissando em uma vnet/sub-rede nova ou existente, ele representa um novo serviço crítico de negócios que requer salvaguarda.  O controle de acesso em conformidade com a política de segurança de informações da empresa deve ser avaliado e implantado para esta nova classe de serviço.

### <a name="vm-sizing-recommendation"></a>Recomendação de dimensionamento de VM
Esta migração também é uma oportunidade para dimensionar corretamente o seu mecanismo de computação HANA.  Pode-se usar [as visualizações do sistema](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) HANA em conjunto com o HANA Studio para entender o consumo de recursos do sistema, o que permite o dimensionamento certo para impulsionar a eficiência dos gastos.

### <a name="storage"></a>Armazenamento 
O desempenho do armazenamento é um dos fatores que impacta a experiência do usuário do aplicativo SAP.  Base em um determinado VM SKU, há um layout mínimo de armazenamento publicado [sap HANA Azure configurações de armazenamento de máquina virtual](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage). Recomendamos rever essas especificações mínimas e comparar com as estatísticas existentes do sistema HLI para garantir a capacidade e o desempenho adequados do IO para o novo VM HANA.

Se você configurar o PPG para o novo HANA VM e seus severs associados, envie um bilhete de suporte para inspecionar e garantir a co-localização do armazenamento e da VM. Uma vez que sua solução de backup pode precisar ser mudada, o custo de armazenamento também deve ser revisitado para evitar surpresas de gastos operacionais.

### <a name="storage-replication-for-disaster-recovery"></a>Replicação de armazenamento para recuperação de desastres
Com o HLI, a replicação de armazenamento foi oferecida como a opção padrão para a recuperação de desastres. Este recurso não é a opção padrão para SAP HANA no Azure VM. Considere hSR, backup/restauração ou outras soluções suportadas satisfazendo suas necessidades de negócios.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Conjuntos de disponibilidade, zonas de disponibilidade e grupos de colocação de proximidade 
Para encurtar a distância entre a camada de aplicativo e o SAP HANA para manter a latência da rede no mínimo, o novo VM do banco de dados e os atuais servidores de aplicativos SAP devem ser colocados em um PPG. Consulte o [Grupo de Colocação de Proximidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) para saber como as zonas de disponibilidade e disponibilidade do Azure funcionam com ppg para implantações SAP.
Se os membros do sistema HANA de destino forem implantados em mais de uma Zona Azure, os clientes devem ter uma visão clara do perfil de latência das regiões escolhidas. A colocação de componentes do sistema SAP é ótima em relação à distância proximal entre a aplicação SAP e o banco de dados.  A [ferramenta de teste de latência da zona de disponibilidade](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) de domínio público ajuda a facilitar a medição.  


### <a name="backup-strategy"></a>Estratégia de backup
Muitos clientes já estão usando soluções de backup de terceiros para SAP HANA no HLI.  Nesse caso, apenas um banco de dados VM e HANA protegidos adicionais precisam ser configurados.  Os trabalhos de backup hli em andamento agora podem ser não programados se a máquina estiver sendo desativada após a migração.
O Backup do Azure para SAP HANA na VM agora está geralmente disponível.  Consulte esses links para obter informações detalhadas sobre: [Backup,](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) [Restauração,](https://docs.microsoft.com/azure/backup/sap-hana-db-restore) [Gerenciar](https://docs.microsoft.com/azure/backup/sap-hana-db-manage) backup SAP HANA em VMs Azure.

### <a name="dr-strategy"></a>Estratégia de DR
Se os objetivos do seu nível de serviço acomodarem um tempo de recuperação mais longo, um simples backup para blob armazenamento e restauração no lugar ou restauração para uma nova VM é a estratégia de DR mais simples e menos cara.  
Como a plataforma de instância sinuosa onde o HANA DR normalmente é feito com HSR; No Azure VM, o HSR também é a solução SAP HANA DR mais natural e nativa.  Independentemente de a implantação da fonte ser de instância única ou agrupada, uma réplica da infra-estrutura de origem é necessária na região DR.
Esta réplica DR será configurada após a migração principal de HLI para VM estiver concluída.  O DR HANA DB registrará-se no SAP HANA primário na instância VM como um site de replicação secundária.  

### <a name="sap-application-server-connectivity-destination-change"></a>Mudança de destino de conectividade do servidor de aplicativos SAP
A migração do HSR resulta em um novo host HANA DB e, portanto, um novo nome de host DB para a camada de aplicativo, os perfis SAP precisam ser modificados para refletir o novo nome do host.  Se a comutação for feita por resolução de nome preservando o nome do host, nenhuma alteração de perfil será necessária.

### <a name="operating-system"></a>Sistema operacional
As imagens do sistema operacional para HLI e VM, apesar de estarem no mesmo nível de lançamento, sles 12 SP4, por exemplo, não são idênticas. Os clientes devem validar os pacotes necessários, correções quentes, patches, kernel e correções de segurança no HLI para instalar os mesmos pacotes no destino.  Ele é suportado para usar HSR para replicar de um sistema operacional mais antigo em uma VM com uma versão mais recente do SISTEMA OPERACIONAL.  Verifique as versões específicas suportadas revisando a [nota SAP 2763388](https://launchpad.support.sap.com/#/notes/2763388).

### <a name="new-sap-license-request"></a>Nova solicitação de licença SAP
Uma simples chamada para solicitar uma nova licença SAP para o novo sistema HANA agora que ele foi migrado para VMs.

### <a name="service-level-agreement-sla-differences"></a>Diferenças de contrato de nível de serviço (SLA)
Os autores gostam de chamar a diferença de disponibilidade sLA entre HLI e Azure VM.  Por exemplo, os pares DE HA HLIs agrupados oferecem 99,99% de disponibilidade. Para obter o mesmo SLA, deve-se implantar VMs em zonas de disponibilidade. Este [artigo](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) descreve a disponibilidade com arquiteturas de implantação associadas para que os clientes possam planejar sua infra-estrutura de destino de acordo.


## <a name="migration-strategy"></a>Estratégia de migração
Neste documento, cobrimos apenas a abordagem de replicação do sistema HANA para a migração do HLI para o Azure VM.  Depende da solução de armazenamento de destino implantada, o processo difere ligeiramente. As etapas de alto nível estão descritas abaixo.

### <a name="vm-with-premiumultra-disks-for-data"></a>VM com discos premium/ultra-discos para dados
Para VMs que são implantados com discos premium ou ultra-discos, a configuração padrão de replicação do sistema SAP HANA é aplicável para configurar o HSR.  O [artigo de ajuda sap](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) fornece uma visão geral das etapas envolvidas na configuração da replicação do sistema, assumindo um sistema secundário, falhando na replicação primária e desativando a replicação do sistema.  Para efeitos da migração, só precisaremos das etapas de configuração, assumindo e desativando as etapas de replicação.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>VM com ANF para volumes de dados e log
Em um alto nível, os mais recentes instantâneos de armazenamento HLI dos volumes completos de dados e log precisam ser copiados para o Azure Storage, onde eles são acessíveis e recuperáveis pelo VM HANA de destino.  O processo de cópia pode ser feito com qualquer ferramenta de cópia Linux nativa.  

> [!IMPORTANT]
> A cópia e a transferência de dados podem levar horas dependendo do tamanho do banco de dados HANA e da largura de banda da rede.  A maior parte do processo de cópia deve ser feita antes do tempo de inatividade primário do HANA DB.

### <a name="mcos-to-mdc-conversion"></a>Conversão MCOS para MDC
O modelo de implantação de Múltiplos Componentes em Um Sistema (MCOS) foi usado por alguns de nossos clientes hli.  A motivação foi contornar a limitação de instantâneo de armazenamento multiple databases (MDC) das versões anteriores do SAP HANA.  No modelo MCOS, várias instâncias independentes do SAP HANA são empilhadas em uma lâmina HLI.  O uso do HSR para a migração funcionaria bem, mas resultando em várias VMs HANA com um DB inquilino cada.  Este estado final torna uma paisagem mais movimentada do que o que um cliente poderia estar acostumado.  Com a implantação padrão do SAP HANA 2.0 sendo MDC, uma alternativa viável é realizar o [movimento do inquilino HANA](https://launchpad.support.sap.com/#/notes/2472478) após a migração do HSR.  Este processo "consolida" esses bancos de dados HANA independentes em co-inquilinos em um único contêiner HANA.  

### <a name="application-layer-consideration"></a>Consideração da camada do aplicativo
O servidor DB é visto como o centro de um sistema SAP.  Todos os servidores de aplicativos devem estar localizados perto do SAP HANA DB.  Em alguns casos, quando o novo uso do PPG é desejado, a realocação dos servidores de aplicativos existentes para o PPG onde o VM HANA é pode ser necessário.  A construção de novos servidores de aplicativos pode ser considerada mais fácil se você já tiver modelos de implantação úteis.  
Se os servidores de aplicativos existentes e o novo VM HANA estiverem localizados de forma otimizada, nenhum novo servidor de aplicativo precisará ser construído a menos que seja necessária capacidade adicional.  
Se uma nova infra-estrutura for construída para melhorar a disponibilidade de serviços, os servidores de aplicativos existentes podem se tornar desnecessários e devem ser desligados e excluídos.
Se o nome de host de VM de destino for alterado e diferir do nome de host HLI, os perfis do servidor de aplicativos SAP precisarão ser ajustados para apontar para o novo host.  Se apenas o endereço IP DO HANA DB tiver sido alterado, uma atualização de registro de DNS é necessária para levar conexões recebidas ao novo VM HANA.

### <a name="acceptance-test"></a>Teste de aceitação
Embora a migração do HLI para o VM não faça nenhuma alteração material no conteúdo do banco de dados em comparação com uma migração heterogênea, ainda recomendamos validar as principais funcionalidades e o aspecto de desempenho da nova configuração.  

### <a name="cutover-plan"></a>Plano cutover
Embora essa migração seja direta, no entanto, envolve o descomissionamento de um DB existente.  Um planejamento cuidadoso para preservar o sistema de origem com seu conteúdo associado e imagens de backup são fundamentais caso seja necessário um recuo.  Um bom planejamento oferece uma reversão mais rápida.   


## <a name="post-migration"></a>Após a migração
O trabalho de migração não é feito até que tenhamos dissociado com segurança quaisquer serviços ou conectividade dependentes do HLI para garantir que a integridade dos dados seja preservada.  Além disso, encerre serviços desnecessários.  Esta seção chama alguns itens de primeira linha.

### <a name="decommissioning-the-hli"></a>Desativação do HLI
Após uma migração bem-sucedida do HANA DB para o Azure VM, certifique-se de que nenhuma transação comercial produtiva seja executada no DDb HLI.  No entanto, manter o HLI funcionando por um período de tempo é igual à janela de retenção de backup local é uma prática segura que garante uma recuperação mais rápida, se necessário.  Só então a lâmina HLI deve ser desativada.  Os clientes devem concluir contratualmente seus compromissos de HLI com a Microsoft entrando em contato com seus representantes da Microsoft.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Remova qualquer proxy (ex: Iptables, BIGIP) configurado para HLI 
Se um serviço proxy como o IPTables for usado para direcionar o tráfego no local para e para o HLI, ele não será mais necessário após a migração bem-sucedida para a VM.  No entanto, este serviço de conectividade deve ser mantido enquanto a lâmina HLI ainda estiver em pé.  Somente desligue o serviço depois que a lâmina HLI estiver totalmente desativada.

### <a name="remove-global-reach-for-hli"></a>Remover alcance global para HLI 
O Global Reach é usado para conectar o gateway ExpressRoute dos clientes com o gateway HLI ExpressRoute.  Ele permite que o tráfego no local dos clientes chegue diretamente ao inquilino do HLI sem o uso de um serviço proxy.  Essa conexão não é mais necessária na ausência da unidade HLI após a migração.  Como no caso do serviço proxy IPTables, o GlobalReach também deve ser mantido até que a lâmina HLI seja totalmente desativada.

### <a name="operating-system-subscription--movereuse"></a>Assinatura do sistema operacional - mover/reutilizar
À medida que os servidores VM são levantados e as lâminas HLI são desativadas, as assinaturas do sistema operacional podem ser substituídas ou reutilizadas para evitar o pagamento duplo de licenças do Sistema Operacional.



## <a name="next-steps"></a>Próximas etapas
 Consulte estes artigos:
- [Configurações e operações de infra-estrutura SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
- [Cargas de trabalho SAP no Azure: lista de verificação de planejamento e implantação](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist).
