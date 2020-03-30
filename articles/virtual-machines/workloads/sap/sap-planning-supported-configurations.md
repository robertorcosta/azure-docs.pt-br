---
title: 'SAP no Azure: Cenários suportados com VMs azure'
description: Azure Virtual Machines suporta cenários com carga de trabalho SAP
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 564c648a550b41017ffc684ca19ff03612fc63d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137621"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Carga de trabalho SAP em cenários suportados por máquina virtual do Azure
Projetar a arquitetura de sistemas `Hybris` SAP NetWeaver, Business one ou S/4HANA no Azure abre muitas oportunidades diferentes para várias arquiteturas e ferramentas usarem para chegar a uma implantação escalável, eficiente e altamente disponível. Embora dependa do sistema operacional ou DBMS utilizado, existem restrições. Além disso, nem todos os cenários que são suportados no local são suportados da mesma forma no Azure. Este documento liderará através das configurações de não alta disponibilidade suportadas e configurações e arquiteturas de alta disponibilidade usando exclusivamente As VMs do Azure. Para cenários suportados com [HANA Large Instances,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)verifique os [cenários suportados do](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)artigo para HANA Large Instances . 


## <a name="2-tier-configuration"></a>Configuração de 2 níveis
Uma configuração SAP 2-Tier é considerada construída a partir de uma camada combinada da camada SAP DBMS e da camada de aplicativo que são executadas no mesmo servidor ou unidade VM. O segundo nível é considerado a camada de interface do usuário. No caso de uma configuração de 2 níveis, a camada de aplicativo DBMS e SAP compartilham os recursos da VM do Azure. Como resultado, você precisa configurar os diferentes componentes de uma forma que aqueles não competem por recursos. Você também precisa ter cuidado para não subscrever demais os recursos da VM. Tal configuração não oferece nenhuma alta disponibilidade, além dos contratos de Nível de Serviço do [Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes do Azure envolvidos.

Uma representação gráfica de tal configuração pode parecer:

![Configuração simples de 2 níveis](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Essas configurações são suportadas com Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS do SQL Server, Oracle, Db2, maxDB e SAP ASE para casos de produção e não-produção. Para o SAP HANA como DBMS, esse tipo de configurações é suportada apenas para casos de não-produção. Isso inclui o caso de implantação do [Azure HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) também.
Para todas as combinações de OS/DBMS suportadas no Azure, esse tipo de configuração é suportada. No entanto, é obrigatório que você defina a configuração dos componentes DBMS e SAP de forma que os componentes DBMS e SAP não competem pelos recursos de memória e CPU e, assim, excedam os recursos físicos disponíveis. Isso precisa ser feito restringindo a memória que o DBMS pode alocar. Você também precisa limitar a memória estendida SAP nas instâncias do aplicativo. Você também precisa monitorar o consumo de CPU da VM em geral para ter certeza de que os componentes não estão maximizando os recursos da CPU. 

> [!NOTE]
> Para sistemas SAP de produção, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, conforme descrito posteriormente neste documento


## <a name="3-tier-configuration"></a>Configuração de 3 níveis
Nessas configurações, você separa a camada de aplicativo SAP e a camada DBMS em diferentes VMs. Você geralmente faz isso para sistemas maiores e por razões de ser mais flexível nos recursos da camada de aplicação SAP. Na configuração mais simples, não há alta disponibilidade além dos contratos de Nível de Serviço do [Azure](https://azure.microsoft.com/support/legal/sla/) dos diferentes componentes do Azure envolvidos. 

A representação gráfica parece:

![Configuração simples de 2 níveis](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Esse tipo de configuração é suportado no Windows, Red Hat, SUSE e Oracle Linux para os sistemas DBMS do SQL Server, Oracle, Db2, SAP HANA, maxDB e SAP ASE para casos de produção e não-produção. Esta é a configuração de implantação padrão para [Instâncias Grandes do Azure HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture). Para simplificação, não distinguimos entre as instâncias de diálogo SAP Central Services e SAP na camada de aplicativo SAP. Nesta configuração simples de 3 níveis, não haveria proteção de alta disponibilidade para serviços centrais SAP.

> [!NOTE]
> Para sistemas SAP de produção, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, conforme descrito posteriormente neste documento


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Várias instâncias DBMS por unidade vm ou hana de grande instância
Neste tipo de configuração, você hospeda várias instâncias DBMS por unidade Azure VM ou HANA Large Instance. A motivação pode ser ter menos sistemas operacionais para manter e com isso custos reduzidos. Outras motivações podem ser ter mais flexibilidade e mais eficiência compartilhando recursos de uma unidade maior de VM ou HANA Large Instance entre várias instâncias de DBMS. Até agora, essas configurações estavam aparecendo principalmente para sistemas não-produtivos.

Uma configuração como essa poderia parecer:

![Várias instâncias de DBMS em uma unidade](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Este tipo de implantação do DBMS é suportado para:

- SQL Server no Windows
- IBM Db2. Encontrar detalhes no artigo [Várias instâncias (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Para a Oracle. Para obter detalhes, consulte [nota de suporte SAP #1778431](https://launchpad.support.sap.com/#/notes/1778431) e notas SAP relacionadas
- Para o SAP HANA, várias instâncias em um VM, o SAP chama esse método de implantação mcos, é suportado. Para obter detalhes, consulte o artigo SAP [Multiple SAP HANA Systems on One Host (MCOS)];https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Executando várias instâncias de banco de dados em um host, você precisa ter certeza de que as diferentes instâncias não estão competindo por recursos e, assim, exceder os limites de recursos físicos da VM. Isso é especialmente verdadeiro para a memória onde você precisa tampar a memória qualquer um dos casos que compartilham a VM pode alocar. Isso também pode ser verdade para os recursos da CPU que as diferentes instâncias de banco de dados podem aproveitar. Todos os DBMS mencionados têm configurações que permitem limitar a alocação de memória e os recursos da CPU em um nível de instância.
Para ter suporte para tal configuração para VMs do Azure, espera-se que os discos ou volumes usados para os arquivos de registro/redo log dos bancos de dados gerenciados pelas diferentes instâncias sejam separados. Ou em outras palavras, dados ou arquivos de log/redo log de bancos de dados gerenciados por diferentes instâncias DBMS não devem compartilhar os mesmos discos ou volumes. 

A configuração de disco para HANA Large Instances é fornecida configurada e é detalhada em [cenários suportados para HANA Large Instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos). 

> [!NOTE]
> Para sistemas SAP de produção, recomendamos configurações adicionais de alta disponibilidade e eventual recuperação de desastres, conforme descrito posteriormente neste documento. As VMs com várias instâncias DBMS não são suportadas com as configurações de alta disponibilidade descritas posteriormente neste documento.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Várias instâncias de diálogo SAP em uma VM
Em muitos casos, várias instâncias de diálogo foram implantadas em servidores de metal nu ou mesmo em VMs em execução em nuvens privadas. A razão para tais configurações foi adaptar certas instâncias de diálogo SAP a determinadas cargas de trabalho, funcionalidades de negócios ou tipos de carga de trabalho. A razão para não isolar essas instâncias em VMs separadas foi o esforço de manutenção e operações do sistema operacional. Ou, em inúmeros casos, os custos no caso do hoster ou operador da VM está pedindo uma taxa mensal por VM operado e administrado. No Azure, um cenário de hospedagem de várias instâncias de diálogo SAP dentro de um único VM nos EUA suportado para fins de produção e não-produção nos sistemas operacionais do Windows, Red Hat, SUSE e Oracle Linux. O parâmetro do kernel SAP PHYS_MEMSIZE, disponível no Windows e nos modernos núcleos Linux, deve ser definido se várias instâncias do SAP Application Server estiverem sendo executados em uma única VM. Também é aconselhável limitar a expansão da Memória Estendida SAP em sistemas operacionais, como o Windows, onde o crescimento automático da memória estendida SAP é implementado. Isso pode ser feito com o `em/max_size_MB`parâmetro de perfil SAP .

Na configuração de 3 níveis, onde várias instâncias de diálogo SAP são executadas dentro das VMs do Azure, pode parecer:

![Várias instâncias de DBMS em uma unidade](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Para simplificação, não distinguimos entre as instâncias de diálogo SAP Central Services e SAP na camada de aplicativo SAP. Nesta configuração simples de 3 níveis, não haveria proteção de alta disponibilidade para serviços centrais SAP. Para sistemas de produção, não é recomendável deixar os Serviços Centrais SAP desprotegidos. Para obter detalhes sobre as chamadas configurações multi-SID em torno das instâncias centrais SAP e da alta disponibilidade dessas configurações multiSID, consulte seções posteriores deste documento.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Proteção de alta disponibilidade para a camada SAP DBMS
À medida que você procura implantar sistemas de produção SAP, você precisa considerar o tipo de configurações de alta disponibilidade em standby. Especialmente com o SAP HANA, onde os dados precisam ser carregados na memória antes de serem capazes de obter o desempenho completo e a escalabilidade de volta, a cura do serviço Azure não é uma medida ideal para alta disponibilidade. 

Em geral, a Microsoft suporta apenas configurações de alta disponibilidade e pacotes de software descritos na seção de carga de trabalho SAP em docs.microsoft.com. Você pode ler a mesma declaração na nota SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). A Microsoft não fornecerá suporte para outras estruturas de software de terceiros de alta disponibilidade que não sejam documentadas pela Microsoft em conjunto com a carga de trabalho SAP. Nesses casos, o fornecedor terceirizado da estrutura de alta disponibilidade é a parte de suporte para a configuração de alta disponibilidade que precisa ser contratada por você como cliente no processo de suporte. Exceções serão mencionadas neste artigo. 

Em geral, a Microsoft suporta um conjunto limitado de configurações de alta disponibilidade em vms azure ou unidades HANA Large Instances. Para os cenários suportados de HANA Large Instances, leia os [cenários suportados pelo documento para hana grandes instâncias](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario).

Para VMs Azure, as seguintes configurações de alta disponibilidade são suportadas no nível DBMS:

- Replicação do sistema SAP HANA baseada no Linux Pacemaker no SUSE e Red Hat. Veja os artigos detalhados:
    - [Alta disponibilidade do SAP HANA nas VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
    - [Alta disponibilidade do SAP HANA em VMs do Azure no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)
- Configurações n+m de escala SAP HANA usando [arquivos Azure NetApp](https://azure.microsoft.com/services/netapp/) no SUSE e Red Hat. Os detalhes estão listados nestes artigos:
    - [Implantar um sistema de scale-out SAP HANA com nó de espera em VMs do Azure usando arquivos Do Azure NetApp no SUSE Linux Enterprise Server}](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
    - [Implantar um sistema de scale-out SAP HANA com nó de espera em VMs azure usando arquivos Azure NetApp no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- SQL Server Failover cluster baseado em Serviços de arquivo de saída de escala do Windows. Embora a recomendação para sistemas de produção seja usar o SQL Server Always On em vez de agrupamento. SQL Server Always On oferece melhor disponibilidade usando armazenamento separado. Os detalhes estão descritos neste artigo: 
    - [Configure uma instância de cluster de failover do SQL Server em máquinas virtuais do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-create-failover-cluster)
- O SQL Server Always On é suportado com o sistema operacional Windows para SQL Server no Azure. Esta é a recomendação padrão para as instâncias do SQL Server de produção no Azure. Os detalhes estão descritos nestes artigos:
    - [Introduzindo grupos de disponibilidade do SQL Server Always On em máquinas virtuais Do Zure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).
    - [Configurar um Grupo de Disponibilidade Always On em máquinas virtuais do Azure em diferentes regiões](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-dr).
    - [Configure um balanceador de carga para um grupo de disponibilidade Always On no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).
- Oracle Data Guard para Windows e Oracle Linux. Detalhes para oracle Linux podem ser encontrados neste artigo:
    - [Implementar o Oracle Data Guard em uma máquina virtual Linux do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
- IBM Db2 HADR no SUSE e RHEL Documentação detalhada para SUSE e RHEL usando Pacemaker é fornecida aqui:
    - [Alta disponibilidade de IBM Db2 LUW em VMs Azure no SUSE Linux Enterprise Server com Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
    - [Alta disponibilidade do IBM Db2 LUW nas VMs do Azure no Red Hat Enterprise Linux Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw)
- Configuração SAP ASE e SAP maxDB conforme detalhado nestes documentos:
    - [Implantação do DBMS de Máquinas Virtuais do SAP ASE Azure para carga de trabalho do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
    - [SAP MaxDB, liveCache e implantação do Servidor de Conteúdo nas VMs do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)
- Os cenários de alta disponibilidade do HANA Large Instances são detalhados em:
    - [Cenários suportados para HANA Large Instances- HSR com STONITH para alta disponibilidade](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability)
    - [Cenários suportados para HANA Large Instances - Failover automático host (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11)

> [!IMPORTANT]
> Para nenhum dos cenários descritos acima, suportamos configurações de várias instâncias DBMS em uma VM. Significa que, em cada um dos casos, apenas uma instância de banco de dados pode ser implantada por VM e protegida com os métodos descritos de alta disponibilidade. Proteger várias instâncias DBMS o mesmo cluster de failover do Windows ou Pacemaker **NÃO** é suportado neste momento. Além disso, o Oracle Data Guard é suportado apenas para uma instância única por casos de implantação de VM. 

Vários sistemas de banco de dados permitem hospedar vários bancos de dados uma instância DBMS. Como no caso do SAP HANA, vários bancos de dados podem ser hospedados em vários contêineres de banco de dados (MDC). Para os casos em que essas configurações de vários bancos de dados estão funcionando dentro de um recurso de cluster failover, essas configurações são suportadas. Configurações que não são suportadas são casos em que vários recursos de cluster seriam necessários. Quanto às configurações em que você definiria vários grupos de disponibilidade do servidor SQL, em uma instância do SQL Server.


![Configuração do DBMS HA](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

Dependendo dos sistemas operacionais DBMS ou operacional, componentes como o balanceador de carga Azure podem ou não ser necessários como parte da arquitetura da solução. 

Especificamente para maxDB, a configuração de armazenamento precisa ser diferente. No maxDB, os arquivos de dados e log precisam estar localizados no armazenamento compartilhado para configurações de alta disponibilidade. Apenas no caso do maxDB, o armazenamento compartilhado é suportado para alta disponibilidade. Para todas as outras pilhas de armazenamento separadas DBMS por nó são as únicas configurações de disco suportadas.

Outros frameworks de alta disponibilidade são conhecidos por existirem e são conhecidos por serem executados no Microsoft Azure também. No entanto, a Microsoft não testou essas estruturas. Se você quiser construir sua configuração de alta disponibilidade com essas frameworks, você precisará trabalhar com o provedor desse software para:

- Desenvolver uma arquitetura de implantação
- Implantação da arquitetura
- Suporte à arquitetura

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de aparelhos macios que fornecem soluções de armazenamento em cima do armazenamento nativo do Azure. Esses aparelhos macios podem ser usados para criar ações NFS, bem como que teoricamente poderiam ser usados nas implantações de escala SAP HANA onde um nó de espera é necessário. Devido a várias razões, nenhum desses aparelhos macios de armazenamento é suportado para qualquer uma das implantações DBMS pela Microsoft e SAP no Azure. As implantações de DBMS em ações smb não são suportadas neste momento. As implantações de DBMS em ações NFS são limitadas a ações NFS 4.1 em [Arquivos Azure NetApp](https://azure.microsoft.com/services/netapp/).


## <a name="high-availability-for-sap-central-service"></a>Alta disponibilidade para serviço central SAP
O SAP Central Services é um segundo ponto único de falha na configuração SAP. Como resultado, você também precisaria proteger esses processos de Serviços Centrais. A oferta suportada e documentada para a carga de trabalho SAP diz:

- Windows Failover Cluster Server usando os Serviços de arquivos de saída de escala do Windows para sapmnt e diretório de transporte global. Os detalhes estão descritos no artigo:
    - [Clustering de uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando o arquivo compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
    - [Preparar a infraestrutura do Azure para alta disponibilidade do SAP usando um cluster de failover do Windows e compartilhamento de arquivos para instâncias ASCS/SCS do SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- Windows Failover Cluster Server usando o compartilhamento De SMB com base em [Arquivos Azure NetApp](https://azure.microsoft.com/services/netapp/) para sapmnt e diretório de transporte global. Os detalhes estão listados no artigo:
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no Windows com Arquivos Azure NetApp (SMB) para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-windows-netapp-files-smb)
- Windows Failover Cluster Server baseado `Datakeeper`no SIOS . Embora documentado pela Microsoft, você precisa de um relacionamento de suporte com o SIOS, para que você possa se envolver com o suporte ao SIOS ao usar essa solução. Os detalhes estão descritos no artigo:
    - [Clusterizar uma instância do SAP ASCS/SCS em um cluster de failover do Windows usando um disco compartilhado de cluster no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - [Prepare a infraestrutura do Azure para alta disponibilidade do SAP usando o cluster de failover do Windows e o disco compartilhado para a instância SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- Marcapasso no sistema operacional SUSE com a criação de um compartilhamento `drdb` nfs altamente disponível usando duas VMs SUSE e para replicação de arquivos. Os detalhes estão documentados no artigo
    - [Alta disponibilidade do SAP NetWeaver em VMs do Azure no SUSE Linux Enterprise Server para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
    - [Alta disponibilidade do NFS em VMs do Azure no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- Sistema operacional Pacemaker SUSE com alavancagem de ações NFS fornecidas pelo [Azure NetApp Files](https://azure.microsoft.com/services/netapp/). Os detalhes estão documentados em
    - [Alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server com Arquivos Azure NetApp para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)
- Marcapasso no sistema operacional Red Hat com `glusterfs` compartilhamento NFS hospedado em um cluster. Detalhes podem ser encontrados nos artigos
    - [Alta disponibilidade de máquinas virtuais do Azure para SAP NetWeaver no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)
    - [`GlusterFS`no Azure VMs no Red Hat Enterprise Linux para SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)
- Marcapasso no sistema operacional Red Hat com compartilhamento NFS hospedado em [Arquivos Azure NetApp](https://azure.microsoft.com/services/netapp/). Os detalhes estão descritos no artigo
    - [Azure Virtual Machines alta disponibilidade para SAP NetWeaver no Red Hat Enterprise Linux com Arquivos Azure NetApp para aplicativos SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)

Das soluções listadas, você precisa de um `Datakeeper` relacionamento de suporte com o SIOS para dar suporte ao produto e para se envolver diretamente com o SIOS em caso de problemas. Dependendo da forma como você licenciou o Windows, Red Hat e/ou SUSE OS, você também pode ser obrigado a ter um contrato de suporte com o seu provedor de SO para ter suporte total das configurações de alta disponibilidade listadas.

A configuração também pode ser exibida como:

![Configuração de HA DBMS e ASCS](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

No lado direito dos gráficos, os serviços centrais SAP altamente disponíveis são mostrados. Além de ter os serviços SAP Central protegidos com uma estrutura de cluster de failover que pode falhar em caso de problema, há a necessidade de um compartilhamento de NFS ou SMB altamente disponível, ou um disco compartilhado do Windows para garantir que o diretório de transporte global e sapmnt seja disponível independente da existência de uma única VM. Algumas das soluções adicionais, como o Windows Failover Cluster Server e pacemaker, exigirão um balanceador de carga do Azure para direcionar ou redirecionar o tráfego para um nó saudável.

Na lista mostrada, não há menção ao sistema operacional Oracle Linux. O Oracle Linux não suporta o Pacemaker como uma estrutura de cluster. Se você deseja implantar seu sistema SAP no Oracle Linux e precisa de uma estrutura de alta disponibilidade para o Oracle Linux, você precisa trabalhar com fornecedores de terceiros. Um dos fornecedores é o SIOS com seu Protection Suite para Linux que é suportado pelo SAP no Azure. Para obter mais informações leia a nota SAP [#1662610 - Detalhes de suporte para o SIOS Protection Suite para Linux para](https://launchpad.support.sap.com/#/notes/1662610) obter mais detalhes.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Armazenamento suportado com os cenários sap central services listados acima
Uma vez que apenas um subconjunto de tipos de armazenamento Azure estão fornecendo ações de NFS ou SMB altamente disponíveis que a qualidade para o uso em nossos cenários de cluster SAP Central Services uma lista de tipos de armazenamento suportados

- O Windows Failover Cluster Server com o Windows Scale-out File Server pode ser implantado em todos os tipos de armazenamento nativos do Azure, exceto arquivos Do Azure NetApp. No entanto, a recomendação é aproveitar o Armazenamento Premium devido a contratos de nível de serviço superiores em throughput e IOPS.
- O Windows Failover Cluster Server com SMB no Azure NetApp Files é suportado em Arquivos Azure NetApp. As ações de SMB nos serviços do Azure File **NÃO** são suportadas neste momento.
- O Windows Failover Cluster Server com o `Datakeeper` disco compartilhado do Windows com base no SIOS pode ser implantado em todos os tipos de armazenamento nativos do Azure, exceto os Arquivos Azure NetApp. No entanto, a recomendação é aproveitar o Armazenamento Premium devido a contratos de nível de serviço superiores em throughput e IOPS.
- O Marcapasso SUSE ou Red Hat usando ações NFS no Azure NetApp Files é suportado em Arquivos Azure NetApp. 
- O SUSE Pacemaker usando uma `drdb` configuração entre duas VMs é suportado usando tipos de armazenamento nativos do Azure, exceto arquivos Azure NetApp. No entanto, a recomendação é aproveitar o Armazenamento Premium devido a contratos de nível de serviço superiores em throughput e IOPS.
- O Red Hat `glusterfs` Pacemaker, usado para fornecer compartilhamento NFS, é suportado usando tipos de armazenamento nativos do Azure, exceto arquivos Azure NetApp. No entanto, a recomendação é aproveitar o Armazenamento Premium devido a contratos de nível de serviço superiores em throughput e IOPS.

> [!IMPORTANT]
> O Microsoft Azure Marketplace oferece uma variedade de aparelhos macios que fornecem soluções de armazenamento em cima do armazenamento nativo do Azure. Esses aparelhos macios podem ser usados para criar ações NFS ou SMB, bem como que teoricamente poderiam ser usados também nos serviços centrais SAP agrupados em failover. Essas soluções não são suportadas diretamente para a carga de trabalho SAP pela Microsoft. Se você decidir usar essa solução para criar seu compartilhamento NFS ou SMB, o suporte para a configuração do Serviço Central SAP precisa ser fornecido pelo terceiro que possui o software no aparelho macio de armazenamento.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Clusters de failover do Multi-SID SAP Central Services
Para reduzir o número de VMs necessários em grandes paisagens SAP, o SAP permite executar instâncias do SAP Central Services de vários sistemas SAP diferentes na configuração de cluster failover. Imagine casos em que você tem 30 ou mais sistemas de produção NetWeaver ou S/4HANA. Sem clustering multi-SID, essas configurações exigiriam 60 ou mais VMs em 30 ou mais configurações de cluster de failover do Windows ou Pacemaker. Além dos clusters de failover DBMS necessários. A implantação de vários serviços centrais SAP em dois nós em uma configuração de cluster failover pode reduzir significativamente o número de VMs. No entanto, a implantação de várias instâncias de serviços SAP Central em uma única configuração de cluster de dois nós também tem algumas desvantagens. Problemas em torno de um único VM na configuração de cluster se aplicam a vários sistemas SAP. A manutenção do sistema operacional convidado em execução na configuração do cluster requer mais coordenação, uma vez que vários sistemas SAP de produção são afetados. Ferramentas como o SAP LaMa não estão suportando clustering multi-SID em seu processo de clonagem de sistema.

No Azure, uma configuração de cluster multi-SID é suportada para o sistema operacional Windows com ENSA1 e ENSA2. A recomendação é não combinar a arquitetura enqueue replication service (ENSA1) mais antiga com a nova arquitetura (ENSA2) em um cluster multi-SID. Detalhes sobre tal arquitetura estão documentados nos artigos

- [Alta disponibilidade de vários SIDs da instância do SAP ASCS/SCS com clustering de failover do Windows Server e disco compartilhado no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk) 
- [Alta disponibilidade de vários SIDs da instância do SAP ASCS com clustering de failover do Windows Server e compartilhamento de arquivos no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share) 

Para o SUSE, também é suportado um cluster multi-SID baseado no Pacemaker. Até agora, a configuração é suportada para:

- Um máximo de cinco instâncias SAP ASCS/SCS
- A antiga arquitetura de gelo do servidor de replicação enue (ENSA1)
- Configurações do cluster Pacemaker de dois nós

A configuração está documentada em [alta disponibilidade para SAP NetWeaver em VMs Azure no SUSE Linux Enterprise Server para aplicativos SAP guia multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)

Um cluster multi-SID com servidor de replicação Enqueue se parece esquematicamente

![Configuração de HA DBMS e ASCS](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Cenários de scale-out SAP HANA
Os cenários de scale-out SAP HANA são suportados para um subconjunto das VMs Azure certificadas hana, conforme listado no [diretório de hardware SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Todas as VMs marcadas com 'Sim' na coluna 'Clustering' podem ser usadas para a escala Ção OLAP ou S/4HANA. As configurações sem espera são suportadas com os tipos de armazenamento do Azure: 

- Armazenamento Premium Do Azure, incluindo acelerador Azure Write para o volume /hana/log
- [Disco Ultra](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-ultra-ssd)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

As configurações de scale-out SAP HANA para OLAP ou S/4HANA com nó de espera são suportadas exclusivamente com NFS compartilhado hospedado no Azure NetApp Files.

Para obter mais informações sobre configurações exatas de armazenamento com ou sem nó de espera, verifique os artigos:

- [Configurações de armazenamento de máquina virtual do SAP HANA no Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) 
- [Implantar um sistema de scale-out SAP HANA com nó de espera em VMs do Azure usando arquivos Do Azure NetApp no SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Implantar um sistema de scale-out SAP HANA com nó de espera em VMs azure usando arquivos Azure NetApp no Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Nota de suporte SAP #2080991](https://launchpad.support.sap.com/#/notes/2080991)

Para obter detalhes sobre as configurações de escala HANA suportadas por HANA, a seguinte documentação se aplica:

- [Cenários suportados para hana large instances scale-out com standby](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby)
- [Cenários suportados para hana large instances scale-out sem espera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Cenário de Recuperação de Desastres
Há uma variedade de cenários de recuperação de desastres que são suportados. Definimos arquiteturas de desastres como arquiteturas que devem compensar uma região completa do Azure saindo da grade. Isso significa que precisamos que o alvo de recuperação de desastres seja uma região diferente do Azure como alvo para executar sua paisagem SAP. Separamos métodos e configurações na camada DBMS e na camada não-DBMS. 

### <a name="dbms-layer"></a>Camada de DBMS
Para a camada DBMS, as configurações que usam os mecanismos de replicação nativa sustais, como Always On, Oracle Data Guard, DB2 HADR, SAP ASE Always-On ou HANA System Replication são suportadas. É obrigatório que o fluxo de replicação nesses casos seja assíncrono, em vez de síncrono como em cenários típicos de alta disponibilidade que são implantados dentro de uma única região Azure. Um exemplo típico de tal configuração de recuperação de desastres DBMS suportada é descrito no artigo [SAP HANA disponibilidade em todas as regiões do Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions#combine-availability-within-one-region-and-across-regions). O segundo gráfico nessa seção descreve um cenário com HANA como exemplo. As principais bases de dados suportadas para aplicativos SAP são todas capazes de ser implantadas em tal cenário.

Ele é suportado para usar uma VM menor como instância-alvo na região de recuperação de desastres, uma vez que a VM não experimenta o tráfego completo de carga de trabalho. Ao fazer isso, você precisa ter em mente as seguintes considerações:

- Tipos menores de VM não permitem que muitos discos conectados do que VMs menores
- VMs menores têm menos throughput de rede e armazenamento
- O redimensionamento entre as famílias de VM pode ser um problema quando as VMs diferentes são coletadas em um conjunto de disponibilidade do Azure ou quando o redimensionamento deve acontecer entre a família M-Series e a família Mv2 de VMs
- O consumo de CPU e memória para a instância do banco de dados pode receber o fluxo de alterações com atraso mínimo e recursos suficientes de CPU e memória para aplicar essas alterações com o mínimo de atraso nos dados  

Mais detalhes sobre limitações de diferentes tamanhos de VM podem ser encontrados [aqui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) 

Outro método suportado para implantar um alvo DR é ter uma segunda instância DBMS instalada em uma VM que executa uma instância DBMS não-produção de uma instância SAP não-produção. Isso pode ser um pouco mais desafiador, já que você precisa descobrir o que na memória, recursos da CPU, largura de banda da rede e largura de banda de armazenamento é necessário para as instâncias de destino específicas que devem funcionar como instância principal no cenário DR. Especialmente no HANA, é altamente recomendável que você esteja configurando a instância que funciona como destino dr em um host compartilhado para que os dados não sejam pré-carregados na instância de destino do DR.

Para cenários DE DR de instância grande HANA, verifique esses documentos:

- [Nó único com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication)
- [Nó único com DR (multiuso) usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Nó único com DR (multiuso) usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication)
- [Alta disponibilidade com HSR e DR com replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication)
- [Scale-out com DR usando replicação de armazenamento](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication)
- [Nó único com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr)
- [HSR de nó único para DR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized)
- [Alta disponibilidade e recuperação de desastres com HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr)
- [Alta disponibilidade e recuperação de desastres com HSR (custo otimizado)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Scale-out com DR usando HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr)

> [!NOTE]
> O uso do [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) não foi testado para implantações DeDBMS carga de trabalho SAP. Como resultado, ele não é suportado para a camada DBMS de sistemas SAP neste momento. Outros métodos de replicação da Microsoft e sap que não estão listados não são suportados. O uso de software de terceiros para replicar a camada DBMS de sistemas SAP entre diferentes regiões azure, precisa ser suportado pelo fornecedor do software e não será suportado através de canais de suporte Microsoft e SAP. 
 
## <a name="non-dbms-layer"></a>Camada não-DBMS
Para a camada de aplicativos SAP e eventuais compartilhamentos ou locais de armazenamento necessários, os dois principais cenários são aproveitados pelos clientes:

- As metas de recuperação de desastres na segunda região do Azure não estão sendo usadas para qualquer finalidade de produção ou não de produção. Neste cenário, as VMs que funcionam como meta de recuperação de desastres não são idealmente implantadas e a imagem e as alterações nas imagens da camada de aplicação SAP de produção são replicadas na região de recuperação de desastres. Uma funcionalidade que pode executar tal tarefa é [a Recuperação do Site do Azure](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-move-overview). O Azure Site Recovery suporta um cenário de replicação Azure-to-Azure como este. 
- As metas de recuperação de desastres são VMs que estão realmente em uso por sistemas não-produtivos. Toda a paisagem SAP está espalhada por duas regiões Azure diferentes com sistemas de produção geralmente em uma região e sistemas não-produtivos em outra região. Em muitas implantações de clientes, o cliente tem um sistema de não-produção que equivale a um sistema de produção. O cliente tem instâncias de aplicação de produção pré-instaladas nos sistemas de não-produção da camada de aplicativo. Em caso de failover, as instâncias de não produção seriam desligadas, os nomes virtuais das VMs de produção foram transferidos para as VMs não-produtoras (depois de atribuir novos endereços IP em DNS), e as instâncias de produção pré-instaladas estão sendo iniciadas

### <a name="sap-central-services-clusters"></a>Clusters sap serviços centrais
Os clusters SAP Central Services que estão usando discos compartilhados (Windows), compartilhamentos de SMB (Windows) ou NFS são um pouco mais difíceis de replicar. No lado do Windows, a replicação de armazenamento do Windows é uma solução possível. No Linux rsync é uma solução viável.



## <a name="non-supported-scenario"></a>Cenário não suportado
Há uma lista de cenários, que não são suportados para carga de trabalho SAP em arquiteturas Azure. **Não suportado** significa que o SAP e a Microsoft não serão capazes de suportar essas configurações e precisam adiar para um eventual terceiro envolvido que forneceu software para estabelecer tais arquiteturas. Duas das categorias são:

- Armazenamento de aparelhos macios: Há uma série de aparelhos macios de armazenamento oferecidos no mercado Azure. Alguns dos fornecedores oferecem documentação própria sobre como usar esses aparelhos macios de armazenamento no Azure relacionados ao software SAP. O suporte de configurações ou implantações envolvendo tais aparelhos macios de armazenamento precisa ser fornecido pelo fornecedor desses aparelhos macios de armazenamento. Esse fato também se manifesta na [nota de suporte sap #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- Estruturas de alta disponibilidade: apenas o Pacemaker e o Windows Server Failover Cluster são estruturas de alta disponibilidade suportadas para carga de trabalho SAP no Azure. Como mencionado anteriormente, a `Datakeeper` solução do SIOS é descrita e documentada pela Microsoft. No entanto, os componentes do SIOS `Datakeeper` precisam ser suportados através do SIOS como o fornecedor que fornece esses componentes. A SAP também listou outras estruturas certificadas de alta disponibilidade em várias notas SAP. Alguns deles foram certificados pelo fornecedor terceirizado para o Azure também. No entanto, o suporte para configurações que utilizam esses produtos precisa ser fornecido pelo fornecedor do produto. Diferentes fornecedores têm integração diferente nos processos de suporte sap. Você deve esclarecer qual processo de suporte funciona melhor para o fornecedor em particular antes de decidir usar o produto nas configurações SAP implantadas no Azure.
- Os clusters de disco compartilhados onde os arquivos de banco de dados estão residindo nos discos compartilhados não são suportados com exceção do maxDB. Para todos os outros bancos de dados, a solução suportada é ter locais de armazenamento separados em vez de um compartilhamento de SMB ou NFS ou disco compartilhado para configurar cenários de alta disponibilidade

Outros cenários, que não são suportados são cenários como:

- Cenários de implantação que introduzem uma latência de rede maior entre o nível de aplicativo SAP e o nível SAP `Hybris`DBMS na arquitetura comum do SAP, como mostrado no NetWeaver, S/4HANA e por exemplo. Isso inclui:
    - Implantação de um dos níveis no local, enquanto o outro nível é implantado no Azure
    - Implantando o nível de aplicação SAP de um sistema em uma região Azure diferente do nível DBMS
    - Implantação de um nível em data centers que estão co-localizados para o Azure e o outro nível no Azure, exceto quando esses padrões de arquitetura são fornecidos por um serviço nativo do Azure
    - Implantação de dispositivos virtuais de rede entre o nível de aplicativo SAP e a camada DBMS
    - Aproveitando o armazenamento hospedado em data centers co-localizados no data center do Azure para o nível SAP DBMS ou diretório global de transporte SAP
    - Implantando as duas camadas com dois fornecedores de nuvem diferentes. Por exemplo, implantar o nível DBMS na Oracle Cloud Infrastructure e o nível de aplicativo no Azure
- Configurações de cluster HANA Pacemaker de várias instâncias
- Configurações do Windows Cluster com discos compartilhados através de SOFS ou SMB no ANF para bancos de dados SAP suportados no Windows. Em vez disso, recomendamos o uso de replicação nativa de alta disponibilidade dos bancos de dados específicos e o uso de pilhas de armazenamento separadas
- Implantação de bancos de dados SAP suportados no Linux com arquivos de banco de dados localizados em compartilhamentos NFS em cima da ANF, com exceção do SAP HANA
- Implantação do Oracle DBMS em qualquer outro sistema operacional convidado que não o Windows e o Oracle Linux. Veja também a [nota de suporte sap #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Cenário(s) que não testamos e, portanto, não temos experiência com lista como:

- Azure Site Recovery replicando VMs de camada DBMS. Como resultado, recomendamos aproveitar a funcionalidade de replicação assíncrona nativa do banco de dados para configuração potencial de recuperação de desastres
 

## <a name="next-steps"></a>Próximas etapas
Leia os próximos passos no planejamento e implementação do [Azure Virtual Machines para o SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)




  



