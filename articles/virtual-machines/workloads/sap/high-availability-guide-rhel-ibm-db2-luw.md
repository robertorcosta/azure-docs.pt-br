---
title: Configurar o IBM Db2 HADR em máquinas virtuais (VMs) do Azure no RHEL | Microsoft Docs
description: Estabeleça alta disponibilidade do IBM Db2 LUW em VMs (Azure) Virtual Machines (VMs).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.openlocfilehash: c6a230f6abeab45c56aab2db40b8b1defcc06d90
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77598690"
---
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[1612105]:https://launchpad.support.sap.com/#/notes/1612105
[2694118]:https://launchpad.support.sap.com/#/notes/2694118


[db2-hadr-11.1]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[db2-hadr-10.5]:https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.ha.doc/doc/c0011267.html
[dbms-db2]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm

[sap-instfind]:https://help.sap.com/viewer/9e41ead9f54e44c1ae1a1094b0f80712/ALL/en-US/576f5c1808de4d1abecbd6e503c9ba42.html
[rhel-ha-addon]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index
[rhel-ha-admin]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index
[rhel-ha-ref]:https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index
[rhel-azr-supp]:https://access.redhat.com/articles/3131341
[rhel-azr-inst]:https://access.redhat.com/articles/3252491
[rhel-db2-supp]:https://access.redhat.com/articles/3144221
[ascs-ha-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel
[glusterfs]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs
[rhel-pcs-azr]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker
[anf-rhel]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[azr-sap-plancheck]:https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist



# <a name="high-availability-of-ibm-db2-luw-on-azure-vms-on-red-hat-enterprise-linux-server"></a>Alta disponibilidade do IBM Db2 LUW nas VMs do Azure no Red Hat Enterprise Linux Server

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
| [2002167] | Red Hat Enterprise Linux 7.x: instalação e atualização |
| [2694118] | Red Hat Enterprise Linux HA Add-On no Azure |
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
| [Visão geral do complemento de alta disponibilidade para Red Hat Enterprise Linux 7][rhel-ha-addon] |
| [Administração de complemento de alta disponibilidade][rhel-ha-admin] |
| [Referência de complemento de alta disponibilidade][rhel-ha-ref] |
| [Políticas de suporte para clusters de alta disponibilidade do RHEL - máquinas virtuais do Microsoft Azure como membros de cluster][rhel-azr-supp]
| [Instalando e configurando um Cluster de alta disponibilidade do Red Hat Enterprise Linux 7.4 (e posterior) no Microsoft Azure][rhel-azr-inst]
| [Implantação do IBM Db2 Azure Virtual Machines DBMS para carga de trabalho SAP][dbms-db2] |
| [IBM DB2 HADR 11.1][db2-hadr-11.1] |
| [IBM DB2 HADR 10.5][db2-hadr-10.5] |
| [Política de suporte para clusters de alta disponibilidade RHEL - Gerenciamento do IBM Db2 para Linux, Unix e Windows em um cluster][rhel-db2-supp]



## <a name="overview"></a>Visão geral
Para obter alta disponibilidade, o IBM Db2 LUW com HADR está instalado em pelo menos duas máquinas virtuais Azure, que são implantadas em um [conjunto de disponibilidade do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ou em todas as Zonas de Disponibilidade do [Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) 

Os gráficos a seguir exibem uma configuração de duas VMs do servidor de banco de dados Azure. Ambas as VMs do servidor de banco de dados Azure têm seu próprio armazenamento conectado e estão em funcionamento. No HADR, uma instância de banco de dados em uma das VMs do Azure tem o papel da instância primária. Todos os clientes estão conectados à instância primária. Todas as alterações nas transações de banco de dados são persistidas localmente no registro de transações Db2. À medida que os registros de registro de transações são persistidos localmente, os registros são transferidos via TCP/IP para a instância do banco de dados no segundo servidor de banco de dados, no servidor de espera ou na instância de espera. A instância de espera atualiza o banco de dados local, encaminhando os registros de registro de transações transferidos. Desta forma, o servidor de espera é mantido em sincronia com o servidor principal.

HADR é apenas uma funcionalidade de replicação. Não tem detecção de falhas nem instalações automáticas de aquisição ou failover. Uma aquisição ou transferência para o servidor de espera deve ser iniciada manualmente por um administrador de banco de dados. Para obter uma aquisição automática e detecção de falhas, você pode usar o recurso de clusterdo Linux Pacemaker. O Pacemaker monitora as duas instâncias do servidor de banco de dados. Quando a instância do servidor de banco de dados principal falha, o Pacemaker inicia uma aquisição *automática* de HADR pelo servidor de espera. O Pacemaker também garante que o endereço IP virtual seja atribuído ao novo servidor principal.

![Visão geral de alta disponibilidade do IBM Db2](./media/high-availability-guide-rhel-ibm-db2-luw/ha-db2-hadr-lb-rhel.png)

Para que os servidores de aplicativos SAP se conectem ao banco de dados principal, você precisa de um nome de host virtual e um endereço IP virtual. No caso de um failover, os servidores de aplicativos SAP se conectarão à nova instância de banco de dados principal. Em um ambiente Azure, um [balanceador de carga do Azure](https://microsoft.sharepoint.com/teams/WAG/AzureNetworking/Wiki/Load%20Balancing.aspx) é necessário para usar um endereço IP virtual da maneira que for necessário para o HADR do IBM Db2. 

Para ajudá-lo a entender completamente como o IBM Db2 LUW com HADR e Pacemaker se encaixa em uma configuração de sistema SAP altamente disponível, a imagem a seguir apresenta uma visão geral de uma configuração altamente disponível de um sistema SAP baseado no banco de dados IBM Db2. Este artigo abrange apenas o IBM Db2, mas fornece referências a outros artigos sobre como configurar outros componentes de um sistema SAP.

![Visão geral do ambiente completo de alta disponibilidade do IBM DB2](./media/high-availability-guide-rhel-ibm-db2-luw/end-2-end-ha-rhel.png)


### <a name="high-level-overview-of-the-required-steps"></a>Visão geral de alto nível das etapas necessárias
Para implantar uma configuração IBM Db2, você precisa seguir estas etapas:

  + Planeje seu ambiente.
  + Implante as VMs.
  + Atualize o RHEL Linux e configure sistemas de arquivos.
  + Instale e configure o Marcapasso.
  + Cluster [de glusterfs de configuração][glusterfs] ou [arquivos do Azure NetApp][anf-rhel]
  + Instale [ASCS/ERS em um cluster separado][ascs-ha-rhel].
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
| Esgrima azure | O método para evitar situações cerebrais divididas é evitado. |
| Azure Load Balancer | Utilização de básico ou padrão (recomendado), porta de sonda para banco de dados Db2 (nossa recomendação 62500) **porta-sonda**. |
| Resolução de nomes| Como funciona a resolução de nomes no ambiente. O serviço de DNS é altamente recomendado. O arquivo hosts local pode ser usado. |
    
Para obter mais informações sobre o Linux Pacemaker no Azure, consulte [Configurando o Pacemaker no Red Hat Enterprise Linux no Azure][rhel-pcs-azr].

## <a name="deployment-on-red-hat-enterprise-linux"></a>Implantação no Red Hat Enterprise Linux

O agente de recursos do IBM Db2 LUW está incluído no Red Hat Enterprise Linux Server HA Addon. Para a configuração descrita neste documento, você deve usar o Red Hat Enterprise Linux para SAP. O Azure Marketplace contém uma imagem para Red Hat Enterprise Linux 7.4 para SAP ou superior que você pode usar para implantar novas máquinas virtuais Azure. Esteja ciente dos vários modelos de suporte ou serviço que são oferecidos pela Red Hat através do Azure Marketplace quando você escolher uma imagem VM no Azure VM Marketplace.

### <a name="hosts-dns-updates"></a>Hosts: Atualizações de DNS
Faça uma lista de todos os nomes de host, incluindo nomes de host virtuais, e atualize seus servidores DNS para habilitar o endereço IP adequado para a resolução do nome do host. Se um servidor DNS não existir ou você não puder atualizar e criar entradas DNS, você precisa usar os arquivos host locais das VMs individuais que estão participando deste cenário. Se você estiver usando entradas de arquivos host, certifique-se de que as entradas sejam aplicadas a todas as VMs no ambiente do sistema SAP. No entanto, recomendamos que você use seu DNS que, idealmente, se estende para o Azure


### <a name="manual-deployment"></a>Implantação manual

Certifique-se de que o sistema operacional selecionado é suportado pela IBM/SAP para IBM Db2 LUW. A lista de versões suportadas do SO para versões Azure VMs e Db2 está disponível na nota SAP [1928533]. A lista de lançamentos do SO por versão db2 individual está disponível na Matriz de Disponibilidade de Produtos SAP. Recomendamos um mínimo de Red Hat Enterprise Linux 7.4 para SAP devido a melhorias de desempenho relacionadas ao Azure nesta ou mais tarde versões do Red Hat Enterprise Linux.

1. Crie ou selecione um grupo de recursos.
1. Crie ou selecione uma rede e uma sub-rede virtuais.
1. Crie um conjunto de disponibilidade do Azure ou implante uma zona de disponibilidade.
    + Para o conjunto de disponibilidade, defina os domínios de atualização máxima para 2.
1. Criar máquina virtual 1.
    + Use o Red Hat Enterprise Linux para imagem SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure criado na etapa 3 ou selecione Zona de disponibilidade.
1.  Criar máquina virtual 2.
    + Use o Red Hat Enterprise Linux para imagem SAP no Azure Marketplace.
    + Selecione o conjunto de disponibilidade do Azure criado na etapa 3 ou selecione Zona de disponibilidade (não a mesma região da etapa 3).
1. Adicione discos de dados às VMs e, em seguida, verifique a recomendação de uma configuração do sistema de arquivos no artigo [IBM Db2 Azure Virtual Machines DBMS implantação para carga de trabalho SAP][dbms-db2].

## <a name="create-the-pacemaker-cluster"></a>Criar o cluster do Pacemaker
    
Para criar um cluster pacemaker básico para este servidor IBM Db2, consulte [Configurando o Pacemaker no Red Hat Enterprise Linux no Azure][rhel-pcs-azr]. 

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

#### <a name="red-hat-firewall-rules"></a>Regras de firewall do Red Hat
Red Hat Enterprise Linux tem firewall ativado por padrão. 

<pre><code>#Allow access to SWPM tool. Rule is not permanent.
sudo firewall-cmd --add-port=4237/tcp</code></pre>

### <a name="installation-hints-for-setting-up-ibm-db2-luw-with-hadr"></a>Dicas de instalação para configurar o IBM Db2 LUW com HADR

Para configurar a principal instância de banco de dados IBM Db2 LUW:

- Use a opção de alta disponibilidade ou distribuída.
- Instale a instância SAP ASCS/ERS e Banco de dados.
- Faça um backup do banco de dados recém-instalado.

> [!IMPORTANT] 
> Anote a "porta de comunicação do banco de dados" definida durante a instalação. Deve ser o mesmo número de porta para ambas as instâncias do banco de dados.
>![Definição da porta SAP SWPM](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-swpm-db2-port.png)

### <a name="ibm-db2-hadr-settings-for-azure"></a>Configurações do IBM Db2 HADR para Azure

   Quando você usar um agente de esgrima Azure Pacemaker, defina os seguintes parâmetros:

   - Duração da janela do peer HADR (segundos) (HADR_PEER_WINDOW) = 240  
   - Valor do tempo hadr (HADR_TIMEOUT) = 45

Recomendamos os parâmetros anteriores com base nos testes iniciais de failover/aquisição. É obrigatório que você teste para a funcionalidade adequada de failover e aquisição com essas configurações de parâmetro. Como as configurações individuais podem variar, os parâmetros podem exigir ajuste. 

> [!NOTE]
> Específico para o IBM Db2 com configuração HADR com inicialização normal: A instância secundária ou de banco de dados em standby deve estar em funcionamento antes de iniciar a instância primária do banco de dados.

   
> [!NOTE]
> Para instalação e configuração específicas do Azure e pacemaker: Durante o procedimento de instalação através do SAP Software Provisioning Manager, há uma pergunta explícita sobre a alta disponibilidade para IBM Db2 LUW:
>+ Não selecione **o IBM Db2 pureScale**.
>+ Não selecione **Instalar a automação do sistema IBM Tivoli para multiplataformas**.
>+ Não selecione **Gerar arquivos de configuração de cluster**.
>![Opções SAP SWPM - DB2 HA](./media/high-availability-guide-rhel-ibm-db2-luw/swpm-db2ha-opt.png)


Para configurar o servidor de banco de dados standby usando o procedimento de cópia homogênea do sistema SAP, execute estas etapas:

1. Selecione a opção **de cópia** do sistema > instância de banco de dados distribuído de **sistemas** > **de** > **destino**.
1. Como método de cópia, **selecione Sistema Homogêneo** para que você possa usar backup para restaurar um backup na instância do servidor de espera.
1. Quando você chegar à etapa de saída para restaurar o banco de dados para cópia homogênea do sistema, saia do instalador. Restaurar o banco de dados a partir de um backup do host principal. Todas as fases de instalação subseqüentes já foram executadas no servidor de banco de dados principal.

#### <a name="red-hat-firewall-rules-for-db2-hadr"></a>Regras de firewall red hat para DB2 HADR
Adicionar regras de firewall para permitir que o tráfego para DB2 e entre DB2 para HADR funcionar:
+ Porta de comunicação do banco de dados. Se estiver usando partições, adicione essas portas também.
+ Porta HADR (valor do parâmetro DB2 HADR_LOCAL_SVC)
+ Porta de sonda Azure
<pre><code>sudo firewall-cmd --add-port=&lt;port&gt;/tcp --permanent
sudo firewall-cmd --reload</code></pre>

#### <a name="ibm-db2-hadr-check"></a>Verificação DE HADR IBM Db2
Para fins de demonstração e os procedimentos descritos neste artigo, o SID do banco de dados é **o ID2**.

Depois de configurar o HADR e o status for PEER e CONNECTED nos nós primário e de espera, execute a seguinte verificação:

<pre><code>
Execute command as db2&lt;sid&gt; db2pd -hadr -db &lt;SID&gt;

#Primary output:
Database Member 0 -- Database ID2 -- Active -- Up 1 days 15:45:23 -- Date 2019-06-25-10.55.25.349375

                            <b>HADR_ROLE = PRIMARY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 1
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.076494 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 5
                   HEARTBEAT_EXPECTED = 52
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 5
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 369280
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 132242668
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 300
                      PEER_WINDOW_END = 06/25/2019 11:12:03.000000 (1561461123)
             READS_ON_STANDBY_ENABLED = N



#Secondary output:
Database Member 0 -- Database ID2 -- Standby -- Up 1 days 15:45:18 -- Date 2019-06-25-10.56.19.820474

                            <b>HADR_ROLE = STANDBY
                          REPLAY_TYPE = PHYSICAL
                        HADR_SYNCMODE = NEARSYNC
                           STANDBY_ID = 0
                        LOG_STREAM_ID = 0
                           HADR_STATE = PEER
                           HADR_FLAGS =
                  PRIMARY_MEMBER_HOST = az-idb01
                     PRIMARY_INSTANCE = db2id2
                       PRIMARY_MEMBER = 0
                  STANDBY_MEMBER_HOST = az-idb02
                     STANDBY_INSTANCE = db2id2
                       STANDBY_MEMBER = 0
                  HADR_CONNECT_STATUS = CONNECTED</b>
             HADR_CONNECT_STATUS_TIME = 06/25/2019 10:55:05.078116 (1561460105)
          HEARTBEAT_INTERVAL(seconds) = 7
                     HEARTBEAT_MISSED = 0
                   HEARTBEAT_EXPECTED = 10
                HADR_TIMEOUT(seconds) = 30
        TIME_SINCE_LAST_RECV(seconds) = 1
             PEER_WAIT_LIMIT(seconds) = 0
           LOG_HADR_WAIT_CUR(seconds) = 0.000
    LOG_HADR_WAIT_RECENT_AVG(seconds) = 598.000027
   LOG_HADR_WAIT_ACCUMULATED(seconds) = 598.000
                  LOG_HADR_WAIT_COUNT = 1
SOCK_SEND_BUF_REQUESTED,ACTUAL(bytes) = 0, 46080
SOCK_RECV_BUF_REQUESTED,ACTUAL(bytes) = 0, 367360
            PRIMARY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
            STANDBY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
                  HADR_LOG_GAP(bytes) = 0
     STANDBY_REPLAY_LOG_FILE,PAGE,POS = S0000012.LOG, 14151, 3685322855
       STANDBY_RECV_REPLAY_GAP(bytes) = 0
                     PRIMARY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
                     STANDBY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
              STANDBY_REPLAY_LOG_TIME = 06/25/2019 10:45:42.000000 (1561459542)
         STANDBY_RECV_BUF_SIZE(pages) = 2048
             STANDBY_RECV_BUF_PERCENT = 0
           STANDBY_SPOOL_LIMIT(pages) = 1000
                STANDBY_SPOOL_PERCENT = 0
                   STANDBY_ERROR_TIME = NULL
                 PEER_WINDOW(seconds) = 1000
                      PEER_WINDOW_END = 06/25/2019 11:12:59.000000 (1561461179)
             READS_ON_STANDBY_ENABLED = N

</code></pre>



## <a name="db2-pacemaker-configuration"></a>Configuração do Marcapasso DB2

Quando você usa pacemaker para failover automático no caso de uma falha no nó, você precisa configurar suas instâncias db2 e Pacemaker de acordo. Esta seção descreve este tipo de configuração.

Os seguintes itens são prefixados com:

- **[A]**: Aplicável a todos os nós
- **[1]**: Aplicável apenas ao nó 1 
- **[2]**: Aplicável apenas ao nó 2

**[A]** Pré-requisito para configuração do Marcapasso:
1. Desligou ambos os servidores\<de banco de dados com o> de> do usuário db2 com db2stop.
1. Alterar o ambiente shell\<para db2 sid> usuário para */bin/ksh*:
<pre><code># Install korn shell:
sudo yum install ksh
# Change users shell:
sudo usermod -s /bin/ksh db2&lt;sid&gt;</code></pre>
   

### <a name="pacemaker-configuration"></a>Configuração do marcapasso

**[1] Configuração** do Marcapasso específico do IBM Db2 HADR:
<pre><code># Put Pacemaker into maintenance mode
sudo pcs property set maintenance-mode=true 
</code></pre>

**[1]** Crie recursos ibm Db2:
<pre><code># Replace <b>bold strings</b> with your instance name db2sid, database SID, and virtual IP address/Azure Load Balancer.
sudo pcs resource create Db2_HADR_<b>ID2</b> db2 instance='<b>db2id2</b>' dblist='<b>ID2</b>' master meta notify=true resource-stickiness=5000

#Configure resource stickiness and correct cluster notifications for master resoruce
sudo pcs resource update Db2_HADR_<b>ID2</b>-master meta notify=true resource-stickiness=5000

# Configure virtual IP - same as Azure Load Balancer IP
sudo pcs resource create vip_<b>db2id2</b>_<b>ID2</b> IPaddr2 ip='<b>10.100.0.40</b>'

# Configure probe port for Azure load Balancer
sudo pcs resource create nc_<b>db2id2</b>_<b>ID2</b> azure-lb port=<b>62500</b>

#Create a group for ip and Azure loadbalancer probe port
sudo pcs resource group add g_ipnc_<b>db2id2</b>_<b>ID2</b> vip_<b>db2id2</b>_<b>ID2</b> nc_<b>db2id2</b>_<b>ID2</b>

#Create colocation constrain - keep Db2 HADR Master and Group on same node
sudo pcs constraint colocation add g_ipnc_<b>db2id2</b>_<b>ID2</b> with master Db2_HADR_<b>ID2</b>-master

#Create start order constrain
sudo pcs constraint order promote Db2_HADR_<b>ID2</b>-master then g_ipnc_<b>db2id2</b>_<b>ID2</b>
</code></pre>

**[1]** Inicie os recursos do IBM Db2:
* Coloque o Pacemaker fora do modo de manutenção.
<pre><code># Put Pacemaker out of maintenance-mode - that start IBM Db2
sudo pcs property set maintenance-mode=false</pre></code>

**[1]** Certifique-se de que o status do cluster está OK e que todos os recursos foram iniciados. Não importa em que nó os recursos estão funcionando.
<pre><code>sudo pcs status</code>
2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Lista completa de recursos:

 rsc_st_azure (stonith:fence_azure_arm): Iniciado az-idb01 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2] Masters: [ az-idb01 ] Escravos: [ az-idb02 ] Grupo de recursos: g_ipnc_db2id2_ID2 vip_db2id2_ID2 (ocf::heartbeat:IPaddr2): Começou az-idb01 nc_db2id2_ID2 (ocf::heartbeat:azure-lb): Started az-idb

Status do Daemon: corosync: marcapasso ativo/desativado: pcsd ativo/desativado: ativo/ativado
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

**[A]** Adicionar regra de firewall para porta de teste:
<pre><code>sudo firewall-cmd --add-port=<b><probe-port></b>/tcp --permanent
sudo firewall-cmd --reload</code></pre>

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
 
1. Faça login no servidor de aplicação principal da instância J2EE e execute:
    
    <pre><code>sudo /usr/sap/*SID*/*Instance*/j2ee/configtool/configtool.sh</code></pre>  
    
1. No quadro esquerdo, escolha a **loja de segurança**.
1. No quadro direito, escolha `jdbc/pool/\<SAPSID>/url`a chave .
1. Altere o nome do host na URL JDBC para o nome do host virtual.
    
    <pre><code>jdbc:db2://db-virt-hostname:5912/TSP:deferPrepares=0</code></pre>  
    
1. Selecione **Adicionar**.
1. Para salvar suas alterações, selecione o ícone de disco no canto superior esquerdo.
1. Feche a ferramenta de configuração.
1. Reinicie a instância Java.

## <a name="configure-log-archiving-for-hadr-setup"></a>Configurar o arquivamento de log para configuração HADR
Para configurar o arquivamento de log db2 para configuração HADR, recomendamos que você configure o banco de dados principal e o banco de espera para ter capacidade automática de recuperação de log de todos os locais de arquivamento de log. Tanto o banco de dados principal quanto o de espera devem ser capazes de recuperar arquivos de log de todos os locais de arquivo de log para os quais qualquer uma das instâncias do banco de dados pode arquivar arquivos de log. 

O arquivamento de log é realizado apenas pelo banco de dados principal. Se você alterar as funções HADR dos servidores de banco de dados ou se ocorrer uma falha, o novo banco de dados principal será responsável pelo arquivamento de log. Se você configurar vários locais de arquivo de log, seus logs podem ser arquivados duas vezes. No caso de um catch-up local ou remoto, você também pode ter que copiar manualmente os logs arquivados do servidor principal antigo para a localização ativa do log do novo servidor principal.

Recomendamos a configuração de um compartilhamento comum de NFS ou GlusterFS, onde os logs são escritos a partir de ambos os nós. A parte NFS ou GlusterFS tem que estar altamente disponível. 

Você pode usar ações NFS altamente disponíveis ou GlusterFS para transportes ou um diretório de perfil. Para obter mais informações, consulte:

- [GlusterFS em VMs do Azure no Red Hat Enterprise Linux para SAP NetWeaver][glusterfs] 
- [Alta disponibilidade para SAP NetWeaver em VMs Azure no Red Hat Enterprise Linux com Arquivos Azure NetApp para aplicativos SAP][anf-rhel]
- [Arquivos Do Azure NetApp](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction) (para criar compartilhamentos NFS)

## <a name="test-the-cluster-setup"></a>Testar a configuração do cluster

Esta seção descreve como você pode testar sua configuração DB2 HADR. Todos os testes supõem que o IBM Db2 primário está sendo executado na máquina virtual *az-idb01.* O usuário com privilégios sudo ou raiz (não recomendado) deve ser usado.

O status inicial para todos os casos de teste é explicado aqui: (crm_mon -r ou pcs status)

- **o status dos pcs** é um instantâneo do status pacemaker no momento da execução 
- **crm_mon -r** é a saída contínua do status Pacemaker

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled
</code></pre>

O status original em um sistema SAP está documentado no Transaction DBACOCKPIT > Configuration > Overview, conforme mostrado na imagem a seguir:

![DBACockpit - Pré-Migração](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-org-rhel.png)




### <a name="test-takeover-of-ibm-db2"></a>Teste de aquisição do IBM Db2


> [!IMPORTANT] 
> Antes de começar o teste, certifique-se de que:
> * O Pacemaker não tem nenhuma ação fracassada (status de pcs).
> * Não há restrições de localização (sobras do teste de migração)
> * A sincronização IBM Db2 HADR está funcionando. Verifique com o\<usuário db2 sid> <pre><code>db2pd -hadr -db \<DBSID></code></pre>


Migre o nó que está executando o banco de dados Db2 principal executando o seguinte comando:
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master</code></pre>

Depois que a migração é feita, a saída de status do crm parece:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

</code></pre>

O status original em um sistema SAP está documentado no Transaction DBACOCKPIT > Configuration > Overview, conforme mostrado na imagem a seguir:

![DBACockpit - Pós-Migração](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-post-rhel.png)

A migração de recursos com "movimentação de recursos de pcs" cria restrições de localização. As restrições de localização neste caso estão impedindo a execução da instância IBM Db2 no az-idb01. Se as restrições de localização não forem excluídas, o recurso não poderá ser reparado.

Remova o restrição de localização e o nó de espera será iniciado no az-idb01.
<pre><code>sudo pcs resource clear Db2_HADR_<b>ID2</b>-master</code></pre>
E o status do cluster muda para:
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb01
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

![DBACockpit - Restrição de localização removida](./media/high-availability-guide-rhel-ibm-db2-luw/hadr-sap-mgr-clear-rhel.png)


Migre o recurso de volta para *az-idb01* e limpe as restrições de localização
<pre><code>sudo pcs resource move Db2_HADR_<b>ID2</b>-master az-idb01
sudo pcs resource clear Db2_HADR_<b>ID2</b>-master
</code></pre>

- **pzs \<movimentação <host>de recursos res_name>:** Cria restrições de localização e pode causar problemas com a aquisição
- **recursos pcs \<claro res_name>**: Limpa restrições de localização
- **limpeza \<de recursos pcs res_name>**: Limpa todos os erros do recurso

### <a name="test-a-manual-takeover"></a>Teste uma aquisição manual

Você pode testar uma aquisição manual parando o serviço Pacemaker no nó *az-idb01:*
<pre><code>systemctl stop pacemaker</code></pre>

status em *az-ibdb02*
<pre><code>2 nodes configured
5 resources configured

Node az-idb01: pending
Online: [ az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Daemon Status:
  corosync: active/disabled
  pacemaker: active/disabled
  pcsd: active/enabled</code></pre>

Após o failover, você pode iniciar o serviço novamente no *az-idb01*.
<pre><code>systemctl start  pacemaker</code></pre>


### <a name="kill-the-db2-process-on-the-node-that-runs-the-hadr-primary-database"></a>Mate o processo Db2 no nó que executa o banco de dados primário HADR

<pre><code>#Kill main db2 process - db2sysc
[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2ptr    34598  34596  8 14:21 ?        00:00:07 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 34598
</code></pre>

A instância db2 vai falhar, e pacemaker moverá nó mestre e reportará o seguinte status:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=49, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 09:57:35 2019', queued=0ms, exec=362ms</code></pre>

O Pacemaker reiniciará a instância de banco de dados principal do Db2 no mesmo nó, ou falhará no nó que está executando a instância de banco de dados secundário e um erro é relatado.

### <a name="kill-the-db2-process-on-the-node-that-runs-the-secondary-database-instance"></a>Mate o processo Db2 no nó que executa a instância de banco de dados secundário

<pre><code>[sapadmin@az-idb02 ~]$ sudo ps -ef|grep db2sysc
db2id2    23144  23142  2 09:53 ?        00:00:13 db2sysc 0
[sapadmin@az-idb02 ~]$ sudo kill -9 23144
</code></pre>

O nó entra em falha declarada e erro relatado
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01

Failed Actions:
* Db2_HADR_ID2_monitor_20000 on az-idb02 'not running' (7): call=144, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 10:02:09 2019', queued=0ms, exec=0ms</code></pre>

A instância Db2 é reiniciada na função secundária que havia atribuído anteriormente.

### <a name="stop-db-via-db2stop-force-on-the-node-that-runs-the-hadr-primary-database-instance"></a>Parar DB via força db2stop no nó que executa a instância de banco de dados primário HADR

À medida\<que o usuário db2 sid> executar comando db2stop force:
<pre><code>az-idb01:db2ptr> db2stop force</code></pre>

Falha detectada:

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Slaves: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Stopped
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Stopped

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</code></pre>

A instância de banco de dados secundário DB2 HADR foi promovida para a função principal.
<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

 rsc_st_azure   (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02

Failed Actions:
* Db2_HADR_ID2_demote_0 on az-idb01 'unknown error' (1): call=110, status=complete, exitreason='none',
    last-rc-change='Wed Jun 26 14:03:12 2019', queued=0ms, exec=355ms</pre></code>


### <a name="crash-the-vm-that-runs-the-hadr-primary-database-instance-with-halt"></a>Crash a VM que executa a instância de banco de dados primário HADR com "halt"

<pre><code>#Linux kernel panic. 
sudo echo b > /proc/sysrq-trigger</code></pre>

Nesse caso, o Pacemaker detectará que o nó que está executando a instância primária do banco de dados não está respondendo.

<pre><code>2 nodes configured
5 resources configured

Node az-idb01: UNCLEAN (online)
Online: [ az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb01 ]
     Slaves: [ az-idb02 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb01
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb01</code></pre>

O próximo passo é verificar uma situação *cerebral dividida.* Depois que o nó sobrevivente determinou que o nó que executou a última instância do banco de dados primário foi desligado, um failover de recursos é executado.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb02 ]
OFFLINE: [ az-idb01 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Stopped: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02 </code></pre>


No caso de um pânico no kernel, o nó falho será restared por agente de esgrima. Depois que o nó falho estiver de volta on-line, você deve iniciar o cluster do marca-passo por
<pre><code>sudo pcs cluster start</code></pre> ele inicia a instância Db2 na função secundária.

<pre><code>2 nodes configured
5 resources configured

Online: [ az-idb01 az-idb02 ]

Full list of resources:

rsc_st_azure    (stonith:fence_azure_arm):      Started az-idb02
 Master/Slave Set: Db2_HADR_ID2-master [Db2_HADR_ID2]
     Masters: [ az-idb02 ]
     Slaves: [ az-idb01 ]
 Resource Group: g_ipnc_db2id2_ID2
     vip_db2id2_ID2     (ocf::heartbeat:IPaddr2):       Started az-idb02
     nc_db2id2_ID2      (ocf::heartbeat:azure-lb):      Started az-idb02</code></pre>

## <a name="next-steps"></a>Próximas etapas
- [Arquitetura de alta disponibilidade e cenários para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Configurando o Pacemaker no Red Hat Enterprise Linux no Azure][rhel-pcs-azr]
