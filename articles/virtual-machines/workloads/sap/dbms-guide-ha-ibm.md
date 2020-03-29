---
title: Configurar o IBM Db2 HADR em máquinas virtuais Azure (VMs) | Microsoft Docs
description: Estabeleça alta disponibilidade do IBM Db2 LUW em máquinas virtuais Azure (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/06/2020
ms.author: juergent
ms.openlocfilehash: 614ac8b651224a3b6ec605a6bffd520449a1d793
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78926737"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105

[sles-for-sap-bp]:https://www.suse.com/documentation/sles-for-sap-12/
[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm
[sles-pacemaker]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker
[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[nfs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs
[sles-ha-guide]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP4/
[ascs-ha]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-suse-linux-enterprise-server-with-pacemaker"></a>Alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker

O IBM Db2 para Linux, UNIX e Windows (LUW) na [configuração de alta disponibilidade e recuperação de desastres (HADR)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html) consiste em um nó que executa uma instância primária do banco de dados e pelo menos um nó que executa uma instância de banco de dados secundário. As alterações na instância do banco de dados principal são replicadas em uma instância de banco de dados secundário de forma sincronizada ou assíncrona, dependendo da sua configuração. 

Este artigo descreve como implantar e configurar as máquinas virtuais Azure (VMs), instalar a estrutura de cluster e instalar o IBM Db2 LUW com configuração HADR. 

O artigo não abrange como instalar e configurar o IBM Db2 LUW com instalação de software HADR ou SAP. Para ajudá-lo a realizar essas tarefas, fornecemos referências aos manuais de instalação SAP e IBM. Este artigo foca em peças específicas para o ambiente Azure. 

As versões suportadas do IBM Db2 são 10.5 e posteriores, conforme documentado na nota SAP [1928533].

Antes de iniciar uma instalação, consulte as seguintes notas sap e documentação:

| Nota SAP | Descrição |
| --- | --- |
| [1928533] | Aplicações SAP no Azure: produtos suportados e tipos de VM do Azure |
| [2015553] | SAP no Azure: Pré-requisitos de suporte |
| [2178632] | Principais métricas de monitoramento para SAP no Azure |
| [2191498] | SAP no Linux com Azure: Monitoramento aprimorado |
| [2243692] | VM do Linux on Azure (IaaS): problemas de licença SAP |
| [1984787] | SUSE LINUX Enterprise Server 12: notas de instalação |
| [1999351] | Solução de problemas de monitoramento aprimorado do Azure para SAP |
| [2233094] | DB6: Aplicativos SAP no Azure que usam IBM Db2 para Linux, UNIX e Windows - informações adicionais |
| [1612105] | DB6: PERGUNTAS FREQUENTES no Db2 com HADR |


| Documentação | 
| --- |
| [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Tem todas as notas SAP necessárias para Linux |
| [Planejamento e implementação de máquinas virtuais do Azure para SAP no][planning-guide] guia Linux |
| [Implantação do Azure Virtual Machines para SAP no Linux][deployment-guide] (este artigo) |
| [Implantação do sistema de gerenciamento de banco de dados do Azure Virtual Machines (DBMS) para SAP no][dbms-guide] guia Linux |
| [Lista de verificação de planejamento e implantação de carga de trabalho SAP no Azure][azr-sap-plancheck] |
| [SUSE Linux Enterprise Server para sap aplicativos 12 guias de práticas recomendadas do SP4][sles-for-sap-bp] |
| [SUSE Linux Enterprise Extensão de alta disponibilidade 12 SP4][sles-ha-guide] |
| [Implantação do IBM Db2 Azure Virtual Machines DBMS para carga de trabalho SAP][dbms-db2] |
| [IBM DB2 HADR 11.1][db2-hadr-11.1] |
| [IBM DB2 HADR R 10.5][db2-hadr-10.5] |

## <a name="overview"></a>Visão geral
Para obter alta disponibilidade, o IBM Db2 LUW com HADR está instalado em pelo menos duas máquinas virtuais Azure, que são implantadas em um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou em todas as Zonas de Disponibilidade do [Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Os gráficos a seguir exibem uma configuração de duas VMs do servidor de banco de dados Azure. Ambas as VMs do servidor de banco de dados Azure têm seu próprio armazenamento conectado e estão em funcionamento. No HADR, uma instância de banco de dados em uma das VMs do Azure tem o papel da instância primária. Todos os clientes estão conectados a esta instância primária. Todas as alterações nas transações de banco de dados são persistidas localmente no registro de transações Db2. À medida que os registros de registro de transações são persistidos localmente, os registros são transferidos via TCP/IP para a instância do banco de dados no segundo servidor de banco de dados, no servidor de espera ou na instância de espera. A instância de espera atualiza o banco de dados local, encaminhando os registros de registro de transações transferidos. Desta forma, o servidor de espera é mantido em sincronia com o servidor principal.

HADR é apenas uma funcionalidade de replicação. Não tem detecção de falhas nem instalações automáticas de aquisição ou failover. Uma aquisição ou transferência para o servidor de espera deve ser iniciada manualmente por um administrador de banco de dados. Para obter uma aquisição automática e detecção de falhas, você pode usar o recurso de clusterdo Linux Pacemaker. O Pacemaker monitora as duas instâncias do servidor de banco de dados. Quando a instância do servidor de banco de dados principal falha, o Pacemaker inicia uma aquisição *automática* de HADR pelo servidor de espera. O Pacemaker também garante que o endereço IP virtual seja atribuído ao novo servidor principal.

![Visão geral de alta disponibilidade do IBM Db2](./media/dbms-guide-ha-ibm/ha-db2-hadr-lb.png)

Para que os servidores de aplicativos SAP se conectem ao banco de dados principal, você precisa de um nome de host virtual e um endereço IP virtual. No caso de um failover, os servidores de aplicativos SAP se conectarão à nova instância de banco de dados principal. Em um ambiente Azure, um [balanceador de carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é necessário para usar um endereço IP virtual da maneira que for necessário para o HADR do IBM Db2. 

Para ajudá-lo a entender completamente como o IBM Db2 LUW com HADR e Pacemaker se encaixa em uma configuração de sistema SAP altamente disponível, a imagem a seguir apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP baseado no banco de dados IBM Db2. Este artigo abrange apenas o IBM Db2, mas fornece referências a outros artigos sobre como configurar outros componentes de um sistema SAP.

![Visão geral do ambiente completo de alta disponibilidade do IBM DB2](.//media/dbms-guide-ha-ibm/end-2-end-ha.png)


### <a name="high-level-overview-of-the-required-steps"></a>Visão geral de alto nível das etapas necessárias
Para implantar uma configuração IBM Db2, você precisa seguir estas etapas:

  + Planeje seu ambiente.
  + Implante as VMs.
  + Atualize o SUSE Linux e configure sistemas de arquivos.
  + Instale e configure o Marcapasso.
  + Instale [o NFS altamente disponível][nfs-ha].
  + Instale [ASCS/ERS em um cluster separado][ascs-ha].
  + Instale o banco de dados IBM Db2 com opção distribuída/de alta disponibilidade (SWPM).
  + Instale e crie um nó e instância de banco de dados secundários e configure o HADR.
  + Confirme que hadr está trabalhando.
  + Aplique a configuração Pacemaker para controlar o IBM Db2.
  + Configure o Balancer de carga Azure.
  + Instale servidores de aplicativos de diálogo e principal.
  + Verifique e adapte a configuração dos servidores de aplicativos SAP.
  + Realize testes de failover e aquisição.



## <a name="plan-azure-infrastructure-for-hosting-ibm-db2-luw-with-hadr"></a>Planeje a infra-estrutura do Azure para hospedar o IBM Db2 LUW com hadr

Complete o processo de planejamento antes de executar a implantação. O planejamento constrói a base para a implantação de uma configuração de Db2 com HADR no Azure. Os elementos-chave que precisam fazer parte do planejamento do IMB Db2 LUW (parte do banco de dados do ambiente SAP) estão listados na tabela a seguir:

| Tópico | Descrição breve |
| --- | --- |
| Definir grupos de recursos do Azure | Grupos de recursos onde você implanta VM, VNet, Azure Load Balancer e outros recursos. Pode ser existente ou novo. |
| Definição de rede virtual / sub-rede | Onde as VMs para IBM Db2 e Azure Load Balancer estão sendo implantadas. Pode ser existente ou recém-criado. |
| Máquinas virtuais hospedando IBM Db2 LUW | Tamanho vM, armazenamento, rede, endereço IP. |
| Nome do host virtual e IP virtual para banco de dados IBM Db2| O IP virtual ou o nome do host usado para conexão de servidores de aplicativos SAP. **db-virt-hostname**, **db-virt-ip**. |
| Esgrima azure | Esgrima azure ou esgrima SBD (altamente recomendado). Método para evitar situações cerebrais divididas. |
| SBD VM | Tamanho da máquina virtual SBD, armazenamento, rede. |
| Azure Load Balancer | Utilização de básico ou padrão (recomendado), porta de sonda para banco de dados Db2 (nossa recomendação 62500) **porta-sonda**. |
| Resolução de nomes| Como funciona a resolução de nomes no ambiente. O serviço de DNS é altamente recomendado. O arquivo hosts local pode ser usado. |
    
Para obter mais informações sobre o Linux Pacemaker no Azure, consulte [Configurar o Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker).

## <a name="deployment-on-suse-linux"></a>Implantação no SUSE Linux

O agente de recursos do IBM Db2 LUW está incluído no SUSE Linux Enterprise Server para aplicativos SAP. Para a configuração descrita neste documento, você deve usar o SUSE Linux Server para aplicativos SAP. O Azure Marketplace contém uma imagem para o SUSE Enterprise Server para aplicativos SAP 12 que você pode usar para implantar novas máquinas virtuais Azure. Esteja ciente dos vários modelos de suporte ou serviço que são oferecidos pela SUSE através do Azure Marketplace quando você escolher uma imagem VM no Azure VM Marketplace. 

### <a name="hosts-dns-updates"></a>Hosts: Atualizações de DNS
Faça uma lista de todos os nomes de host, incluindo nomes de host virtuais, e atualize seus servidores DNS para habilitar o endereço IP adequado para a resolução do nome do host. Se um servidor DNS não existir ou você não puder atualizar e criar entradas DNS, você precisa usar os arquivos host locais das VMs individuais que estão participando deste cenário. Se você estiver usando entradas de arquivos host, certifique-se de que as entradas sejam aplicadas a todas as VMs no ambiente do sistema SAP. No entanto, recomendamos que você use seu DNS que, idealmente, se estende para o Azure


### <a name="manual-deployment"></a>Implantação manual

Certifique-se de que o sistema operacional selecionado é suportado pela IBM/SAP para IBM Db2 LUW. A lista de versões suportadas do SO para versões Azure VMs e Db2 está disponível na nota SAP [1928533]. A lista de lançamentos do SO por versão db2 individual está disponível na Matriz de Disponibilidade de Produtos SAP. Recomendamos um mínimo de SLES 12 SP4 devido a melhorias de desempenho relacionadas ao Azure nesta ou posterior versões do SUSE Linux.

1. Crie ou selecione um grupo de recursos.
1. Crie ou selecione uma rede e uma sub-rede virtuais.
1. Crie um conjunto de disponibilidade do Azure ou implante uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, defina os domínios de atualização máxima para 2.
1. Criar máquina virtual 1.
    + Use SLES para imagem SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure criado na etapa 3 ou selecione Zona de disponibilidade.
1.  Criar máquina virtual 2.
    + Use SLES para imagem SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure criado na etapa 3 ou selecione Zona de disponibilidade (não a mesma região da etapa 3).
1. Adicione discos de dados às VMs e, em seguida, verifique a recomendação de uma configuração do sistema de arquivos no artigo [IBM Db2 Azure Virtual Machines DBMS implantação para carga de trabalho SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster do Pacemaker
    
Para criar um cluster pacemaker básico para este servidor IBM Db2, consulte [Configurar o Pacemaker no SUSE Linux Enterprise Server no Azure][sles-pacemaker]. 

## <a name="install-the-ibm-db2-luw-and-sap-environment"></a>Instale o ambiente IBM Db2 LUW e SAP

Antes de iniciar a instalação de um ambiente SAP baseado no IBM Db2 LUW, revise a seguinte documentação:

+ Documentação do Azure
+ Documentação SAP
+ Documentação da IBM

Os links para esta documentação estão fornecidos na seção introdutória deste artigo.

Verifique os manuais de instalação do SAP sobre a instalação de aplicativos baseados no NetWeaver no IBM Db2 LUW.

Você pode encontrar os guias no portal SAP Help usando o [Localizador do Guia de Instalação SAP][sap-instfind].

Você pode reduzir o número de guias exibidas no portal definindo os seguintes filtros:

- Eu quero: "Instale um novo sistema"
- Meu banco de dados: "IBM Db2 para Linux, Unix e Windows"
- Filtros adicionais para versões SAP NetWeaver, configuração de pilha ou sistema operacional

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para configurar o IBM Db2 LUW com HADR

Para configurar a principal instância de banco de dados IBM Db2 LUW:

- Use a opção de alta disponibilidade ou distribuída.
- Instale a instância SAP ASCS/ERS e Banco de dados.
- Faça um backup do banco de dados recém-instalado.


> [!IMPORTANT] 
> Anote a "porta de comunicação do banco de dados" definida durante a instalação. Deve ser o mesmo número de porta para ambas as instâncias de banco de dados

Para configurar o servidor de banco de dados standby usando o procedimento de cópia homogênea do sistema SAP, execute estas etapas:

1. Selecione a opção **de cópia** do sistema > instância de banco de dados distribuído de **sistemas** > **de** > **destino**.
1. Como método de cópia, **selecione Sistema Homogêneo** para que você possa usar backup para restaurar um backup na instância do servidor de espera.
1. Quando você chegar à etapa de saída para restaurar o banco de dados para cópia homogênea do sistema, saia do instalador. Restaurar o banco de dados a partir de um backup do host principal. Todas as fases de instalação subseqüentes já foram executadas no servidor de banco de dados principal.
1. Configure o HADR para IBM Db2.

   > [!NOTE]
   > Para instalação e configuração específicas do Azure e pacemaker: Durante o procedimento de instalação através do SAP Software Provisioning Manager, há uma pergunta explícita sobre a alta disponibilidade para IBM Db2 LUW:
   >+ Não selecione **o IBM Db2 pureScale**.
   >+ Não selecione **Instalar a automação do sistema IBM Tivoli para multiplataformas**.
   >+ Não selecione **Gerar arquivos de configuração de cluster**.

   Quando você usar um dispositivo SBD para Linux Pacemaker, defina os seguintes parâmetros do DB2 HADR:
   + Duração da janela do peer HADR (segundos) (HADR_PEER_WINDOW) = 300  
   + Valor do tempo do HADR (HADR_TIMEOUT) = 60

   Quando você usar um agente de esgrima Azure Pacemaker, defina os seguintes parâmetros:
   + Duração da janela do peer HADR (segundos) (HADR_PEER_WINDOW) = 900  
   + Valor do tempo do HADR (HADR_TIMEOUT) = 60

Recomendamos os parâmetros anteriores com base nos testes iniciais de failover/aquisição. É obrigatório que você teste para a funcionalidade adequada de failover e aquisição com essas configurações de parâmetro. Como as configurações individuais podem variar, os parâmetros podem exigir ajuste. 

> [!IMPORTANT]
> Específico para o IBM Db2 com configuração HADR com inicialização normal: A instância secundária ou de banco de dados em standby deve estar em funcionamento antes de iniciar a instância primária do banco de dados.

Para fins de demonstração e os procedimentos descritos neste artigo, o SID do banco de dados é **PTR**.

#### <a name="ibm-db2-hadr-check"></a>Verificação DE HADR IBM Db2
Depois de configurar o HADR e o status for PEER e CONNECTED nos nós primário e de espera, execute a seguinte verificação:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
# Database Member 0 -- Database PTR -- Active -- Up 1 days 01:51:38 -- Date 2019-02-06-15.35.28.505451
# 
#                             <b>HADR_ROLE = PRIMARY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 1
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.170561 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6137
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 13
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000025
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223713
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 374400
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15571, 27902548040
#        STANDBY_RECV_REPLAY_GAP(bytes) = 0
#                      PRIMARY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#                      STANDBY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:34:39.000000 (1549467279)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:40:25.000000 (1549467625)
#              READS_ON_STANDBY_ENABLED = N

#Secondary output:
# Database Member 0 -- Database PTR -- Standby -- Up 1 days 01:46:43 -- Date 2019-02-06-15.38.25.644168
# 
#                             <b>HADR_ROLE = STANDBY
#                           REPLAY_TYPE = PHYSICAL
#                         HADR_SYNCMODE = NEARSYNC
#                            STANDBY_ID = 0
#                         LOG_STREAM_ID = 0
#                            HADR_STATE = PEER
#                            HADR_FLAGS = TCP_PROTOCOL
#                   PRIMARY_MEMBER_HOST = azibmdb02
#                      PRIMARY_INSTANCE = db2ptr
#                        PRIMARY_MEMBER = 0
#                   STANDBY_MEMBER_HOST = azibmdb01
#                      STANDBY_INSTANCE = db2ptr
#                        STANDBY_MEMBER = 0
#                   HADR_CONNECT_STATUS = CONNECTED</b>
#              HADR_CONNECT_STATUS_TIME = 02/05/2019 13:51:47.205067 (1549374707)
#           HEARTBEAT_INTERVAL(seconds) = 15
#                      HEARTBEAT_MISSED = 0
#                    HEARTBEAT_EXPECTED = 6186
#                 HADR_TIMEOUT(seconds) = 60
#         TIME_SINCE_LAST_RECV(seconds) = 5
#              PEER_WAIT_LIMIT(seconds) = 0
#            LOG_HADR_WAIT_CUR(seconds) = 0.000
#     LOG_HADR_WAIT_RECENT_AVG(seconds) = 0.000023
#    LOG_HADR_WAIT_ACCUMULATED(seconds) = 434.595
#                   LOG_HADR_WAIT_COUNT = 223725
# SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
# SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 372480
#             PRIMARY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#             STANDBY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#                   HADR_LOG_GAP(bytes) = 0
#      STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000280.LOG, 15574, 27902562173
#        STANDBY_RECV_REPLAY_GAP(bytes) = 155
#                      PRIMARY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#                      STANDBY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#               STANDBY_REPLAY_LOG_TIME = 02/06/2019 15:37:34.000000 (1549467454)
#          STANDBY_RECV_BUF_SIZE(pages) = 2048
#              STANDBY_RECV_BUF_PERCENT = 0
#            STANDBY_SPOOL_LIMIT(pages) = 0
#                 STANDBY_SPOOL_PERCENT = NULL
#                    STANDBY_ERROR_TIME = NULL
#                  PEER_WINDOW(seconds) = 300
#                       PEER_WINDOW_END = 02/06/2019 15:43:19.000000 (1549467799)
#              READS_ON_STANDBY_ENABLED = N
</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuração do Marcapasso DB2

Quando você usa pacemaker para failover automático no caso de uma falha no nó, você precisa configurar suas instâncias db2 e Pacemaker de acordo. Esta seção descreve este tipo de configuração.

Os seguintes itens são prefixados com:

- **[A]**: Aplicável a todos os nós
- **[1]**: Aplicável apenas ao nó 1 
- **[2]**: Aplicável apenas ao nó 2

**[A]** Pré-requisitos para configuração do Marcapasso:
1. Desligou ambos os servidores\<de banco de dados com o> de> do usuário db2 com db2stop.
1. Alterar o ambiente shell\<para db2 sid> usuário para */bin/ksh*. Recomendamos que você use a ferramenta Yast. 


### <a name="pacemaker-configuration"></a>Configuração do marcapasso

> [!IMPORTANT]
> Testes recentes revelaram situações, onde a netcat deixa de responder às solicitações devido ao atraso e sua limitação de lidar com apenas uma conexão. O recurso netcat pára de ouvir as solicitações do balanceador de carga do Azure e o IP flutuante fica indisponível.  
> Para clusters Pacemaker existentes, recomendamos no passado substituir o netcat por socat. Atualmente recomendamos o uso do agente de recursos azure-lb, que faz parte dos agentes de recursos do pacote, com os seguintes requisitos de versão do pacote:
> - Para SLES 12 SP4/SP5, a versão deve ser pelo menos agentes de recursos-4.3.018.a7fb5035-3.30.1.  
> - Para sles 15/15 SP1, a versão deve ser pelo menos agentes de recursos-4.3.0184.6ee15eb2-4.13.1.  
>
> Observe que a alteração exigirá um breve tempo de inatividade.  
> Para os clusters Pacemaker existentes, se a configuração já foi alterada para usar socat como descrito no [Azure Load-Balancer Detection Hardening](https://www.suse.com/support/kb/doc/?id=7024128), não há necessidade de mudar imediatamente para o agente de recursos azure-lb.

**[1] Configuração** do Marcapasso específico do IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo crm configure property maintenance-mode=true
</code></pre>

**[1]** Crie recursos ibm Db2:
<pre><code># Replace **bold strings** with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.

sudo crm configure primitive rsc_Db2_db2ptr_<b>PTR</b> db2 \
        params instance="<b>db2ptr</b>" dblist="<b>PTR</b>" \
        op start interval="0" timeout="130" \
        op stop interval="0" timeout="120" \
        op promote interval="0" timeout="120" \
        op demote interval="0" timeout="120" \
        op monitor interval="30" timeout="60" \
        op monitor interval="31" role="Master" timeout="60"

# Configure virtual IP - same as Azure Load Balancer IP
sudo crm configure primitive rsc_ip_db2ptr_<b>PTR</b> IPaddr2 \
        op monitor interval="10s" timeout="20s" \
        params ip="<b>10.100.0.10</b>"

# Configure probe port for Azure load Balancer
sudo crm configure primitive rsc_nc_db2ptr_<b>PTR</b> azure-lb port=<b>62500</b>

sudo crm configure group g_ip_db2ptr_<b>PTR</b> rsc_ip_db2ptr_<b>PTR</b> rsc_nc_db2ptr_<b>PTR</b>

sudo crm configure ms msl_Db2_db2ptr_<b>PTR</b> rsc_Db2_db2ptr_<b>PTR</b> \
        meta target-role="Started" notify="true"

sudo crm configure colocation col_db2_db2ptr_<b>PTR</b> inf: g_ip_db2ptr_<b>PTR</b>:Started msl_Db2_db2ptr_<b>PTR</b>:Master

sudo crm configure order ord_db2_ip_db2ptr_<b>PTR</b> inf: msl_Db2_db2ptr_<b>PTR</b>:promote g_ip_db2ptr_<b>PTR</b>:start

sudo crm configure rsc_defaults resource-stickiness=1000
sudo crm configure rsc_defaults migration-threshold=5000
</code></pre>

**[1]** Inicie os recursos do IBM Db2:
* Coloque o Pacemaker fora do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo crm configure property maintenance-mode=false</pre></code>

**[1]** Certifique-se de que o status do cluster está OK e que todos os recursos foram iniciados. Não importa em que nó os recursos estão funcionando.
<pre><code>sudo crm status</code>

# <a name="2-nodes-configured"></a>2 nós configurados
# <a name="5-resources-configured"></a>5 recursos configurados

# <a name="online--azibmdb01-azibmdb02-"></a>Online: [ azibmdb01 azibmdb02 ]

# <a name="full-list-of-resources"></a>Lista completa de recursos:

#  <a name="stonith-sbd----stonithexternalsbd-started-azibmdb02"></a>stonith-sbd (stonith:externo/sbd): Iniciado azibmdb02
#  <a name="resource-group-g_ip_db2ptr_ptr"></a>Grupo de recursos: g_ip_db2ptr_PTR
#      <a name="rsc_ip_db2ptr_ptr--ocfheartbeatipaddr2-------started-azibmdb02"></a>rsc_ip_db2ptr_PTR (ocf::heartbeat:IPaddr2): Iniciado azibmdb02
#      <a name="rsc_nc_db2ptr_ptr--ocfheartbeatazure-lb------started-azibmdb02"></a>rsc_nc_db2ptr_PTR (ocf::heartbeat:azure-lb): Começou azibmdb02
#  <a name="masterslave-set-msl_db2_db2ptr_ptr-rsc_db2_db2ptr_ptr"></a>Conjunto Mestre/Escravo: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
#      <a name="masters--azibmdb02-"></a>Mestres: [ azibmdb02 ]
#      <a name="slaves--azibmdb01-"></a>Escravos: [ azibmdb01 ]
</pre>

> [!IMPORTANT]
> Você deve gerenciar a instância Db2 agrupada pacemaker usando ferramentas pacemaker. Se você usar comandos db2 como db2stop, pacemaker detectará a ação como uma falha de recurso. Se você estiver realizando a manutenção, você pode colocar os nós ou recursos no modo de manutenção. O Pacemaker suspende os recursos de monitoramento e, em seguida, você pode usar comandos normais de administração db2.


### <a name="configure-azure-load-balancer"></a>Configurar o Azure Load Balancer
Para configurar o Azure Load Balancer, recomendamos que você use o [Azure Standard Load Balancer SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) e, em seguida, faça o seguinte;

> [!NOTE]
> O Standard Load Balancer SKU tem restrições ao acessar endereços IP públicos a partir dos nós abaixo do Balanceador de carga. O artigo [Conectividade de ponto final público para máquinas virtuais usando o Azure Standard Load Balancer em cenários de alta disponibilidade SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) está descrevendo maneiras de permitir que esses nodes acessem endereços IP públicos

1. Crie um pool de IP front-end:

   a. No portal Azure, abra o Azure Load Balancer, selecione **o pool de IP frontend**e selecione **Adicionar**.

   b. Digite o nome do novo pool de IP front-end (por exemplo, **conexão Db2**).

   c. Defina a **Atribuição** **como Estática**e digite o endereço IP **Virtual-IP** definido no início.

   d. Selecione **OK**.

   e. Depois que o novo pool de IP de front-end for criado, anote o endereço IP do pool.

1. Crie um pool de back-end:

   a. No portal Azure, abra o Azure Load Balancer, selecione **pools de back-end**e selecione **Adicionar**.

   b. Digite o nome do novo pool back-end (por exemplo, **Db2-backend**).

   c. Selecione **Adicionar uma máquina virtual**.

   d. Selecione o conjunto de disponibilidade ou as máquinas virtuais que hospedam o banco de dados IBM Db2 criados na etapa anterior.

   e. Selecione as máquinas virtuais do cluster IBM Db2.

   f. Selecione **OK**.

1. Crie uma sonda de saúde:

   a. No portal Azure, abra o Azure Load Balancer, selecione **testes de saúde**e selecione **Adicionar**.

   b. Digite o nome do novo teste de saúde (por exemplo, **Db2-hp**).

   c. Selecione **TCP** como protocolo e porta **62500**. Mantenha o valor **do intervalo** definido em **5**e mantenha o valor **limite insalubre** definido como **2**.

   d. Selecione **OK**.

1. Crie as regras de balanceamento de carga:

   a. No portal Azure, abra o Balanceador de carga do Azure, selecione **Regras de balanceamento de carga**e selecione **Adicionar**.

   b. Digite o nome da nova regra do Balanceador de carga (por exemplo, **Db2-SID**).

   c. Selecione o endereço IP front-end, o pool back-end e o teste de saúde que você criou anteriormente (por exemplo, **db2-frontend**).

   d. Mantenha o **protocolo** definido como **TCP**e digite a *porta de comunicação do banco de dados da porta*.

   e. Aumente o **tempo limite de ociosidade** para 30 minutos.

   f. Certifique-se de **habilitar IP Flutuante**.

   g. Selecione **OK**.


### <a name="make-changes-to-sap-profiles-to-use-virtual-ip-for-connection"></a>Faça alterações nos perfis SAP para usar ip virtual para conexão
Para se conectar à instância primária da configuração HADR, a camada de aplicativo SAP precisa usar o endereço IP virtual que você definiu e configurou para o Azure Load Balancer. As seguintes alterações são necessárias:

/sapmnt/\<SID>/perfil/DEFAULT. Pfl
<pre><code>SAPDBHOST = db-virt-hostname
j2ee/dbhost = db-virt-hostname
</code></pre>

/sapmnt/\<SID>/global/db6/db2cli.ini
<pre><code>Hostname=db-virt-hostname
</code></pre>



## <a name="install-primary-and-dialog-application-servers"></a>Instale servidores de aplicativos de diálogo e principal

Ao instalar servidores de aplicativos de diálogo e principal contra uma configuração DB2 HADR, use o nome de host virtual escolhido para a configuração. 

Se você executou a instalação antes de criar a configuração DB2 HADR, faça as alterações conforme descrito na seção anterior e da seguinte forma para pilhas SAP Java.

### <a name="abapjava-or-java-stack-systems-jdbc-url-check"></a>ABAP+Java ou Java stack sistemas JDBC URL check

Use a ferramenta J2EE Config para verificar ou atualizar a URL jdbc. Como a ferramenta J2EE Config é uma ferramenta gráfica, você precisa ter o servidor X instalado:
 
1. Faça login no servidor de aplicação principal da instância J2EE e execute:`sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh`
1. No quadro esquerdo, escolha a **loja de segurança**.
1. No quadro direito, escolha a chave jdbc/pool/\<SAPSID>/url.
1. Altere o nome do host na URL JDBC para o nome do host virtual.
     `jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0`
1. Selecione **Adicionar**.
1. Para salvar suas alterações, selecione o ícone de disco no canto superior esquerdo.
1. Feche a ferramenta de configuração.
1. Reinicie a instância Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurar o arquivamento de log para configuração HADR
Para configurar o arquivamento de log db2 para configuração HADR, recomendamos que você configure o banco de dados principal e o banco de espera para ter capacidade automática de recuperação de log de todos os locais de arquivamento de log. Tanto o banco de dados principal quanto o de espera devem ser capazes de recuperar arquivos de log de todos os locais de arquivo de log para os quais qualquer uma das instâncias do banco de dados pode arquivar arquivos de log. 

O arquivamento de log é realizado apenas pelo banco de dados principal. Se você alterar as funções HADR dos servidores de banco de dados ou se ocorrer uma falha, o novo banco de dados principal será responsável pelo arquivamento de log. Se você configurar vários locais de arquivo de log, seus logs podem ser arquivados duas vezes. No caso de um catch-up local ou remoto, você também pode ter que copiar manualmente os logs arquivados do servidor principal antigo para a localização ativa do log do novo servidor principal.

Recomendamos a configuração de um compartilhamento comum de NFS onde os logs são escritos a partir de ambos os nós. A ação da NFS tem que estar altamente disponível. 

Você pode usar as ações NFS disponíveis para transportes ou um diretório de perfil. Para obter mais informações, consulte:

- [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server][nfs-ha] 
- [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com arquivos Azure NetApp para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- [Arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (para criar compartilhamentos NFS)


## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como você pode testar sua configuração DB2 HADR. *Todos os testes assumem que você está conectado como raiz* de usuário e a primária do IBM Db2 está sendo executado na máquina virtual *azibmdb01.*

O status inicial para todos os casos de teste é explicado aqui: (crm_mon -r ou crm status)

- **crm status** é um instantâneo do status Pacemaker no momento da execução 
- **crm_mon -r** é a saída contínua do status Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   Promoting azibmdb01
     Slaves: [ azibmdb02 ]
</code></pre>

O status original em um sistema SAP está documentado no Transaction DBACOCKPIT > Configuration > Overview, conforme mostrado na imagem a seguir:

![DBACockpit - Pré-Migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-org.png)




### <a name="test-takeover-of-ibm-db2"></a>Teste de aquisição do IBM Db2


> [!IMPORTANT] 
> Antes de começar o teste, certifique-se de que:
> * O marca-passo não tem nenhuma ação fracassada (status crm).
> * Não há restrições de localização (sobras do teste de migração)
> * A sincronização IBM Db2 HADR está funcionando. Verifique com o\<usuário db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó que está executando o banco de dados Db2 principal executando o seguinte comando:
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb02</code></pre>

Depois que a migração é feita, a saída de status do crm parece:
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]
</code></pre>

O status original em um sistema SAP está documentado no Transaction DBACOCKPIT > Configuration > Overview, conforme mostrado na imagem a seguir:

![DBACockpit - Pós-Migração](./media/dbms-guide-ha-ibm/hadr-sap-mgr-post.png)

A migração de recursos com "migração de recursos crm" cria restrições de localização. As restrições de localização devem ser excluídas. Se as restrições de localização não forem excluídas, o recurso não poderá falhar ou você poderá experimentar aquisições indesejadas. 

Migre o recurso de volta para *o azibmdb01* e limpe as restrições de localização
<pre><code>crm resource migrate msl_<b>Db2_db2ptr_PTR</b> azibmdb01
crm resource clear msl_<b>Db2_db2ptr_PTR</b>
</code></pre>

- **recurso de \<crm \<migrar res_name>> de host:** Cria restrições de localização e pode causar problemas com a aquisição
- **crm recurso \<claro res_name>**: Limpa restrições de localização
- **limpeza \<de recursos crm res_name>**: Limpa todos os erros do recurso

### <a name="test-the-fencing-agent"></a>Teste o agente de esgrima

Neste caso, testamos esgrima SBD, o que recomendamos que você faça quando você usa o SUSE Linux.

<pre><code>
azibmdb01:~ # ps -ef|grep sbd
root       2374      1  0 Feb05 ?        00:00:17 sbd: inquisitor
root       2378   2374  0 Feb05 ?        00:00:40 sbd: watcher: /dev/disk/by-id/scsi-36001405fbbaab35ee77412dacb77ae36 - slot: 0 - uuid: 27cad13a-0bce-4115-891f-43b22cfabe65
root       2379   2374  0 Feb05 ?        00:01:51 sbd: watcher: Pacemaker
root       2380   2374  0 Feb05 ?        00:00:18 sbd: watcher: Cluster

azibmdb01:~ # kill -9 2374
</code></pre>

O nó de cluster *azibmdb01* deve ser reiniciado. O papel HADR principal do IBM Db2 será movido para *azibmdb02*. Quando *o azibmdb01* estiver novamente on-line, a instância Db2 será mover-se na função de uma instância de banco de dados secundário. 

Se o serviço Pacemaker não iniciar automaticamente na antiga primária reiniciada, certifique-se de iniciá-lo manualmente com:

<code><pre>sudo service pacemaker start</code></pre>

### <a name="test-a-manual-takeover"></a>Teste uma aquisição manual

Você pode testar uma aquisição manual parando o serviço Pacemaker no nó *azibmdb01:*
<pre><code>service pacemaker stop</code></pre>

status em *azibmdb02*
<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]
</code></pre>

Após o failover, você pode iniciar o serviço novamente no *azibmdb01*.
<pre><code>service pacemaker start</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Mate o processo Db2 no nó que executa o banco de dados primário HADR

<pre><code>#Kill main db2 process - db2sysc
azibmdb01:~ # ps -ef|grep db2s
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0

azibmdb01:~ # kill -9 34598
</code></pre>

A instância db2 vai falhar, e pacemaker relatará o seguinte status:

<pre><code>
2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Slaves: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms

</code></pre>

O Pacemaker reiniciará a instância de banco de dados principal do Db2 no mesmo nó, ou falhará no nó que está executando a instância de banco de dados secundário e um erro é relatado.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=157, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:28:19 2019', queued=40ms, exec=223ms
</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Mate o processo Db2 no nó que executa a instância de banco de dados secundário

<pre><code>azibmdb02:~ # ps -ef|grep db2s
db2ptr    65250  65248  0 Feb11 ?        00:09:27 db2sysc 0

azibmdb02:~ # kill -9</code></pre>

O nó entra em falha declarada e erro relatado
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb02
     Masters: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>

A instância Db2 é reiniciada na função secundária que havia atribuído anteriormente.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_monitor_30000 on azibmdb02 'not running' (7): call=144, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:36:59 2019', queued=0ms, exec=0ms</code></pre>



### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Parar DB via força db2stop no nó que executa a instância de banco de dados primário HADR

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

À medida\<que o usuário db2 sid> executar comando db2stop force:
<pre><code>azibmdb01:~ # su - db2ptr
azibmdb01:db2ptr> db2stop force</code></pre>

Falha detectada
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

 stonith-sbd    (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Stopped
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Stopped
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     rsc_Db2_db2ptr_PTR      (ocf::heartbeat:db2):   FAILED azibmdb01
     Slaves: [ azibmdb02 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_demote_0 on azibmdb01 'unknown error' (1): call=201, status=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:25 2019', queued=1ms, exec=150ms</code></pre>

A instância de banco de dados secundário DB2 HADR foi promovida para o papel principal
<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb01
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ]

Failed Actions:
* rsc_Db2_db2ptr_PTR_start_0 on azibmdb01 'unknown error' (1): call=205, stat
us=complete, exitreason='',
    last-rc-change='Tue Feb 12 14:45:27 2019', queued=0ms, exec=865ms</pre></code>


### <a name="crash-vm-with-restart-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Falha da VM com reinicialização no nó que executa a instância de banco de dados primário HADR

<pre><code>#Linux kernel panic - with OS restart
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

O Marcapasso promoverá a instância secundária para a função de instância primária. A antiga instância primária passará para a função secundária após a VM e todos os serviços forem totalmente restaurados após a reinicialização da VM:

<pre><code> nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>



### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Crash a VM que executa a instância de banco de dados primário HADR com "halt"

<pre><code>#Linux kernel panic - halts OS
azibmdb01:~ # echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, o Pacemaker detectará que o nó que está executando a instância primária do banco de dados não está respondendo.

<pre><code>2 nodes configured
5 resources configured

Node azibmdb01: UNCLEAN (online)
Online: [ azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb01
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb01
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb01 ]
     Slaves: [ azibmdb02 ]</code></pre>

O próximo passo é verificar uma situação *cerebral dividida.* Depois que o nó sobrevivente determinou que o nó que executou a última instância do banco de dados primário foi desligado, um failover de recursos é executado.
<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb02 ]
OFFLINE: [ azibmdb01 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Stopped: [ azibmdb01 ] </code></pre>


No caso de uma "parada" do nó, o nó falho deve ser reiniciado através de ferramentas de gerenciamento do Azure (no portal Azure, PowerShell ou no Azure CLI). Depois que o nó falho estiver novamente on-line, ele inicia a instância Db2 na função secundária.

<pre><code>2 nodes configured
5 resources configured

Online: [ azibmdb01 azibmdb02 ]

Full list of resources:

stonith-sbd     (stonith:external/sbd): Started azibmdb02
 Resource Group: g_ip_db2ptr_PTR
     rsc_ip_db2ptr_PTR  (ocf::heartbeat:IPaddr2):       Started azibmdb02
     rsc_nc_db2ptr_PTR  (ocf::heartbeat:azure-lb):      Started azibmdb02
 Master/Slave Set: msl_Db2_db2ptr_PTR [rsc_Db2_db2ptr_PTR]
     Masters: [ azibmdb02 ]
     Slaves: [ azibmdb01 ]</code></pre>

## <a name="next-steps"></a>Próximas etapas
- [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configure o Pacemaker no SUSE Linux Enterprise Server no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)

